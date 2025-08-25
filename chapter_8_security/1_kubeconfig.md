For example, assume your cluster is named "my kube playground." You can send a curl request to the Kubernetes API server with the client certificate, key, and CA certificate:

curl https://my-kube-playground:6443/api/v1/pods \
  --key admin.key \
  --cert admin.crt \
  --cacert ca.crt
The API server validates the certificate and responds with output similar to:

{
  "kind": "PodList",
  "apiVersion": "v1",
  "metadata": {
    "selfLink": "/api/v1/pods"
  },
  "items": []
}
When using kubectl, you normally pass the same connection information with the corresponding options:

kubectl get pods \
  --server my-kube-playground:6443 \
  --client-key admin.key \
  --client-certificate admin.crt \
  --certificate-authority ca.crt
This command might return:

No resources found.
Typing these options every time can become tedious. To simplify your workflow, you can move the connection details into a configuration file known as a kubeconfig file. By default, kubectl looks for a file named config under the .kube directory in your home directory. If the kubeconfig file is in its default location, you don’t have to specify connection options for each command:

kubectl get pods

Kubeconfig Structure
The kubeconfig file is organized into three primary sections:

Clusters: Define the various Kubernetes clusters you need access to. You might have separate clusters for development, testing, production, or different cloud providers.
Users: Define the user accounts holding credentials (such as client certificates and keys) needed to access these clusters.
Contexts: Link clusters and users together. A context specifies which user credentials should be used to access a particular cluster. For example, you could have a context called “admin@production,” which uses the admin user’s credentials for the production cluster.
These components work together to streamline connectivity and authentication in your Kubernetes environment.

In our example, the server address and CA certificate information belong in the clusters section, while the admin user’s keys and certificates go in the users section. A context then binds these settings together. Below is a sample kubeconfig file in YAML format:

apiVersion: v1
kind: Config
clusters:
- name: my-kube-playground  # values hidden for brevity
- name: development
- name: production
- name: google
contexts:
- name: my-kube-admin@my-kube-playground
- name: dev-user@google
- name: prod-user@production
users:
- name: my-kube-admin
- name: admin
- name: dev-user
- name: prod-user
Note that you do not create Kubernetes objects for these configurations. Instead, kubectl reads this file to obtain the necessary connection details.

kubectl selects a context from the kubeconfig based on the current-context field. For example, if you set:

current-context: my-kube-admin@my-kube-playground
kubectl will default to that context. Alternatively, you can specify a different kubeconfig file from the command line using the --kubeconfig flag:

kubectl config view --kubeconfig=my-custom-config
Default Kubeconfig Example
An example output of the default kubeconfig file might be:

apiVersion: v1
kind: Config
current-context: kubernetes-admin@kubernetes
clusters:
- cluster:
    certificate-authority-data: REDACTED
    server: https://172.17.0.5:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
users:
- name: kubernetes-admin
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
And when using a custom config file:

kubectl config view --kubeconfig=my-custom-config
apiVersion: v1
kind: Config
current-context: my-kube-admin@my-kube-playground
clusters:
- name: my-kube-playground
- name: development
- name: production
contexts:
- name: my-kube-admin@my-kube-playground
- name: prod-user@production
users:
- name: my-kube-admin
- name: prod-user



The kubeconfig file typically references certificate file paths. For clarity and robustness, it is best practice to use the full path to each certificate. Alternatively, you can embed the certificate data directly into the file by base64-encoding the certificate. For instance, instead of defining:

apiVersion: v1
kind: Config
clusters:
- name: production
  cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
you can embed the certificate data:

apiVersion: v1
kind: Config
clusters:
- name: production
  cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJU...

    