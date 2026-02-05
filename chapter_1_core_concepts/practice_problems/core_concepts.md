### Create a namespace called 'mynamespace' and a pod with image nginx called nginx on this namespace

<details><summary>show</summary>
<p>

```bash
kubectl create namespace mynamespace
kubectl run nginx --image=nginx --restart=Never -n mynamespace
```

</p>
</details>

### Create the pod that was just described using YAML

<details><summary>show</summary>
<p>

Easily generate YAML with:

```bash
kubectl run nginx --image=nginx --restart=Never --dry-run=client -n mynamespace -o yaml > pod.yaml
```

```bash
cat pod.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
  namespace: mynamespace
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

```bash
kubectl create -f pod.yaml
```

Alternatively, you can run in one line

```bash
kubectl run nginx --image=nginx --restart=Never --dry-run=client -o yaml | kubectl create -n mynamespace -f -
```

</p>
</details>

### Create a busybox pod (using kubectl command) that runs the command "env". Run it and see the output

<details><summary>show</summary>
<p>

```bash
kubectl run busybox --image=busybox --command --restart=Never -it --rm -- env # -it will help in seeing the output, --rm will immediately delete the pod after it exits
# or, just run it without -it
kubectl run busybox --image=busybox --command --restart=Never -- env
# and then, check its logs
kubectl logs busybox

kubectl run busybox --image=busybox
# this by default runs sh and exists and it creates deployment you get CrashLoopBackOff not equal to bug
# “Your container finished too fast for something that is supposed to stay running.”
# we add --restart=Never to complete it or add -- sleep 3600 to keep it alive
# Important details:

# busybox is a very small Linux image

# It contains basic Unix commands (sh, ls, env, etc.)

# Perfect for debugging
```
</p>
</details>

### Create the YAML for a new ResourceQuota called 'myrq' with hard limits of 1 CPU, 1G memory and 2 pods without creating it

<details><summary>show</summary>
<p>

```bash
kubectl create quota myrq --hard=cpu=1,memory=1G,pods=2 --dry-run=client -o yaml
# Resource	Limit	Meaning
# cpu=1	1 CPU core total	All pods combined can request max 1 CPU
# memory=1G	1 gigabyte RAM	All pods combined can request max 1GB memory
# pods=2	2 pods max	Namespace can have only 2 pods total
# This is applied to default namesapce and applied only to namespaces. You have to explicitly mention the  namesapce if you want it applied to your namesapce
# Only hard keyword is used, no other keywords, but used can be seen as status from k8s itself, you do not mention this
```
</p>
</details>