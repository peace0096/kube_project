# 쿠버네티스 환경설정

Ref : https://docs.docker.com/engine/install/ubuntu/

### Set up the repository

## apt패키지 인덱스 업데이트 및 설치

```
sudo apt-get update
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

## Docker GPG키 등록

```
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

## Repository 설정

```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

## 도커설치

### apt패키지 인덱스 업데이트

```
sudo apt-get update
```

### 도커 엔진, containerd, 도커 컴포즈 설치

```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

### 도커 테스트

```
sudo docker run hello-world
```

## 도커 엔진 삭제

### 도커엔진, CLI, containerd, 도커컴포즈 삭제

```
sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

### 디렉토리 삭제

```
sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-compose-plugin
```
<br>

### cri-dockerd 설치

```
git clone https://github.com/Mirantis/cri-dockerd.git
```

### command

```
cd cri-dockerd
mkdir bin
go build -o bin/cri-dockerd
mkdir -p /usr/local/bin
install -o root -g root -m 0755 bin/cri-dockerd /usr/local/bin/cri-dockerd
cp -a packaging/systemd/* /etc/systemd/system
sed -i -e 's,/usr/bin/cri-dockerd,/usr/local/bin/cri-dockerd,' /etc/systemd/system/cri-docker.service
systemctl daemon-reload
systemctl enable cri-docker.service
systemctl enable --now cri-docker.socket
```
---

<br>

## Kubeadm, kubelet, kubectl 설치

 ```kubeadm```:클러스터를 부트스트랩하는 명령어(클러스터링 담당)
```kubelet```:클러스터의 모든 머신에서 실행되는 파드와 컨테이너 시작과 같은 작업을 수행하는 컴포넌트(오브젝트 관리)
```kubectl```:클러스터와 통신하기 위한 커맨드 라인 유틸리티(쿠버네티스 CLI)

<br>

### 1.apt 패키지 인덱스 업데이트 및 설치

```
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
```

### 2.구글 클라우드 공개 사이닝 키를 다운로드

```
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
```

### 3.쿠버네티스 apt 레포지토리 추가

```
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

### apt 패키지 색인을 업데이트 및 쿠버네티스 설치

```
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

### 쿠버네티스 대시보드 설치 및 실행

설치

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.6.0/aio/deploy/recommended.yaml
```

<br>

Svc 형태가 ClusterIP이므로, ```kubectl proxy```를 통해 개방해야한다.


```
kubectl proxy --disable-filter=true --address=0.0.0.0
```