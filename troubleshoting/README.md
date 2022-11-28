## Multi CRI-socket found

#### 에러 내용

```
Found multiple CRI endpoints on the host. Please define which one do you wish to use by setting the 'criSocket' field in the kubeadm configuration file: unix:///var/run/containerd/containerd.sock, unix:///var/run/cri-dockerd.sock
```

#### 원인
cri 소켓이 두 개 이상 감지되었을 때 나타나는 에러이다. containerd.sock, cri-dockerd.sock 중 하나를 선택하여 ```kubeadm init```을 해주면 된다.

<br>

#### 해결방법1
```
kubeadm init --cri-socket /var/run/cri-dockerd.sock
```

#### 해결방법2
```
swapoff -a
```

<br>

## container is not running

에러내용
```
W1125 12:58:32.733485   26426 configset.go:348] WARNING: kubeadm cannot validate component configs for API groups [kubelet.config.k8s.io kubeproxy.config.k8s.io]
[init] Using Kubernetes version: v1.19.4
[preflight] Running pre-flight checks
error execution phase preflight: [preflight] Some fatal errors occurred:
        [ERROR CRI]: container runtime is not running: output: time="2020-11-25T12:58:32Z" level=fatal msg="getting status of runtime failed: rpc error: code = Unimplemented desc = unknown service runtime.v1alpha2.RuntimeService"
, error: exit status 1
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
To see the stack trace of this error execute with --v=5 or higher
```

<br>

해결방법
```
sudo rm /etc/containerd/config.toml
sudo systemctl restart containerd
sudo kubeadm init
```


<br>

## nodelocaldns status is crashloopbackoff

에러내용
```
NAMESPACE     NAME                              READY   STATUS             RESTARTS         AGE
kube-system   calico-node-6ngpx                 1/1     Running            0                146m
kube-system   calico-node-pczsm                 1/1     Running            0                146m
kube-system   coredns-74d6c5659f-9gbh8          1/1     Running            0                146m
kube-system   coredns-74d6c5659f-rjzzh          1/1     Running            0                146m
kube-system   dns-autoscaler-59b8867c86-6rzg2   1/1     Running            0                146m
kube-system   kube-apiserver-node1              1/1     Running            1                147m
kube-system   kube-controller-manager-node1     1/1     Running            1                147m
kube-system   kube-proxy-mh6r4                  1/1     Running            0                146m
kube-system   kube-proxy-wq5hf                  1/1     Running            0                146m
kube-system   kube-scheduler-node1              1/1     Running            1                147m
kube-system   nginx-proxy-node2                 1/1     Running            9                146m
kube-system   nodelocaldns-5lb7h                0/1     CrashLoopBackOff   33 (2m41s ago)   146m
kube-system   nodelocaldns-vq8c2                1/1     Running            0                146m
```

에러원인

```169.254.25.10```이라는 dns server로 health 체크를 하고 있었다.

```resolv.conf```의 nameserver인데 이를 변경해야한다.


<br>

해결방법

```resolv.conf```의 nameserver가 변경되지 않도록 ```mask``` 작업을 해야 한다.

```systemd-resolved```를 사용하고 있다면 다른 경로에 위치한 ```resolv.conf```의 내용도 동일시해야한다.

아래 명령어를 적용시킨 뒤, 다시 쿠버네티스 설치를 해야한다(```kubeadm init```)

```
sudo systemctl mask systemd-resolved
rm -f /etc/resolv.conf
sudo ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf
```

<br>

그렇다면, 다시 돌아올 것이다.

```
NAMESPACE     NAME                              READY   STATUS    RESTARTS   AGE
kube-system   calico-node-4k22j                 1/1     Running   0          9m18s
kube-system   calico-node-gj8pc                 1/1     Running   0          9m18s
kube-system   coredns-74d6c5659f-2ml2l          1/1     Running   0          9m2s
kube-system   coredns-74d6c5659f-vsvsx          1/1     Running   0          9m
kube-system   dns-autoscaler-59b8867c86-qx27j   1/1     Running   0          9m1s
kube-system   kube-apiserver-node1              1/1     Running   1          10m
kube-system   kube-controller-manager-node1     1/1     Running   1          10m
kube-system   kube-proxy-4hn2b                  1/1     Running   0          9m28s
kube-system   kube-proxy-q62pw                  1/1     Running   0          9m28s
kube-system   kube-scheduler-node1              1/1     Running   1          10m
kube-system   nginx-proxy-node2                 1/1     Running   0          9m31s
kube-system   nodelocaldns-5vm4w                1/1     Running   0          9m1s
kube-system   nodelocaldns-nhldg                1/1     Running   0          9m1s
```


<br>



