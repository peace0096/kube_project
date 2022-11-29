# Taint & toleration

<br>

### Taint
<br>
Taint가 설정된 노드에는 일반적인 pod는 배포될 수 없다. taint에 걸린 node에 pod를 배포하려면 taint를 해제하거나 toleration을 설정하여 배포해야 한다.

### 형식


```
kubectl taint node {nodename} {key}={value}:{taintname}
```


### taint name 종류

|이름|설명|
|---|---|
|NoSchedule|포드를 노드에 배치할 수 없음|
|PreferNoSchedule|포드를 노드에 배치할 수 없지만, 항상 보장하지 않음|
|NoExecute|포드가 노드에 배치되지 않거나, 제거한다|


### Toleration
<br>
Taint가 적용된 node에 pod가 배포할 수 있다.

## Ex)마스터 노드에 Pod 배포하기

<br>
우선, 각 노드의 역할을 부여해준다.

```
# master node
kubectl label nodes <node-name> node-role.kubernetes.io/master=

# worker node
kubectl label nodes <node-name> node-role.kubernetes.io/worker=
```

<br>

taint를 통해 Master 노드의 NoScheduled 권한을 없앤다.
```
kubectl label nodes <node-name> node-role.kubernetes.io/master-
```