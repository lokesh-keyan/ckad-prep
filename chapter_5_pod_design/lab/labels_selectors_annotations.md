# Labels, Selectors, and Annotations - Lab Exercise

## Exercise Questions and Solutions

### 1. How many pods have the label `env=dev`?

Count pods with the `env=dev` label:

```sh
kubectl get pods --selector env=dev --no-headers | wc -l
```

**Alternative command:**
```sh
kubectl get pods -l env=dev --no-headers | wc -l
```

---

### 2. How many objects are in the prod environment including PODs, ReplicaSets and any other objects?

Count all objects in the production environment:

```sh
kubectl get all --selector env=prod --no-headers | wc -l
```

**Alternative approach (to see the objects):**
```sh
kubectl get all --selector env=prod
```

This will display all objects (pods, services, deployments, replicasets, etc.) that have the `env=prod` label.

---

### 3. Identify the POD which is part of the prod environment, the finance BU and of frontend tier

Find pods matching multiple label criteria:

```sh
kubectl get pod --selector env=prod,bu=finance,tier=frontend
```

**Explanation:**
- `env=prod` - Production environment
- `bu=finance` - Finance business unit
- `tier=frontend` - Frontend tier

This uses comma-separated selectors which act as an AND operation.

---

## Additional Practice Commands

### Basic Label Selection

```sh
# Get all pods with a specific label
kubectl get pods -l app=nginx

# Get all pods with label key regardless of value
kubectl get pods -l app

# Get all pods without a specific label
kubectl get pods -l '!app'
```

### Advanced Label Selection

```sh
# Select pods with multiple labels (AND operation)
kubectl get pods -l env=prod,tier=frontend

# Select pods using set-based selectors
kubectl get pods -l 'env in (prod,staging)'

# Select pods NOT matching a label value
kubectl get pods -l 'env!=dev'

# Select pods with version not equal to beta
kubectl get pods -l 'version!=beta'
```

### Useful Counting and Formatting

```sh
# Count objects with specific labels
kubectl get pods -l env=prod --no-headers | wc -l

# Show only names
kubectl get pods -l env=prod -o name

# Show with custom columns
kubectl get pods -l env=prod -o custom-columns=NAME:.metadata.name,STATUS:.status.phase
```

---

## Tips for Lab Success

1. **Use shorthand**: `-l` is shorthand for `--selector`
2. **Count efficiently**: Use `--no-headers | wc -l` to count objects
3. **Multiple conditions**: Comma-separated selectors create AND conditions
4. **View details**: Remove `--no-headers | wc -l` to see actual objects
5. **Test selectors**: Always verify your selectors return expected results

