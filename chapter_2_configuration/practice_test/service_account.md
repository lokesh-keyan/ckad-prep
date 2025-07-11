# Service Account Practice Test

## 1. How many service accounts exist?

```sh
kubectl get serviceaccount
```

**Expected output:**
```
NAME      SECRETS   AGE
default   0         9m46s
dev       0         26s
```

---

## 2. Check the Secret Token for the Default Service Account

```sh
kubectl describe serviceaccount default
```

---

## 3. Inspect the Dashboard Deployment

```sh
kubectl get deployments
kubectl describe deployment web-dashboard
```

---

## 4. Determine the Service Account Used by the Dashboard Application

The logs and error messages confirm that the default service account is being used. To verify which service account is mounted on the dashboard pod:

```sh
kubectl get pod
kubectl describe pod <pod-name>
```

Within the pod description, locate the **Service Account** section, which should indicate it is set to "default." Additionally, note that the credentials are mounted from:

```
/var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-swjvh (ro)
```

This directory is where the dashboard pod accesses its service account tokens and related secrets.

---

## 5. Create a New Service Account for the Dashboard

Since the default service account has limited permissions, create a new service account (named `dashboard-sa`) with enhanced rights.

### Create the New Service Account

```sh
kubectl create serviceaccount dashboard-sa
```

**Confirmation message:**
```
serviceaccount/dashboard-sa created
```

### RBAC Configuration

Inside `var/rbac` directory, create the following RBAC files:

**RoleBinding (`role-binding.yaml`):**
```yaml
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: ServiceAccount
  name: dashboard-sa # Name is case sensitive
  namespace: default
roleRef:
  kind: Role # this must be Role or ClusterRole
  name: pod-reader # this must match the name of the Role or ClusterRole you wish to bind to
  apiGroup: rbac.authorization.k8s.io
```

**Role (`role.yaml`):**
```yaml
---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups:
  - ''
  resources:
  - pods
  verbs:
  - get
  - watch
  - list
```

---

## 6. Update the Deployment to Use the New Service Account

To eliminate the need for manual token retrieval, update the dashboard deployment so it automatically uses `dashboard-sa`.

### Export the Current Deployment Configuration

```sh
kubectl get deployment web-dashboard -o yaml > dashboard.yaml
```

### Update the Deployment YAML

Open `dashboard.yaml` in your preferred editor and locate the pod specification within the `spec` section. Add the `serviceAccountName` field with the value `"dashboard-sa"` under the pod spec.

**Updated deployment configuration:**
```yaml
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      name: web-dashboard
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        name: web-dashboard
    spec:
      serviceAccountName: dashboard-sa
      containers:
      - env:
        - name: PYTHONUNBUFFERED
          value: "1"
        image: gcr.io/kodekloud/customimage/my-kubernetes-dashboard
        imagePullPolicy: Always
        name: web-dashboard
        ports:
        - containerPort: 8080
          protocol: TCP
        resources: {}
```

### Apply the Updated Configuration

```sh
kubectl apply -f dashboard.yaml
```

> **Note:** You might see a warning about a missing annotation due to changes in resource management history; this warning is harmless as the configuration will be patched automatically.

### Verify the Deployment

Check the deployment status:

```sh
kubectl get deployment
```