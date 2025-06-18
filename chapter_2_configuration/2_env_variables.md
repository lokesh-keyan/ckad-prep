# Environment Variables in Kubernetes

Environment variables are essential for configuring applications running in Kubernetes Pods. You can set them directly in the Pod specification or reference them from ConfigMaps and Secrets.

---

## Direct Environment Variable Assignment

When creating a Pod, you can directly assign environment variables using the `env` property in the container specification. The `env` property is an array where each variable is defined with a `name` and `value`.

### Docker vs Kubernetes Comparison

**Docker command:**
```sh
docker run -e APP_COLOR=pink simple-webapp-color
```

**Kubernetes Pod manifest:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: simple-webapp-color
spec:
  containers:
  - name: simple-webapp-color
    image: simple-webapp-color
    ports:
    - containerPort: 8080
    env:
    - name: APP_COLOR
      value: pink
```

---

## Using ConfigMaps for Environment Variables

Instead of hardcoding values, you can reference ConfigMaps to make your configuration more flexible and reusable.

### Direct Value (Not Recommended)
```yaml
env:
- name: APP_COLOR
  value: pink
```

### ConfigMap Reference (Recommended)
```yaml
env:
- name: APP_COLOR
  valueFrom:
    configMapKeyRef:
      name: my-config
      key: app_color
```

In this configuration, Kubernetes retrieves the value for `APP_COLOR` from a ConfigMap named `my-config` instead of hardcoding it in the manifest.

---

## Benefits of Using ConfigMaps

- **Separation of Concerns**: Configuration is separate from application code
- **Reusability**: Same ConfigMap can be used across multiple Pods
- **Environment-specific**: Different ConfigMaps for dev, staging, and production
- **Dynamic Updates**: Configuration changes without rebuilding container images