---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Parallels Server 4 Bare Metal Server 개요
{: #overview-of-parallels-server-4-bare-metal}

Parallels Server Bare Metal은 가상 머신과 컨테이너* 둘 다에 대한 기술을 제공하여 Virtuozzo의 소프트웨어 가상화와 함께 하드웨어 가상화를 제공하는 가상화 솔루션입니다.
{:shortdesc}

**명령행 인터페이스**

일반적인 Virtuozzo 명령과 함께 `pctl`, `pmigrate`, `pstat`, 및 `prl_disk_tool` 등의 다른 명령을 사용할 수 있습니다.

**마이그레이션**

이제 서비스의 통합을 허용하여 Virtuozzo 컨테이너를 가상 머신으로, 실제 서버를 Virtuozzo 컨테이너로, 실제 서버를 가상 머신으로 마이그레이션할 수 있습니다. 또한 이 기능을 사용하면 컨테이너 또는 VM을 Bare Metal Server 간에 마이그레이션하고, 다른 가상화된 환경(V2V)에서 작성된 VM을 변환하고, 템플리트에서 Windows VM을 복제 또는 배치할 때 Windows SID를 변경할 수 있습니다. 

**IP 주소 사용법 및 VLAN**

VM에 있는 Parallels Tools를 사용하여 실제 서버에서 IP에 VM을 지정할 수 있습니다. 또한 내부 VM 네트워크 트래픽을 더 잘 보안하기 위해 PSBM을 통해 가상 스위치 및 VLAN이 VM 내에서 작성되도록 합니다. 

**명령 실행**

사용자 비밀번호 재설정을 포함하여 Virtuozzo Container 및 VM 내에서 직접 실제 서버에서 원시 명령을 실행하십시오(병렬 도구가 VM 내에 설치된 경우). 

**프로세스 계정**

Parallels Server Bare Metal을 사용하면 VM에 신속하게 할당된 리소스의 우선순위(CPU, 디스크 I/O 우선순위)를 늘리거나 줄일 수 있습니다. 

**백업**

Parallels Server Bare Metal은 전체 증분 백업을 포함하도록 로컬 베어 메탈 서버 또는 원격 베어 메탈 서버 중 하나에서 VM 및 컨테이너를 백업 및 복원하는 기능을 제공합니다. 

**네트워크 계정**

Parallels Server Bare Metal을 사용하여 현재 및 히스토리 네트워크 처리량을 기반으로 VM 또는 컨테이너를 쉽게 보고 찾을 수 있습니다.

**네트워크**

Parallels Server Bare Metal은 휴대용 IP 주소를 사용하는 반면, Virtuozzo는 휴대용 또는 정적 IP 주소 중 하나를 사용합니다(구성에 따라 다름).

\* {{site.data.keyword.BluSoftlayer_full}}의 경우 주문 양식에 달리 표시되지 않는 한 Parallels Server 4 Bare Metal의 하드웨어 가상 머신만 라이센스가 부여됩니다.
_VM_ = 가상 머신. _V_ = VPS 또는 컨테이너.
