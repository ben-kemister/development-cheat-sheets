---
title: "Recurring Jobs"
tags:
- kubernetes
- longhorn
- backup
---

[Recurring Jobs](https://longhorn.io/docs/1.7.0/snapshots-and-backups/scheduling-backups-and-snapshots/) can be used to 
schedule [longhorn](../longhorn) volume maintenance activities such as snapshot cleanup and backup. 
<!--more-->

## Weekly backup example

```yaml
#
# Description: The default weekly backup job.
#             By default, all Volumes belong to the 'default' group, so unless specified otherwise this backup job will apply.
#
# kubectl apply -f ./default-weekly-backup-recurring-job.yaml
#
---
apiVersion: longhorn.io/v1beta2
kind: RecurringJob
metadata:
  name: default-weekly-backup
  namespace: longhorn-system
spec:
  # The number of jobs to run concurrently. It should be no less than 1.
  concurrency: 1
  # 3pm UTC
  cron: 0 15 * * ?
  # Any volumes belonging to these groups will be targeted by this job
  # Having 'default' in groups will automatically schedule this recurring job to any volume with no recurring job
  groups:
    - default
    - weekly-backup
    - all-weekly-jobs
  labels: {}
  name: default-weekly-backup
  # How many snapshots/backups Longhorn will retain for each volume job. It should be no less than 1.
  retain: 3
  # backup - periodically create snapshots then do backups after cleaning up outdated snapshots
  task: backup
```

