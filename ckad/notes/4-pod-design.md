# POD Design

## Labels, Selectors, & Annotations
labels and selectors are used to separate objects into groups. annotations are used for additional information.

### Syntax
```yaml
...
kind: Pod
metadata:
    name: <pod_name>
    labels:
        <label_name>: <label_value>
        ...
...
```
```
kubectl get pods --selector <lable_name>=<label_value>
```

```yaml
...
kind: ReplicaSet
...
spec:
    ...
    selector:
        matchLabels:
            <pod_label_name>: <pod_label_value>
    template: # template for pods
        metadata:
            labels:
                <pod_label_name>: <pod_label_value>
                ...
        spec:
            containers:
            - name: ...
              image: ...
...
```

```yaml
...
kind: ReplicaSet
metadata:
    name: ...
    ...
    annotations:
        buildversion: 1.34
...
```

## Rolling Updates & Rollbacks
Deployment -> rollout -> revision for rollback

### Syntax
To deploy changes
```
kubectl edit deployment <deployment_name> --record
```
or just apply deployment.yaml

To check rollout status and history:
```
kubectl rollout status deployment <deployment_name>
```
```
kubectl rollout history deployment <deployment_name> # --revision=1 for status of specific version
```

To rollback:
```
kubectl rollout undo deployment <deployment_name> --to-revision=1
```

## Deployment Strategy
- Recreate Deployment </br>
Take down all at once and bring up the new version

- Rolling Update (Default) </br>
Take down old version and bring up new version 1 by 1

- Blue-Green Deployment </br>
parallel run with old version initially as green and new version as blue
after testing and verification -> switch the colors and route all traffic to the green group. Be implemented using labels and selectors (change tags in the svc yaml and let it point to the newer replicasets)

- Canary Deployment </br>
route small percentage of traffic to a canary deployment with new version and run test, then upgrade the rest if things go well. delete the canary deployment afterwards. Control the number of pods in the canary deployment. Have limited control over split of traffic -> Istio better.

## Jobs and Cron Jobs in k8s
```yaml
apiVersion: batch/v1
kind: Job
...
spec:
    completions: 3 # target runs
    parallelism: 3 # to spawn pods to execute parallelly
    template:
        spec:
            containers:
            - name: ...
            ...
            
            restartPolicy: Always # if process is ephemeral then it will fall in a restart loop. Never / OnFailure alternatively
...
```

```yaml
apiVersion: batch/v1beta1
kind: CronJob
...
spec:
    schedule: "*/1 * * * *" # min hr day_of_mth mth day_of_wk
    jobTemplate:
        spec:
            completions: 3 # target runs
            parallelism: 3 # to spawn pods to execute parallelly
            template:
                spec:
                    containers:
                        - name: ...
                        ...
                        
                    restartPolicy: Never
...
```
