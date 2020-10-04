## Namespace Practice Exercise

1. How many namespaces?
```shell script
kubectl get ns 
```
2. How many pods exist in the 'research' namespace?
    
```shell script
kubectl get po -n research
```    

3. Create a POD in the 'finance' namespace.
- Name: redis
- Image Name: redis
```shell script
kubectl run redis --image=redis --generator=run-pod/v1 --namespace=finance
```

5. Which service name should be used by the blue pod to access a service in the `dev` ns?

```shell script

# service-name.svc.namespace
curl mysql.dev.svc.cluster.local
curl mysql-service.svc
```