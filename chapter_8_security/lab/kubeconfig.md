1. Switching Context to Use the Dev User
To access "test-cluster-1" using the "research" context (which utilizes the dev user), run the following command. Be sure to specify the kubeconfig file containing the desired configuration:

root@controlplane ~ ⟶ kubectl config use-context research --kubeconfig /root/my-kube-config
Switched to context "research".