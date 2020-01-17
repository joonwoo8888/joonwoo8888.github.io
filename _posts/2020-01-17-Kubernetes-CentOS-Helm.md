---
layout: post
title:  "[Tech] 쿠버네티스 Helm 사용해보기"
subtitle:   "[Tech] 쿠버네티스 Helm 사용해보기"
categories: Tech
tags: Kubernetes
---


# Kubernetes Helm 사용해보기

### 소개

Helm은 Kubernetes 애플리케이션을 설치하고 수명 주기를 관리하는 오픈 소스 패키징 도구입니다. APT, Yum 등의 Linux 패키지 관리자와 마찬가지로 Helm은 사전 구성된 Kubernetes 리소스의 패키지인 Kubernetes 차트를 관리하는 데 사용합니다.

### Helm 구조

![/assets/img/2020-01-17-Kubernetes-CentOS-Helm/Untitled.png](/assets/img/2020-01-17-Kubernetes-CentOS-Helm/Untitled.png)

용어 설명

- helm client: 사용자를 위한 CLI Client
- tiller Server: 쿠버네티스와 API 통신을하는 서버입니다.
- chart: 쿠버네티스 어플리케이션을 구성하는 정보들의 몪음입니다.
- release: 특정 컨피그를 이용해서 실행중인 차트의 인스턴스 입니다.

# helm 설치

    curl https://raw.githubusercontent.com/helm/helm/master/scripts/get > get_helm.sh
    chmod 700 get_helm.sh
    ./get_helm.sh
    
    helm version

### 공식 Chart Repository 등록

    helm repo add stable https://kubernetes-charts.storage.googleapis.com/
    
    helm repo update
    
    helm search repo stable
    
    사용가능한 목록이 조회됩니다.

### Tiller Server POD 배포

    kubectl create serviceaccount tiller --namespace kube-system
    kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller

    helm init --service-account=tiller
    
    kubectl get pod -n kube-system
    # tiller-deploy pod 상태가 Running인지 확인한다.

### 기본 사용법

    # 차트 검색
    helm search [empty or name]
    
    # 차트 상세보기
    helm inspect stable/mariadb
    
    # 차트로 mariadb 설치
    helm install stable/mariadb
    * 임의 이름으로 release가 override 하게됩니다. 
    * --name 플래그를 주면 다른 release로 설치됩니다.
    
    # 설치한 release 목록 조회
    helm list
    
    # release의 상태조회
    helm status nosy-indri
    
    # 설치한 release 삭제
    helm delete nosy-indri
    

참고

- [https://helm.sh/docs/intro/install/](https://helm.sh/docs/intro/install/)