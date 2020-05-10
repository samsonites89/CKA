Run the script named curl-test.sh to send multiple requests to test the web application. Take a note of the output.
Execute the script at /root/curl-test.sh.

```shell script

master $ ./curl-test.sh

for i in {1..35}; do
   kubectl exec --namespace=kube-public curl -- sh -c 'test=`wget -qO- -T 2  http://webapp-service.default.svc.cluster.local:8080/info 2>&1` && echo "$test OK" || echo "Failed"';
   echo ""

Hello, Application Version: v1 ; Color: blue OK

Hello, Application Version: v1 ; Color: blue OK

Hello, Application Version: v1 ; Color: blue OK
```

What container image is used to deploy the applications?

```shell script
kubectl describe deployments.apps frontend
Name:                   frontend
Namespace:              default
CreationTimestamp:      Sun, 10 May 2020 16:56:59 +0000
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               name=webapp
Replicas:               4 desired | 4 updated | 4 total | 4 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        20
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  name=webapp
  Containers:
   simple-webapp:
    Image:        kodekloud/webapp-color:v1
    Port:         8080/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
```
Inspect the deployment and identify the current strategy

```shell script
# 위와 동일한 command로 검색
Replicas:               4 desired | 4 updated | 4 total | 4 available | 0 unavailable
...
StrategyType:           RollingUpdate
...
```

If you were to upgrade the application now what would happen?
> PODS are upgraded few at a time

Let us try that. Upgrade the application by setting the image on the deployment to 'kodekloud/webapp-color:v2'
Do not delete and re-create the deployment. Only set the new image name for the existing deployment.


```shell script
# kubectl edit 활용
kubectl edit deployments.apps frontend

# 아래 부분 수정
spec:
  containers:
  - image : ...

master $ kubectl edit deployments.apps frontend
deployment.apps/frontend edited
```


Change the deployment strategy to 'Recreate'
Do not delete and re-create the deployment. Only update the strategy type for the existing deployment.

```shell script

# When updating you can see all the pods going down at once!
master $ kubectl get po
NAME                        READY   STATUS        RESTARTS   AGE
frontend-84bb97f469-2qtbl   0/1     Terminating   0          6m51s
frontend-84bb97f469-bmq2j   0/1     Terminating   0          7m16s
frontend-84bb97f469-c5b25   0/1     Terminating   0          6m51s
frontend-84bb97f469-mkkmd   0/1     Terminating   0          7m16s
```
