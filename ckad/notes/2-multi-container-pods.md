# Multi-Container Pods

### Use Case
As a sub-service work alongside the main service of the same pod.

## Design Patterns
- Ambassador </br>
ambassador container serves as a proxy between other pods of the same host to external services

- Adapter </br>
adapter container process the log before forwarding it to a centralised server

- Sidecar </br>
logging agent along side a web server that collects and forwards log info to a centralised log server

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

    - name: <contaienr_2>
      image: ...
...
```

## init Containers
containers that only run when pod is first created. process in the initContainer must run to a completion before the real container hosting the application starts.

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
    initContainers:
    - name: <contaienr_2>
      image: ...
...
```