# Deployment
Pod와 ReplicaSet에 대해 정의하고 배포 전략을 선언할 수 있는 오브젝트

<br>

#### deployment_order.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:           # 오브젝트 정의
  name: order-1.0
  namespace: order
spec:
  selector:         # 대상 Pod에 대한 설명 정의
    matchLabels:
      app: order
      version: "1.0"
  template:
    metadata:
      labels:
        app: order
        version: "1.0"
    spec:
      containers:
      - name: order
        image: yoonjeong/snackbar-order:1.0
        resources:
          requests:
            memory: "32Mi"
            cpu: "100m"
          limits:
            memory: "128Mi"
            cpu: "500m"
        ports:
        - containerPort: 8080
        env:           # 환경변수 가져오기(config 참조)
          - name: PORT
            valueFrom:
              configMapKeyRef:
                key: ORDER_HTTP_PORT
                name: port-config
```

<br>

#### apply 결과
```
$ kubectl get deployment -n order
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
order-1.0   1/1     1            1           25s
```