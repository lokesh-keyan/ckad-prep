# Jobs and CronJobs Lab Exercise

## Lab Exercises and Solutions

### 1. Deploying the Pod

Create a simple pod that runs the throw-dice application:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: throw-dice-pod
spec:
  containers:
    - image: kodekloud/throw-dice
      name: throw-dice
  restartPolicy: Never
```

**Apply the configuration:**

```sh
kubectl apply -f throw-dice-pod.yaml
```

**Verification:**

```sh
# Check pod status
kubectl get pods

# View pod logs
kubectl logs throw-dice-pod
```

---

### 2. Creating a Job to Measure Attempts

Create a Job that will retry the throw-dice application until it succeeds:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: throw-dice-job
spec:
  template:
    spec:
      containers:
      - name: throw-dice-job
        image: kodekloud/throw-dice
      restartPolicy: Never
  backoffLimit: 25
```

**Apply the Job:**

```sh
kubectl apply -f throw-dice-job.yaml
```

**Key Configuration:**
- **`backoffLimit: 25`**: Maximum number of retry attempts
- **`restartPolicy: Never`**: Required for Jobs (prevents infinite restarts)

---

### 3. How many attempts were made?

Check the Job details to see how many attempts were made:

```sh
kubectl describe job throw-dice-job
```

**What to look for in the output:**
- **Pods Statuses**: Shows succeeded/failed pod counts
- **Events**: Lists each attempt and its outcome
- **Conditions**: Shows job completion status

**Example output sections:**
```
Pods Statuses:  1 Succeeded / 0 Failed
Start Time:     Mon, 05 Aug 2025 10:30:00 +0000
Completions:    1/1
Duration:       45s
```

---

### 4. Set completions and parallelism

Configure the Job to run multiple completions with parallel execution:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: throw-dice-job-parallel
spec:
  completions: 3
  parallelism: 3
  template:
    spec:
      containers:
      - name: throw-dice-job
        image: kodekloud/throw-dice
      restartPolicy: Never
  backoffLimit: 25
```

**Configuration Explanation:**
- **`completions: 3`**: Job needs 3 successful completions
- **`parallelism: 3`**: Run up to 3 pods simultaneously

**Verification:**

```sh
# Watch job progress
kubectl get jobs -w

# Check pod creation
kubectl get pods -l job-name=throw-dice-job-parallel
```

---

### 5. CronJob Configuration

Create a CronJob to run the throw-dice application on a schedule:

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: throw-dice-cron-job
spec:
  schedule: "30 21 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: throw-dice
            image: kodekloud/throw-dice
          restartPolicy: Never
```

**Schedule Explanation:**
- **`"30 21 * * *"`**: Runs daily at 9:30 PM
- Format: `minute hour day month day-of-week`

**Apply the CronJob:**

```sh
kubectl apply -f throw-dice-cronjob.yaml
```

**Verification:**

```sh
# Check CronJob status
kubectl get cronjobs

# View CronJob details
kubectl describe cronjob throw-dice-cron-job

# Check jobs created by CronJob
kubectl get jobs
```

---

## Additional Job and CronJob Commands

### Job Management

```sh
# Delete a job and its pods
kubectl delete job throw-dice-job

# Delete job but keep pods
kubectl delete job throw-dice-job --cascade=orphan

# View job logs
kubectl logs job/throw-dice-job

# Get job in YAML format
kubectl get job throw-dice-job -o yaml
```

### CronJob Management

```sh
# Suspend a CronJob
kubectl patch cronjob throw-dice-cron-job -p '{"spec":{"suspend":true}}'

# Resume a CronJob
kubectl patch cronjob throw-dice-cron-job -p '{"spec":{"suspend":false}}'

# Manually trigger a CronJob
kubectl create job manual-run --from=cronjob/throw-dice-cron-job

# View CronJob history
kubectl get jobs -l cronjob=throw-dice-cron-job
```

---

## Common Schedule Patterns

| Schedule | Description | Example |
|----------|-------------|---------|
| `"0 */2 * * *"` | Every 2 hours | Runs at 00:00, 02:00, 04:00, etc. |
| `"0 9 * * 1-5"` | Weekdays at 9 AM | Monday to Friday at 9:00 AM |
| `"*/15 * * * *"` | Every 15 minutes | Runs every 15 minutes |
| `"0 0 1 * *"` | Monthly | First day of every month at midnight |
| `"0 0 * * 0"` | Weekly | Every Sunday at midnight |

---

## Best Practices

1. **Set appropriate backoffLimit**: Prevent infinite retries with reasonable limits
2. **Use proper restartPolicy**: Always use `Never` or `OnFailure` for Jobs
3. **Resource limits**: Set CPU/memory limits for job containers
4. **Cleanup policies**: Configure `ttlSecondsAfterFinished` for automatic cleanup
5. **Monitoring**: Watch job progress and check logs for debugging
6. **CronJob history**: Set `successfulJobsHistoryLimit` and `failedJobsHistoryLimit`

---

## Key Takeaways

- **Jobs**: For one-time or batch processing tasks
- **CronJobs**: For scheduled, recurring tasks
- **Parallelism**: Control concurrent pod execution
- **Completions**: Define success criteria for jobs
- **Monitoring**: Use `kubectl describe` and `kubectl logs` for troubleshooting