# mock exam 1

## Q1.

Deploy a pod named `nginx-pod` using the `nginx:alpine image.

```bash
kubectl run nginx-pod --generator=run-pod/v1 --image=nginx:alpine
```

## Q2.

Deploy a `messaging` pod using the `redis:alpine` image with the labels set to `tier=msg`.

```bash
kubectl run messaging --generator=run-pod/v1 --image=redis:alpine --dry-run -o yaml > msg.yaml

master $ cat msg.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: messaging
    tier: msg
  name: messaging
spec:
  containers:
  - image: redis:alpine
    name: messaging
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

```


## Q3.

Create a namespace named apx-x9984574

```bash
kubectl create ns apx-x9984574
```

## 04.

Get the list of nodes in JSON format and store it in a file at /opt/outputs/nodes-z3444kd9.json

```bash
kubectl get nodes -o json > /opt/outputs/nodes-z3444kd9.json

```

## 05. 

Create a service messaging-service to expose the messaging application within the cluster on port 6379.
**Use imperative command** 

- Service: messaging-service
- Port: 6379
- Type: ClusterIp
- Use the right labels

```bash
 kubectl expose pod messaging --name=messaging-service --port=6379 --selector=tier=msg --type=ClusterIP
```


## Q6.

Create a deployment named `hr-web-app` using the image `kodekloud/webapp-color` with `2` replicas

```bash
kubectl create deployment hr-web-app --image=kodekloud/webapp-color --dry-run -o yaml > dep.yaml
# change replicas : 2

```

## Q7.

Create a static pod named `static-busybox` on the master node that uses the `busybox` image and the command `sleep 1000`

```bash
#create a yaml within /etc/kubernetes/manifests

master $ cat static-busybox.yaml
apiVersion: v1
kind: Pod
metadata:
  name: static-busybox
spec:
  containers:
  - image: busybox
    name: static-busybox
    args: ["sleep","1000"]
    resources: {}

```
## Q8.

Create a POD in the `finance` namespace named `temp-bus` with the image `redis:alpine`.

```bash
kubectl run temp-bus --generator=run-pod/v1 --image=redis:alpine --namespace=finance
```

## Q9.

A new application orange is deployed. There is something wrong with it. Identify and fix the issue.


## Q10

Expose the `hr-web-app` as service `hr-web-app-service` application on port 30082 on the nodes on the cluster
- Name: hr-web-app-service
- Type: NodePort
- Endpoints: 2
- Port: 8080
- NodePort: 30082

```bash
kubectl expose deployment hr-web-app --type=NodePort --port=8080 --name=hr-web-app-service --dry-run -o yaml > hr-web-app-service.yaml

# fix the nodePort

```

## Q11
Use JSON PATH query to retrieve the osImages of all the nodes and store it in a file /opt/outputs/nodes_os_x43kj56.txt

```bash
kubectl get nodes -o jsonpath='{.items[*].status.nodeInfo.osImage}' > /opt/outputs/nodes_os_x43kj56.txt

```
## Q12
Create a Persistent Volume with the given specification


Volume Name: pv-analytics
Storage: 100Mi
Access modes: ReadWriteMany
Host Path: /pv/data-analytics

```yaml

apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-analytics
spec:
  capacity:
    storage: 100Mi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: /pv/data-analytics
   


```