# Service Accounts in Kubernetes

## Account Types in Kubernetes

There are two primary account types in Kubernetes:

- **User Accounts**: For human users such as administrators and developers
- **Service Accounts**: For machine users, such as monitoring tools (e.g., Prometheus) or CI/CD systems like Jenkins

---

## Use Case Example

Consider a simple dashboard application, "my Kubernetes dashboard," built in Python. When deployed, it:
1. Queries the Kubernetes API to retrieve the list of Pods
2. Displays them on a web page
3. Uses a service account to authenticate securely with the Kubernetes API

---

## Creating a Service Account

```sh
kubectl create serviceaccount dashboard-sa
```

**View service accounts:**
```sh
kubectl get serviceaccount
```

**View the token:**
The token is stored in a Secret object. To view the token, describe the corresponding secret:
```sh
kubectl describe secret dashboard-sa-token-kbbdm
```

---

## Automatic Token Mounting

When a third-party application is deployed on a Kubernetes cluster, the service account token is **automatically mounted as a volume** inside the Pod. This allows your application to read the token from a well-known location rather than providing it manually.

### Default Service Account

By default, each namespace in Kubernetes includes a service account named `default`. When a Pod is created **without specifying a service account**, Kubernetes mounts the default service account's token into the Pod.

**Example Pod definition:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-kubernetes-dashboard
spec:
  containers:
    - name: my-kubernetes-dashboard
      image: my-kubernetes-dashboard
```

---

## Token Mount Location

A typical pod description shows that a secret (e.g., `default-token-j4hkv`) is mounted at `/var/run/secrets/kubernetes.io/serviceaccount`:

```yaml
Name:         my-kubernetes-dashboard
Namespace:    default
Status:       Running
IP:           10.244.0.15
Containers:
  my-kubernetes-dashboard:
    Image:      my-kubernetes-dashboard
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-j4hkv (ro)
Volumes:
  default-token-j4hkv:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-j4hkv
    Optional:    false
```

**Verify token mount:**
```sh
kubectl exec -it my-kubernetes-dashboard -- ls /var/run/secrets/kubernetes.io/serviceaccount
```

**Typical contents:**
- `ca.crt` - Certificate Authority certificate
- `namespace` - Current namespace name
- `token` - JWT token for authentication

