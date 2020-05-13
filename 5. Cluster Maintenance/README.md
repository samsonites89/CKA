# [WIP] OS Upgrade

## OS Upgrades

version. update cluster. a DR scenario.


`Pod eviction time`

node 가 내려간 후 다시 online화 될때까지 기다리는 시간
`kube-controller-manager --pod-eviction-time`. 기본값은 5분이다.

Node가 복구될 때 이 eviction 시간이 지나면,내부에 있던 pod이 다시 생성되지 않는다.

`kubectl drain node-1` 노드를 drain해서 리소스를 이동시킨다.
drain을 하게 되면 node는 cordoned 이라고 표시되며, 리소스를 할당 받을 수 없는 상태 도입(unschedulable)

`uncordon`을 사용해서 노드를 다시 schedulable하게금 설정


`kubectl cordon` : 노드는 추가 리소스를 할당 받을 수 가 없음



## Cluster Upgrade Process

`kube-apiserver` 를 기준으로
- n-1 호환되는 모듈
  + `controller-manager`
  + `kube-scheduler`
  
- n-2 호환되는 모듈
  + `kubelet`
  + `kube-proxy
  ` 
- n+1 , n-1
  + `kubectl`
  
for example if `kube-apiserver` version is at **1.10.0**,
- `contoller-manager` : can be **1.10.0** or **1.9.0**
- `kube-scheduler` : can be **1.10.0** or **1.9.0**
- `kubelet` : can be **1.10.0** or **1.9.0** or **1.8.0**
- `kube-proxy` : can be **1.10.0** or **1.9.0** or **1.8.0**
- `kubectl` : can be **1.11.0** or **1.10.0** or **1.9.0** or **1.8.0**

이런 규칙(?)은 cluster를 업그레이드 할때 도움이된다. 

`kubeadm`을 사용했을 경우, `kubeadm upgrade plan`,`kubeadm upgrade apply`를 사용해서 업그레이드를 진행할 수 있다.
`kubelet`은 이후 수동으로 업그레이드해야된다. 
```shell script
# kubelet은 apt로 업드레이드가 가능한다.
apt-get upgrade -y kubelet=1.12.0-00
# 업그레이드 된후 kubelet 재시작
systemctl restart kubelet

# Worker node는 압서 배운 내용 처럼, drain을 하고 업그레이드를 진행하면된다.
kubectl drain node-1
# apt로 업데이트 진행 
apt-get upgrade -y kubelet=1.12.0-00
# 이후 kubeadm 업데이트 실행!!!!!
kubeadm upgrade node config --kubelet-version v1.12.0

# 업그레이드 된후 kubelet 재시작
systemctl restart kubelet

# Scheduling 활성화
kubectl uncordon node-1
```

### Upgrade 방법
1. `master` 노드 업그레이드
2. `worker` 노드 업그레이드

`master` 가 내려가 있어도 클러스터는 유지되지만, `kubectl` 로 클러스터 접근은 안 되며 리소스 변경/수정 또한 안된다.


`worker` 노드를 내릴 수 있는 방법은 아래와 같다.

1. strategy 1: worker node 한꺼번에 다 업그레이드하기 (down and then up)
   + 이 방법을 활용하며 서비스를 일시 중단해야한다.
2. strategy 2: worker node를 하나씩 업데이트하기
3. stretegy 3: 신규 worker node를 추가
   + 해당 방법은 리소스 생성이 쉬운 클라우드 환경에서 유용하게 사용할 수 있다.


## Backup and restore

backup candidates
- resource configuration


pod defintion을 사용하면 복제하기 쉽다. github에 저장하라우

혹은 apiserver를 활용해서 모든 리소스를 들고오는 방안도 있다.
```shell script
kubectl get all --all-namespace -o yaml > all-deploy-services.yaml
```
etcd cluster를 백업해도된다.
`etcd --data-dir`

`etcdctl snapshot save` command로 유용하다.


etcdctl is a command line client for etcd.=
To make use of etcdctl for tasks such as back up and restore, make sure that you set the ETCDCTL_API to 3.

You can do this by exporting the variable ETCDCTL_API prior to using the etcdctl client. This can be done as follows:

export ETCDCTL_API=3

On the Master Node:

To see all the options for a specific sub-command, make use of the -h or --help flag.
For example, if you want to take a snapshot of etcd, use:

etcdctl snapshot save -h and keep a note of the mandatory global options.
Since our ETCD database is TLS-Enabled, the following options are mandatory:

--cacert                                                verify certificates of TLS-enabled secure servers using this CA bundle

--cert                                                    identify secure client using this TLS certificate file

--endpoints=[127.0.0.1:2379]          This is the default as ETCD is running on master node and exposed on localhost 2379.

--key                                                      identify secure client using this TLS key file



Similarly use the help option for snapshot restore to see all available options for restoring the backup.

etcdctl snapshot restore -h

For a detailed explanation on how to make use of the etcdctl command line tool and work with the -h flags, check out the solution video for the Backup and Restore Lab.
