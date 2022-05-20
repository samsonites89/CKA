Imperative Commands

Create a service redis-service to expose the redis application within the cluster on port 6379.
Use imperative commands.

```bash
kubectl expose pod redis --name=redis-service --type=ClusterIP --port 6379

```

Create a deployment named `webapp` using the image `kodekloud/webapp-color` with 3 replicas.
```bash
kubectl create deploy webapp --image=kodekloud/webapp-color --replicas=3


```

Create a new pod called `custom-nginx` using the `nginx` image and expose it on container port `8080`.

```bash
kubectl run custom-nginx --image=nginx --port 8080

kubectl expose pod custom-nginx --port=8080


```

