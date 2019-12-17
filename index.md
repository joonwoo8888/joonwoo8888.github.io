<!-- 1. OpenShift OKD, Rancher -->

# OpenShift (OKD) vs Rancher 구축및 비교


## OpenShift (OKD)란?

OpenShift 는 개발자 및 IT 운영팀을 단일 플랫폼에서 통합하여, 하이브리드 클라우드 및 멀티 클라우드 인프라 전반에서 애플리케이션을 일관되게 구축, 배포 및 관리하도록 지원하는 플랫폼입니다.
OKD는 Kubernetes 위에 개발자 및 운영 중심 도구를 추가 하여 빠른 응용 프로그램 개발, 쉬운 배포 및 확장, 중소 규모 팀의 장기 수명주기 유지 관리를 지원합니다.

### 개발이점

 - Openshift는 개발자에게 셀프 서비스 방식으로 애플리케이션과 컴포넌트를 프로비저닝, 빌드 및 배포하도록 지원하는 최적읜 플랫폼입니다.
 - Source To Image(S2I) 프로세스 같은 자동화된 워크플로우 덕분에 소스 형상 관리의 소스 코드를 즉시 실행 가능한 도커 포멧 컨테이너 이미지로 간단하게 생성할 수 있습니다.
 - Openshift는 CI(Continuios Integration) 와 CD(Continuous Delivery)의 통합 톨을 제공하기 때문에 모든 조직에서 이상적인 솔루션이 될 수 있습니다.
 
### 운영이점

 - Openshift는 IT운영팀에 애플리케이션 빌드 배포 자동화와 정책 기반 권한 지원한는 안전한 엔트프라이즈급 k8s를 제공합니다.
 - 클러스터 서비스, 스케줄링 그리고 오케스트레이션을 통해 부하 분산관 자동 스케일링 기능을 제공합니다. 보안 기능을 통해 테넌트가 다른 에플리케이션이나 기본 호스트 에 지장을 주지 않도록 방지합니다.
 - Openshift는 Persitent Stroage를 Linux 컨테이너에 직접 연결할 수 있기 때문에 IT 조직은 하나의 플랫폼에서 스테이트플(stateful) 및 스테이트리스(stateless) 애플리케이션을 모두 실행할 수 있습니다.

### openshift 종류

- OpenShift Origin (OKD)
  - OpenShift Online, OpenShift Dedicated 및 OpenShift Container Platform에서 사용되는 업스트림 커뮤니티 프로젝트입니다.

- OpenShift Container Platform
  - OpenShift Container Platform은 Red Hat이 제공하고 지원하는 엔터프라이즈 버전입니다.

- OpenShift Online
  - OpenShift Online은 Red Hat의 호스팅형 퍼블릭 PaaS 로서 클라우드에서 애플리케이션 개발, 구축, 배포, 호스팅 솔루션을 제공합니다.

- OpenShift Dedicated
  - OpenShift Dedicated는 퍼블릭 클라우드에서 관리형 싱글 테넌트 OpenShift 환경을 제공합니다. 전체 OpenShift Cluster를 기업 전용 솔루션으로 구축하고 Red Hat을 통해 종합적으로 관리합니다.


## 설치

### 테스트 환경

```
 OS: CentOS Linux Relases 7.7.1908 (Core)
 RAN: 2G
```


### 도커 설치

```
sudo yum update -y

sudo yum install -y yum-utils device-mapper-persistent-data lvm2

sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

sudo yum install -y docker-ce docker-cecli containerd.io

tee /etc/containers/registries.conf<<EOF
{
  [registries.insecure]
  registries = ['172.30.0.0/16']
}
EOF

tee /etc/docker/daemon.json<<EOF
{
   "insecure-registries": [
     "172.30.0.0/16"
   ]
}
EOF
```


### OKD 설치

```
wget https://github.com/openshift/origin/releases/download/v3.11.0/openshift-origin-client-tools-v3.11.0-0cbc58b-linux-64bit.tar.gz

tar xvf openshift-origin-client-tools*.tar.gz

cd openshift-origin-client*/

sudo mv  oc kubectl  /usr/local/bin/

oc version
```


### OKD 실행

```
oc cluster up 
또는
export no_proxy=<ip>
oc cluster up --public-hostname=<ip> --routing-suffix=<ip>


Web Console 접속URL
https://<ip>:8443/console
```


### 참고자료

https://docs.okd.io/latest/getting_started/administrators.html

https://computingforgeeks.com/setup-openshift-origin-local-cluster-on-centos/

https://tech.osci.kr/2019/06/10/78611423/


### 오류대처방법

- github 저장소 접근 오류

```
systemctl stop docker && \
iptables -F && \
iptables -t nat -F && \
systemctl start docker && \
docker ps -a | awk '{print $1}' | xargs docker start
```

## app 빌드&배포등 사용은 Rancher 구축후 비교 예정

