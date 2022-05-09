## Practice Test

How many PODs exist on the system?
in the current(default) namespace

```bash
kubectl get pods
```

Create a new pod with the NGINX image

```bash
# generator 사용. (deprecated)
kubectl run nginx --generator=run-pod/v1 --image=nginx

## kubectl 1.23.3 k3s 
kubectl run nginx --image=nginx

```

What is the image used to create the new pods?
You must look at one of the new pods in detail to figure this out

```bash
# use describe command to get more info
kubectl describe pod $podname | grep Image
```

Which nodes are these pods placed on?
You must look at all the pods in detail to figure this out

```bash
# Use -o wide to get info.
kubectl get po -o wide
```

How many containers are part of the pod 'webapp'?

```bash
kubectl describe pod webapp
...

Containers:
  nginx:
    Container ID:   docker://1c7302ed6ae43d42e7364f3a95682e45c72d2b69773d630e113dd85bb7d9e50f
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:86ae264c3f4acb99b2dee4d0098c40cb8c46dcf9e1148f05d3a51c4df6758c12
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Fri, 08 May 2020 04:07:32 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-w9chd (ro)
  agentx:
    Container ID:
    Image:          agentx
    Image ID:
    Port:           <none>
    Host Port:      <none>
    State:          Waiting
      Reason:       ImagePullBackOff
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-w9chd (ro)
```


Why do you think the container 'agentx' in pod 'webapp' is in error?
Try to figure it out from the events section of the pod

```bash
# check the Events portion from kubectl describe command

Events:
  Type     Reason     Age                From               Message
  ----     ------     ----               ----               -------
  Normal   Scheduled  <unknown>          default-scheduler  Successfully assigned default/webapp to node01
  Normal   Pulling    96s                kubelet, node01    Pulling image "nginx"
  Normal   Pulled     95s                kubelet, node01    Successfully pulled image "nginx"
  Normal   Created    95s                kubelet, node01    Created container nginx
  Normal   Started    94s                kubelet, node01    Started container nginx
  Warning  Failed     50s (x3 over 93s)  kubelet, node01    Failed to pull image "agentx": rpc error: code= Unknown desc = Error response from daemon: pull access denied for agentx, repository does not exist or may require 'docker login'
  Normal   BackOff    14s (x5 over 92s)  kubelet, node01    Back-off pulling image "agentx"
  Warning  Failed     14s (x5 over 92s)  kubelet, node01    Error: ImagePullBackOff
  Normal   Pulling    1s (x4 over 94s)   kubelet, node01    Pulling image "agentx"
  Warning  Failed     0s (x4 over 93s)   kubelet, node01    Error: ErrImagePull

```

Delete the 'webapp' Pod.

```bash
kubectl delete pod webapp
```

Create a new pod with the name 'redis' and with the image 'redis123'
Use a **pod-definition YAML** file. And yes the image name is wrong!

```bash
# Create yaml
kubectl run redis --generator=run-pod/v1 --image=redis123 --dry-run -o yaml > redis.yaml

# run the YAML definition file
master $ kubectl apply -f redis.yaml

```

yaml 내용:

```yaml
apiVersion: v1
kind: Pod
metadata: redis
spec:
    containers:
    - name: nginx
      image: nginx123
      imagePullPolicy: IfNotPresent #always
```


Now fix the image on the pod to 'redis'.
Update the pod-definition file and use 'kubectl apply' command or use 'kubectl edit pod redis' command.

```shell script
# let's try kubectl edit
kubectl edit pod redis

# fix the image portion in the spec:
spec:
  containers:
  - image: redis
    imagePullPolicy: Always
    name: redis
# wq! to save
pod/redis edited # this message will be shown

```
