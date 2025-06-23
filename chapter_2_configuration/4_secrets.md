# Kubernetes Secrets

Kubernetes Secrets are used to store sensitive information, such as:
- Passwords
- Tokens
- SSH keys
- TLS certificates

> **Note:** Secrets are base64-encoded by default (not encrypted), but are treated specially by Kubernetes for security.

---

## Key Features of Secrets
- Can be mounted as files or exposed as environment variables
- Avoided in logs and command output
- Stored separately from normal configuration

---

## Secrets vs ConfigMaps

| Feature                | ConfigMap | Secret |
|------------------------|:---------:|:------:|
| For sensitive data     |     ❌     |   ✅   |
| Stored in etcd         | Plaintext | Base64 (can be encrypted at rest) |
| Auto-masked in CLI/logs|     ❌     |   ✅   |
| Mounted into pods      |     ✅     |   ✅   |
| Requires base64        |     ❌     |   ✅   |

> While you can technically store a secret in a ConfigMap, it is **not secure or recommended**.

---

## Sample Secret (YAML)

Here’s a secret storing a username and password:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  username: YWRtaW4=      # 'admin' in base64
  password: cGFzc3dvcmQ=  # 'password' in base64
```

Create it with:
```sh
kubectl apply -f my-secret.yaml
```

Or via CLI:
```sh
kubectl create secret generic my-secret \
  --from-literal=username=admin \
  --from-literal=password=password
```

---

## Accessing a Secret in a Pod

As an environment variable:
```yaml
env:
  - name: DB_USER
    valueFrom:
      secretKeyRef:
        name: my-secret
        key: username
```
Or mount it as a file inside the container.

---

## Best Practices
- Use Secrets for anything sensitive
- Consider enabling encryption at rest for etcd
- Use RBAC to control who can access Secrets

---

## Why Base64 Encode?
Kubernetes Secret objects use the `data:` field to store base64-encoded values. This is not encryption—just encoding—and Kubernetes expects the values to already be encoded when you create the secret using a YAML file.

**Example:**
```yaml
data:
  username: YWRtaW4=
  password: cGFzc3dvcmQ=
```
You are expected to have manually encoded:
- `'admin'` → `YWRtaW4=`
- `'password'` → `cGFzc3dvcmQ=`

You can do it in the terminal using:

```sh
echo -n 'admin' | base64
echo -n 'password' | base64
```

