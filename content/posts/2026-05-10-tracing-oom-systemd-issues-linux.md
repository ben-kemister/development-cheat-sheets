---
title: "Tacking down OOM issues on Linux"
date: 2026-05-10T17:32:55+11:00
draft: false
tags:
- k3s
- linux
- systemd
---

This post covers the steps used to tack down an `Out of memory: Kill process.` impacting a critical `systemd` service.
<!--more-->

## Initial problem

In my personal k3s (Kubernetes) cluster one of the master Nodes went into a `NodeReady` state.
```shell
kubectl get nodes
```
```text
NAME            STATUS     ROLES                       AGE    VERSION
...
optiplex-7050   NotReady   control-plane,etcd,master   728d   v1.31.13+k3s1
...
```

## Investigation - Node NoteReady

Looking at the Conditions of the Node it looks like the Kubelet had stopped reporting the status of the node:
```shell
kubectl describe node optiplex-7050
```
```text
Conditions:
  Type             Status    LastHeartbeatTime                 LastTransitionTime                Reason              Message
  ----             ------    -----------------                 ------------------                ------              -------
  EtcdIsVoter      True      Sun, 10 May 2026 06:13:50 +1000   Sun, 29 Dec 2024 09:53:54 +1100   MemberNotLearner    Node is a voting member of the etcd cluster
  MemoryPressure   Unknown   Sat, 09 May 2026 20:32:28 +1000   Sat, 09 May 2026 20:34:20 +1000   NodeStatusUnknown   Kubelet stopped posting node status.
  DiskPressure     Unknown   Sat, 09 May 2026 20:32:28 +1000   Sat, 09 May 2026 20:34:20 +1000   NodeStatusUnknown   Kubelet stopped posting node status.
  PIDPressure      Unknown   Sat, 09 May 2026 20:32:28 +1000   Sat, 09 May 2026 20:34:20 +1000   NodeStatusUnknown   Kubelet stopped posting node status.
  Ready            Unknown   Sat, 09 May 2026 20:32:28 +1000   Sat, 09 May 2026 20:34:20 +1000   NodeStatusUnknown   Kubelet stopped posting node status.
```

On the Node the `k3s.service` had exited with a FAILURE, but was trying to restart:
```shell
$ sudo systemctl status k3s.service
● k3s.service - Lightweight Kubernetes
     Loaded: loaded (/etc/systemd/system/k3s.service; enabled; vendor preset: enabled)
     Active: activating (auto-restart) (Result: exit-code) since Sat 2026-05-09 20:18:23 UTC; 338ms ago
       Docs: https://k3s.io
    Process: 2671118 ExecStartPre=/bin/sh -xc ! /usr/bin/systemctl is-enabled --quiet nm-cloud-setup.service (code=exited, status=0/SUCCESS)
    Process: 2671120 ExecStartPre=/sbin/modprobe br_netfilter (code=exited, status=0/SUCCESS)
    Process: 2671121 ExecStartPre=/sbin/modprobe overlay (code=exited, status=0/SUCCESS)
    Process: 2671122 ExecStart=/usr/local/bin/k3s server --config /etc/rancher/k3s/config.yaml (code=exited, status=1/FAILURE)
   Main PID: 2671122 (code=exited, status=1/FAILURE)
        CPU: 1.352s
```

Looking at the `k3s.service` dependencies it seems like the `iscsid.service` had failed:
```shell
$ sudo systemctl list-dependencies k3s.service
k3s.service
● ├─system.slice
● ├─network-online.target
● │ └─systemd-networkd-wait-online.service
● └─sysinit.target
●   ├─apparmor.service
●   ├─blk-availability.service
●   ├─dev-hugepages.mount
●   ├─dev-mqueue.mount
●   ├─finalrd.service
×   ├─iscsid.service  <-- FAILED
●   ├─keyboard-setup.service
```

Looking at the status of the `iscsid.service` it was killed by the Out Of Memory (OOM) killer:
```shell
$ sudo systemctl status iscsid.service
× iscsid.service - iSCSI initiator daemon (iscsid)
     Loaded: loaded (/lib/systemd/system/iscsid.service; enabled; vendor preset: enabled)
     Active: failed (Result: oom-kill) since Sat 2026-05-09 10:35:03 UTC; 9h ago
TriggeredBy: ● iscsid.socket
       Docs: man:iscsid(8)
   Main PID: 696 (code=killed, signal=KILL)
        CPU: 4.278s

...
May 09 10:35:03 optiplex-7050 systemd[1]: iscsid.service: A process of this unit has been killed by the OOM killer.
May 09 10:35:03 optiplex-7050 systemd[1]: iscsid.service: Main process exited, code=killed, status=9/KILL
May 09 10:35:03 optiplex-7050 systemd[1]: iscsid.service: Failed with result 'oom-kill'.
May 09 10:35:03 optiplex-7050 systemd[1]: iscsid.service: Consumed 4.278s CPU time.
```

Looking at the kernel logs (`dmesg`) for to see the OOM killer’s report:
```shell
$ sudo dmesg -T | grep -i "killed process"
[Sat May  9 10:34:42 2026] Out of memory: Killed process 1587999 (livenessprobe) total-vm:1246184kB, anon-rss:8000kB, file-rss:0kB, shmem-rss:0kB, UID:0 pgtables:140kB oom_score_adj:1000
[Sat May  9 10:34:49 2026] Out of memory: Killed process 963 (containerd) total-vm:1561184kB, anon-rss:51596kB, file-rss:0kB, shmem-rss:0kB, UID:0 pgtables:760kB oom_score_adj:0
[Sat May  9 10:34:49 2026] Out of memory: Killed process 1556532 (containerd-shim) total-vm:1239056kB, anon-rss:4416kB, file-rss:0kB, shmem-rss:0kB, UID:0 pgtables:124kB oom_score_adj:1
[Sat May  9 10:35:00 2026] Out of memory: Killed process 696 (iscsid) total-vm:15072kB, anon-rss:3492kB, file-rss:11508kB, shmem-rss:24kB, UID:0 pgtables:72kB oom_score_adj:-17
[Sat May  9 10:35:00 2026] Out of memory: Killed process 744 (k3s-server) total-vm:29943712kB, anon-rss:11498952kB, file-rss:0kB, shmem-rss:0kB, UID:0 pgtables:34424kB oom_score_adj:-999
```

> The OOM killer doesn't always kill the "guilty" process that leaked memory; it often kills a "victim" that is easy to replace or has a high "badness score".

This showed that the `iscsid.service` PID 696 was killed _in preference_ to the `k3s-server` process as it's `oom_score_adj` 
was not as low (i.e. A score of -1000 effectively makes the process "unkillable" by the OOM killer.).

## Fix - Modifying `iscsid.service` OOMScoreAdjust

Since the `iscsid.service` is critical for storage, we should tell the kernel not to kill it by adjusting its OOM score. 

Create a "drop-in" configuration file with the command `sudo systemctl edit iscsid.service`:
Set the Service with a `OOMScoreAdjust=-1000`
```text
### Editing /etc/systemd/system/iscsid.service.d/override.conf
### Anything between here and the comment below will become the new contents of the file

[Service]
# tell the kernel not to kill this process by adjusting its OOM score (was -17)
OOMScoreAdjust=-1000

### Lines below this comment will be discarded
```

Save and exit, then reload: `sudo systemctl daemon-reload`

You can see now the override.conf file being used for `iscsid.service`:
```shell
$ sudo systemctl status iscsid.service
× iscsid.service - iSCSI initiator daemon (iscsid)
     Loaded: loaded (/lib/systemd/system/iscsid.service; enabled; vendor preset: enabled)
    Drop-In: /etc/systemd/system/iscsid.service.d
             └─override.conf      <-- OVERRIDE FILE!
     Active: failed (Result: oom-kill) since Sat 2026-05-09 10:35:03 UTC; 10h ago
...
```

Start the service: with `sudo systemctl start iscsid.service` and we can check the status to see if it is running now;
```shell
$ sudo systemctl status iscsid.service
● iscsid.service - iSCSI initiator daemon (iscsid)
     Loaded: loaded (/lib/systemd/system/iscsid.service; enabled; vendor preset: enabled)
    Drop-In: /etc/systemd/system/iscsid.service.d
             └─override.conf
     Active: active (running) since Sat 2026-05-09 20:40:56 UTC; 4s ago    <--- YAY SERVICE IS RUNNING!
```

I tried to restart the `k3s.service` but something else is not working:
```shell
$ sudo systemctl restart k3s.service
Job for k3s.service failed because the control process exited with error code.
See "systemctl status k3s.service" and "journalctl -xeu k3s.service" for details.
```

## Investigation - `k3s.service` not stating

Looking at the `journalctl` logs with `sudo journalctl -xeu k3s.service` looked like there was an issue with the etcd wal (Write Ahead Log) file?
```shell
May 09 20:45:48 optiplex-7050 systemd[1]: Starting Lightweight Kubernetes...
░░ Subject: A start job for unit k3s.service has begun execution
░░ Defined-By: systemd
░░ Support: http://www.ubuntu.com/support
░░
░░ A start job for unit k3s.service has begun execution.
░░
░░ The job identifier is 533977.
May 09 20:45:48 optiplex-7050 sh[2676801]: + /usr/bin/systemctl is-enabled --quiet nm-cloud-setup.service
May 09 20:45:48 optiplex-7050 sh[2676802]: Failed to get unit file state for nm-cloud-setup.service: No such file or directory
May 09 20:45:48 optiplex-7050 k3s[2676805]: time="2026-05-09T20:45:48Z" level=info msg="Starting k3s v1.31.13+k3s1 (a4ca1794)"
May 09 20:45:48 optiplex-7050 k3s[2676805]: time="2026-05-09T20:45:48Z" level=info msg="Managed etcd cluster bootstrap already complete and initialized"
May 09 20:45:48 optiplex-7050 k3s[2676805]: time="2026-05-09T20:45:48Z" level=info msg="Starting temporary etcd to reconcile with datastore"
May 09 20:45:48 optiplex-7050 k3s[2676805]: {"level":"info","ts":"2026-05-09T20:45:48.629163Z","caller":"embed/etcd.go:134","msg":"configuring socket options","reuse-address":true,"reuse-port":true}
May 09 20:45:48 optiplex-7050 k3s[2676805]: {"level":"info","ts":"2026-05-09T20:45:48.629223Z","caller":"embed/etcd.go:140","msg":"configuring peer listeners","listen-peer-urls":["http://127.0.0.1:2400"]}
May 09 20:45:48 optiplex-7050 k3s[2676805]: {"level":"info","ts":"2026-05-09T20:45:48.629448Z","caller":"embed/etcd.go:148","msg":"configuring client listeners","listen-client-urls":["http://127.0.0.1:2399"]}
May 09 20:45:48 optiplex-7050 k3s[2676805]: {"level":"info","ts":"2026-05-09T20:45:48.629575Z","caller":"embed/etcd.go:323","msg":"starting an etcd server","etcd-version":"3.5.21","git-sha":"HEAD","go-version":"go1.23.12","go-os":"linux","go-arch":"amd64","max-cpu-set">
May 09 20:45:48 optiplex-7050 k3s[2676805]: {"level":"info","ts":"2026-05-09T20:45:48.652729Z","caller":"etcdserver/backend.go:81","msg":"opened backend db","path":"/var/lib/rancher/k3s/server/db/etcd-tmp/member/snap/db","took":"22.926614ms"}
May 09 20:45:49 optiplex-7050 k3s[2676805]: {"level":"info","ts":"2026-05-09T20:45:49.070687Z","caller":"embed/etcd.go:408","msg":"closing etcd server","name":"optiplex-7050-fb9bc235","data-dir":"/var/lib/rancher/k3s/server/db/etcd-tmp","advertise-peer-urls":["http://1>
May 09 20:45:49 optiplex-7050 k3s[2676805]: {"level":"info","ts":"2026-05-09T20:45:49.070836Z","caller":"embed/etcd.go:410","msg":"closed etcd server","name":"optiplex-7050-fb9bc235","data-dir":"/var/lib/rancher/k3s/server/db/etcd-tmp","advertise-peer-urls":["http://12>
May 09 20:45:49 optiplex-7050 k3s[2676805]: time="2026-05-09T20:45:49Z" level=fatal msg="Failed to reconcile with temporary etcd: walpb: crc mismatch"
May 09 20:45:49 optiplex-7050 systemd[1]: k3s.service: Main process exited, code=exited, status=1/FAILURE
░░ Subject: Unit process exited
░░ Defined-By: systemd
░░ Support: http://www.ubuntu.com/support
░░
░░ An ExecStart= process belonging to unit k3s.service has exited.
░░
░░ The process' exit code is 'exited' and its exit status is 1.
May 09 20:45:49 optiplex-7050 systemd[1]: k3s.service: Failed with result 'exit-code'.
░░ Subject: Unit failed
░░ Defined-By: systemd
░░ Support: http://www.ubuntu.com/support
░░
░░ The unit k3s.service has entered the 'failed' state with result 'exit-code'.
May 09 20:45:49 optiplex-7050 systemd[1]: Failed to start Lightweight Kubernetes.
```

The error `walpb: crc mismatch` confirms that the etcd Write-Ahead Log (WAL) is physically corrupted. 
This can happen after an unclean shutdown, power failure, or from the OOM kill I experienced.

## Fix - Remove and Rejoin Node

As the cluster has other **healthy** master nodes, we can purge the corrupted node and let it resync from the healthy peers.

### On a **HEALTHY** master node of the cluster...

First you need to install `etcdctl` as K3s does not bundle `etcdctl`, see: [Using etcdctl | k3s](https://docs.k3s.io/advanced#using-etcdctl)

Then you can identify the id of the failing node:
```shell
sudo etcdctl member list --write-out=table \
  --cacert=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt \
  --cert=/var/lib/rancher/k3s/server/tls/etcd/client.crt  \
  --key=/var/lib/rancher/k3s/server/tls/etcd/client.key 
```
```text
+------------------+---------+------------------------+---------------------------+-----------------------------+------------+
|        ID        | STATUS  |          NAME          |        PEER ADDRS         |       CLIENT ADDRS          | IS LEARNER |
+------------------+---------+------------------------+---------------------------+-----------------------------+------------+
| 3322056e99494079 | started | optiplex-7050-fb9bc235 | https://192.168.0.191:2380 | https://192.168.0.191:2379 |      false |
...
```

Remove it from the etcd quorum:
```shell
sudo etcdctl member remove 3322056e99494079 \
  --cacert=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt \
  --cert=/var/lib/rancher/k3s/server/tls/etcd/client.crt  \
  --key=/var/lib/rancher/k3s/server/tls/etcd/client.key 
```
```text
Member 3322056e99494079 removed from cluster  44e4a7149e85789
```

Delete the node:
```shell
sudo k3s kubectl delete node optiplex-7050
node "optiplex-7050" deleted
```

And find the cluster token (I needed this to rejoin the corrupted node to cluster):
```shell
sudo cat /var/lib/rancher/k3s/server/node-token
K10c657cd35ac7a9<REDACTED>::server:a1c6d5bc<REDACTED>6c0ab0c
```

### On the **CORRUPTED** master node...

Wipe the local database:
```shell
sudo systemctl stop k3s
sudo rm -rf /var/lib/rancher/k3s/server/db
```

> If this the Node was the first one created when the cluster was initially created you need to do some additional steps
> namely...

Check/update the k3s `/etc/rancher/k3s/config.yaml` configuration file abd disable the `cluster-init: true` if present:
```shell
nano /etc/rancher/k3s/config.yaml
```
```yaml
# cluster-init: true  <-- Disable if present
```

Check/update the cluster token file `sudo cat /etc/rancher/k3s/cluster-token`, this needs to match the token value from 
a healthy node (see above)

Check/update the _Service_ section of the k3s systemd service definition `sudo cat /etc/systemd/system/k3s.service`.
It should look like this:
```text
ExecStart=/usr/local/bin/k3s server --server https://<HEALTHY_MASTER_IP>:6443 --config /etc/rancher/k3s/config.yaml --token-file /etc/rancher/k3s/cluster-token
```

Start the k3s service with: `sudo systemctl start k3s`.

If the k3s service starts properly then it will join as a Node of the cluster:
```shell
$ sudo k3s kubectl get nodes
```
```text
NAME            STATUS   ROLES                       AGE    VERSION
optiplex-7050   Ready    control-plane,etcd,master   29m    v1.31.13+k3s1
...
```

In this situation there was a bunch of other failed systemd services (these were identified with `sudo systemctl --failed`),
so I decided to reboot the machine `sudo reboot` 

## What caused it?

I am still unsure what caused k3s to consume so much memory that it caused the OS to run the Out Of Memory (OOM) killer 
in the first place.

My first guess is that there might be one or more Pods which contain applications that have memory leak and do not have 
any resource limits in place...