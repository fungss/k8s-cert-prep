# State Persistence

## Volumes in k8s
pod data persists on the host(node)
```yaml
...
kind: Pod
metadata:
    name: <pod_name>
spec:
    containers:
    - image: ...
      name: ...
      volumeMounts:
      - mountPath: /opt
        name: data-volume

    volumes:
    - name: data-volume
      hostPath:
        path: /data
        type: Directory
      # aweElasticBlockStore: # external replicated cluster storage
      #   volumeID: <volume-id>
      #   fsType: ext4
...
```

## Persistence Volumes
cluster-wide pool of storage volumes
```yaml
...
kind: PersistentVolume
metadata:
    name: pv-vol1
spec:
    accessModes:
        - ReadWriteOnce # ReadWriteMany | ReadOnlyMany
    capacity:
        storage: 1Gi
    hostPath:
        path: /tmp/data # not recommended in prod
    # aweElasticBlockStore: # external replicated cluster storage
      #   volumeID: <volume-id>
      #   fsType: ext4
    persistentVolumeReclaimPolicy: Retain # Delete | ...
```

## Persistence Volume Claims
- To claim the created pv
- 1:1 relationship
- accessModes should match with pv
```yaml
...
kind: PersistentVolumeClaim
metadata:
    name: pv-vol1-claim
spec:
    accessModes:
        - ReadWriteOnce # ReadWriteMany | ReadOnlyMany
    resources:
        requests:
            storage: 500Mi
```

```yaml
...
kind: Pod
metadata:
    name: <pod_name>
spec:
    containers:
    - image: ...
      name: ...
      volumeMounts:
      - mountPath: /opt
        name: data-volume

    volumes:
    - name: data-volume
      persistentVolumeClaim:
       claimName: <claim_name>
...
```

## Storage Classes
Provision storage on demand
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
    name: <storage_class_name>
provisioner: kubernetes.io/gce-pd
```

```yaml
...
kind: PersistentVolumeClaim
metadata:
    name: pv-vol1-claim
spec:
    accessModes:
        - ReadWriteOnce # ReadWriteMany | ReadOnlyMany
    storageClassName: <storage_class_name>
    resources:
        requests:
            storage: 500Mi
```

## Stateful Sets
## Headless Services
## Storage in StatefulSets