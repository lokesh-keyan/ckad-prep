# Kubernetes Deployments

## Deployment Strategies

Kubernetes supports two primary deployment strategies:

### Recreate Strategy

With the recreate strategy, if you have, for example, five replicas, all replicas are terminated and then replaced with new ones running the updated version. 

**Characteristics:**
- All old replicas are terminated first
- New replicas are created with updated version
- **Drawback**: Potential downtime during replacement
- Application is temporarily unavailable

### Rolling Update Strategy

The rolling update strategy gradually replaces the old version with the new one by updating replicas one at a time. This ensures continuous application availability during the upgrade. 

**Characteristics:**
- Updates replicas incrementally
- Maintains application availability
- **Default strategy** when no specific strategy is configured
- Zero-downtime deployments

---

## Updating a Deployment

There are multiple methods to update your deployment. You can update the container image version, modify labels, or adjust the number of replicas.

### Method 1: Using Definition File

If you are using a deployment definition file, simply modify the file and apply the update:

```sh
kubectl apply -f deployment-definition.yml
```

This triggers a new rollout and creates a new deployment revision.

### Method 2: Direct Image Update

Alternatively, update just the container image with:

```sh
kubectl set image deployment/myapp-deployment nginx=nginx:1.9.1
```

---

## Example Deployment Configuration

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
  labels:
    app: myapp
    type: front-end
spec:
  replicas: 3
  selector:
    matchLabels:
      type: front-end
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
      containers:
      - name: nginx-container
        image: nginx:1.7.1
```

---

## Examining Deployment Details

To get detailed information about your deployment:

```sh
kubectl describe deployment myapp-deployment
```

---

## Rolling Back Deployments

### Performing a Rollback

To perform a rollback to the previous revision:

```sh
kubectl rollout undo deployment/myapp-deployment
```

### Before Rollback

Check the current state of ReplicaSets:

```sh
kubectl get replicasets
```

**Output:**
```
NAME                        DESIRED   CURRENT   READY   AGE
myapp-deployment-67c749c58c  0         0         0       22m
myapp-deployment-7d57dbd8d   5         5         5       20m
```

### After Rollback

After running the rollback command:

```sh
kubectl get replicasets
```

**Output:**
```
NAME                        DESIRED   CURRENT   READY   AGE
myapp-deployment-67c749c58c  5         5         5       22m
myapp-deployment-7d57dbd8d   0         0         0       20m
```

### Rollback Confirmation

When you run the undo command, you should see:

```sh
kubectl rollout undo deployment/myapp-deployment
```

**Output:**
```
deployment "myapp-deployment" rolled back
```

---

## Additional Rollout Commands

### Check Rollout Status

```sh
kubectl rollout status deployment/myapp-deployment
```

### View Rollout History

```sh
kubectl rollout history deployment/myapp-deployment
```

### Rollback to Specific Revision

```sh
kubectl rollout undo deployment/myapp-deployment --to-revision=2
```

### Pause/Resume Rollout

```sh
# Pause rollout
kubectl rollout pause deployment/myapp-deployment

# Resume rollout
kubectl rollout resume deployment/myapp-deployment
```

---

## Quick Reference Summary

Below is a summary of the key commands used to create, update, monitor, and roll back your deployments:

| Command | Description |
|---------|-------------|
| `kubectl create -f deployment-definition.yml` | Create a new deployment |
| `kubectl get deployments` | List current deployments |
| `kubectl apply -f deployment-definition.yml` | Apply changes from the deployment definition file |
| `kubectl set image deployment/myapp-deployment nginx=nginx:1.9.1` | Update a deployment's container image |
| `kubectl rollout status deployment/myapp-deployment` | Check the status of the deployment rollout |
| `kubectl rollout history deployment/myapp-deployment` | View rollout history |
| `kubectl rollout undo deployment/myapp-deployment` | Roll back to the previous deployment revision |
| `kubectl rollout undo deployment/myapp-deployment --to-revision=N` | Roll back to a specific revision |

---

## Best Practices

1. **Use Rolling Updates**: Prefer rolling updates for zero-downtime deployments
2. **Monitor Rollouts**: Always check rollout status after deployment changes
3. **Keep History**: Maintain rollout history for easy rollbacks
4. **Test Before Production**: Validate deployments in staging environments
5. **Resource Limits**: Set appropriate resource requests and limits
6. **Health Checks**: Use readiness and liveness probes for reliable deployments
