## CKAD

**TODO**
- [x] 0. Basics
- [x] 1. Configurations
- [x] 2. Multi Container Pods
- [x] 3. Observability
- [x] 4. POD Design
- [x] 5. Services & Networking
- [x] 6. State Persistence 1 - except Stateful Sets
- [x] 7. Security 2 *
- [x] 8. Helm Fundamentals

## Miscellaneous
```yaml
command: ["/bin/sh", "-c"]
args:
- |-
  dflfsmlksfdl;k
```

Lightning Lab 1
Q1 - pv, pvc, storageClass
Q2 - networkpolicy -> service and nodePort
Q3 - ns, pod, ms
Q4 - deploy, rollout undo
Q5 - deploy, resources

Lightning Lab 2
Q1 - livenessProbe, readinessProbe
Q2 - cronJob
Q3 - secretVolume, pod, nodeSelector
Q4 - Ingress
Q5 - linux command, k logs -c

expose deployment to create service

Simulator
Q3 - for busybox, 'sh', '-c'
Q10 - tmp pod, command, log, service by dns, expose svc
Q11 - podman, docker
Q12 - pv, pvc, deploy
Q13 - sc, pvc, log
Q14 - volumes, secret
Q15 - cm --from-file, mutli-line strings in yaml 
Q16 - sidecar container, logs -c, deploy
Q17 - initContainers, command: ['sh', '-c', 'echo "check this out!" > /tmp/web-content/index.html']
Q18 - svc, selector
Q19 - expose as pod's port to node level
Q20 - networkpolicy
Q21 - resources
Q22 - labels and annotations

grep -i
-o jsonpath="{}"
k run tmp --restart=Never --rm -i --image= -- curl ...