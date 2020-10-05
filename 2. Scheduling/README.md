# Scheduling 관련

## Taints and Tolerance 

#### Taints
`Taints`는 노드에 적용되는 사항으로서 어떤 `pod`이 scheduling 이 될 수 있는지 제한을 한다.
`taint`는 `<key>=<value>` 방식으로 적용할 수 있다. 

예시:
```bash
# kubectl taint node <node-name> <key>=<value>:<Condition>
kubectl taint nodes node1 app=blue:NoSchdule
```

`Taint` 제거 방법
```bash
# 키값 기반으로 삭제 가능
kubectl taint ndoes node1 app-
```


#### Tolerance
`Tolerance`같은 경우 `pod`에 적용되는 사항이며, 특정 노드에 `taint`가 걸려있을 시 
해당 taint에 대한 `tolerance`가 **있어야지만 해당 노드에 배포(scheduling)이 될 수 있다.** 

예시:
```yaml
#definition.yaml
#모든 값은 double quote로 작성해야함!!!!
...
spec:
  tolerations:
  - key: "<key>"
    operator: "<Equal>" #Equal , Exists 
    value: "<value>"
    effect: "NoSchedule" 

```
## [WIP] Node Affinity
`Node Affinity` 같은 경우 특정 노드로 `pod`를 유도(?)하고자 할때 사용되는 방법이다.
Node Affinity 를 설정하기 위해서는 노드에 `label`을 달아줘야 한다.  

- 복잡한 조건설정 가능. 다수 label
- "Soft Setting" 가능 - `preferred`를 사용하면 됨

#### NodeSelector vs. Node Affinity


```yaml
#Node Selector 예시
spec:
  nodeSelector:
    <key>: <value>
```
```yaml
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: kubernetes.io/e2e-az-name
            operator: In # Exists
            values: 
            - e2e-az1
            - e2e-az2
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference: ##
          matchExpressions:
          - key: another-node-label-key
            operator: In
            values:
            - another-node-label-value
```
## [WIP] Resource Limits and Requests

