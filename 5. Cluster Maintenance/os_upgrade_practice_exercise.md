#

On which nodes are the applications hosted on?

```bash
# Pod가 어떤 노드에 할당됬는지 확인하기 위해서는 -o wide 옵션으로 부가 정보 조회 
master $ kubectl get po -o wide
NAME                    READY   STATUS    RESTARTS   AGE   IP          NODE     NOMINATED NODE   READINESS GATES
blue-68df9fb9d9-2ctqp   1/1     Running   0          53s   10.44.0.2   node01   <none>           <none>
blue-68df9fb9d9-cxnct   1/1     Running   0          53s   10.36.0.2   node02   <none>           <none>
blue-68df9fb9d9-pm88q   1/1     Running   0          53s   10.42.0.1   node03   <none>           <none>
red-5dc59c9654-9gqmb    1/1     Running   0          53s   10.36.0.1   node02   <none>           <none>
red-5dc59c9654-plfgm    1/1     Running   0          53s   10.44.0.1   node01   <none>           <none>
```

We need to take node01 out for maintenance. Empty the node of all applications and mark it unschedulable.
```bash
# drain으로 node01에 있는 리소스 제거.
master $ kubectl drain node01
node/node01 cordoned
error: unable to drain node "node01", aborting command...

There are pending nodes to be drained:
 node01
error: cannot delete DaemonSet-managed Pods (use --ignore-daemonsets to ignore): kube-system/kube-proxy-jbs2h, kube-system/weave-net-mmfc4


#daemonsets을 ignore하기 위해서 --ignore-daemonsets 사용
master $ kubectl drain node01 --ignore-daemonsets
node/node01 already cordoned
WARNING: ignoring DaemonSet-managed Pods: kube-system/kube-proxy-jbs2h, kube-system/weave-net-mmfc4
evicting pod "red-5dc59c9654-plfgm"
evicting pod "blue-68df9fb9d9-2ctqp"
pod/blue-68df9fb9d9-2ctqp evicted
pod/red-5dc59c9654-plfgm evicted
node/node01 evicted
```

The maintenance tasks have been completed. Configure the node to be schedulable again.

```bash
# uncordon으로 노드를 schedulable하게 재설정

master $ kubectl uncordon node01
node/node01 uncordoned

# node01에는 아직 리소스가 없음. 이유는 새로 생성되는 pod만 schedule될 수 있음
```


Why are there no pods placed on the master node?

```bash
# master노드에 pod이 존재하지 않은 이유는 기본적으로 taint가 되어 있기 때문임

master $ kubectl describe nodes master
Name:               master
Roles:              master
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
...

Taints:             node-role.kubernetes.io/master:NoSchedule
Unschedulable:      false
Conditions:
...
```

It is now time to take down node02 for maintenance. 
Before you remove all workload from node02 answer the following question.

Can you drain node02 using the same command as node01? Try it.


```bash
master $ kubectl drain node02 --ignore-daemonsets
node/node02 cordoned
error: unable to drain node "node02", aborting command...

There are pending nodes to be drained:
 node02
error: cannot delete Pods not managed by ReplicationController, ReplicaSet, Job, DaemonSet or StatefulSet (use --force to override): default/hr-app
```

Why do you need to force the drain?
> 해당 노드는 replicaset에 속해있지 않는 pod들이 존재함.

What is the name of the POD not part of a replicaset hosted on node02?
```bash
master $ kubectl get po -o wide
NAME                    READY   STATUS    RESTARTS   AGE     IP          NODE     NOMINATED NODE   READINESS GATES
blue-68df9fb9d9-cxnct   1/1     Running   0          11m     10.36.0.2   node02   <none>           <none>
blue-68df9fb9d9-j2t9l   1/1     Running   0          7m33s   10.36.0.3   node02   <none>           <none>
blue-68df9fb9d9-pm88q   1/1     Running   0          11m     10.42.0.1   node03   <none>           <none>
hr-app                  1/1     Running   0          2m22s   10.36.0.4   node02   <none>           <none>
red-5dc59c9654-6296h    1/1     Running   0          7m33s   10.42.0.2   node03   <none>           <none>
red-5dc59c9654-9gqmb    1/1     Running   0          11m     10.36.0.1   node02   <none>           <none>

master $ kubectl get rs
NAME              DESIRED   CURRENT   READY   AGE
blue-68df9fb9d9   3         3         3       12m
red-5dc59c9654    2         2         2       12m

# red와 blue는 rs에 속함. 정답은 hr-app

```

What would happen to `hr-app` if node02 is drained forcefully?
> hr-app will be lost forever

Drain node02

```bash
master $ kubectl drain node02 --ignore-daemonsets --force
node/node02 already cordoned
WARNING: deleting Pods not managed by ReplicationController, ReplicaSet, Job, DaemonSet or StatefulSet: default/hr-app; ignoring DaemonSet-managed Pods: kube-system/kube-proxy-pqw5h, kube-system/weave-net-nl5nk
evicting pod "red-5dc59c9654-9gqmb"
evicting pod "blue-68df9fb9d9-cxnct"
evicting pod "hr-app"
evicting pod "blue-68df9fb9d9-j2t9l"
pod/hr-app evicted
pod/blue-68df9fb9d9-cxnct evicted
pod/blue-68df9fb9d9-j2t9l evicted
pod/red-5dc59c9654-9gqmb evicted
node/node02 evicted
```

Node03 has our critical applications. 
We do not want to schedule any more apps on node03. 
Mark node03 as unschedulable but do not remove any apps currently running on it .

```bash
# 노드를 unschedulable로 설정하기 위해서는 cordon을 해야함
master $ kubectl cordon node03
node/node03 cordoned
```






