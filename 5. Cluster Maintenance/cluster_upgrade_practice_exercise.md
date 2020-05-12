This lab tests your skills on upgrading a kubernetes cluster. 
We have a production cluster with applications running on it. 
Let us explore the setup first.

What is the current version of the cluster?
```shell script
# 클러스터 버전을 보려면 kubectl version 실행
kubectl version
```



You are tasked to upgrade the cluster. 
User's accessing the applications must not be impacted. 
And you cannot provision new VMs. 
What strategy would you use to upgrade the cluster?
> update one node at a time



What is the latest stable version available for upgrade?

```shell script
master $ kubeadm upgrade plan
[upgrade/config] Making sure the configuration is correct:
[upgrade/config] Reading configuration from the cluster...
[upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
[preflight] Running pre-flight checks.
[upgrade] Making sure the cluster is healthy:
[upgrade] Fetching available versions to upgrade to
[upgrade/versions] Cluster version: v1.16.0
[upgrade/versions] kubeadm version: v1.17.0
I0512 13:13:14.712307    8715 version.go:251] remote version is much newer: v1.18.2; falling back to: stable-1.17
[upgrade/versions] Latest stable version: v1.17.5
[upgrade/versions] Latest version in the v1.16 series: v1.16.9

```


We will be upgrading the master node first. 
Drain the master node of workloads and mark it UnSchedulable
```shell script
# daemonset인 weavenet, proxy는 ignore
master $ kubectl drain master --ignore-daemonsets
node/master already cordoned
WARNING: ignoring DaemonSet-managed Pods: kube-system/kube-proxy-6zcd5, kube-system/weave-net-wsgnf
evicting pod "red-5dc59c9654-kml85"
pod/red-5dc59c9654-kml85 evicted
node/master evicted
```

Upgrade the master components to exact version v1.17.0
```shell script
# upgrade kubeadm first
master $ kubeadm upgrade apply v1.17.0
[upgrade/config] Making sure the configuration is correct:
[upgrade/config] Reading configuration from the cluster...
[upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
[preflight] Running pre-flight checks.
[upgrade] Making sure the cluster is healthy:
[upgrade/version] You have chosen to change the cluster version to "v1.17.0"
[upgrade/versions] Cluster version: v1.16.0
[upgrade/versions] kubeadm version: v1.17.0
[upgrade/confirm] Are you sure you want to proceed with the upgrade? [y/N]:
[upgrade/successful] SUCCESS! Your cluster was upgraded to "v1.17.0". Enjoy!
[upgrade/kubelet] Now that your control plane is upgraded, please proceed with upgrading your kubelets if you haven't already done so.

# upgrade kubelet on master
apt install kubelet=1.17.0-00
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following package was automatically installed and is no longer required:
  libuv1
Use 'apt autoremove' to remove it.
The following packages will be upgraded:
  kubelet
1 to upgrade, 0 to newly install, 0 to remove and 245 not to upgrade.
Need to get 19.2 MB of archives.
After this operation, 11.6 MB disk space will be freed.
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubelet amd64 1.17.0-00 [19.2 MB]
Fetched 19.2 MB in 1s (14.6 MB/s)
(Reading database ... 248896 files and directories currently installed.)
Preparing to unpack .../kubelet_1.17.0-00_amd64.deb ...
Unpacking kubelet (1.17.0-00) over (1.16.0-00) ...

master $ systemctl restart kubelet
master $ kubectl get nodes
NAME     STATUS                        ROLES    AGE   VERSION
master   NotReady,SchedulingDisabled   master   14m   v1.17.0
node01   Ready                         <none>   13m   v1.16.0

# uncordon
master $ kubectl uncordon master
node/master uncordoned
master $ kubectl get nodes
NAME     STATUS   ROLES    AGE   VERSION
master   Ready    master   15m   v1.17.0
node01   Ready    <none>   14m   v1.16.0

worker node는 아래 실행
node01 $ kubeadm upgrade node --kubelet-version v1.17.0


```




