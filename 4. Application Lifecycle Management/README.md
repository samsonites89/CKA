#


Rollout Command
```shell script
kubectl rollout status deployment/myapp-deployment

kubectl rollout history deployment/myapp-deployment
```

2 Types of Deployment Strategy

1. Recreate Strategy : Destroy and Re create
   = Apps are Down
2. Rolling Update: Down 1 Up 1 (default)


What does it mean to update

This ia after changing the deployment def.
`kubectl apply`


 kubectl set image deployment/m
 
 
 ```shell script
# possible to view the update strategy
kubectl describe deployment
```


### Upgrade 


`kubectl get rs`


Rollback your update.

```shell script
kubectl rollout undo deployment/myapp-deployment
```

