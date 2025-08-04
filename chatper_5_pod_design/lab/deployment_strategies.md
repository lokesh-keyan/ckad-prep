# Deployment Strategies Lab Exercise

## Lab Questions and Solutions

### 1. Find what deployment strategy is being used

**Task:** Identify the deployment strategy for the frontend deployment.

**Solution:**

```sh
kubectl describe deployment frontend
```

**What to look for in the output:**
- **Strategy Type**: Look for `StrategyType: RollingUpdate` or `StrategyType: Recreate`
- **Rolling Update Configuration**: Check `maxUnavailable` and `maxSurge` values

**Example output section:**
```
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
```

---

### 2. Configure Blue-Green Deployment for Traffic Splitting

**Scenario:** A new deployment called `frontend-v2` has been created in the default namespace using the image `kodekloud/webapp-color:v2`. This deployment will be used to test a newer version of the same app.

**Requirements:**
- Configure the deployment so that the service called `frontend-service` routes less than 20% of traffic to the new deployment
- Do not increase the replicas of the frontend deployment

**Solution:**

```sh
kubectl scale deployment --replicas=1 frontend-v2
```

**Explanation:**
- If `frontend` deployment has 5 replicas and `frontend-v2` has 1 replica
- Total pods: 6 (5 + 1)
- Traffic to v2: 1/6 = ~16.7% (less than 20% ✓)

**Verification:**

```sh
# Check current replicas
kubectl get deployments

# Verify service endpoints
kubectl get endpoints frontend-service

# Check pod distribution
kubectl get pods -l app=frontend -o wide
```

**Traffic Distribution Calculation:**
```
V1 pods: 5
V2 pods: 1
Total: 6
V2 traffic percentage: (1/6) × 100 = 16.7%
```

---

### 3. Complete Blue-Green Deployment Cutover

**Task:** Scale down the v1 version of the apps to 0 replicas and scale up the new (v2) version to 5 replicas.

**Solution:**

#### Step 1: Scale down the original frontend deployment

```sh
kubectl scale deployment --replicas=0 frontend
```

**Expected output:**
```
deployment.apps/frontend scaled
```

#### Step 2: Scale up the frontend-v2 deployment to handle all traffic

```sh
kubectl scale deployment --replicas=5 frontend-v2
```

**Expected output:**
```
deployment.apps/frontend-v2 scaled
```

**Verification:**

```sh
# Verify scaling completed
kubectl get deployments

# Check all pods are running
kubectl get pods -l app=frontend

# Verify service endpoints point to v2 pods only
kubectl get endpoints frontend-service
```

---

## Deployment Strategy Types

### Rolling Update (Default)
- **Gradual replacement** of old pods with new ones
- **Zero downtime** during updates
- **Configurable** with `maxUnavailable` and `maxSurge`

### Recreate
- **Terminate all** old pods before creating new ones
- **Downtime** during the update process
- **Faster** but less resilient

### Blue-Green (Manual)
- **Two identical environments** (Blue = current, Green = new)
- **Instant cutover** by switching traffic
- **Easy rollback** but requires double resources

### Canary (Manual)
- **Gradual traffic shift** to new version
- **Risk mitigation** through controlled exposure
- **Monitoring-based** rollout decisions

---

## Additional Commands for Deployment Strategies

### Monitoring Deployment Progress

```sh
# Watch deployment rollout
kubectl rollout status deployment/frontend

# Monitor pods during scaling
kubectl get pods -w

# Check deployment history
kubectl rollout history deployment/frontend
```

### Traffic Management

```sh
# Check service endpoints distribution
kubectl describe service frontend-service

# View service selector
kubectl get service frontend-service -o yaml

# List all pods with labels
kubectl get pods --show-labels
```

### Rollback Operations

```sh
# Rollback to previous version
kubectl rollout undo deployment/frontend-v2

# Rollback to specific revision
kubectl rollout undo deployment/frontend-v2 --to-revision=1

# Check rollback status
kubectl rollout status deployment/frontend-v2
```

---

## Best Practices for Deployment Strategies

1. **Test in Staging**: Always test deployment strategies in non-production environments
2. **Monitor Metrics**: Watch application metrics during traffic shifts
3. **Gradual Rollouts**: Use canary deployments for high-risk changes
4. **Quick Rollback**: Have rollback procedures ready and tested
5. **Resource Planning**: Consider resource requirements for blue-green deployments
6. **Health Checks**: Ensure proper readiness and liveness probes are configured

---

## Key Takeaways

- **Strategy Identification**: Use `kubectl describe` to check deployment strategy
- **Traffic Splitting**: Control traffic distribution through replica scaling
- **Blue-Green Cutover**: Complete environment switch through scaling operations
- **Verification**: Always verify changes with multiple kubectl commands
- **Monitoring**: Watch deployment progress and pod health during strategy execution