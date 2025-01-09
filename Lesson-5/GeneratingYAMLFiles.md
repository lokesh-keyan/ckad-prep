# Generating YAML Files

- Do not write yaml files, just generate them
- kubectl run mybusybox --image=busybox -- sleep 3600 --dry-run=client -o yaml
  - after we -- it means we are telling the container commands, so if we use --dry-run=client , it is not a sheel command
  - instead we do "kubectl run mybusybox --image=busybox --dry-run=client -o yaml -- sleep 3600 > mybusy.yaml"

- Goto kubernetes.io/docs
  - search for pod
  - open the first link
  - copy the yaml file if you want