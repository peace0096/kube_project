# Helm 설치 (매우 간단)
---

## Helm 파일 다운로드

1) https://github.com/helm/helm/releases 에서 사양에 맞는 파일 다운로드

2) 압축해제 후 파일 이동
```
tar -xvzf helm-v3.10.1-linux-amd64.tar.gz
mv linux-amd64/helm /usr/local/bin/helm
```

3) 설치 확인
```
# helm version
version.BuildInfo{Version:"v3.10.1", GitCommit:"9f88ccb6aee40b9a0535fcc7efea6055e1ef72c9", GitTreeState:"clean", GoVersion:"go1.18.7"}
```

helm upgrade --cleanup-on-fail \
  --install jupyterhub-test jupyterhub/jupyterhub \
  --namespace jupyter \
  --create-namespace \
  --version=2.0.0 \
  --values config.yaml