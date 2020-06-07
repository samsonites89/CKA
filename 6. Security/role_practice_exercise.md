- Inspect the environment and identify the authorization modes configured on the cluster.
Check the kube-api server settings

```yaml
master $ kubectl -n kube-system describe po kube-apiserver-master
Name:                 kube-apiserver-master
Namespace:            kube-system
Priority:             2000000000
Priority Class Name:  system-cluster-critical
Node:                 master/172.17.0.74
Start Time:           Thu, 14 May 2020 09:59:57 +0000
Labels:               component=kube-apiserver
                      tier=control-plane
Annotations:          kubernetes.io/config.hash: 8b9d4957868a6c771d4b71353ddc6e63
                      kubernetes.io/config.mirror: 8b9d4957868a6c771d4b71353ddc6e63
                      kubernetes.io/config.seen: 2020-05-14T09:59:54.487120764Z
                      kubernetes.io/config.source: file
Status:               Running
IP:                   172.17.0.74
IPs:
  IP:  172.17.0.74
Containers:
  kube-apiserver:
    Container ID:  docker://e1fb115dc6d52e46df79831a9fcbae06d504f61de925095942984a0e06684104    Image:         k8s.gcr.io/kube-apiserver:v1.16.0
    Image ID:      docker-pullable://k8s.gcr.io/kube-apiserver@sha256:f4168527c91289da2708f62ae729fdde5fb484167dd05ffbb7ab666f60de96cd
    Port:          <none>
    Host Port:     <none>
    Command:
      kube-apiserver
      --advertise-address=172.17.0.74
      --allow-privileged=true
      --authorization-mode=Node,RBAC #####
      --client-ca-file=/etc/kubernetes/pki/ca.crt
      --enable-admission-plugins=NodeRestriction
      --enable-bootstrap-token-auth=true
      --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt
      --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
      --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key
      --etcd-servers=https://127.0.0.1:2379
      --insecure-port=0
      --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt
      --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key
      --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
      --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt
      --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key
      --requestheader-allowed-names=front-proxy-client
      --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt
      --requestheader-extra-headers-prefix=X-Remote-Extra-
      --requestheader-group-headers=X-Remote-Group
      --requestheader-username-headers=X-Remote-User
      --secure-port=6443
      --service-account-key-file=/etc/kubernetes/pki/sa.pub
      --service-cluster-ip-range=10.96.0.0/12
      --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
      --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
    State:          Running
      Started:      Thu, 14 May 2020 09:59:59 +0000
    Ready:          True
    Restart Count:  0
    Requests:
      cpu:        250m
    Liveness:     http-get https://172.17.0.74:6443/healthz delay=15s timeout=15s period=10s #success=1 #failure=8
    Environment:  <none>
    Mounts:
      /etc/ca-certificates from etc-ca-certificates (ro)
      /etc/kubernetes/pki from k8s-certs (ro)
      /etc/pki from etc-pki (ro)
      /etc/ssl/certs from ca-certs (ro)
      /usr/local/share/ca-certificates from usr-local-share-ca-certificates (ro)
      /usr/share/ca-certificates from usr-share-ca-certificates (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  ca-certs:
    Type:          HostPath (bare host directory volume)
    Path:          /etc/ssl/certs
    HostPathType:  DirectoryOrCreate
  etc-ca-certificates:
    Type:          HostPath (bare host directory volume)
    Path:          /etc/ca-certificates
    HostPathType:  DirectoryOrCreate
  etc-pki:
    Type:          HostPath (bare host directory volume)
    Path:          /etc/pki
    HostPathType:  DirectoryOrCreate
  k8s-certs:
    Type:          HostPath (bare host directory volume)
    Path:          /etc/kubernetes/pki
    HostPathType:  DirectoryOrCreate
  usr-local-share-ca-certificates:
    Type:          HostPath (bare host directory volume)
    Path:          /usr/local/share/ca-certificates
    HostPathType:  DirectoryOrCreate
  usr-share-ca-certificates:
    Type:          HostPath (bare host directory volume)
    Path:          /usr/share/ca-certificates
    HostPathType:  DirectoryOrCreate
QoS Class:         Burstable
Node-Selectors:    <none>
Tolerations:       :NoExecute
Events:
  Type    Reason   Age    From             Message
  ----    ------   ----   ----             -------
  Normal  Pulled   2m20s  kubelet, master  Container image "k8s.gcr.io/kube-apiserver:v1.16.0" already present on machine
  Normal  Created  2m20s  kubelet, master  Created container kube-apiserver
  Normal  Started  2m19s  kubelet, master  Started container kube-apiserver
```

- How many roles exist in the default namespace?
```bash
kubectl get roles
```


- What are the resources the weave-net role in the kube-system namespce is given access to?

```bash
# describe to see what's up
master $ kubectl describe roles  -n kube-system weave-net
Name:         weave-net
Labels:       name=weave-net
Annotations:  cloud.weave.works/launcher-info:
                {
                  "original-request": {
                    "url": "/k8s/v1.10/net.yaml?k8s-version=v1.16.0",
                    "date": "Mon Oct 28 2019 18:38:09 GMT+0000 (UTC)"
                  },
                  "email-address": "support@weave.works"
                }
              kubectl.kubernetes.io/last-applied-configuration:
                {"apiVersion":"rbac.authorization.k8s.io/v1","kind":"Role","metadata":{"annotations":{"cloud.weave.works/launcher-info":"{\n  \"original-r...
PolicyRule:
  Resources   Non-Resource URLs  Resource Names  Verbs
  ---------   -----------------  --------------  -----
  configmaps  []                 []              [create]  ###
  configmaps  []                 [weave-net]     [get update] ###

```

- Which account is the weave-net role assigned to it?

```bash
kubectl describe rolebindings.rbac.authorization.k8s.io -n kube-system weave-net
Name:         weave-net
Labels:       name=weave-net
Annotations:  cloud.weave.works/launcher-info:
                {
                  "original-request": {
                    "url": "/k8s/v1.10/net.yaml?k8s-version=v1.16.0",
                    "date": "Mon Oct 28 2019 18:38:09 GMT+0000 (UTC)"
                  },
                  "email-address": "support@weave.works"
                }
              kubectl.kubernetes.io/last-applied-configuration:
                {"apiVersion":"rbac.authorization.k8s.io/v1","kind":"RoleBinding","metadata":{"annotations":{"cloud.weave.works/launcher-info":"{\n  \"ori...
Role:
  Kind:  Role
  Name:  weave-net
Subjects:
  Kind            Name       Namespace
  ----            ----       ---------
  ServiceAccount  weave-net  kube-system

```


- A user dev-user is created. User's details have been added to the kubeconfig file. 
Inspect the permissions granted to the user. Check if the user can list pods in the default namespace.

```bash
# following the suggestion to use --as user commmand
master $ kubectl auth can-i list pods --as dev-user
no

```

- Create the necessary roles and role bindings required for the dev-user to create, list and delete pods in the default namespace.
specs are as follows:
- Role: developer
- Role Resources: pods
- Role Actions: list
- Role Actions: create
- RoleBinding: dev-user-binding
- RoleBinding: Bound to dev-user

```yaml
# define role below
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["create", "list"]

---

apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: dev-user-binding
subjects:
- kind: User
  name: dev-user
roleRef:
  kind: Role #this must be Role or ClusterRole
  name: developer
  apiGroup: rbac.authorization.k8s.io

```


- The dev-user is trying to get details about the `dark-blue-app` pod in the `blue` namespace. 
  Investigate and fix the issue.
  
```bash
# the resource name is wrong so fix it. blue-app -> dark-blue-app
```

