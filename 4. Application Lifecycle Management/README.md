# Rollout and Rollbacks

Rollout Command
```shell script
kubectl rollout status deployment/myapp-deployment

kubectl rollout history deployment/myapp-deployment
```

2 Types of Deployment Strategy

1. Recreate Strategy : Destroy and Re create
   = Apps are Down
2. Rolling Update: Down 1 Up 1 (default)


What does it mean to update

This ia after changing the deployment def.
`kubectl apply`


 kubectl set image deployment/m
 
 
 ```shell script
# possible to view the update strategy
kubectl describe deployment
```


### Upgrade 

`kubectl get rs`


Rollback your update.

```shell script
kubectl rollout undo deployment/myapp-deployment
```





## Environment Variables
`yaml` 정의 파일에 아래와 같이 `env`를 추가시켜 환경 변수를 넘길 수 있다. 
여기서 `value`를 사용하면 해당 `env variable`에 값을 바로 입력하고..
`configmap` 및 `secret` 도 사용 가능하다.


```yaml
env:
  -  name: COLOR
     value: pink
```
#### ConfigMap 사용
```yaml
env:
  -  name: COLOR
     valueFrom:
       configMapKeyRef: 
```

#### Secret 사용
```yaml
env:
  -  name: COLOR
     valueFrom:
       secretKeyRef:
```

## ConfigMaps
`ConfigMap`은 key-value pair로 정의되어 있는 설정(configuration)값을 정의할 수 있게끔 지원하며,
Pod 및 리소스마다 별도로 configuration을 설정하는 것 보다, 한꺼번에 관련된 값을 관리할 수 있다.

1. Create ConfigMap
2. Inject into Pod

### ConfigMap 생성

#### Imperative method
```shell script
# cm 약자도 사용 가능
kubectl create configmap \
<configmap-name> --from-literal=<key>=<value>

# Color라는 key에 blue value 값 세팅
kubectl create cm color --from-literal=COLOR=blue

# 다수 key-value configMap을 생성하기 위해서는 --from-literal를 반복함
kubectl create cm app-config \ 
--from-literal=APP_COLOR=red \
--from-literal=APP_ENV=prod \
--from-literal=APP_VERSION=1.0


# 파일 기반으로 cm 생성
kubectl create cm app-config \
--from-file=appconfig.properties

```
#### Declarative method
yaml definition을 활용해서 `configMap` 생성

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
# spec 대신 data 사용
data:
  APP_COLOR: red
  APP_ENV: prod
```
`kubectl create -f **.yaml` 로 관련 `configmap` 생성

### ConfigMap 조회 

```shell script
# get cm으로 configmap 조회
kubectl get configmaps

#describe로 configmap 디테일 조회
```

### Pod에 configmap 적용
```yaml
# spec > containers > envFrom로 설정
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
      - containerPort: 8080
    envFrom:
      - configMapRef:
            name: nginx-config 
```

single env이면,
```yaml
env:
  -  name: APP_COLOR
     valueFrom:
       configMapKeyRef:
         name: app-config
         key: APP_COLOR
```
volume 으로도 가능 
```yaml
volumes:
-  name: app-config-volume
   configMap:
      name: app-config
```

## Secrets
Secret인 비밀번호 및 키와 같이 sensitive한 데이터를 공유하기 위해 사용한다.
`configmap`은 평문!

1. create sercret
2. inject into pod

### secret 생성
#### imperative method
`kubectl create secret generic` 사용

```shell script
#example
kubectl create secret generic \
app-secret --from-literal=DB_HOST=mysql

#file 로 생성
kubectl create secret generic \
app-secret --from-file=secret.properties 

```



#### declarative method
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
data:
  DB_HOST: mysql
  DB_USER: root
```
이렇게 하면 secret은 평문으로 노출이된다. 그럼으로 encoding을 할 필요가 있다.
```shell script
echo -n '$text' | base64
```

### secret 조회
```shell script

# get
kubectl get secrets

# describe
kubectl describe secrets app-secret

# value를 보고싶으면 yaml로 출력해야함.
kubectl describe secrets app-secret -o yaml 

```
Decoding
```shell script
echo -n 'baseEncode' | base64 --decode
```

### Pod에 secret 적용
yaml에 envFrom 설정
```yaml
spec:
  containers:
  -  name: test
     image: test
     envFrom:
        - secretRef:
            name: app-secret
```
`configmap` 과 동일하게 inject하는 방식 더 있음

single secret
```yaml
env:
  - name: db-password
    valueFrom:
      secretKeyRef:
         name: app-secret
         key: db-pass
```

volume 활용
```yaml
volumes:
- name: app-secret-volume
  secret:
    secretName: app-secret
```
volumemapping 시 아래와 같이 조회 가능

`ls /opt/app-secret-volumes`

