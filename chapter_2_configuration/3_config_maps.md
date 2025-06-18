# ConfigMaps in Kubernetes

ConfigMaps provide a way to inject configuration data into Pods. There are two approaches to create ConfigMaps: **imperative** (command line) and **declarative** (YAML file).

---

## Imperative Approach

The imperative method allows you to create a ConfigMap directly from the command line.

### Creating ConfigMap from literals

To create a ConfigMap named `app-config` with key-value pairs for `APP_COLOR` and `APP_MODE`:

```sh
kubectl create configmap app-config --from-literal=APP_COLOR=blue --from-literal=APP_MODE=prod
```

### Creating ConfigMap from file

If you have configuration data stored in a file (e.g., `app_config.properties`), you can create a ConfigMap from that file:

```sh
kubectl create configmap app-config --from-file=app_config.properties
```

---

## Declarative Approach

For the declarative method, define a ConfigMap in a YAML file and apply it using `kubectl create -f`.

### Example YAML file (`config-map.yaml`):

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_COLOR: blue
  APP_MODE: prod
```

### Deploy the ConfigMap:

```sh
kubectl create -f config-map.yaml
```

---

## Injecting a ConfigMap into a Pod

After creating a ConfigMap, you can inject the configuration data into a Pod. A common method is using the `envFrom` property within the Pod's container specification:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: simple-webapp-color
  labels:
    name: simple-webapp-color
spec:
  containers:
    - name: simple-webapp-color
      image: simple-webapp-color
      ports:
        - containerPort: 8080
      envFrom:
        - configMapRef:
            name: app-config
```

In this configuration, all key-value pairs from the `app-config` ConfigMap are injected as environment variables into the container.

---

## Summary

- **Imperative**: Quick creation using `kubectl create configmap` command
- **Declarative**: YAML-based approach for version control and reproducibility
- **Injection**: Use `envFrom` to inject all ConfigMap data as environment variables into containers