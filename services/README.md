# Service 오브젝트
<br>

## Service란?
현재 실행중인 Pod오브젝트들을 외부로 노출시켜주는 오브젝트
<br>
<br>
<br>


### 1.ClusterIP
Pod들이 클러스터 내부의 다른 오브젝트들과 통신할 수 있도록 하는 오브젝트
오직 내부 클러스터에서만 접근 가능. 외부에 노출 X
<br>
<br>

### 2.NodePort
외부에서 노드 IP의 특정 포트로 들어오는 요청을 해당 포트와 연결된 Pod로 트래픽을 전달해주는 오브젝트
<br>
<br>

### 3.LoadBalancer
서비스를 자체 클라우드측의 로드 밸런서를 노출시킨다.

<br>

### 4.ExternelName
Selector 대신 DnsName을 사용할 때 쓰는 오브젝트

---
<br>

서비스는 ```kubectl apply -f [filepath]```로 오브젝트를 올릴 수 있다.

아래 형식처럼, 하나의 yaml파일을 통해 여러 개의 서비스를 한 번에 올릴 수도 있다.

### service_all.Yaml
```
apiVersion: v1
kind: Service
metadata:
  name: delivery-app
  namespace: delivery
spec:
  type: NodePort
  selector:
    app: delivery
  ports:
  - port: 80
    targetPort: 8080

---

apiVersion: v1
kind: Service
metadata:
  name: order-app
  namespace: order
spec:
  selector:
    app: order
  ports:
  - port: 80
    targetPort: 8080

---

apiVersion: v1
kind: Service
metadata:
  name: payment-app
  namespace: payment
spec:
  type: NodePort
  selector:
    app: payment
  ports:
  - port: 80
    targetPort: 8080

```

#### 네임스페이스의 서비스 오브젝트 확인
```
kubectl get svc -n [namespace]
```

<br>

## 현재 서비스 구조
#### payment
```
NAME          TYPE       CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
payment-app   NodePort   10.80.1.12   <none>        80:30891/TCP   13m
```
#### order
```
NAME        TYPE           CLUSTER-IP    EXTERNAL-IP                               PORT(S)   AGE
delivery    ExternalName   <none>        delivery-app.delivery.svc.cluster.local   80/TCP    62s
order-app   ClusterIP      10.80.9.146   <none>                                    80/TCP    11m
payment     ExternalName   <none>        payment-app.payment.svc.cluster.local     80/TCP    62s
```
#### delivery
```
NAME           TYPE       CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
delivery-app   NodePort   10.80.2.169   <none>        80:32527/TCP   13m
```

<br>

#### 엔드포인트 확인
```
kubectl get endpoints -n [namespace]
```

예시
```
$ kubectl get endpoints -n order
NAME        ENDPOINTS   AGE
order-app   <none>      43m
```
