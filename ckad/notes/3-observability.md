# Observability

## POD lifecycle
- Pending </br>
scheduler determining where to schedule the pod
- ContainerCreating </br>
pulling image and starting container
- Running </br>
...

## POD Conditions
list of true/false
- PodScheduled
- Initialized
- ContainersReady # containers inside the pod
- Ready # pod-wise

## Readiness Probes
To prevent service disruption due to under-initialized pods.

### Syntax
```yaml
...
kind: Pod
...
spec:
    containers:
    - name: <container_1>
      image: ...
      ...
      readinessProbe: # user-defined definition of a pod's readliness
        httpGet:  # or tcpSocket or exec with command
            path: /api/ready
            port: 8080
        initialDelaySeconds: 10 # seconds before the first readinessProbe
        periodSeconds: 5
        failureThreshold: 8 # default is 3 
...
```

## Liveness Probes
To collect zombie containers which the inner application is not properly functioning, especially when the application is freezed but not failed.

### Syntax
```yaml
...
kind: Pod
...
spec:
    containers:
    - name: <container_1>
      image: ...
      ...
      livenessProbe: # user-defined definition of a pod's healthiness
        httpGet:  # or tcpSocket or exec with command
            path: /api/healthy
            port: 8080
        initialDelaySeconds: 10 # seconds before the first livenessProbe
        periodSeconds: 5
        failureThreshold: 8 # default is 3 
...
```

## Container Logging
To print logs of a pod in the terminal

### Syntax
```
kubectl logs -f <pod_name> <container_name_if_any>
```

## Monitor and Debug Applications
### Common metrics
- node: number of nodes in cluster, health of the nodes, CPU, memory, network, and disk utilization
- pod: number of pods, CPU, memory, etc

--> Prometheus, Elastic stack, Datadog (covered in CKA)

### Metrics Server (CKAD related)
in-memory monitoring solution

### Activate metrics-server
```
minikube addons enable metrics-server
```
or
```
git clone https://github.com/kubernetes-incubator/metrics-server.git
kubectl create -f <metric_server_dir>
```

### Syntax
```
kubectl top node/pod
```