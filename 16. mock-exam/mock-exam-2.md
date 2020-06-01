## Mock Exam 2

### Q1
Take a backup of the etcd cluster and save it to /tmp/etcd-backup.db
https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#backing-up-an-etcd-cluster

```
ETCDCTL_API=3 etcdctl version

cd /etc/kubernetes/manifests
cat etcd.yaml


ETCDCTL_API=3 etcdctl --endpoints=https://172.17.0.77:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt \
 --cert=/etc/kubernetes/pki/etcd/healthcheck-client.crt  --key=/etc/kubernetes/pki/etcd/healthcheck-client.key  member list
ETCDCTL_API=3 etcdctl --endpoints=https://172.17.0.77:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt \
 --cert=/etc/kubernetes/pki/etcd/healthcheck-client.crt  --key=/etc/kubernetes/pki/etcd/healthcheck-client.key   snapshot save /tmp/etcd-backup.db
ETCDCTL_API=3 etcdctl --endpoints=https://172.17.0.77:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt \
 --cert=/etc/kubernetes/pki/etcd/healthcheck-client.crt  --key=/etc/kubernetes/pki/etcd/healthcheck-client.key   snapshot status /tmp/etcd-backup.db -w table

```

### Q2
Create a Pod called redis-storage with image: redis:alpine with a Volume of type emptyDir that lasts for the life of the Pod. Specs on the right.

Pod named 'redis-storage' created
Pod 'redis-storage' uses Volume type of emptyDir
Pod 'redis-storage' uses volumeMount with mountPath = /data/redis 

```
kubectl run --generator=run-pod/v1 elephant --image=redis --dry-run -o yaml > elephant.yaml


resources:
    requests:
      cpu: "1"
      memory: "200Mi"

kubectl create -f elephant.yaml      

kubectl describe pod elephant
```

### Q3
Create a new pod called super-user-pod with image busybox:1.28. Allow the pod to be able to set system_time

The container should sleep for 4800 seconds

Pod: super-user-pod
Container Image: busybox:1.28
SYS_TIME capabilities for the conatiner? 

https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

```
kubectl run --generator=run-pod/v1 super-user-pod --image=busybox:1.28 --dry-run -o yaml > super-user-pod.yaml

    command: ["sleep", "4800"]
    securityContext:
      capabilities:
        add: ["SYS_TIME"]

kubectl create -f super-user-pod.yaml
kubectl describe pod super-user-pod        

```

### Q4
A pod definition file is created at /root/use-pv.yaml. Make use of this manifest file and mount the persistent volume called pv-1. Ensure the pod is running and the PV is bound.

mountPath: /data persistentVolumeClaim Name: my-pvc

persistentVolume Claim configured correctly
pod using the correct mountPath
pod using the persistent volume claim? 

https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims

```
kubectl get pv

vi pvc.yaml

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Mi

kubectl create -f pvc.yaml

kubectl get pv

vi user-pv.yaml

      volumeMounts:
      - mountPath: "/data"
        name: mypd
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: my-pvc

kubectl create -f user-pv.yaml

kubectl describe pod user-pv

```

### Q5
Create a new deployment called nginx-deploy, with image nginx:1.16 and 1 replica. Record the version. Next upgrade the deployment to version 1.17 using rolling update. Make sure that the version upgrade is recorded in the resource annotation.

Deployment : nginx-deploy. Image: nginx:1.16
Image: nginx:1.16
Task: Upgrade the version of the deployment to 1:17
Task: Record the changes for the image upgrade 

```
kubectl run nginx-deploy --image=nginx:1.16 --replicas=1 --record
kubectl get deployments.

kubeclt rollout history deployment nginx-deploy

kubeclt set image deployment/nginx-deploy nginx-deploy=nginx:1.17 --record
kubectl get deployments.

kubectl describe deployments. nginx-deploy | grep -i image  
kubectl get pod xxx
kubectl describe pod xxx | grep -i image

kubectl rollout history deployment nginx-deploy
```


### Q6
Create a new user called john. Grant him access to the cluster. John should have permission to create, list, get, update and delete pods in the development namespace . The private key exists in the location: /root/john.key and csr at /root/john.csr

CSR: john-developer Status:Approved
Role Name: developer, namespace: development, Resource: Pods
Access: User 'john' has appropriate permissions 

https://kubernetes.io/docs/tasks/tls/managing-tls-in-a-cluster/#create-a-certificate-signing-request-object-to-send-to-the-kubernetes-api

```
kubectl api-versions | grep certificates

vi john.yaml

apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  name: john-developer
spec:
  request: $(cat john.csr | base64 | tr -d '\n')
  usages:
  - digital signature
  - key encipherment
  - server auth

kubectl get csr

kubectl certificate approve john-developer
kubectl get csr

kubectl create role developer --resource=pods --verb=create,list,get,update,delete --namespace=development

kubectl describe role developer -n development

kubectl create rolebinding developer-role-binding --role=developer --user=john --namespace=development

kubectl -n development describe rolebindings. developer-role-binding


kubectl auth can-i update pods

kubectl auth can-i update pods --as=john

kubectl auth can-i update pods --namespace=development --as=john
kubectl auth can-i list  pods --namespace=development --as=john
kubectl auth can-i watch  pods --namespace=development --as=john

```


### Q7
Create an nginx pod called nginx-resolver using image nginx, expose it internally with a service called nginx-resolver-service. Test that you are able to look up the service and pod names from within the cluster. Use the image: busybox:1.28 for dns lookup. Record results in /root/nginx.svc and /root/nginx.pod

Pod: nginx-resolver created
Service DNS Resolution recorded correctly
Pod DNS resolution recorded correctly 

```
kubectl run --generator=run-pod/v1 nginx-resolver --image=nginx
kubectl expose pod nginx-resolver --name=nginx-resolver-service --port=80 --target-port=80 --type=ClusterIP 

kubectl describe svc nginx-resolver-service

kubectl get pod nginx-resolver -o wide

kubectl get svc

kubectl run --generator=run-pod/v1 test-nslookup --image=busybox:1.28 --rm -it -- nslookup nginx-resolver-service
 > /root/nginx.svc

cat /root/nginx.svc

kubectl get pod nginx-resolver -o wide
kubectl run --generator=run-pod/v1 test-nslookup --image=busybox:1.28 --rm -it -- nslookup 10-31-0-5.default.pod
 > /root/nginx.pod

 cat /root/nginx.pod

```



### Q8
Create a static pod on node01 called nginx-critical with image nginx. Create this pod on node01 and make sure that it is recreated/restarted automatically in case of a failure.

Use /etc/kubernetes/manifests as the Static Pod path for example.

Kubelet Configured for Static Pods
Pod nginx-critical-node01 is Up and running 

```
kubectl get nodes
ssh node01

systemctl status kubelet
cd /var/lib/kubelet
vi config.yaml # staticPodPath check!

cd /etc/kubernetes
mkdir manifests

exit

#master node
kubectl run --generator=run-pod/v1 nginx-critical --image=nginx --dry-run -o yaml > nginx-critical.yaml

#copy yaml contents
cat nginx-critical.yaml

ssh node01
cd /etc/kubernetes/manifests
vi nginx-critical.yal

docker ps | grep -i nginx-critical

exit

kubectl get pods


```
