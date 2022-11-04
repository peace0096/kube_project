# TLS Bootstrapping

## 클러스터 생성

### flannel
```
kubeadm init --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=172.30.1.77
```

### calico
```
kubeadm init --pod-network-cidr=192.168.0.0/16 --apiserver-advertise-address=192.168.50.133
```


kubeadm init --apiserver-advertise-address=172.30.1.38


### CNI

#### weave net

```
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml

kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```
