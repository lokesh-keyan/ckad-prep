1. The application stores logs at location /log/app.log. View the logs.

You can exec in to the container and open the file:

kubectl exec webapp -- cat /log/app.log

## 2. Configure a volume to store these logs at /var/log/webapp on the host.

Use the spec provided below.

Name: webapp
Image Name: kodekloud/event-simulator
Volume HostPath: /var/log/webapp
Volume Mount: /log

```bash
k edit pod webapp
k replace --force -f /tmp/kubectl-edit-3967743311.yaml
```

## 3. Create PV

## Persistent Volume

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-log
spec:
  capacity:
    storage: 100Mi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /pv/log
```

## 4. Create a PVC

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: claim-log-1
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 50Mi
```

# Above PV and PVC has accessModes mismatch, update it

## 5. Update the webapp pod to use the persistent volume claim as its storage. Replace hostPath configured earlier with the newly created PersistentVolumeClaim.

Name: webapp
Image Name: kodekloud/event-simulator
Volume: PersistentVolumeClaim=claim-log-1
Volume Mount: /log

```yaml
volumeMounts:
- mountPath: /log
    name: log-volume
  volumes:
  - persistentVolumeClaim:
      claimName: claim-log-1
```

## 6. What happens when you delete the pvc when its being used 
Stuck in terminating status


## 7. Since the PV reclaim policy was Retain, it will be in the Released status but not deleted. The PVC and pod will be deleted when you delete the pod
