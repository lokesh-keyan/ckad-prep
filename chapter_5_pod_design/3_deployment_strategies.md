# Deployment Strategies

## Canary Promotion Flow

Here's the complete canary promotion workflow:

### Initial State

You start with two deployments:

- **`web-primary`** - The original stable version
- **`web-canary`** - A small percentage of traffic, testing new version

### Promotion Process

Once you're confident the canary version is good, you promote it:

#### Step 1: Create New Primary Deployment

1. Create a new deployment based on `web-canary`, name it `web-primary`
2. Use label `version=stable` (or whatever your stable label is)

```sh
kubectl apply -f primary.yaml
```

This spins up new pods under this new deployment.

#### Step 2: Monitor Dual Deployment State

At this point, both `web-primary` and `web-canary` are running:

- Both have the same app label (`app=web`)
- Your Service might be routing to both unless you're controlling it via selectors

#### Step 3: Clean Up Canary

Once you're satisfied the `web-primary` pods are up and running, clean up the old canary deployment:

```sh
kubectl delete deployment web-canary
```

This removes the old canary deployment completely.

---

## Complete Deployment Strategy Types

### 1. Rolling Update (Default)

**Characteristics:**
- Gradual replacement of old pods with new ones
- Zero downtime during updates
- Default Kubernetes strategy

**Configuration:**
```yaml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 25%
      maxSurge: 25%
```

**Use Cases:**
- Standard application updates
- Low-risk deployments
- Continuous delivery pipelines

---

### 2. Recreate Strategy

**Characteristics:**
- Terminates all old pods before creating new ones
- Causes downtime during deployment
- Faster deployment process

**Configuration:**
```yaml
spec:
  strategy:
    type: Recreate
```

**Use Cases:**
- Applications that can't run multiple versions simultaneously
- Database schema changes
- Maintenance windows

---

### 3. Blue-Green Deployment

**Characteristics:**
- Two identical environments (Blue = current, Green = new)
- Instant traffic switching
- Easy rollback capability

**Implementation Steps:**
1. Deploy new version (Green) alongside current (Blue)
2. Test Green environment thoroughly
3. Switch traffic from Blue to Green
4. Keep Blue as rollback option

**Example Commands:**
```sh
# Deploy green version
kubectl apply -f green-deployment.yaml

# Switch service to green
kubectl patch service myapp -p '{"spec":{"selector":{"version":"green"}}}'

# Remove blue deployment (after validation)
kubectl delete deployment myapp-blue
```

---

### 4. Canary Deployment

**Characteristics:**
- Gradual traffic shift to new version
- Risk mitigation through controlled exposure
- Monitoring-based rollout decisions

**Implementation Approaches:**

#### Method 1: Replica-based Traffic Splitting
```sh
# Scale canary to 10% of traffic
kubectl scale deployment myapp-canary --replicas=1
kubectl scale deployment myapp-primary --replicas=9

# Gradually increase canary traffic
kubectl scale deployment myapp-canary --replicas=3
kubectl scale deployment myapp-primary --replicas=7
```

#### Method 2: Ingress-based Traffic Splitting
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myapp-canary
  annotations:
    nginx.ingress.kubernetes.io/canary: "true"
    nginx.ingress.kubernetes.io/canary-weight: "10"
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: myapp-canary
            port:
              number: 80
```

---

## Best Practices

### Monitoring and Validation

1. **Health Checks**: Ensure proper readiness and liveness probes
2. **Metrics Monitoring**: Watch key performance indicators during deployment
3. **Error Rate Monitoring**: Track error rates and response times
4. **Rollback Preparation**: Have rollback procedures ready and tested

### Strategy Selection Guidelines

| Strategy | Downtime | Rollback Speed | Resource Usage | Complexity |
|----------|----------|----------------|----------------|------------|
| Rolling Update | None | Medium | Low | Low |
| Recreate | Yes | Fast | Low | Low |
| Blue-Green | None | Instant | High | Medium |
| Canary | None | Variable | Medium | High |

### Common Commands

```sh
# Check deployment status
kubectl rollout status deployment/myapp

# View deployment history
kubectl rollout history deployment/myapp

# Rollback to previous version
kubectl rollout undo deployment/myapp

# Rollback to specific revision
kubectl rollout undo deployment/myapp --to-revision=2

# Pause/Resume rollout
kubectl rollout pause deployment/myapp
kubectl rollout resume deployment/myapp
```

---

## Key Takeaways

1. **Choose the Right Strategy**: Select based on your application requirements and risk tolerance
2. **Test Thoroughly**: Always test deployment strategies in staging environments
3. **Monitor Actively**: Watch metrics and logs during deployment processes
4. **Plan for Rollback**: Have quick rollback procedures for all strategies
5. **Automate When Possible**: Use CI/CD pipelines for consistent deployments
