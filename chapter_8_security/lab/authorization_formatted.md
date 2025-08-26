# Authorization Lab

## 1. Inspect the Environment and Authorization Modes

Inspect the environment and identify the authorization modes configured on the cluster.

Check the kube-apiserver settings:

```bash
k get pods -n kube-system -l component=kube-apiserver
```

```bash
k describe pod kube-apiserver-controlplane -n kube-system
```

Look for:

```
...
Containers:
  kube-apiserver:
    Command:
      kube-apiserver
      --advertise-address=10.0.0.1
      --allow-privileged=true
      --authorization-mode=Node,RBAC 👈
      --client-ca-file=/etc/kubernetes/pki/ca.crt
      ...
```

---

## 2. Check Roles in Default Namespace

How many roles exist in the default namespace?

```bash
k get roles -n default
```

```
No resources found in default namespace.
```

---

## 3. List Roles in All Namespaces

Roles in all namespaces:

```bash
k get roles --all-namespaces
```

```
NAMESPACE     NAME                                             CREATED AT
blue          developer                                        2025-08-26T15:12:45Z
kube-public   kubeadm:bootstrap-signer-clusterinfo             2025-08-26T15:06:51Z
kube-public   system:controller:bootstrap-signer               2025-08-26T15:06:50Z
kube-system   extension-apiserver-authentication-reader        2025-08-26T15:06:50Z
kube-system   kube-proxy                                       2025-08-26T15:06:52Z
kube-system   kubeadm:kubelet-config                           2025-08-26T15:06:51Z
kube-system   kubeadm:nodes-kubeadm-config                     2025-08-26T15:06:51Z
kube-system   system::leader-locking-kube-controller-manager   2025-08-26T15:06:50Z
kube-system   system::leader-locking-kube-scheduler            2025-08-26T15:06:50Z
kube-system   system:controller:bootstrap-signer               2025-08-26T15:06:50Z
kube-system   system:controller:cloud-provider                 2025-08-26T15:06:50Z
kube-system   system:controller:token-cleaner                  2025-08-26T15:06:50Z
```

---

## 4. Examine kube-proxy Role Resources

What are the resources the kube-proxy role in the kube-system namespace is given access to?

```bash
k describe role kube-proxy -n kube-system
```

```
Name:         kube-proxy
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources   Non-Resource URLs  Resource Names  Verbs
  ---------   -----------------  --------------  -----
  configmaps  []                 [kube-proxy]    [get]
```

---

## 5. Check kube-proxy Role Assignment

Which account is the kube-proxy role assigned to?

```bash
k get rolebindings -n kube-system
```

```bash
k describe rolebindings kube-proxy -n kube-system
```

```
Name:         kube-proxy
Labels:       <none>
Annotations:  <none>
Role:
  Kind:  Role
  Name:  kube-proxy
Subjects:
  Kind   Name                                             Namespace
  ----   ----                                             ---------
  Group  system:bootstrappers:kubeadm:default-node-token  
```

---

## 6. Test User Permissions

```bash
k auth can-i list pods --namespace=default --as dev-user
```

```
no
```

---

## 7. Create Role and RoleBinding for dev-user

Create the necessary roles and role bindings required for the dev-user to create, list and delete pods in the default namespace.

**Use the given spec:**
- **Role**: developer
- **Role Resources**: pods
- **Role Actions**: list, create, delete
- **RoleBinding**: dev-user-binding
- **RoleBinding**: Bound to dev-user

### Create the Role

First, create a Role named `developer` that specifies the permissions. This role will grant the verbs `create`, `list`, and `delete` on the `pods` resource.

**YAML Manifest (`developer-role.yaml`):**

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
  namespace: default
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["create", "list", "delete"]
```

### Create the RoleBinding

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: dev-user-binding
  namespace: default
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```

---

## 8. Add Deployment Permissions

Add a new rule in the existing role developer to grant the dev-user permissions to create deployments in the blue namespace.

Remember to add api group "apps".

```bash
kubectl edit role developer -n blue
```

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
  namespace: blue
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["create", "list", "delete"]
- apiGroups: ["apps"]
  resources: ["deployments"]
  verbs: ["create"]
```
