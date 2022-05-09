## Deployment

1. How many replicasets are there?
```bash
kubectl get rs
```

2. How many deployments?
```bash
kubectl get deploy
```


4. What images are being used?
```bash
kubectl describe deploy front-end deployment
Name:                   frontend-deployment
Namespace:              default
CreationTimestamp:      Sun, 21 Jun 2020 08:56:36 +0000
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               name=busybox-pod
Replicas:               4 desired | 4 updated | 4 total | 0 available | 4 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  name=busybox-pod
  Containers:
   busybox-container:
    Image:      busybox888
```

5.  Create a new Deployment with the below attributes using your own deployment definition file
- Name: httpd-frontend; 
- Replicas: 3; 
- Image: httpd:2.4-alpine

```shell script
# 제일 쉬운 방법은 kubectl create --dry-run
kubectl create deployment httpd-frontend --iamge=httpd:2.4-alpine --replicas=3 --dry-run=client -o yaml > def.yaml

kubectl apply -f def.yaml
```
