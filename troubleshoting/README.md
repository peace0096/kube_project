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


## :6443 connection refused

에러원인
kube proxy가 swap 메모리를 차지하여 발생한 문제라고 알려져있지만, 이미 swap 메모리가 비활성화된 상태이다. 

해결방법
### kubeconfig를 worker node에 전달
```
scp $HOME/.kube/config root@192.168.50.254:/root/.kube/
```