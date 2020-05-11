How many Secrets exist on the system?
in the current(default) namespace

```shell script
master $ kubectl get secrets
NAME                  TYPE                                  DATA   AGE
default-token-wrtnz   kubernetes.io/service-account-token   3      118s
```

How many secrets are defined in the 'default-token' secret?

```shell script
# data 수를 보면 3 이라고 표시 되어 있음
default-token-wrtnz   kubernetes.io/service-account-token   3      118s
```
What is the type of the 'default-token' secret?

```shell script
master $ kubectl describe secrets default-token-wrtnz
Name:         default-token-wrtnz
Namespace:    default
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: default
              kubernetes.io/service-account.uid: aae038a9-dd27-46ca-a3ad-f5a00f037d5b

Type:  kubernetes.io/service-account-token

Data
====
namespace:  7 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IkhxU25uY0NEUGlKQ2h6a2cyVXNWa3BPUmhsSDY4Q20wQ0sxdUJDNF9jZWcifQTsUw...
ca.crt:     1025 bytes
```

The reason the application is failed is because we have not created the secrets yet. 
Create a new Secret named 'db-secret' with the data given(on the right).
You may follow any one of the methods discussed in lecture to create the secret.

Secret Name: db-secret
Secret 1: DB_Host=sql01
Secret 2: DB_User=root
Secret 3: DB_Password=password123

```shell script
kubectl create secret generic db-secret \
--from-literal=DB_host=sql01 \
--from-literal=DB_User=root \
--from-literal=DB_Password=password123
```


Configure webapp-pod to load environment variables from the newly created secret.
Delete and recreate the pod if required

Pod name: webapp-pod
Image name: kodekloud/simple-webapp-mysql
Env From: Secret=db-secret

```shell script
master $ kubectl get pod webapp-pod -o yaml > webapp.yaml
master $ kubectl delete pod webapp-pod
pod "webapp-pod" deleted


spec:
  containers:
  - image: kodekloud/simple-webapp-mysql
    imagePullPolicy: Always
    name: webapp
    envFrom:
      secretRef:
        name: db-secret

```
