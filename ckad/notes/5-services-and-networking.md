# Service & Networking
Service - enable communication between components (group of pods) within and outside the application

## Services and Cluster IP
1. NodePort - expose pod process to node level
```yaml
...
kind: Service
...
spec:
    type: NodePort # LoadBalaner / ClusterIP
    ports:
        - targetPort: 80 # port of pod
          port: 80 # to be referenced by other svc in the same cluster
          nodePort: 30008 # from 30000 - 32767
    selector:
        <pod_label_name>: <pod_lable_value>
...
```

2. ClusterIP - enable communication of processes between nodes and within the same cluster
```yaml
...
kind: Service
metadata:
    name: back-end # e.g.
...
spec:
    type: ClusterIP
    ports:
        - targetPort: 80 # port of pod
          port: 80 # to be referenced by other svc in the same cluster
    selector:
        <pod_label_name>: <pod_lable_value>
        type: back-end
...
```

3. LoadBalancer
???

## Network Policies
Define ingress/egress rules for specific service. Supported by Kube-router, Calio, Romana, Weave-net, etc.

Ingress - incoming traffic
Egress - out going traffic

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
    name: db-policy
spec:
    podSelector:
        matchLabels:
            role: db
    policyTypes:
    - Ingress
    ingress:
    - from:
      - podSelector:
          matchLabels:
            name: api-pod
      ports:
      - protocol: TCP
        port: 3306
...
```

## Ingress Networking
Built-in layer 7 load balancer that supports SSL and routes external traffic to pod groups (services). Proxy server, like Nginx, would be required if Ingress not used.

### Ingress Controller
GCP HTTP(S) Load Balancer, Nginx, Contour, HAPROXY, Istio

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: nginx-ingress-controller
spec:
    replicas: 1
    selector:
        matchLabels:
            name: nginx-ingress
    template:
        metadata:
            labels:
                name: nginx-ingress
        spec:
            containers:
                - name: nginx-ingress-controller
                  image: quay.io/kubernetes-ingress-controller/nginx-ingress-controller:0.21.0
            args:
                - /nginx-ingress-controller
                - --configmap=$(POD_NAMESPACE)/nginx-configuration
            env:
                - name: POD_NAME
                  valueFrom:
                    fieldRef:
                      fieldPath: metadata.name
                - name: POD_NAMESPACE
                  valueFrom:
                    fieldRef:
                      fieldPath: metadata.namespace

    ports:
        - name: http
          containerPort: 80
        - name: https
          containerPort: 443
```

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
    name: nginx-ingress-configuration
data:
    ...
```

```yaml
apiVersion: v1
kind: Service
metadata:
    name: nginx-ingress
spec:
    type: NodePort
    ports:
    - port: 80
      targetPort: 80
      protocol: TCP
      name: http
    - port: 443
      targetPort: 443
      protocol: TCP
      name: https
    selector:
        name: nginx-ingress
```

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
    name: nginx-ingress-serviceaccount
...
```

### Ingress Resources
set of rules and configs apply to ingress controller

1. different hosts
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
    name: ingress-wear
spec:
    rules:
    - host: wear.my-online-store.com
      http:
        paths:
          backend: # define where the traffic will be routed to
            serviceName: wear-service
            servicePort: 80
    - host: watch.my-online-store.com
      http:
        paths:
          backend:
            serviceName: watch-service
            servicePort: 80

```

2. same host but diff paths
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
    name: ingress-wear
spec:
    rules:
    - http:
        paths:
        - path: /wear
          backend: # define where the traffic will be routed to
            service:
                name: wear-service
                port: 
                    number: 80
        - path: /watch
          backend:
            service:
                name: watch-service
                port: 
                    number: 80
```