# Security Context Practice Test

## 1. Find the user used to run the pod

```sh
kubectl exec -it ubuntu-sleeper -- bash
```

**Command breakdown:**
| Part              | Meaning                                                      |
|-------------------|--------------------------------------------------------------|
| `kubectl`         | Kubernetes CLI tool                                          |
| `exec`            | Execute a command inside a container                         |
| `-it`             | Interactive + allocate a TTY (like `docker exec -it`)       |
| `ubuntu-sleeper`  | Name of the pod you want to exec into                       |
| `--`              | Separates the kubectl args from the command you want to run inside the pod |
| `bash`            | The command to run inside the container (starts a bash shell) |

---

## 2. Run the pod using user 1010

Use `whoami` to get the current user inside the pod.

```sh
kubectl get pod ubuntu-sleeper -o yaml > ubuntu-sleeper.yaml
```

Add the following to the container specification:

```yaml
securityContext:
  runAsUser: 1010
```

---

## 3. Multi-container pod with different users

- **Sidecar** container runs with user `1001`
- **Web** container runs with user `1002`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-pod
spec:
  securityContext:
    runAsUser: 1001
  containers:
  - image: ubuntu
    name: web
    command: ["sleep", "5000"]
    securityContext:
      runAsUser: 1002
  - image: ubuntu
    name: sidecar
    command: ["sleep", "5000"]
```

---

## 4. Add `SYS_TIME` capability

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2023-07-26T22:12:07Z"
  name: ubuntu-sleeper
  namespace: default
  resourceVersion: "945"
  uid: 34c800d-d278-498b-b6be-f9e41086be9f
spec:
  containers:
  - command:
    - sleep
    - "4800"
    image: ubuntu
    imagePullPolicy: Always
    name: ubuntu
    securityContext:
      capabilities:
        add: ['SYS_TIME']
```

### 🔍 Explanation
- **`securityContext`**: Defines security-related settings for a pod or container (like user ID, capabilities, etc.)
- **`capabilities.add`**: Adds Linux kernel capabilities that are not included by default in the container's sandbox
- **`SYS_TIME`**: A specific Linux capability that allows a process to change the system clock (e.g., using `date -s`, `clock_settime`, etc.)

---

## 5. Update the pod to use the `NET_ADMIN` capability

```yaml
securityContext:
  capabilities:
    add: ['SYS_TIME', 'NET_ADMIN']
```