# Config 오브젝트
.env 파일처럼, 환경변수를 담고 있는 오브젝트
<br>

####파일기반 configmap 생성
```
kubectl create configmap [name] -n [namespace] --from-file=[filepath]
```
예시
```
$ kubectl create configmap port-config -n order --from-file=configs/ORDER_HTTP_PORT
configmap/port-config created
```
<br>
####configmap 확인
```
kubectl get configmap [name] -n [namespace]
```
예시
```
$ kubectl get configmap port-config -n order
NAME          DATA   AGE
port-config   1      82s
```