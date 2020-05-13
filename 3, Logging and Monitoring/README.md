# Kubernetes Monitoring and Logging

## Monitoring

`kubelet `별 `cAdvisor` 가 동작하고 있는 pod에 대해서 monitoring을 하지만 **in-memory** 임으로 `metrics-server` 를 활용하여
monitoring 수행한다. `metrics-server` 는 git clone으로 받아서 배포한다.
```shell script
git clone https://github.com/kubernetes-incubator/metrics-server.git
```
해당 repo에 접근하여 `kubectl` 실행 시 아래와 같은 리소스들이 생성된다. 

```shell script
kubectl create –f deploy/1.8+/
clusterrolebinding "metrics-server:system:auth-delegator" created
rolebinding "metrics-server-auth-reader" created
apiservice "v1beta1.metrics.k8s.io" created
serviceaccount "metrics-server" created
deployment "metrics-server" created
service "metrics-server" created
clusterrole "system:metrics-server" created
clusterrolebinding "system:metrics-server" created
```

event서버 기동시 `kubectl top` 으로 리소스 CPU 및 메모리 사용량을 모니터링할 수 있다.
```shell script
# node resource monitoring
kubectl top node
NAME CPU(cores) CPU% MEMORY(bytes) MEMORY%
kubemaster 166m 8% 1337Mi 70%
kubenode1 36m 1% 1046Mi 55%
kubenode2 39m 1% 1048Mi 55%

# pod resource monitoring
kubectl top pod
NAME CPU(cores) CPU% MEMORY(bytes) MEMORY%
nginx 166m 8% 1337Mi 70%
redis 36m 1% 1046Mi 55%
```

## Logging
Kubernetes 내에 있는 리소스에 로그는 아래와 같이 `kubectl logs` command 를 활용하면 된다.
해당 command는 pod내 동작하는 `container`에 로그 정보를 보여준다.

```shell script

# log 조회
master $ kubectl logs -f --tail 10 webapp-1
[2020-05-10 15:10:03,392] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2020-05-10 15:10:03,392] INFO in event-simulator: USER4 logged in
[2020-05-10 15:10:04,394] INFO in event-simulator: USER3 is viewing page1
[2020-05-10 15:10:05,395] INFO in event-simulator: USER4 is viewing page3
[2020-05-10 15:10:06,401] WARNING in event-simulator: USER7 Order failed as the item is OUT OF STOCK.
[2020-05-10 15:10:06,402] INFO in event-simulator: USER2 is viewing page3
[2020-05-10 15:10:07,403] INFO in event-simulator: USER4 is viewing page3
[2020-05-10 15:10:08,404] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2020-05-10 15:10:08,405] INFO in event-simulator: USER3 logged out
[2020-05-10 15:10:09,409] INFO in event-simulator: USER2 is viewing page3
[2020-05-10 15:10:10,411] INFO in event-simulator: USER1 logged out
```



