# Role-Based Access Control (RBAC)

## Creating a Role

To start, you need to define a Role object. Create a YAML file (e.g., `developer-role.yaml`) and set the API version to `rbac.authorization.k8s.io/v1` and the kind to `Role`. In this example, we define a role named "developer" with permissions that allow developers to manage pods and create configmaps. Each permission rule contains three key sections: API groups, resources, and verbs. For resources in the core API group, leave the API group field blank.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["list", "get", "create", "update", "delete"]
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["create"]
```

Create the role using the following command:

```bash
kubectl create -f developer-role.yaml
```

---

## Binding a User to the Role

Once the role is defined, you need to link a user to this role by creating a RoleBinding. A RoleBinding associates a user with the specified role within a given namespace. In this example, we'll create a role binding named `devuser-developer-binding` that assigns the "developer" role to the user `dev-user` in the default namespace.

Below is the YAML definition for the role binding (e.g., `devuser-developer-binding.yaml`):

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: devuser-developer-binding
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```

Create the role binding with the command:

```bash
kubectl create -f devuser-developer-binding.yaml
```

---

## Testing Access Permissions

You can test whether a user has access to specific Kubernetes resources using the `kubectl auth can-i` command:

```bash
kubectl auth can-i create deployments
```

Expected output:

```
yes
```

And if you test deleting nodes:

```bash
kubectl auth can-i delete nodes
```

Expected output:

```
no
```

If you need to simulate actions as another user, use the `--as` flag. Even though the developer role does not have permission to create deployments, it can create pods:

```bash
kubectl auth can-i create deployments --as dev-user
```

Expected output:

```
no
```

```bash
kubectl auth can-i create pods --as dev-user
```

Expected output:

```
yes
```

You can also specify a particular namespace using the `--namespace` flag if the permissions are scoped accordingly.

---

## Granting Access to Specific Resources

In some cases, you might want to restrict a user's permissions to specific resources. For instance, if you need to allow a user to manage only two pods named "blue" and "orange" within a namespace, refine the role rule by including the `resourceNames` field:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "create", "update"]
  resourceNames: ["blue", "orange"]
```

