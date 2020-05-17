## Reference Notes

> 속도는 핵심

CKA 시험은 `CLI` 환경에서 제공된다. `vim`, `nano`가 익숙하지 않으면 *yaml* 파일을 생성, 수정하는게 어려울 수도 있다.

따라서, `vim` 혹은 `nano` 사용법을 필히 숙지하는 것을 물론,

간단한 kubernetes 리소스 생성을 할때는 `kubectl run` 을 활용하는 방법도 도움이 된다.

[kubernetes conventions (Bookmark!)](https://kubernetes.io/docs/reference/kubectl/conventions/)

### `kubectl run` 활용 방안

#### kubernetes 리소스 생성

`kubectl run`을 활용하면 *yaml* 파일 없이도 리소스를 생성할 수 있다.

```bash
# nginx pod 생성
kubectl run --generator=run-pod/v1 nginx --image=nginx
```
```bash
# nginx deployment 생성
kubectl create deployment --image=nginx nginx
```

#### definition yaml 생성

- **-o yaml** : kubernetes 리소스를 생성할 때 `-o yaml` 옵션을 활용하면 생성된 리소스에 대한 *yaml* 을 출력해준다.
해당 *yaml*을 `> filename.yaml` 로 template 생성, 해당 파일을 수정하면 더 수월하게 작업을 할 수 있다. 

- **--dry-run** : `kubectl run` 혹은 `create` 할시 `--dry-run` 옵션을 활용하면 실제 리소스는 생성이 안됨 :)

이 두가지 옵션을 활용해서 template을 만들어서 수정 작업을 하면 된다!!

```bash
# deployment yaml 생성
kubectl create deployment --image=nginx nginx --dry-run -o yaml > nginx-deployment.yaml
```


#### 축약어
```yaml
pod : po
replicationcontroller : rc
replicaset : rs
deployment : deploy
namespace : ns
service : svc
certificatesigningrequest : csr
ingress : ing
networkpolicies : netpol
node: no
persistentvolumeclaim : pvc
persistentvolume : pv
serviceaccount : sa
daemonset : ds

--namespace : -n
--selector : -l
```
