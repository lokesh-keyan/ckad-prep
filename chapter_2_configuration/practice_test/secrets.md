# Kubernetes Secrets Management

This document provides a step-by-step guide on managing Kubernetes secrets, including viewing, creating, and configuring pods to use these secrets.

---

## 1. View existing Secrets on the system

To view all the secrets currently available in the Kubernetes cluster, use the following command:

```sh
kubectl get secrets
```

---

## 2. Inspect the `dashboard-token` secret

To view the details of the `dashboard-token` secret, including the data keys it contains, run:

```sh
kubectl get secret dashboard-token -o yaml
```

In the output, look for the `data:` section. It typically contains keys like `ca.crt`, `namespace`, and `token`, each followed by a base64-encoded string.

---

## 3. Create a Secret named `db-secret` with the following data

| Key           | Value         |
|---------------|---------------|
| DB_Host       | sql01         |
| DB_User       | root          |
| DB_Password   | password123   |

**Imperative command:**
```sh
kubectl create secret generic db-secret \
  --from-literal=DB_Host=sql01 \
  --from-literal=DB_User=root \
  --from-literal=DB_Password=password123
```

**Declarative YAML:**
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  DB_Host: c3FsMDE=              # sql01
  DB_User: cm9vdA==              # root
  DB_Password: cGFzc3dvcmQxMjM=  # password123
```

**Apply the YAML:**
```sh
kubectl apply -f db-secret.yaml
```

**Base64 encoding reference:**
```sh
echo -n 'sql01' | base64          # c3FsMDE=
echo -n 'root' | base64           # cm9vdA==
echo -n 'password123' | base64    # cGFzc3dvcmQxMjM=
```

---

## 4. Configure `webapp-pod` to load environment variables from the newly created secret

> **Note:** If the pod already exists, you must delete and recreate it. Updating a pod's environment variables (like `envFrom`) is not allowed on a running pod.

- **Pod name:** `webapp-pod`
- **Image name:** `kodekloud/simple-webapp-mysql`
- **Env From:** Secret=`db-secret`

**Pod manifest:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: webapp-pod
spec:
  containers:
  - name: webapp
    image: kodekloud/simple-webapp-mysql
    envFrom:
    - secretRef:
        name: db-secret
```

