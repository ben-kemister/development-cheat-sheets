---
title: "Building images with Kaniko in Jenkins"
date: 2023-10-27T10:28:32Z
draft: false
tags:
- kaniko
- jenkins
- kubernetes
- containers
- docker
---

An overview of how I set up Jenkins to be able to build (docker) images with [kaniko](https://github.com/GoogleContainerTools/kaniko) 
inside my Kubernetes cluster.
<!--more-->

## Goals and situation

This project spawns out of a want/need to automate the creation of custom Docker images. I wanted to be able to create 
these custom images within my home CI/CD environment (Jenkins) and be able to publish them to my private image registry 
using a GitOps style pattern which is triggered from a commit to my private git repository (gogs).

I also wanted to parameterise the location of my private image registry so that if it changes in the future it can be 
easily updated in the build.

## kaniko:debug image

The entrypoint on the standard kaniko image is set to `/kaniko/executor`. 
This is not an issue if the required build arguments are passed into the container as soon as it start.

Unfortunately this doesn't work in (my?) Jenkins setup as the build job spins up a pod with all containers needed to
do the build tasks. When this happens we need the kaniko container to wait until the earlier build stages/steps, such as 
git checkout of the source code building of any required artifacts ect, to have been completed before we are ready to 
build the image.

To accommodate this pattern we can use the [kaniko:debug](https://github.com/GoogleContainerTools/kaniko#debug-image) image 
which includes a busybox shell. We can then use a simple `sleep <duration>` command to keep the kaniko container alive 
until it is needed.

For this to work we need to override the default image entrypoint, by using the `command: [ "/busybox/sleep" ]` in our
Kubernetes pod.

## Project Folder structure

Below is an overview of the important files within this project:

```text
.jenkins/build-pod.yaml
Dockerfile
Jenkinsfile 
```

## Jenkins pod template

I like to externalise the pod template that Jenkins uses in a separate file. 
For this project I came up the Jenkins pod template below:

```yaml
#
# .jenkins/build-pod.yaml
# This is the pod template that Jenkins will use for the build
# 
---
apiVersion: v1
kind: Pod
metadata:
  labels:
    build-job: kaniko-build
spec:
  containers:
    - name: jnlp
      image: "jenkins/inbound-agent:4.11.2-4"
    - name: kaniko
      # Use the 'debug' version as it contains busybox (and sleep)
      # which means we can get the kaniko container to wait for the other Jenkins build stages to complete
      image: "kaniko-project/executor:debug"
      # Override the default Kaniko entrypoint/command
      command: [ "/busybox/sleep" ]
      args: ["10m"]
      tty: true
      volumeMounts:
        - mountPath: /kaniko/.docker
          name: kaniko-secret
  volumes:
    - name: kaniko-secret
      secret:
        secretName: kaniko-image-registry-secret

  nodeSelector:
    # Best to build on 'standard' chip architecture
    kubernetes.io/arch: amd64
```

## Image registry secret

For kaniko to push the built image to a private, or protected, image registry it needs the authentication details.
We are going to use a Kubernetes secret which will be mapped to the kaniko container at runtime.

### base64 encoded credentials

First we need to base64 encode the image registry credentials for the kaniko configuration:

```shell
echo -n <USERNAME>:<PASSWORD> | base64
```

### kaniko config.json

Next we need to embed these credentials into kaniko's configuraiton file which looks like this:

```json
{
  "auths": {
    "https://<IMAGE_REGISTRY_HOST>:<PORT>" : {
      "auth": "<YOUR_BASE64_ENCODED_CREDENTIALS>"
    }
  }
}
```

### Create Kubernetes secret

Lastly we need to create a secret in Kubernetes to store the kaniko configuration. For this we use the command:

```shell
kubectl create secret generic kaniko-image-registry-secret --from-file=config.json
```

## Jenkins Properties

I wanted to parameterise my private image registry location so that is more portable and easier to change in the future.

To do this I used Jenkins Global Environmental variables, which can be reference in the build job or Jenkinfile and are
resolved to their set values during the build.

To set these in Jenkins go to: Dashboard --> Manage Jenkins --> System --> Global properties --> Environment variables

Here you can set whatever values you need, for me this was:
1.
   * Name: IMAGE_REGISTRY_PULL
   * Value: <IMAGE_REGISTRY_HOST>:<PORT>
2.
   * Name: IMAGE_REGISTRY_PUSH
   * Value: <IMAGE_REGISTRY_HOST>:<PORT>

## Dockerfile

For this project we have a very simple `Dockerfile` as follows:

```dockerfile
#
# Description: Builds a simple image with inotify-tools and rsync
#
ARG IMAGE_REGISTRY=""
FROM ${IMAGE_REGISTRY}alpine:3.18.4

RUN /bin/sh -c set -eux; apk update; apk add inotify-tools rsync ; rm -rf /var/cache/apk/*
```

> Note the use of the Docker ARG `IMAGE_REGISTRY` so that we can set a pull registry rather than using the kaniko defaults.

## Jenkinsfile

Next we create our Jenkinfile something like the following for using a `Dockerfile`

```groovy
pipeline {

    agent {
        kubernetes {
            yamlFile '.jenkins/build-pod.yaml'
            //retries 1
        }
    }

    options {
        // We want to skip any of the later stages if the Build becomes unstable
        skipStagesAfterUnstable()
    }

    stages {
        stage("echo variables"){
            steps {
                echo "Hello from Jenkins build script"
                echo "Using image pull registry: ${IMAGE_REGISTRY_PULL}"
                echo "Using image push registry: ${IMAGE_REGISTRY_PUSH}"
            }
        }
        stage('kaniko build') {
            steps {
                echo 'Kaniko building...'
                container('kaniko') {

                    script {
                        sh '''
                        /kaniko/executor --dockerfile `pwd`/Dockerfile \
                                        --context `pwd` \
                                        --build-arg "IMAGE_REGISTRY=${IMAGE_REGISTRY_PULL}/" \
                                        --destination=${IMAGE_REGISTRY_PUSH}/container-tools/inotify-rsync:latest
                        '''
                    }
                }
            }
        }
    }
}
```

## Jenkins build job and commit hook

The last things to do is to set up the Jenkins build job (just pointing it at your git repository and using the `Jenkinsfile`),
and a commit hook to trigger the _multibranch-scan-webhook-trigger_ at ``JENKINS_URL/multibranch-webhook-trigger/invoke?token=BUILD_JOB_TOKEN_HERE``
when commits are made to the git repository.

Sorry, I am not detailing these steps in this post you will need to find/discover these details on your own :)

## Done!

You should now have a working Jenkins build project which uses kaniko within your Kubernetes cluster to build your docker
images and push them to your image registry.

## Helpful links

Below are some links to the pages I found helpful for this project:

* [kaniko - Build Images In Kubernetes](https://github.com/GoogleContainerTools/kaniko)
* [How to use Kaniko to build container image on Jenkins](https://senertugrul.medium.com/how-to-use-kaniko-to-build-docker-images-on-jenkins-216a68caf7b8)
* [Kubernetes - Define a Command and Arguments for a Container](https://kubernetes.io/docs/tasks/inject-data-application/define-command-argument-container/)
* [Dockerfile FROM + ARG trick — parameterized dockerfile base image](https://jmarcos-cano.medium.com/dockerfile-from-arg-trick-42152ece677)