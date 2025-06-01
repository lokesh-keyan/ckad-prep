
# Kubernetes Nodes, Clusters, and Master Components

## Nodes
A node is a machine, physical or virtual, and that is where containers will be launched by Kubernetes. It was also known as minions in the past. What if the node fails? Our app goes down. So we need more nodes.

## Clusters
A cluster is a group of nodes. This way, if one node fails, we have your app still accessible from the other nodes. Moreover, having multiple nodes helps in sharing load as well. Now we have a cluster. But who is responsible for managing it and monitoring the nodes? That's when a master comes in; it watches over the other nodes and orchestrates the containers.

## Master Components
When you install Kubernetes, you get all this:
- **API Server**: Acts as the front end for Kubernetes. The users, management devices, command line interfaces all talk to the API server to interact with the Kubernetes cluster.
- **ETCD**: A distributed reliable key-value store used by Kubernetes to store all data to manage the cluster. It stores all the information of master, worker nodes in your cluster, responsible for implementing locks within the cluster to ensure that there are no conflicts between the masters.