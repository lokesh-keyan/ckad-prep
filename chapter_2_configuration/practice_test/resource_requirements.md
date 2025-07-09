# Resource Requirements Practice Test

1. Check cpu limit

kubectl describe pod <podname>

2. Why a pod is failing

Back-off restarting failed container mem-stress in pod elephant_default(4976a74c-c5c1-41e1-8bb9-a26cc7e8581e)

3. Increase the memory

kubectl get pod elephant -o yaml > elephant.yaml