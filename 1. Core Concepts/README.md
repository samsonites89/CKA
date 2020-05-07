## Kubernetes Core Concepts

![core concept arch](../images/kubernetes-constructs-concepts-architecture.jpg)

### ETCD
### kubectl
### kubelet
### kube-apiserver
### scheduler
### kube-proxy
### container runtime

#### definition yaml template
```yaml
apiVersion: v1 #apiVersion 명시
kind: Pod #리소스 타입: Pod, ReplicaSet, Deployment, etc.
metadata: # name, label등 리소스에 대한 meta값
    ...
spec: #리소스에 대한 specification 정의. 어떤 kind인지에 따라 해당 내용이 많이 바뀐다.
    ...
```
