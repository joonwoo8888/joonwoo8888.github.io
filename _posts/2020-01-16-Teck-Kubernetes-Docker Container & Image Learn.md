---
layout: post
title:  "[Tech] 도커 컨테이너&이미지 경로 알아보기"
subtitle:   "[Tech] 도커 컨테이너&이미지 경로 알아보기"
categories: Tech
tags: Kubernetes
---

# 도커 컨테이너&이미지 경로 알아보기

도커의 이미지에 대해 이야기해보려합니다.

도커의 이미지는 기본적으로 docker hub에서 pull 받아 사용하지만 직접 빌드해서 이미지를 생성할수도 있습니다.

이런 이미지는 어떻게 저장되며 관리되는지 확인해보겠습니다.

Centos에서 yum으로 docker를 설치하게 되면  도커홈의 경로는 /var/lib/docker 입니다.

어떤 파일들이 있는지 확인해보겠습니다.

![/assets/img/2020-01-16-Teck-Kubernetes-Docker%20Container%20&%20Image%20Learn/Untitled.png](/assets/img/2020-01-16-Teck-Kubernetes-Docker%20Container%20&%20Image%20Learn/Untitled.png)

containers: 도커 컨테이너의 정보를 관리

image: 로컬 저장소를 관리

swarm: 비어있음.

containers :컨테이너 아이디의 디렉토리로 구분되어 있는걸 알수있습니다.

![/assets/img/2020-01-16-Teck-Kubernetes-Docker%20Container%20&%20Image%20Learn/Untitled%201.png](/assets/img/2020-01-16-Teck-Kubernetes-Docker%20Container%20&%20Image%20Learn/Untitled%201.png)

컨테이너를 조회하니 구성 파일과 마운트 디렉토리가 보입니다.

![/assets/img/2020-01-16-Teck-Kubernetes-Docker%20Container%20&%20Image%20Learn/Untitled%202.png](/assets/img/2020-01-16-Teck-Kubernetes-Docker%20Container%20&%20Image%20Learn/Untitled%202.png)

/var/lib/docker은 도커의 컨테이너&이미지&볼륨등 모든 정보를 가지고 있습니다.

사용을 많이하게 되면 그만큼 자원도 많이 사용하게 되기 때문에 운영으로 사용할때는 위치를 변경해서 사용하는게 좋을거 같습니다.

centos 7 기준 

ExecStart 명령어 뒤에 -g 옵션으로 경로를 변경할수 있습니다.

    vi /usr/lib/systemd/system/docker.service
    
    [Unit]
    Description=Docker Application Container Engine
    Documentation=https://docs.docker.com
    BindsTo=containerd.service
    After=network-online.target firewalld.service containerd.service
    Wants=network-online.target
    Requires=docker.socket
    
    [Service]
    Type=notify
    # the default is not to use systemd for cgroups because the delegate issues still
    # exists and systemd currently does not support the cgroup feature set required
    # for containers run by docker
    ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock -g <변경할 경로>
    ExecReload=/bin/kill -s HUP $MAINPID
    TimeoutSec=0
    RestartSec=2
    Restart=always
    
    # Note that StartLimit* options were moved from "Service" to "Unit" in systemd 229.
    # Both the old, and new location are accepted by systemd 229 and up, so using the old location
    # to make them work for either version of systemd.
    StartLimitBurst=3
    
    # Note that StartLimitInterval was renamed to StartLimitIntervalSec in systemd 230.
    # Both the old, and new name are accepted by systemd 230 and up, so using the old name to make
    # this option work for either version of systemd.
    StartLimitInterval=60s
    
    # Having non-zero Limit*s causes performance problems due to accounting overhead
    # in the kernel. We recommend using cgroups to do container-local accounting.
    LimitNOFILE=infinity
    LimitNPROC=infinity
    LimitCORE=infinity
    
    # Comment TasksMax if your systemd version does not support it.
    # Only systemd 226 and above support this option.
    TasksMax=infinity
    
    # set delegate yes so that systemd does not reset the cgroups of docker containers
    Delegate=yes
    
    # kill only the docker process, not all processes in the cgroup
    KillMode=process
    
    [Install]
    WantedBy=multi-user.target

적용하기

    systemctl restart docker