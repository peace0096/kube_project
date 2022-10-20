# 쿠버네티스 공부용 Markdown
## - 환경설정편

환경설정
1.gcloud sdk 설치
2.kubectl 설치
3.GKE 클러스터 생성(스탠다드)
<br>
GCP 접속
```
gcloud container clusters get-credentials kbw-cluster --zone [region] --project [project-name]
```
<br>
클러스터 정보 확인
```
kubectl config view
```
<br>
클러스터 스위칭
```
kubectl config use-context [cluster-name]
```
<br>
네임스페이스 생성
```
kubectl create namespace [namespace]
```