---
title: "Hack for SQLite use on Kubernetes"
draft: false
tags:
 - db
 - sqlite
 - kubernetes
 - mealie
 - inotifywait
 - rsync
---

This page captures a technique I developed to work around the issues you can encounter when using Persistent Volumes (PVs)
that contain SQLite databases used by containers in Kubernetes.
<!--more-->

## Situation

For containers within my Home Lab Kubernetes (k3s) that needed a persistent state I have been using a pattern where I 
would create a PV (and PVC) to be able to store these files. The PV is mapped to a NFS share on my Synology NAS.

Most of the time this pattern works without any issues, albeit it can sometimes be a little slow if the application is frequently 
accessing the files stored in the PV/NAS.

However recently I encountered a **locked database** error with my [Mealie](https://github.com/mealie-recipes/mealie) instance 
running in my home cluster.

## Issue

After some investigation and some googling I came across [this comment in github](https://github.com/mealie-recipes/mealie/issues/2409#issuecomment-1732699915).  

So for my case the latency of the calls to the SQLite database file(s) stored on my NAS was causing the application to 
report the database as locked. Apparently a known limitation of using SQLite on network drives.

## My file based hacked solution

So ideally in this type of situation you would move to using a database that is better suited to handling network traffic, 
like [my Gogs DB migration]({{< ref "/posts/2023-06-23-Gogs-SQLite-to-MariaDB-migration.md">}}). But in this instance I 
wanted to try a container based solution still using SQLite.

For most _small scale_ situations SQLite performs great if the database file(s) are stored on a drive on the same host 
where the application resides.

Given this I came up with this hacky solution:

1. Copy the needed files from the NAS to an [emptyDir](https://kubernetes.io/docs/concepts/storage/volumes/#emptydir) volume (which is really a directory on the host)
2. Expose the volume to the application that needs access to SQLite
3. Monitor the files (using `inotifywait`), and when a file change is detected
4. Copy the modified files back to their original location on the NAS using `rsync`

For my Mealie deployment I use a Helm chart that I created so to implement the hacky solution I could just amend my custom
helm chart and redeploy it as needed. The code snippets below have been extracted from my custom helm chart.

### Volumes

This solution relies heavily on the use of Kubernetes volumes to copy, share and backup the required files.

```yaml
      volumes:
        - name: mealie-data
          persistentVolumeClaim:
            claimName: {{ .Values.volumes.claimName }}
        - name: mealie-working
          emptyDir: {}
        - name: backup-script
          configMap:
            name: {{ include "mealie.fullname" . }}-backup-script
            # Make the script executable
            defaultMode: 0777
```

### Copying the files to the emptyDir

We use busybox to copy the files from the PVC to the `emptyDir`, which actually resides on the machine hosting the pod.

```yaml      
      initContainers:
        - name: init
          image: "busybox:1.36"
          imagePullPolicy: IfNotPresent
          volumeMounts:
            - mountPath: /source
              name: mealie-data
            - mountPath: /target
              name: mealie-working
          command: ['sh', '-c', 'echo "The init container is running!" && cp -r /source/* /target/']
```

### Map the emptyDir to the application

```yaml
      containers:
        - name: {{ .Chart.Name }}
          securityContext:
            {{- toYaml .Values.securityContext | nindent 12 }}
          image: "{{ .Values.mealie.repository }}:{{ .Values.mealie.tag | default .Chart.AppVersion }}"
          imagePullPolicy: {{ .Values.mealie.pullPolicy }}
          ports:
            - name: http
              containerPort: {{ .Values.service.port }}
              protocol: TCP
          volumeMounts:
            # Map the emptyDir to the application (which now contains the files from the PVC)
            - mountPath: /app/data
              name: mealie-working
```

### Create the backup image

This solution uses `inotifywait` to watch for file changes and `rsync` to copy the changes back to the _source_ PVC. 
I couldn't find a trusted lightweight container which had these tools, so I decided to create my own using the `Dockerfile` below.

```dockerfile
#
# To build:
#   docker build -f .\Dockerfile -t [IMAGE_REGISTRY:PORT/]proof-of-concept/alpine-inotify .
# To publish:
#   docker push [IMAGE_REGISTRY:PORT/]proof-of-concept/alpine-inotify
#
FROM alpine:3.18.4

RUN /bin/sh -c set -eux; apk update; apk add inotify-tools rsync ; rm -rf /var/cache/apk/*
```

### Backup script

To make the solution a bit more portable I decided to use an external script rather than embed the script within the 
`proof-of-concept/alpine-inotify` image.

I embedded the script within a Kubernetes `ConfigMap` so that it formed part of the Kubernetes objects that were created 
when my custom helm chart was deployed.

```yaml
#
# Descriptions: Contains the backup script to watch for file changes in the /source directory
#               and run rsync to mirror the changes to the /target directory.
#
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ include "mealie.fullname" . }}-backup-script
  labels:
    {{- include "mealie.labels" . | nindent 4 }}
data:
   # Backup script
   backup.sh: |-
       #!/bin/bash -x
   
       echo "Waiting for {{ .Values.backup.startUpWaitSeconds }} seconds for application to startup..."
       sleep {{ .Values.backup.startUpWaitSeconds }}s
           
       echo "Backup script running..."
       
       while true; do
         echo ""
         echo "Waiting for new file changes..."
         echo ""
       
         FILE_CHANGES=$(inotifywait -r -e modify -e create -e delete -e move -q --format '%e %w%f' /source/ )
         echo $(echo $FILE_CHANGES | awk '{ print "Detected " $1 " event(s) on file: " $2 " at: "}') $(date)
         echo ""
       
         # Sleep a little to let file changes settle/finish
         sleep {{ .Values.backup.sleepValue }}{{ .Values.backup.sleepUnits }}
         # Backup
         echo "Backing up files..."
         # -a = -rlptgoD, p = set permissions, g = set group on destination, o = preserve owner
         rsync -rltDvz --del --force --ignore-errors /source/ /backup/
         
       done
```

### Backup container

Lastly we mapped the script within the `ConfigMap` into the backup container and called it.

```yaml
        - name: backup
          image: "proof-of-concept/alpine-inotify:latest"
          imagePullPolicy: IfNotPresent
          volumeMounts:
            - mountPath: /source
              name: mealie-working
            - mountPath: /backup
              name: mealie-data
            - mountPath: /backup.sh
              name: backup-script
              subPath: backup.sh
          command: [ 'sh', './backup.sh' ]
```

## Reflection on solution

I will admit this solution is a bit hacky as it relies upon the `rsync` file copy working correctly and not corrupting 
the SQLite database or any of the other files needed by the application. But given this is just for my own use and I 
have backups of the Mealie data I am not too worried if that happens.

Getting this solution to work correctly took sometime, but did allow me to play with Alpine linux and some linux tools 
that I had not used before. A good learning experience!
