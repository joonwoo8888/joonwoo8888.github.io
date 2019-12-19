# OpenShift (OKD) vs Rancher 구축및 비교(2/3)

## Rancher 란?
2014년 Rancher 개발이 시작되어 컨테이너 관리, 보안 및 오케스트레이션 관리하는 플랫폼입니다. k8s 클러스터의 중앙 집중식 관리를 합니다. 

## Rancher 지원 소프트웨어
 - RKE 
   - Kubernestes 커뮤니티의 일반적인 설치 복잡성 문제및 운영 단순화되고 쉽게 자동화할수 있습니다. 운영 체제 및 플랫폼과 완전히 독립적입니다.
 - k3s
   - 쿠버네이티스의 경량버전입니다.
 - RIO
   - 빠르게 구축, 테스트, 배포, 확장 및 버전화를 할 수 있습니다. 자동 DNS 및 HTTPS, 로드밸런싱, Auto Scale 등을 지원합니다.


## 설치

### 테스트사양

```
 OS: CentOS Linux Relases 7.7.1908 (Core)
 RAN: 16G
```
### 준비

테스트에선 방화벽을 사용하지 않겠습니다.
```
systemctl stop firewalld

systemctl disable firewalld
```

### Rancher 설치

Rancher는 Container에서 동작하기때문에 아래 명령어를 입력하면 이미지를 받아 바로 실행됩니다.
```
docker run -d --restart=unless-stopped \
-p 80:80 -p 443:443 \
rancher/rancher:v2.3-head
```

### 화면

1. )
