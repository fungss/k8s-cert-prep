## Configruation

### Commands and Arguments
-----
In a Dockerfile, 
- ```CMD ["executable", "param1"]``` is the default command the container will execute once it is up (replaced)
- ```ENTRYPOINT ["executable"]``` will be referred when an argument is passed to the container (appended)
Combining both (both ENTRYPOINT and CMD will be chained),
```yaml
...
ENTRYPOINT ["sleep"]
CMD ["5"]
```
To modified default executable,
```
docker run --entrypoint <another_executable> <container_name> <args>
```
In a pod file,
```yaml
# overwrite CMD in Dockerfile
...
kind: Pod
...
spec:
    containers:
        - name: ...
          image: ...
          args: ["10"]
...
```
```yaml
# overwrite ENTRYPOINT in Dockerfile
...
kind: Pod
...
spec:
    containers:
        - name: ...
          image: ...
          command: ["another_executable"]
          args: ["10"]
...
```

### ConfigMap
-----
K8s object that stores commonly used environment variables, for configurations, to be shared between services.

```yaml
# Template
apiVersion: v1
kind: ConfigMap
metadata:
    name: test-config
data:
    APP_COLOR: blue
    APP_MODE: prod
    <env_name>: <env_value>
    ...
```
Usage
```yaml
# w/o ConfigMap
...
kind: Pod
...
spec:
    containers:
    - name: ...
      image: ...
      ...
      env:
      - name: <env_name>
        value: <env_value>
    ...
...
``` 
```yaml
# w/ ConfigMap
...
kind: Pod
...
spec:
    containers:
    - name: ...
      image: ...
      ...
      envFrom:
      - configMapRef:
            name: <configmap_name>
    ...
...
``` 
```yaml
# for particular env
...
kind: Pod
...
spec:
    containers:
    - name: ...
      image: ...
      ...
      env:
      - name: <env_name>
        valueFrom:
            configMapKeyRef:
                name: <configmap_name>
                key: <env_name>
    ...
...
``` 

### Secret
-----
Similar to ConfigMap but for sensitive information. Encoding is required.

To encode / decode,
```
echo -n 'password_value' | base64 # --decode
```

```yaml
# Template
apiVersion: v1
kind: Secret
metadata:
    name: <secret_name>
data:
    DB_Host: bXlzcWw=
    DB_User: cm9vdA==
    <secret_key>: <encoded_secret_value>
    ...
```

Usage
```yaml
# w/ Secret
...
spec:
    containers:
    - name: ...
      image: ...
      ...
      envFrom:
      - secretRef:
            name: <secret_key>
    ...
...
``` 
```yaml
# for particular secret
...
kind: Pod
...
spec:
    containers:
    - name: ...
      image: ...
      ...
      env:
      - name: <secret_key>
        valueFrom:
            secretKeyRef:
                name: <secret_name>
                key: <env_name>
    ...
...
``` 
```yaml
# mount secret as volumes in pod
...
kind: Pod
...
spec:
    containers:
    - name: ...
      image: ...
      ...
    volumes:
    - name: test-secret-volume
      secret:
        secretName: <secret_name>
    ...
...
```

### Encrypting Secret Data at Rest !!!!!!TODO!!!!!!
-----
Skipped.

### Security Contexts
Privilge and Access Control on pod/container level.
-----
```yaml
...
kind: Pod
...
spec:
    securityContext:
      runAsUser: 1010
    containers:
    ...
...
```

### Resource Requirements
-----


### ServiceAccount
-----
- Token is created to be used by external applications and stored in a secret object
- since 1.22, time bound token is supported and mounted (automounting is replaced by TokenRequestAPI)
- since 1.24, removal of auto-generated secret-based service account tokens -> manually create token is required

```yaml
...
kind: Pod
...
spec:
    containers:
    - name: ...
      image: ...
      volumeMounts:
      - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
        name: <token_name>
        readOnly: true
      ...
    serviceAccountName: <svc_ac_name>
    automountServiceAccountToken: false # optional
    volumes:
    - name: <token_name>
     projected:
        defaultMode: 420
        sources:
        - serviceAccountToken:
            expirationSeconds: 3607
            path: token
...
```

For non-expiring token,
```yaml
...
kind: Secret
type: kubernetes.io/service-account-token
metadata:
  name: <secret_name>
  annotations:
    kubernetes.io/service-account.name: <svc_ac_name>

```

### Taints, Tolerations and Node Affinity
### Taints and Tolerations
-----
Taints
- tags on nodes to accept certain pods
- taint-effect determines the follow-up action to the pod if it doesn't tolerate the given taint </br>
NoSchedule = pod will not be scheduled </br>
PreferNoSchedule = try to avoid placing the pod to the node but not gurantee </br>
NoExecute = not schedule current pod + evict existing pods if not tolerate </br>

Usage
```
kubectl taint node <node_name> <taint_key>=<taint_value>:<taint-effect>
```
or
```
kubectl edit node <node_name>
```

Tolerations

tags on pods to be scheduled on certain nodes

Usage
```yaml
...
kind: Pod
...
spec:
    containers:
    - name: ...
      image: ...
      ...
    tolerations:
    - key: <taint_key>
      operator: "Equal" # Exists
      value: <taint_value>
      effect: "NoSchedule" # or PreferNoSchedule or NoExecute
    ...
...
```

### NodeSelector
-----
To put pods in specific nodes, perhaps according to resource requirements, with a matching label

Usage

To lable nodes,
```
kubectl label node <node_name> <label_key>=<label_value>
```

To assign pod to labelled nodes,
```yaml
...
kind: Pod
...
spec:
    containers:
    - name: ...
      image: ...
      ...
    nodeSelector:
      size: Large # node label required
    ...
...
```

### Node Affinity
-----
Similar to NodeSelector but is designed to handle more complex relations (Or, Not)
- requiredDuringScheduling -> do not place the pod if affinity not matched
- preferredDuringScheduling -> try place the pod even though affinity not matched
- IgnoredDuringExecution -> change in node affinity would not affect existing pods

```yaml
...
kind: Pod
...
spec:
    containers:
    - name: ...
      image: ...
      ...
    affinity:
      nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
          nodeSelectorTerms:
          - matchExpressions:
            - key: size
              operator: In # NotIn, Exists <- simply checks label existence
              values: # omitted if Exists is used
              - Large
              - Medium
    ...
...
```