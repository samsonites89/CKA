What is the environment variable name set on the container in the pod?


```shell script

Containers:
  webapp-color:
    Container ID:   docker://cfd4038abe56607acb687eeacd23a173bc1a075ba875c36a6254518b16189ee9
    Image:          kodekloud/webapp-color
    Image ID:       docker-pullable://kodekloud/webapp-color@sha256:99c3821ea49b89c7a22d3eebab5c2e1ec651452e7675af243485034a72eb1423
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Mon, 11 May 2020 15:57:08 +0000
    Ready:          True
    Restart Count:  0
    Environment:
      APP_COLOR:  pink #####
    Mounts:

```


How many ConfigMaps exist in the environment?
```shell script
master $ kubectl get cm
NAME        DATA   AGE
db-config   3      7s
master $

```

Identify the database host from the config map 'db-config'
```shell script
master $ kubectl describe cm db-config
Name:         db-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
DB_NAME:
----
SQL01
DB_PORT:
----
3306
DB_HOST:
----
SQL01.example.com
Events:  <none>

```

Create a new ConfigMap for the 'webapp-color' POD. Use the spec given on the right.

ConfigName Name: webapp-config-map
Data: APP_COLOR=darkblue

```shell script
kubectl create cm webapp-config-map --from-literal=APP_COLOR=darkblue
```

Update the environment variable on the POD use the newly created ConfigMap
Note: Delete and recreate the POD. Only make the necessary changes. Do not modify the name of the Pod.

```shell script
# first delete and update the yaml to use value from cm.
spec:
  containers:
  - envFrom:
    - configMapRef:
        name: webapp-config-map
    image: kodekloud/webapp-color
    imagePullPolicy: Always
====


```

