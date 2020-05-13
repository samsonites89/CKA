We have a working kubernetes cluster with a set of applications running. 
Let us first explore the setup.

How many deployments exist in the cluster?

```shell script
kubectl get deploymemts.apps
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
blue   3/3     3            3           48s
red    2/2     2            2           48s
```

What is the version of ETCD running on the cluster?


```shell script
master $ kubectl describe -n kube-system po etcd-master
Name:                 etcd-master
Namespace:            kube-system
Priority:             2000000000
Priority Class Name:  system-cluster-critical
Node:                 master/172.17.0.106
Start Time:           Tue, 12 May 2020 13:55:29 +0000
Labels:               component=etcd
                      tier=control-plane
Annotations:          kubernetes.io/config.hash: 6e4ca0cff369c52a268b8a930c66870f
                      kubernetes.io/config.mirror: 6e4ca0cff369c52a268b8a930c66870f
                      kubernetes.io/config.seen: 2020-05-12T13:55:25.733173916Z
                      kubernetes.io/config.source: file
Status:               Running
IP:                   172.17.0.106
IPs:
  IP:  172.17.0.106
Containers:
  etcd:
    Container ID:  docker://b69795b01140a8a6ef06f16bf8afff338e478994b4aa21690554649d8bf82518
    Image:         k8s.gcr.io/etcd:3.3.15-0 ###

```


Take a snapshot of the ETCD database using the built-in snapshot functionality.

Store the backup file at location /tmp/snapshot-pre-boot.db
```shell script

ETCDCTL_API=3 etcdctl --endpoints=https://[127.0.0.1]:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt \
     --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key \
     snapshot save /tmp/snapshot-pre-boot.db

```

restore

```shell script
ETCDCTL_API=3 etcdctl --endpoints=https://[127.0.0.1]:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt \
     --name=master \
     --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key \
     --data-dir /var/lib/etcd-from-backup \
     --initial-cluster=master=https://127.0.0.1:2380 \
     --initial-cluster-token etcd-cluster-1 \
     --initial-advertise-peer-urls=https://127.0.0.1:2380 \
     snapshot restore /tmp/snapshot-pre-boot.db

```
