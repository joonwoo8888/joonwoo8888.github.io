---
layout: post
title:  "[Tech] Windows Hyper-V 고정 아이피 사용해보기"
subtitle:   "[Tech] Windows Hyper-V 고정 아이피 사용해보기"
categories: Tech
tags: Windows
---

# Hyper-V static IP Address

### 소개

Hyper-V를 처음 설치하게 되면 Default Switch 가상의 스위치가 생성이되는데 네트워크망이 바뀔때 VM의 아이피도 같이 바뀌게 됩니다. 단일 Guest OS에서 작업을 하게 되면 문제가 없지만 클러스터나 IP주소가 바뀌면 안되는 상황에서 네트워크 망이 바뀔때 마다 IP를 변경하는게 번거롭고 장애가 발생할수있습니다. 그래서 고정된 IP 할당하는 방법을 블로그 작성합니다.

---

### 사전 준비

Guest 환경 

 - Hyper-V (다른 VM 가용 가능)

---

### 구성방법

가상 Switch, Nat을 만들어 가상 네트워크망에 VM 고정IP로 접속하는방식입니다.

![/assets/img/2020-01-17-Teck-Windows-Hyper-V-StaticIP-Network/Untitled.png](/assets/img/2020-01-17-Teck-Windows-Hyper-V-StaticIP-Network/Untitled.png)

1.관리자 권한으로 Powershell을 시작합니다.

2. 내부 스위치 생성

    - New-VMSwitch -SwitchName "SwitchName" -SwitchType Internal

    New-VMSwitch -SwitchName "InternalSwitch" -SwitchType Internal

 3. 만든 내부 스위치의 ifIndex 확인

     - Get-NetAdapter 

    PS) Get-NetAdapter
    Name                  InterfaceDescription               ifIndex Status       MacAddress           LinkSpeed
    ----                  --------------------               ------- ------       ----------           ---------
    InternalSwitch        Hyper-V Virtual Ethernet Adapter        24 Up           00-15-5D-00-6A-01      10 Gbps
    Wi-Fi                 Marvell AVASTAR Wireless-AC Net...      18 Up           98-5F-D3-34-0C-D3     300 Mbps
    Bluetooth Network ... Bluetooth Device (Personal Area...      21 Disconnected 98-5F-D3-34-0C-D4       3 Mbps

 4. 가상 게이트웨이를 생성

     - New-NetIPAddress -IPAddress <NAT Gateway IP> -PrefixLength <NAT Subnet Prefix Length> -InterfaceIndex <ifIndex>

IPAddress: 대역 IP를 입력

PrefixLength: 서브넷 접두사 0 ~ 32 사이 정수 값

                      0은 전체, 32는 매핑된 IP하나만 허용

                      * 일반적으로 24~12사이로 입력

InterfaceIndex: 아까 확인한 ifIndex 정수값을 입력

    New-NetIPAddress -IPAddress 172.16.0.1 -PrefixLength 12 -InterfaceIndex 24

5. 가상 NAT 네트워크를 구성

    - New-NetNat -Name <NATOutsideName> -InternalIPInterfaceAddressPrefix <NAT subnet prefix>

    New-NetNat -Name InternalSwitch -InternalIPInterfaceAddressPrefix 172.16.0.0/12

이것으로 네트워크 구성은 끝났습니다.

6. 마무리

Hyper-V에서 VM의 네트워크를 방금 만든 네트워로 접속하도록 수정하면됩니다.

고정 아이피로 변경하고 172.16.0.0 대역의 아이피를 설정하고 서브넷을 설정하면 인터넷에 접속됩니다.

주의! 그리고 게스트 OS 네트워크가 자동할당으로 선택되어 있으면 인터넷이 안될수있습니다.

참고링크

- [https://docs.microsoft.com/ko-kr/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v](https://docs.microsoft.com/ko-kr/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)
- [https://docs.microsoft.com/ko-kr/virtualization/hyper-v-on-windows/user-guide/setup-nat-network](https://docs.microsoft.com/ko-kr/virtualization/hyper-v-on-windows/user-guide/setup-nat-network)