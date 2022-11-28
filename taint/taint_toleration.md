# Taint & toleration

<br>

### Taint
<br>
Taint가 설정된 노드에는 일반적인 pod는 배포될 수 없다. taint에 걸린 node에 pod를 배포하려면 taint를 해제하거나 toleration을 설정하여 배포해야 한다.

### 형식


```
kubectl taint node {nodename} {key}={value}:{taintname}
```

### Toleration
<br>
Taint가 적용된 node에 pod가 배포할 수 있다.