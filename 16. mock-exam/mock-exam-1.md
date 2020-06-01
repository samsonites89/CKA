## Mock Exam 1

### Q1
Deploy a pod named nginx-pod using the nginx:alpine image.

Once done, click on the Next Question button in the top right corner of this panel. You may navigate back and forth freely between all questions. 
Once done with all questions, click on End Exam. Your work will be validated at the end and score shown. Good Luck!

```
k run nginx-pod --image=nginx:alpine --generator=run-pod/v1
```

### Q2
Deploy a messaging pod using the redis:alpine image with the labels set to tier=msg.

```
k run messaging --image=redis:alpine -l tier=msg --generator=run-pod/v1
```

### Q3
Create a namespace named apx-x9984574

```
k create ns apx-x9984574
```

### Q4
Get the list of nodes in JSON format and store it in a file at /opt/outputs/nodes-z3444kd9.json

```
k get nodes -o json > /opt/outputs/nodes-z3444kd9.json
```


### Q5
Create a service messaging-service to expose the messaging application within the cluster on port 6379.

Use imperative commands

```
k expose pod messaging --port=6379 -l tier=msg --name=messaging-service
```

### Q6
Create a deployment named hr-web-app using the image kodekloud/webapp-color with 2 replicas

```
k create deployment hr-web-app --image=kodekloud/webapp-color --dry-run -o yaml > hr-web-app.yaml
vi hr-web-app.yaml  # replicas=2
k apply -f hr-web-app.yaml
```

### Q7
Create a static pod named static-busybox on the master node that uses the busybox image and the command sleep 1000

```
k run static-busybox --generator=run-pod/v1 --image=busybox --dry-run -o yaml --command -- sleep 1000 > static-busybox.yaml
mv static-busybox.yaml /etc/kubernetes/manifests/
```


### Q8
Create a POD in the finance namespace named temp-bus with the image redis:alpine.

```
k run temp-bus -n finance --image=redis:alpine --generator=run-pod/v1
```

### Q9
A new application orange is deployed. There is something wrong with it. Identify and fix the issue.

```
k logs orange

k get po orange -o yaml > orange.yaml
vi orange.yaml  # sleeeep -> sleep
k delete po orange
k apply -f orange.yaml
```

### Q10
Expose the hr-web-app as service hr-web-app-service application on port 30082 on the nodes on the cluster

The web application listens on port 8080

```
kubectl expose deployment hr-web-app --type=NodePort --port=8080 --name=hr-web-app-service --dry-run -o yaml > hr-web-app-service.yaml
```

vi hr-web-app-service.yaml
```
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: hr-web-app
  name: hr-web-app-service
spec:
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 8080
    nodePort: 30082     # added
  selector:
    app: hr-web-app
  type: NodePort
status:
  loadBalancer: {}
```


### Q11
Use JSON PATH query to retrieve the osImages of all the nodes and store it in a file /opt/outputs/nodes_os_x43kj56.txt

The osImages are under the nodeInfo section under status of each node.

```
k get nodes -o=jsonpath='{.items[*].status.nodeInfo.osImage}' > /opt/outputs/nodes_os_x43kj56.txt
```


### Q12
Create a Persistent Volume with the given specification.

Volume Name: pv-analytics
Storage: 100Mi
Access modes: ReadWriteMany
Host Path: /pv/data-analytics 


```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-analytics
spec:
  capacity:
    storage: 100Mi
  volumeMode: Filesystem
  accessModes:
  - ReadWriteMany
  persistentVolumeReclaimPolicy: Delete
  hostPath:
    path: /pv/data-analytics
```
