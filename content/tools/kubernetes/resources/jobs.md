---
title: Jobs
description: A guide to creating and managing Kubernetes Jobs for one-off, task-based workloads.
author: AI Assistant
date: 2024-06-04
tags: [kubernetes, containerization, batch, yaml, devops]
---

# 🚀 Kubernetes Jobs: Running One-Off Tasks

A **Job** in Kubernetes is a controller designed to manage workloads that run until a specified task is complete, 
and then stop. 
<!--more-->
Unlike standard Deployments, which keep running, a Job is ideal for tasks that must execute and terminate successfully 
(e.g., batch processing, migrations).

> For scheduled, repeating tasks, use a **CronJob** instead of a Job.

## Core Concepts

*   **Functionality:** A Job creates one or more Pods and continuously retries the execution of these Pods until the desired number of successful completions is reached.
*   **Completion:** The Job tracks successful Pod terminations. Once the target completion count is met, the Job is considered complete.
*   **Reliability:** If an initial Pod fails (e.g., due to node failure), the Job controller automatically starts a new Pod until the task succeeds.
*   **Cleanup:** Deleting a Job cleans up the Pods it created.

## 🛠️ Job Specification (`Job` Kind)

A Job object requires the standard Kubernetes fields (`apiVersion`, `kind`, `metadata`) and a crucial `.spec` section.

### Key Fields

| Field | Description | Requirement | Notes |
| :--- | :--- | :--- | :--- |
| `apiVersion` | `batch/v1` | Required | Specifies the API version. |
| `kind` | `Job` | Required | Identifies the resource type. |
| `spec.template` | Pod Template | Required | Defines the Pod specs (containers, image, etc.). |
| `spec.restartPolicy` | `Never` or `OnFailure` | Recommended | Only these policies are allowed for Jobs. |

### Job Labels
By default, Jobs automatically add labels like `batch.kubernetes.io/job-name` and `batch.kubernetes.io/controller-uid`.

## ⏱️ Timeouts and Deadlines

Jobs can be managed by time limits to prevent indefinite running or excessive retries.

### `activeDeadlineSeconds`
*   **Purpose:** This field sets an absolute time limit for the entire Job's duration, measured in seconds.
*   **Behavior:** Once the Job runtime exceeds this specified limit, **all running Pods are immediately terminated**, and the Job status will become `Failed` with the reason `DeadlineExceeded`.
*   **Precedence:** The `activeDeadlineSeconds` takes precedence over the `backoffLimit`. If the time limit is reached, the Job will stop creating new Pods, even if the `backoffLimit` has not been reached.
*   **Unset Behavior:** If `activeDeadlineSeconds` is unset, the Job will only terminate based on the failure limits (`backoffLimit`) or manual intervention.

**Example YAML Inclusion:**
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pi-with-timeout
spec:
  backoffLimit: 5 # Standard retry limit
  activeDeadlineSeconds: 3600 # Time limit: 1 hour
  template:
    spec:
      containers:
      - name: pi
        image: perl:5.34.0
        command: ["perl", "...", ...]
      restartPolicy: Never
```

## ⚙️ Job Patterns (Types of Jobs)

Jobs can be configured in three primary ways, controlled by the `.spec` field:

### 1. Non-Parallel Job (Single Task)
*   **Behavior:** Only one Pod is started. The Job completes as soon as this single Pod terminates successfully.
*   **Configuration:** Leave both `.spec.completions` and `.spec.parallelism` unset (they default to `1`).

### 2. Fixed Completion Count (Parallel Tasks)
*   **Use Case:** Running several independent tasks in parallel, all contributing to a single overall task completion.
*   **Configuration:** Specify `spec.completions` (a non-zero positive integer).
    *   `spec.parallelism` (optional): Controls how many Pods start at once (defaults to `1`).
    *   `completionMode`: Can be set to `"Indexed"` to assign unique indices (0 to `N-1`) to each Pod.

### 3. Work Queue Job (Distributed Tasks)
*   **Use Case:** Tasks that require multiple Pods to coordinate work from a shared source (e.g., processing a queue of items).
*   **Configuration:**
    *   **MUST** unset `spec.completions`.
    *   Set `spec.parallelism` to the desired non-negative integer.
    *   Pods must coordinate amongst themselves or an external service. The Job completes when all running Pods have terminated successfully.

## 🖥️ Example Implementation

### YAML Example (`controllers/job.yaml`)
This example runs a simple task (computing Pi to 2000 places) and waits for successful completion.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: perl:5.34.0
        command: ["perl", "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
  backoffLimit: 4 # Max number of retries
```

### Usage Commands

| Action           | Command                                   | Purpose                                              |
|:-----------------|:------------------------------------------|:-----------------------------------------------------|
| **Create Job**   | `kubectl apply -f [job.yaml]`             | Deploys the Job resource.                            |
| **Check Status** | `kubectl describe job pi`                 | Views overall status, completion counts, and events. |
| **List Pods**    | `kubectl get pods --selector=job-name=pi` | Lists all Pods associated with the Job.              |
| **View Logs**    | `kubectl logs jobs/pi`                    | Views the aggregated standard output for the Job.    |




