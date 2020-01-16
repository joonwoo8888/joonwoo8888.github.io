---
layout: post
title:  "[Tech] 쿠버네티스 apiVersion ??"
subtitle:   "[Tech] 쿠버네티스 apiVersion ??"
categories: Tech
tags: Kubernetes
---

# apiVersion ??

쿠버네티스를 처음 입문하기 시작하면 막막해서지는게 yaml파일 작성방법일거같다. 그중에 첫번째로 apiVersion에 대해 설명해보려 한다.

대부분이 Google, Github, StackOverflw 등에서 제공하는 예제들에서 많이 보이던 apiVersion은 일것이다.
```
    apiVersion: extensions/v1beta1
    apiVersion: apps/v1beta1
    apiVersion: apps/v1beta2
    apiVersion: apps/v1
    apiVersion: v1
    apiVersion: api/v1
```
쿠버네티스 1.16 부터 extensions/v1beta1, apps/v1beta1, apps/v1beta2  버전은 deprecation 되었다.

현재 버전이 1.18도 나온 만큼 버전을 확인하고 사용해야될거같다.

|Kind|apiVersion|
|:---:|:---:|
|CertificateSigningRequest|certificates.k8s.io/v1beta1|
|ClusterRoleBinding|rbac.authorization.k8s.io/v1|
|ComponentStatus|v1|
|ConfigMap|v1|
|ControllerRevision|apps/v1|
|CronJob|v1|
|Deployment|v1|
|Endpoints|v1|
|Event|v1|
|HorizontalPodAutoscaler|autoscaling/v1|
|Ingress|v1|
|Job|v1|
|LimitRange|v1|
|NetworkPolicy|v1|
|Node|v1|
|PersistentVolumeClaim|v1|
|PersistentVolume|v1|
|PodDisruptionBudget|policy/v1beta1|
|Pod|v1|
|PodSecurityPolicy|v1|
|PodTemplate|v1|
|ReplicaSet|v1|
|ReplicationController|v1|
|ResourceQuota|v1|
|RoleBinding|rbac.authorization.k8s.io/v1|
|Role|rbac.authorization.k8s.io/v1|
|Secret|v1|
|ServiceAccount|v1|
|Service|v1|
|StatefulSet|apps/v1|
|Secret|v1|

dprecation 된 apiverison은 v1으로 수정하였다. 

설치된 쿠버네티스 버전을 확인은 아래 명령어를 입력해보면된다.

$ kubectl get node 

![/assets/img/2020-01-05-Tech-Kubernetes-apiVersion/Untitled.png](/assets/img/2020-01-05-Tech-Kubernetes-apiVersion/Untitled.png)

쿠버네티스의 apiVersion은 여러 api 버전을 지원한다. 버전에 alpha, beta 등이 포함되면 없어졌을수도있다.  현재 stable 버전은 v1 이다. 

참고

[https://github.com/kubernetes/community/blob/master/contributors/devel/sig-architecture/api_changes.md#alpha-beta-and-stable-versions](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-architecture/api_changes.md#alpha-beta-and-stable-versions)

[https://matthewpalmer.net/kubernetes-app-developer/articles/kubernetes-apiversion-definition-guide.html](https://matthewpalmer.net/kubernetes-app-developer/articles/kubernetes-apiversion-definition-guide.html)