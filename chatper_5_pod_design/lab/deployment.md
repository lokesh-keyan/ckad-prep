# Deployment Lab Exercise

## Lab Questions and Solutions

### 1. Upgrade the application by setting the image on the deployment to `kodekloud/webapp-color:v2`

**Requirements:**
- Do not delete and re-create the deployment
- Only set the new image name for the existing deployment

**Solution:**

```sh
kubectl edit deployment frontend
```

**Alternative methods:**

```sh
# Method 1: Using kubectl set image
kubectl set image deployment/frontend webapp=kodekloud/webapp-color:v2

# Method 2: Using kubectl patch
kubectl patch deployment frontend -p '{"spec":{"template":{"spec":{"containers":[{"name":"webapp","image":"kodekloud/webapp-color:v2"}]}}}}'

# Method 3: Edit and apply YAML file
kubectl get deployment frontend -o yaml > frontend-deployment.yaml
# Edit the image in the file
kubectl apply -f frontend-deployment.yaml
```

**Verification:**

```sh
# Check rollout status
kubectl rollout status deployment/frontend

# Verify the new image
kubectl describe deployment frontend | grep Image

# Check pods for new image
kubectl get pods -o wide
```

---

### 2. Up to how many PODs can be down for upgrade at a time?

**Given Information:**
- Current strategy settings and number of PODs: 4
- Consider the rolling update strategy

**Answer:** `1`

**Explanation:**

In a rolling update strategy with 4 pods, by default:

- **maxUnavailable**: 25% (default) = 1 pod
- **maxSurge**: 25% (default) = 1 pod

This means:
- Maximum 1 pod can be unavailable during the update
- Maximum 1 additional pod can be created above the desired replica count
- Total pods during update: 4 (desired) + 1 (surge) = 5 pods maximum
- Minimum available pods: 4 (desired) - 1 (unavailable) = 3 pods minimum

**Verification Commands:**

```sh
# Check deployment strategy
kubectl describe deployment frontend | grep -A 10 "StrategyType"

# View detailed rolling update configuration
kubectl get deployment frontend -o yaml | grep -A 5 "strategy"
```

---

## Additional Practice Commands

### Deployment Management

```sh
# Create a deployment
kubectl create deployment webapp --image=nginx:1.20

# Scale deployment
kubectl scale deployment webapp --replicas=5

# Update deployment image
kubectl set image deployment/webapp nginx=nginx:1.21

# Check rollout history
kubectl rollout history deployment/webapp

# Rollback to previous version
kubectl rollout undo deployment/webapp

# Rollback to specific revision
kubectl rollout undo deployment/webapp --to-revision=2
```

### Monitoring and Troubleshooting

```sh
# Watch deployment progress
kubectl get deployments -w

# Check pod status during rollout
kubectl get pods -l app=webapp -w

# View deployment events
kubectl describe deployment webapp

# Check ReplicaSet status
kubectl get rs -l app=webapp
```

---

## Rolling Update Strategy Parameters

| Parameter | Default Value | Description |
|-----------|---------------|-------------|
| `maxUnavailable` | 25% | Maximum number of pods that can be unavailable during update |
| `maxSurge` | 25% | Maximum number of pods that can be created above desired replica count |
| `strategy.type` | RollingUpdate | Deployment strategy type |

### Example Strategy Configuration

```yaml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
```

---

## Key Takeaways

1. **Multiple Update Methods**: Use `kubectl edit`, `kubectl set image`, or `kubectl patch`
2. **Rolling Updates**: Default strategy ensures zero-downtime deployments
3. **Unavailability Calculation**: Based on `maxUnavailable` setting and replica count
4. **Always Verify**: Check rollout status and pod health after updates
5. **History Tracking**: Kubernetes maintains rollout history for easy rollbacks