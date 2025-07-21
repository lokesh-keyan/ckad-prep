# Probes Lab Exercise

## Initial State Check

First, let's check the current state of our pods and services:

```sh
kubectl get pod
```
```
NAME              READY   STATUS    RESTARTS   AGE
simple-webapp-1   1/1     Running   0          32s
```

```sh
kubectl get service
```
```
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
kubernetes       ClusterIP   172.20.0.1      <none>        443/TCP          3m21s
webapp-service   NodePort    172.20.94.211   <none>        8080:30080/TCP   39s
```

---

## Task: Set Liveness Probe and Readiness Probe

### Step 1: Clean up existing pods

```sh
kubectl delete pods --all
```

---

## Pod Configuration Examples

### Example 1: Pod with Liveness Probe Only

This pod configuration includes only a liveness probe:

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2022-08-03T15:10:30Z"
  labels:
    name: simple-webapp
  name: simple-webapp-1
  namespace: default
  resourceVersion: "825"
  uid: 694b5225-1ad4-422c-b514-4f139c84ecf2
spec:
  containers:
  - image: kodekloud/webapp-delayed-start
    imagePullPolicy: Always
    name: simple-webapp
    ports:
    - containerPort: 8080
      protocol: TCP
    livenessProbe:
      httpGet:
        path: /live
        port: 8080
      periodSeconds: 1
      initialDelaySeconds: 80
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
```

**Key Configuration:**
- **Liveness Probe**: HTTP GET request to `/live` endpoint
- **Initial Delay**: 80 seconds before first probe
- **Period**: Check every 1 second

---

### Example 2: Pod with Both Liveness and Readiness Probes

This pod configuration includes both liveness and readiness probes:

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2022-08-03T15:17:03Z"
  labels:
    name: simple-webapp
  name: simple-webapp-2
  namespace: default
  resourceVersion: "1858"
  uid: 82ec848b-b098-45f0-9975-513ef5d21b1d
spec:
  containers:
  - env:
    - name: APP_START_DELAY
      value: "30"
    image: kodekloud/webapp-delayed-start
    imagePullPolicy: Always
    name: simple-webapp
    ports:
    - containerPort: 8080
      protocol: TCP
    readinessProbe:
      failureThreshold: 3
      httpGet:
        path: /ready
        port: 8080
        scheme: HTTP
      periodSeconds: 10
      successThreshold: 1
      timeoutSeconds: 1
    livenessProbe:
      httpGet:
        path: /live
        port: 8080
      periodSeconds: 1
      initialDelaySeconds: 80
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
```

**Key Configuration:**

#### Readiness Probe:
- **Endpoint**: `/ready` path
- **Failure Threshold**: 3 consecutive failures
- **Period**: Check every 10 seconds
- **Timeout**: 1 second per check
- **Success Threshold**: 1 successful check to mark as ready

#### Liveness Probe:
- **Endpoint**: `/live` path
- **Initial Delay**: 80 seconds
- **Period**: Check every 1 second

#### Environment Variables:
- **APP_START_DELAY**: Set to 30 seconds to simulate startup time

---

## Probe Comparison

| Probe Type | Purpose | Failure Action | Example Endpoint |
|------------|---------|----------------|------------------|
| **Readiness** | Determines if pod is ready to receive traffic | Removes pod from service endpoints | `/ready` |
| **Liveness** | Determines if container is running properly | Restarts the container | `/live` |

---

## Best Practices

1. **Use both probes**: Combine readiness and liveness probes for comprehensive health monitoring
2. **Set appropriate delays**: Account for application startup time with `initialDelaySeconds`
3. **Configure realistic timeouts**: Ensure probes don't fail due to temporary slowness
4. **Monitor probe failures**: Track probe failures to identify application issues
5. **Use meaningful endpoints**: Create dedicated health check endpoints that reflect true application state