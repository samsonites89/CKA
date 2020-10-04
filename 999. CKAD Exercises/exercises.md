
##### Create a namespace called 'mynamespace' and a pod with image nginx called nginx on this namespace
```shell script
kubectl create ns mynamespace
kubectl run nginx --generator=run-pod/v1 --image=nginx -n mynamespace
```

##### Create the pod that was just described using YAML
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: nginx
    namespace: mynamespace
spec:
    containers:
    - name: nginx
      image: nginx
```

##### Create a busybox pod (using kubectl command) that runs the command "env". Run it and see the output
```shell script
#  kubectl run nginx --image=nginx -- <arg1> <arg2> ... <argN>
 
kubectl run busy --image=busybox --generator=run-pod/v1  -- env

kubectl logs busy 
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=busy
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
HOME=/root
```

##### Create a busybox pod (using YAML) that runs the command "env". Run it and see the output
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: busy
spec:
    containers:
    - name: busy
      image: busybox
      args:
      - env

```

##### Get the YAML for a new namespace called 'myns' without creating it

```
kubectl create ns myns --dry-run -o yaml > ns.yaml
```


##### Get the YAML for a new ResourceQuota called 'myrq' with hard limits of 1 CPU, 1G memory and 2 pods without creating it
```
kubectl create quota --hard=cpu=1,memory=1G,pods=2 --dry-run -o yaml > rq.yaml
```