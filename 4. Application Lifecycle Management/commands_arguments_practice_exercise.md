#

What is the command used to run the pod 'ubuntu-sleeper'?

```shell script
# kubectl describe로 command 확인
kubectl describe pod ubuntu-sleeper
Name:         ubuntu-sleeper
Namespace:    default
Priority:     0
Node:         node01/172.17.0.56
Start Time:   Mon, 11 May 2020 13:00:36 +0000
...
Containers:
  ubuntu:
    Container ID:  docker://0bbdc1760698fcf4a9b61f996170d7f77dd76731f3464ebe1110972385f6fcee
    Image:         ubuntu
    Image ID:      docker-pullable://ubuntu@sha256:747d2dbbaaee995098c9792d99bd333c6783ce56150d1b11e333bbceed5c54d7
    Port:          <none>
    Host Port:     <none>
    Command:   ###################
      sleep
      4800
...
```

Create a pod with the ubuntu image to run a container to sleep for 5000 seconds. Modify the file ubuntu-sleeper-2.yaml.

```yaml
# yaml 파일 수정 
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    commands:
    -  sleep
    -  "5000"
```

Create a pod using the file named 'ubuntu-sleeper-3.yaml'. There is something wrong with it. Try to fix it!

```shell script
# 실행해서 문제 확인. (wrong command. Missing quotation marks)
master $ kubectl apply -f ubuntu-sleeper-3.yaml
Error from server (BadRequest): error when creating "ubuntu-sleeper-3.yaml": 
Pod in version "v1" cannot be handled as a Pod: v1.Pod.Spec: v1.PodSpec.Containers: 
[]v1.Container: v1.Container.Command: []string: 
ReadString: expects " or n, but found 1, error found in #10 byte of ...|["sleep",1200],"imag|..., bigger context ...|ault"},
"spec":{"containers":[{"command":["sleep",1200],"image":"ubuntu","name":"ubuntu"}]}};

```

Update pod 'ubuntu-sleeper-3' to sleep for 2000 seconds.
Note: Only make the necessary changes. Do not modify the name of the pod. Delete and recreate the pod if necessary.

```shell script
# 실행 중인 pod의 command는 수정 불가
# yaml 수정 후 kubectl delete / kubectl apply 실행. 
kubectl edit pod ubuntu-sleeper-3
```

Inspect the file 'Dockerfile' given at /root/webapp-color. What command is run at container startup?

```Dockerfile
ENTRYPOINT ["python", "app.py"]
```

Inspect the two files under directory 'webapp-color-2'. What command is run at container startup?


```Dockerfile

FROM python:3.6-alpine

RUN pip install flask

COPY . /opt/

EXPOSE 8080

WORKDIR /opt

ENTRYPOINT ["python", "app.py"]

CMD ["--color", "red"]

# 실행되는 command는 python app.py --color red
```

```shell script
# yaml 파일에는 entrypoint를 overwrite하게 되어 있음
spec:
  containers:
  - name: simple-webapp
    image: kodekloud/webapp-color
    command: ["--color","green"]
```


그럼 web-app-3 는?

```shell script
spec:
  containers:
  - name: simple-webapp
    image: kodekloud/webapp-color
    command: ["python", "app.py"] # entry
    args: ["--color", "pink"] # command

    # 정답은 python app.py --color pink
```

Create a pod with the given specifications. 
By default it displays a 'blue' background. Set the given command line arguments to change it to 'green'

Pod Name: webapp-green
Image: kodekloud/webapp-color
Command line arguments: --color=green

```shell script
# defintion 파일 생성
master $ kubectl run webapp-green --generator=run-pod/v1 --image=kodekloud/webapp-color --dry-run -o yaml > green.yaml


# Spec밑에 args 추가
spec:
  containers:
  - image: kodekloud/webapp-color
    name: webapp-green
    args:
    - "--color"
    - "green"

```




