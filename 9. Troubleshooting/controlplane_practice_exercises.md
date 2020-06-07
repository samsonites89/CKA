# Control Plane Trouble Shooting

The cluster is broken again. We tried deploying an application but it's not working. Troubleshoot and fix the issue.
```bash
# Checking first to see what is running
master $ kubectl get deploy
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
app    0/1     1            0           16m

# App is not running. check other resources in the cluster
kubectl get pod -A
NAMESPACE     NAME                             READY   STATUS             RESTARTS   AGE
default       app-f54ccc97b-lnw24              0/1     Pending            0          17m
kube-system   coredns-5644d7b6d9-gkvqt         1/1     Running            0          17m
kube-system   coredns-5644d7b6d9-xqvxc         1/1     Running            0          17m
kube-system   etcd-master                      1/1     Running            0          17m
kube-system   kube-apiserver-master            1/1     Running            0          17m
kube-system   kube-controller-manager-master   1/1     Running            0          17m
kube-system   kube-proxy-h7lpn                 1/1     Running            0          17m
kube-system   kube-proxy-q7z6c                 1/1     Running            0          17m
kube-system   kube-scheduler-master            0/1     CrashLoopBackOff   8          17m
kube-system   weave-net-2fnr7                  2/2     Running            0          17m
kube-system   weave-net-7nk7w                  2/2     Running            1          17m

# The kube-scheduler is in a crash loop. Check what is exactly wrong
kubectl describe -n kube-system pod kube-scheduler-master
Last State:     Terminated
  Reason:       ContainerCannotRun
  Message:      OCI runtime create failed: container_linux.go:345: starting container process caused "exec: \"kube-schedulerrrr\": executable file not found in $PATH": unknown
  Exit Code:    127
  Started:      Sun, 31 May 2020 06:31:03 +0000
  Finished:     Sun, 31 May 2020 06:31:03 +0000

# Fix the command that is being run at /etc/kubernetes/manifests
cd /etc/kubernetes/manifests
vim scheduler.yaml

spec:
  containers:
  - command:
    - kube-schedulerrrr # this needs fixing
    - --authentication-kubeconfig=/etc/kubernetes/scheduler.conf
    - --authorization-kubeconfig=/etc/kubernetes/scheduler.conf
    - --bind-address=127.0.0.1
    - --kubeconfig=/etc/kubernetes/scheduler.conf
    - --leader-elect=true

```

Scale the deployment app to 2 pods.

```bash
# increase replica count
kubectl edit deployments.apps app

spec:
   replicas : 2
   
   
# check other proecesses to see if everything is running okay
master $ kubectl get pod -A
NAMESPACE     NAME                             READY   STATUS    RESTARTS   AGE
default       app-f54ccc97b-lnw24              1/1     Running   0          23m
kube-system   coredns-5644d7b6d9-gkvqt         1/1     Running   0          24m
kube-system   coredns-5644d7b6d9-xqvxc         1/1     Running   0          24m
kube-system   etcd-master                      1/1     Running   0          23m
kube-system   kube-apiserver-master            1/1     Running   0          23m
kube-system   kube-controller-manager-master   0/1     Error     4          109s

# check the controller

master $ kubectl -n kube-system logs kube-controller-manager-master
I0531 06:43:52.936419       1 serving.go:319] Generated self-signed cert in-memory
stat /etc/kubernetes/controller-manager-XXXX.conf: no such file or directory

master $ ls /etc/kubernetes/
admin.conf  controller-manager.conf  kubelet.conf  manifests  pki  scheduler.conf

# 기존과 같이 위에 manifest 하위 kube-controller-manager.yaml  수정

```


Something is wrong with scaling again. We just tried scaling the deployment to 3 replicas. But it's not happening.

```bash
master $ kubectl get pod -A
NAMESPACE     NAME                             READY   STATUS             RESTARTS   AGE
default       app-f54ccc97b-8zlpj              1/1     Running            0          89s
default       app-f54ccc97b-lnw24              1/1     Running            0          33m
kube-system   coredns-5644d7b6d9-gkvqt         1/1     Running            0          33m
kube-system   coredns-5644d7b6d9-xqvxc         1/1     Running            0          33m
kube-system   etcd-master                      1/1     Running            0          32m
kube-system   kube-apiserver-master            1/1     Running            0          32m
kube-system   kube-controller-manager-master   0/1     CrashLoopBackOff   2          34s

master $ kubectl -n kube-system logs kube-controller-manager-master
I0531 06:49:16.171244       1 serving.go:319] Generated self-signed cert in-memory
unable to load client CA file: unable to load client CA file: open /etc/kubernetes/pki/ca.crt: no such file or directory

# check pki folder
master $ ls -l /etc/kubernetes/pki
total 60
-rw-r--r-- 1 root root 1216 May 31 06:14 apiserver.crt
-rw-r--r-- 1 root root 1090 May 31 06:14 apiserver-etcd-client.crt
-rw------- 1 root root 1675 May 31 06:14 apiserver-etcd-client.key
-rw------- 1 root root 1679 May 31 06:14 apiserver.key
-rw-r--r-- 1 root root 1099 May 31 06:14 apiserver-kubelet-client.crt
-rw------- 1 root root 1675 May 31 06:14 apiserver-kubelet-client.key
-rw-r--r-- 1 root root 1025 May 31 06:14 ca.crt
-rw------- 1 root root 1675 May 31 06:14 ca.key
drwxr-xr-x 2 root root 4096 May 31 06:14 etcd
-rw-r--r-- 1 root root 1038 May 31 06:14 front-proxy-ca.crt
-rw------- 1 root root 1679 May 31 06:14 front-proxy-ca.key
-rw-r--r-- 1 root root 1058 May 31 06:14 front-proxy-client.crt
-rw------- 1 root root 1679 May 31 06:14 front-proxy-client.key
-rw------- 1 root root 1675 May 31 06:14 sa.key
-rw------- 1 root root  451 May 31 06:14 sa.pub

# 인증서는 일단 있으니.. describe로 pod 확인
      kube-controller-manager
      --authentication-kubeconfig=/etc/kubernetes/controller-manager.conf
      --authorization-kubeconfig=/etc/kubernetes/controller-manager.conf
      --bind-address=127.0.0.1
      --client-ca-file=/etc/kubernetes/pki/ca.crt
      --cluster-signing-cert-file=/etc/kubernetes/pki/ca.crt
      --cluster-signing-key-file=/etc/kubernetes/pki/ca.key
      --controllers=*,bootstrapsigner,tokencleaner
      --kubeconfig=/etc/kubernetes/controller-manager.conf
      --leader-elect=true
      --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt
      --root-ca-file=/etc/kubernetes/pki/ca.crt
      --service-account-private-key-file=/etc/kubernetes/pki/sa.key
      --use-service-account-credentials=true
      
k8s-certs:
  Type:          HostPath (bare host directory volume)
  Path:          /etc/kubernetes/WRONG-PKI-DIRECTORY
  HostPathType:  DirectoryOrCreate

# path 수정


```