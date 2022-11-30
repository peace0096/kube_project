# GPU Operator


## 리소스 관리가 힘들다

쿠버네티스는 장치 플러그인 프레임워크를 통해 GPU와 같은 하드웨어 리소스를 관리한다. 하지만, 이러한 하드웨어 리소스로 구성된 노드를 관리하려면 어려울 뿐더러, 오류가 발생하기 쉬운 드라이버, 컨테이너 런타임 또는 기타 라이브러리와 같은 여러 소프트웨어를 구성해야 한다.

<br>

## GPU Operator
GPU Operator는 쿠버네티스 내 오퍼레이터 프레임워크를 통해 GPU 프로비저닝에 필요한 모든 NVIDIA 소프트웨어 구성 요소의 관리를 자동화하는 소프트웨어이다. 여기에 Nvidia 드라이버, GPU용 쿠버네티스 디바이스 플러그인, Nvidia-Container-Toolkit 등이 포함되어 있다.

<br>

![GPU Operator](./images/nvidia-gpu-operator-image6.jpg)
*그림 : Nvidia GPU Operator*

## Getting Start

<br>

Nvidia GPU Operator를 설치하기 전, 확인해야 할 사항들이 있다.

1. ```Docker CE/EE```, ```containerd```, ```cri-i```와 같은 컨테이너 엔진이 설치된 상태여야 한다.
2. ```Helm``` 레포지토리를 통해 설치하므로, 당연히 helm이 설치된 상태여야 한다.

다음과 같이, Nvidia Helm 레포지토리를 추가한다.
```
helm repo add nvidia https://helm.ngc.nvidia.com/nvidia \
   && helm repo update
```
<br>

헬름 차트 커스터마이징 옵션은 이곳에서 확인할 수 있다.

https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/getting-started.html#operator-install-guide

<br>

이제, Nvidia GPU Operator를 설치한다.

```
helm install --wait --generate-name \
     -n gpu-operator --create-namespace \
     nvidia/gpu-operator
```

## Node 설정

기본적으로, GPU Operator는 모든 워커노드에 배포가 된다. ```feature.node.kubernetes.io/pci-10de.present=true```이라는 라벨을 통해 확인되는데, 워커노드에 배포되는 것을 원치 않는다면, 다음과 같은 명령어를 입력하면 된다.

```
kubectl label nodes <node-name> nvidia.com/gpu.deploy.operands=false
```
