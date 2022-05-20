## Namespace Practice Exercise

1. How many namespaces?
```bash
kubectl get ns 
```
2. How many pods exist in the 'research' namespace?
    
```bash
kubectl get po -n research
```    

3. Create a POD in the 'finance' namespace.
- Name: redis
- Image Name: redis
```bash
kubectl run redis --image=redis --generator=run-pod/v1 --namespace=finance
```

5. Which service name should be used by the blue pod to access a service in the `dev` ns?

```bash

# service-name.svc.namespace
curl mysql.dev.svc.cluster.local
curl mysql-service.svc
```