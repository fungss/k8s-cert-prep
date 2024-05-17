# Security
## Authentication, Authorization, and Admission Control
- Static Password File & Token File
Restart kube-apiservice with the text file storing password and token, not recommended in prod

To authenticate,
```
curl -v -k https://master-node-ip:6443/api/v1/pods -u "user1: password123"
```

- Certificates
CKA topic. not for CKAD

- Identity Services
???

## KubeConfig
```
k get pods --kubeconfig <path_to_config>
```

example KubeConfig File
1. Clusters
2. Users
3. Contexts
```
apiVersion: v1
kind: Config

current-context: dev-admin@dev-cluster # default user

clusters:
- name: dev-cluster
  cluster:
    certificate-authority: <path_to_ca.crt>
    server: https://k8s.example.org/k8s/clusters/c-xxyyzz

users:
- name: dev-admin
  user:
    client-certificate: <path_to_admin.crt>
    client-key: <path_to_admin.key>

contexts:
- name: dev-admin@dev-cluster
  context:
    cluster: dev-cluster
    user: dev-admin
    namespace: dev-app-space # default namespace
```

To inspect config
```
k config view
```

To change user
```
kubectl config use-context prod-user@production
```

## API Groups

## Role Based Access Controls
Allow authorizing users by role, as opposed to ABAC which one would need to create policy definition for each users.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get", "list", "create", "update", "delete"]
  resourceNames: ["blue", "orange"]
- apiGroups: [""]
  resources: ["ConfigMap"]
  verbs: ["create"]
```

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: devuser-developer-binding
subjects:
- kind: User
  name: dev-user # user object from kubeconfig
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```

To check access as admin
```
k auth can-i <verb> <object> --as <user_object> --namespace <namespace_name>
```

## Cluster Roles
Roles to control cluster-wise resources, as opposed to resources by namespace (RBAC).
- nodes
- pv
- clusterroles
- clusterrolebindings
- namespaces

To list resources by namespace/cluster
```
kubectl api-resources --namesapced=true/false
```

### Roles
- Cluster Admin
- Storage Admin

To create cluster role
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-administrator
rules:
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["list", "get", "create", "delete"]
```

then bind with user
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admin-role-binding
subjects:
- kind: User
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-administrator
  apiGroup: rbac.authorization.k8s.io
```

## Admission Controllers
Security measure to enforce what is required/not allowed for users to perform, especially how a cluster is used.

### examples
- AlwaysPullImages
- DefaultStorageClass
- EventRateLimit
- NamespaceExists -> required namespace to exist
- ...

### types
- validating admission controllers
- mutating admission controllers

## API Verisons
## API Deprecations
## Custom Resource Definition
## Custom Controllers