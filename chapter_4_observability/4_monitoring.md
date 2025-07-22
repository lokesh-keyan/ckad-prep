Why Monitor Kubernetes?
Monitoring in Kubernetes is essential for maintaining your cluster’s health and performance. Key metrics to watch include:

Node-Level Metrics: Total nodes, their health status, and overall performance.
Performance Metrics: CPU, memory, network, and disk usage.
Pod-Level Metrics: Number of pods and individual pod performance (e.g., CPU and memory consumption).

Deploying the Metrics Server
For Minikube Users
If you’re running a local cluster with Minikube, you can enable the Metrics Server with this simple command:

minikube addons enable metrics-server
For Other Environments
To deploy the Metrics Server in other environments, first clone the repository, then apply the deployment files as shown below:

git clone https://github.com/kubernetes-incubator/metrics-server.git
kubectl create -f deploy/1.8+/
After running these commands, you should see output similar to the following:

clusterrolebinding "metrics-server:system:auth-delegator" created
rolebinding "metrics-server-auth-reader" created
apiservice "v1beta1.metrics.k8s.io" created
serviceaccount "metrics-server" created
deployment "metrics-server" created
service "metrics-server" created
clusterrole "system:metrics-server" created
clusterrolebinding "system:metrics-server" created
These steps deploy the necessary pods, services, and roles for the Metrics Server to gather real-time performance metrics from your cluster nodes. Allow a few moments for the Metrics Server to start collecting data.

Node Metrics
To see CPU and memory usage for each node, run:

kubectl top node
Example output:

NAME          CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
kubemaster    166          8%     1337Mi          70%
kubnode1      36           1%     1046Mi          55%
kubnode2      39           1%     1048Mi          55%

Pod Metrics
To display pod-level metrics, execute:

kubectl top pod
Example output:

NAME    CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
nginx   166          8%     1337Mi          70%
redis   36           1%     1046Mi          55%