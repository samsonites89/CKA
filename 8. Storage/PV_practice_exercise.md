# PV Practice Exercises

The application stores logs at location /log/app.log. View the logs.

```bash

master $ kubectl exec webapp -- tail -f /log/app.log
[2020-05-17 07:39:25,012] INFO in event-simulator: USER4 logged out
[2020-05-17 07:39:26,013] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2020-05-17 07:39:26,013] INFO in event-simulator: USER3 is viewing page3
[2020-05-17 07:39:27,014] INFO in event-simulator: USER3 is viewing page2
[2020-05-17 07:39:28,016] WARNING in event-simulator: USER7 Order failed as the item is OUT OF STOCK.
[2020-05-17 07:39:28,016] INFO in event-simulator: USER4 logged in
[2020-05-17 07:39:29,017] INFO in event-simulator: USER3 is viewing page3
[2020-05-17 07:39:30,018] INFO in event-simulator: USER1 is viewing page3
[2020-05-17 07:39:31,020] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2020-05-17 07:39:31,020] INFO in event-simulator: USER3 is viewing page2
[2020-05-17 07:39:32,021] INFO in event-simulator: USER1 is viewing page2


```


Configure a volume to store these logs at /var/log/webapp on the host
Use the spec given on the right.

Name: webapp
Image Name: kodekloud/event-simulator
Volume HostPath: /var/log/webapp
Volume Mount: /log

아래와 같이 수정
```yaml
- mountPath: /log
  name: webapp
  readOnly: true
...
volumes:
- name: default-token-mczkd
  secret:
    defaultMode: 420
    secretName: default-token-mczkd
- name: webapp
  hostPath:
    path: /var/log/webapp

```

Create a 'Persistent Volume' with the given specification.

Volume Name: pv-log
Storage: 100Mi
Access modes: ReadWriteMany
Host Path: /pv/log


```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-log
spec:
  accessModes:
    - ReadWriteMany
  capacity:
    storage: 100Mi
  hostPath:
    path: /pv/log

```

Let us claim some of that storage for our application.
Create a 'Persistent Volume Claim' with the given specification.

Volume Name: claim-log-1
Storage Request: 50Mi
Access modes: ReadWriteOnce

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: claim-log-1
spec:
  accessModes: ReadWriteOnce #AccesMode가 달라서 Claim이 안될것임
  resources:
    requests:
      storage: 50Mi


```


Update the webapp pod to use the persistent volume claim as its storage.
Replace hostPath configured earlier with the newly created PersistentVolumeClaim
