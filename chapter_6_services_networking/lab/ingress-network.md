k get pods -n ingress-nginx

k get deployment -n ingress-nginx 

To see ingress resource

k get ingress -A

You need to mention the namespace for an Ingress resource when using k describe because Ingress resources are namespace-scoped, meaning their names must be unique within a given namespace. In contrast, pods can also be namespace-scoped, but they are so fundamental that kubectl often defaults to the current namespace to make common commands quicker to type.

k describe ingress ingress-wear-watch -n app-space


k delete ingress -n critical-space critical-ingress


kubectl create ingress ingress-pay -n critical-space --rule="/pay=pay-service:8282"

