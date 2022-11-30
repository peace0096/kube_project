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



## 1 node(s) had untolerated taint {node.kubernetes.io/disk-pressure:
### 에러 내용
mysql deployment를 적용하던 중, pv가 bounding된 상태임에도 불구하고, 배포된 Pod의 상태가 Pending에 빠졌음. ```kubectl describe```를 통해 다음과 같은 문구를 확인할 수 있었음.
<br>

```
Events:
  Type     Reason            Age   From               Message
  ----     ------            ----  ----               -------
  Warning  FailedScheduling  13s   default-scheduler  0/2 nodes are available: 1 node(s) had untolerated taint {node.kubernetes.io/disk-pressure: }, 1 node(s) had volume node affinity conflict. preemption: 0/2 nodes are available: 2 Preemption is not helpful for scheduling.
```

### 해결방법

주목해야할 것은 ```node.kubernetes.io/disk-pressure:```인데, 디스크 용량이 부족하다는 뜻이다. mysql의 spec을 보면 최소 10G의 pv를 붙여야 하는데 배포된 pv가 10G를 차지하기엔 서버의 용량이 부족하다. 따라서, 이건 디스크 용량 정리가 필요한 부분이다.

<br>

## Pv's status fall in stuck, "Terminating"
### 에러내용
pv 오브젝트를 제거한 뒤, Terminating 상태에 빠지고 삭제가 되지 않는다.

```
NAME         CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS        CLAIM                     STORAGECLASS    REASON   AGE
pv-volumn2   10Gi       RWO            Retain           Terminating   kubeflow/katib-mysql      local-storage            44h
pv-volumn3   20Gi       RWO            Retain           Terminating   kubeflow/minio-pvc        local-storage            44h
pv-volumn4   20Gi       RWO            Retain           Terminating   kubeflow/mysql-pv-claim   local-storage            44h
```
<br>

### 해결방법
강제로 삭제한 뒤, patch를 통해 pv를 완전히 종료시켜야 한다.
```
kubectl delete pv <pv_name> --grace-period=0 --force
kubectl patch pv <pv_name> -p '{"metadata": {"finalizers": null}}'
```


<br>

## Error opening bolt store: open /var/lib/authservice/data.db: permission denied

### 에러내용
Kubeflow 오브젝트 중 하나인 authservice pod의 상태가 ```CrashLoopBackOff```에 빠졌음. 해당 pod의 로그를 확인해봤음.

```
kubectl logs -f authservice-0 -n istio-system
time="2022-11-30T01:39:09Z" level=info msg="Starting readiness probe at 8081"
time="2022-11-30T01:39:09Z" level=info msg="No  USERID_TOKEN_HEADER  specified, using 'kubeflow-userid-token' as default."
time="2022-11-30T01:39:09Z" level=info msg="No  SERVER_HOSTNAME  specified, using '' as default."
time="2022-11-30T01:39:09Z" level=info msg="No  SERVER_PORT  specified, using '8080' as default."
time="2022-11-30T01:39:09Z" level=info msg="No  SESSION_MAX_AGE  specified, using '86400' as default."
time="2022-11-30T01:39:09Z" level=info msg="Starting web server at :8080"
time="2022-11-30T01:39:09Z" level=fatal msg="Error opening bolt store: open /var/lib/authservice/data.db: permission denied"
```

주목해야할 것은 ```open /var/lib/authservice/data.db: permission denied"``` 이부분인데, 접근하려는 db 파일이 관리자권한으로 되어있어서 접근거부가 되고 있다.

### 해결방법
authservice statefulset의 내용을 수정해야한다. 컨테이너가 배포되었을 때 ```chmod -R 777```을 통해 db파일의 접근권한을 유저까지 접근할 수 있도록 수정한다.
우선, 아래처럼 authservice의 yaml로 접근한다.
```
kubectl edit statefulset -n istio-system authservice
```

컨테이너 배포가 되는 동시에 권한을 수정해야하기 때문에 ```spec.template.spec.initContainers```을 아래처럼 추가한다.
```
initContainers:
  - name: modifiy-permission
    image: busybox
    command: ['sh', '-c']
    args: ['chmod -R 777 /var/lib/authservice;']
    volumeMounts:
    - mountPath: /var/lib/authservice
      name: data
```

statefulset의 내용을 지워도 내용이 반영되지 않기 때문에 pod를 지워야 한다(어차피 deployment에 의해 자동으로 다시 배포된다)

그러면 아래와 같이 pod의 상태가 Running으로 바뀐다.
```
NAMESPACE                   NAME                                                     READY   STATUS             RESTARTS         AGE
istio-system                authservice-0                                            1/1     Running            0
```