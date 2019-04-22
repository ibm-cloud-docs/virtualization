---
copyright:
  years: 2014, 2019
lastupdated: "2019-01-15"

subcollection: virtualization

keywords: vmware, ESXi
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# 고급 단일 사이트 VMware 참조 아키텍처
{: #advanced-single-site-vmware-reference-architecture}

고급 단일 사이트 vSphere 환경을 작성하려면 다음 단계를 사용하십시오. 다음 단계에서는 VMware에서 제안하는 우수 사례를 사용하여 {{site.data.keyword.cloud}} 참조 아키텍처 배치를 정의합니다.

참조 아키텍처는 공유 스토리지, VMware High Availability(HA), vSphere Distributed Resource Scheduler(DRS) 및 {{site.data.keyword.cloud_notm}} 게이트웨이 또는 방화벽을 사용하는 환경을 프로비저닝하기 위한 것입니다. 고급 단일 사이트 VMware 참조 아키텍처는 워크로드로 확장할 수 있고 온프레미스 구현을 대체하거나 하이브리드 IT 시나리오로 확장할 수 있는 대부분의 프로덕션 배치에 적합합니다.

## 환경 개요
{: #environment-overview}

간략하게 설명된 대표적인 고급 VMware 환경은 관리 및 용량이라는 별도의 두 가지 클러스터를 관리하는 하나의 데이터 센터로 구성됩니다. 요구사항에 따라 단일 4노드 클러스터를 사용하여 구성을 설정할 수 있습니다. 관리 클러스터에는 인프라 관리에 사용되는 다음 가상 머신(VM)이 포함됩니다.

* VMware vCenter Server 5.5 or 6.0 어플라이언스
* Microsoft Windows 2012 R2 Standard with Active Directory 및 Domain Name System(DNS) 역할

용량 클러스터에는 VM을 작성 및 실행하는 데 필요한 리소스 및 인프라가 포함되어 있습니다. 네트워킹의 경우, 환경은 외부 통신에 사용되는 세 개의 사설, 내부 VLAN 및 하나의 공용 VLAN으로 구성됩니다. 표 1은 VLAN 유형을 지정하며 VLAN 이름은 환경 전반에 사용됩니다.

|VLAN 유형|VLAN 이름|설명|                                                                             |
|---|---|---|
|기본 사설|관리| 실제 ESXi 호스트 및 가상 서버를 관리하고 이에 액세스하도록 지정됩니다.|
|기본 사설| 스토리지 | 각 ESXi 호스트에 연결된 공유 스토리지를 관리하고 이에 액세스하도록 지정됩니다.|
|기본 사설| VM 액세스 | 각 ESXi 호스트에서 실행되는 가상 머신에 지정됩니다.|
|기본 공용| 공용 | 공용 네트워크에서 액세스를 필요로 하는 가상 머신 또는 다른 디바이스에 지정됩니다.|
<caption>표 1. 기본 VLAN</caption>

공유 스토리지의 경우, OS Nexus QuantaStor, 단일 테넌트 공유 스토리지 서버 또는 {{site.data.keyword.cloud_notm}} Endurance 또는 Performance 스토리지 서비스를 사용할 수 있습니다. 어느 경우에나 공유 스토리지 디바이스는 VM을 관리 및 용량 클러스터 모두에 저장하는 데 사용됩니다. 스토리지 옵션에 대한 자세한 정보는 [{{site.data.keyword.cloud_notm}} 스토리지 솔루션 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://www.ibm.com/cloud/storage){: new_window}의 내용을 참조하십시오.

스토리지 환경은 NFS 볼륨을 지원하도록 구성되어 있습니다.

## 1단계: 기본 공용 및 사설 VLAN 순서 지정
{: #step-1-ordering-primary-public-and-private-vlans}

**참고:** VLAN 주문은 검토 및 승인을 받아야 합니다. 자동 승인되도록 VLAN 주문을 플래깅하는 특정 조건 또는 요구사항은 없습니다. VLAN 주문은 다양한 이유로 거부될 수 있습니다.

이 단계에서는 각각 관리용, 스토리지용, VM용, 공용 액세스용으로 4개의 VLAN이 작성됩니다. 서버를 주문하기 전에 이러한 4개의 VLAN을 작성하는 것이 좋습니다. 먼저 VLAN을 주문하면 서버가 올바른 VLAN과 올바른 데이터 센터에 배치되도록 보장합니다.

이 환경은 다섯 개의 ESXi 호스트(두 개는 관리 클러스터용, 세 개는 용량 클러스터용)과 하나의 가상 서버로 구성됩니다. 사설 관리 VLAN은 이를 지원하기 위한 16개의 IP 주소가 있는 서브넷으로 구성되어 있습니다. 스토리지 및 VM 트래픽을 위한 기본 사설 VLAN은 환경에 단일 스토리지 서버가 포함되어 있고 VM이 휴대용 서브넷을 사용하므로 8개의 주소로 구성되어 있습니다. 관리 네트워크에 더 큰 서브넷 범위가 필요한 경우, ESXi 호스트의 수를 계산하고 2씩 곱하여 범위를 조정하십시오. 또한 이러한 VLAN이 작성되는 데이터 센터를 지정하도록 하십시오. 

{{site.data.keyword.cloud_notm}}에 로그인한 후에 **지원 > 티켓 추가**를 선택하여 관리 및 가상 머신 VLAN을 위한 지원 티켓을 여십시오. **표 2:**에 있는 정보로 필드를 채우십시오.

|필드|값|
|---|---|
|주제|사설 네트워크 질문|
|제목|VLAN 주문|
|세부사항|3개의 기본 사설 VLAN 및 1개의 공용 VLAN을 프로비저닝하십시오. 각 VLAN에 대한 다음 주소 지정 체계를 연관시키십시오. <br/><ul><li>기본 사설 VLAN에 대한 1x18 연결(16개 주소)</li><li>기본 사설 VLAN에 대한 2x29 연결(8개 주소)</li><li>기본 공용 VLAN에 대한 1x29 연결(8개 주소)</li></ul>|
|이러한 추가 VLAN이 필요한 이유|VMware 환경에 대한 다른 네트워크에 호스트, 스토리지 및 VM을 배치하기 위해서입니다.
|공용 및 사설 VLAN 필요 여부|사설 및 공용|
|필요한 VLAN 수|사설 = 3, 공용 = 1|
|필요한 IP 주소 수|각 VLAN의 경우 8 - 16|
|VLAN의 뒤에 두어야 하는 라우터|VLAN이 모두 동일한 팟(Pod)에 있는 것은 중요하지 않습니다.|
|VLAN에 두어야 하는 팟(Pod)|모두 <DATA CENTER NAME>의 동일한 팟(Pod)에 있어야 합니다.|
<caption>표 2. 티켓 정보 지원</caption>

VLAN이 프로비저닝되고 나면 VLAN 번호, 서브넷 범위 및 게이트웨이를 기록해 두고 이를 논리적 vSphere 네트워크에 지정하십시오. 부록 A: VLAN 워크시트에 있는 워크시트를 사용하여 VLAN 및 연관된 정보를 기록할 수 있습니다. 예를 들어, 다음과 같습니다.

|VLAN 유형|VLAN 번호|IP 범위|게이트웨이|용도|
|---|---|---|---|---|
|기본 사설|1101|10.X.Y.Z/28|10.X.Y.1|관리|
|기본 사설|1102|10.A.B.C/29|10.A.B.1|스토리지|
|기본 사설|1103|10.Q.R.S/29|10.Q.R.1|가상 머신|
|기본 공용|2101|75.S.T.U/29|75.S.T.1|공용 액세스|
<caption>표 3. 기본 VLAN 샘플</caption>

**참고:** VLAN이 프로비저닝될 때까지 다음 단계로 진행하지 마십시오.

## 2단계: 휴대용 사설 IP 주소 주문
{: #step-2-ordering-portable-private-ip-addresses}

2단계에서는 용량 클러스터에서 VM, VM 커널 스토리지 액세스 및 VM에 대한 휴대용 사설 서브넷을 작성하기 위한 요청이 작성됩니다. 사용자는 각 VLAN 서브넷에 필요한 주소의 수를 판별해야 합니다. 대표적인 환경에서 다음 주소의 순서를 지정하십시오.

* 관리 VLAN - 1x8 주소 /29 – vCenter Appliance용으로 주소 하나, DNS 및 Active Directory용으로 주소 하나.
* 스토리지 VLAN - 1x16 주소 28 – 공유 스토리지 디바이스에 액세스하기 위해 다른 서브넷을 사용하여 각 ESXi 호스트에 있는 두 개의 VM 커널 포트 및 스토리지용 동일한 VLAN에 두 개의 서브넷을 작성하십시오.
* VM VLAN - 1x32 주소 /27 – 이러한 주소는 용량 클러스터에서 VM에 IP 주소를 지정하는 데 사용됩니다.

양을 주문할 때는 다음 30일과 6개월 내에 필요한 IP 주소의 수를 고려해야 합니다. 또는 {{site.data.keyword.cloud_notm}}에서 휴대용 서브넷 블록당 3 - 4개의 IP 주소를 예약하는 것이 중요합니다. 따라서 팟(Pod)이 Hot Standby Router Protocol을 지원하는 경우 4개의 IP 주소 주문이 하나의 IP 주소에 연결됩니다.

다음 단계에 따라 작성하려는 각 서브넷의 각 VLAN에 대한 휴대용 IP 주소 블록을 주문할 수 있습니다.

1. 브라우저 창을 열고 {{site.data.keyword.cloud_notm}}에 로그인하십시오.
2. **계정 > 주문**을 선택하십시오. 
3. 팝업 창에서 **네트워크 > 서브넷 /IP > 주문**으로 이동하십시오.
4. 드롭 다운 메뉴에서 **휴대용 사설**을 선택하십시오. 
5. **XX 휴대용 사설 IP 주소**를 선택하고 **계속**을 클릭하십시오. **참고:** _XX_는 IP 주소의 수를 지정합니다.
6. IP 주소 블록과 연관시킬 VLAN을 선택하고 **계속**을 클릭하십시오.
7. 화면의 정보 입력을 완료하고 **계속**을 클릭하십시오.

IP 주소 작성이 매우 빠르게 진행되고 **네트워크 > IP 관리**에서 **서브넷**을 선택하여 표시됩니다. 부록 A: VLAN 워크시트에 있는 워크시트에 이러한 IP 주소를 기록할 수 있습니다.

## 3단계: 가상 서버 주문
{: #step-3-ordering-a-virtual-server}

Windows 2012 R2 Standard 가상 서버는 ISO용 유틸리티 서버로 사용하도록 프로비저닝되어 있고 이 단계에서는 해당 환경에 대한 액세스를 제공합니다.

1. 브라우저 창을 열고 {{site.data.keyword.cloud_notm}}에 로그인하십시오.
2. **계정 > 주문**을 선택하십시오. 
3. **가상 서버 > 시간별 또는 월별**로 이동하십시오.
4. 가상 서버를 프로비저닝하고 각 옵션에 대해 다음 스펙을 지정하려면 VLAN이 작성된 적절한 데이터 센터를 선택하십시오.
  * 컴퓨팅 인스턴스 - 1x2.0GHz 코어
  * RAM: 4GB
  * 운영 체제: Windows Server 2012 R2 Standard Edition(64비트)
  * 첫 번째 디스크: 100GB(SAN)
  * 업링크 포트 속도 - 1Gbps 공용 및 사설 네트워크 업링크
5. **주문 계속**을 클릭하고, **주문 요약 및 청구** 화면에서 백엔드 및 프론트엔드 VLAN을 선택하십시오. **참고:** VLAN의 선택이 중요하므로 유틸리티를 데이터 센터 내의 올바른 팟(Pod)에 배치할 수 있습니다. 예제 환경에서는 백엔드 VLAN이 관리 VLAN(1101)이고 프론트 엔드 VLAN이 공용 VLAN(2101)입니다. 
6. 서버의 호스트 이름과 도메인 이름을 입력하고 **주문**을 클릭하십시오.

## 4단계: ESXi 호스트 및 게이트웨이/방화벽 주문 
{: #step-4-ordering-esxi-hosts-and-gateway-firewall}

가상 서버가 프로비저닝되는 동안 ESXi 호스트 및 Brocade vRouter(Vyatta) 게이트웨이 및 방화벽 어플라이언스를 주문해야 합니다. 이러한 서버를 동시에 동일한 팟(Pod)에 배치할 수 있도록 모두 동시에 주문하십시오. 별도의 시간에 하드웨어를 주문하면 호스트와 방화벽이 {{site.data.keyword.cloud_notm}} 데이터 센터 내에서 별도의 팟(Pod)에 있게 될 수 있습니다.

### ESXi 호스트
{: #esxi-hosts}

환경에 대해 주문된 각 ESXi 호스트의 경우 VMware ESXi 5.5는 운영 체제입니다. {{site.data.keyword.cloud_notm}} vSphere 라이센스를 사용하려는 경우, 사용량을 기반으로 월별 비용이 발생합니다.

다른 옵션은 자체 ISO를 사용하여 ESXi를 설치하는 것입니다. 이 프로세스에 대한 지시사항은 [원격 콘솔 및 가상 매체를 통해 VMware vSphere ESXi 설치](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi)의 내용을 참조하십시오. 자체 ISO를 사용하여 ESXi를 설치하려는 경우, 주문 프로세스 동안 관리 및 용량 호스트에 대한 운영 체제로서 **운영 체제 없음**을 선택하도록 하십시오. 

**참고:** 이 구현을 위해서는 Enterprise Plus 라이센싱이 vSphere 분산 가상 스위치를 사용해야 합니다. 라이센스가 Enterprise Plus에 대해 유효하지 않은 경우, {{site.data.keyword.cloud_notm}} 제공 VMware Service Provider Program(VSPP) 라이센스를 사용해야 합니다.

### 관리 호스트 주문
{: #ordering-management-hosts}

관리 호스트 서버를 주문하려면 다음 단계를 사용하십시오. 

1. 브라우저 창을 열고 {{site.data.keyword.cloud_notm}}에 로그인하십시오.
2. **계정 > 주문**을 선택하십시오. 
3. **Bare Metal Server > 월별**을 선택하십시오.
4. 서버 목록 화면에서 관리 클러스터에 대한 요구사항을 충족하는 적절한 서버를 선택하십시오. **참고:** ESXi 5.5에는 최소한 단일 튜얼 코어 프로세서, 4GB의 RAM 및 1Gb의 이더넷 컨트롤러가 필요합니다. 
5. ESXi 서버를 프로비저닝하고 각 옵션에 대해 다음 스펙을 지정하려면 VLAN이 작성된 적절한 데이터 센터를 선택하십시오.
  * 수량: 2
  * RAM: 32GB
  * 운영 체제: VMware ESXi 5.5([원격 콘솔 및 가상 매체를 통해 VMware vSphere ESXi 설치](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi)를 사용 중인 경우 '운영 체제 없음')
  * 하드 디스크: 디스크 1 & 2: RAID 1에 500GB SATA 
  * 공용 대역폭: 사설 네트워크에만 해당 -> 0GB 대역폭
  * 업링크 포트 속도: 10Gbps 중복 사설 네트워크 업링크
6. **주문 계속**을 클릭하십시오.
7. **서버 추가**를 클릭하여 용량 클러스터에 대한 ESXi 호스트를 주문에 추가하십시오.

### 용량 호스트 주문
{: #ordering-capacity-hosts}

1. 서버 목록 화면에서 용량 클러스터 호스트에 대한 요구사항을 충족하는 적절한 서버를 선택하십시오. **참고:** ESXi 5.5에는 최소한 단일 튜얼 코어 프로세서, 4GB의 RAM 및 하나의 1GB 이더넷 컨트롤러가 필요합니다. 
2. ESXi 서버를 프로비저닝하고 각 옵션에 대해 다음 스펙을 지정하려면 VLAN이 작성된 적절한 데이터 센터를 선택하십시오.
  * 수량: 3
  * RAM: 128GB
  * 운영 체제: VMware ESXi 5.5([원격 콘솔 및 가상 매체를 통해 VMware vSphere ESXi 설치](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi)를 사용 중인 경우 '운영 체제 없음')
  * 하드 디스크: 디스크 1 & 2: RAID 1에 500GB SATA 
  * 공용 대역폭: 사설 네트워크에만 해당 > 0GB 대역폭
  * 업링크 포트 속도: 10Gbps 중복 사설 네트워크 업링크
3. **주문 계속**을 클릭하십시오.

### 구성 완료
{: #completing-configuration}

이제 장바구니에 ESXi 호스트가 담깁니다. 디바이스를 올바르게 프로비저닝하려면 공용(해당되는 경우) VLAN, 사설 VLAN, 호스트 이름 및 도메인을 디바이스에 지정해야 합니다.

1. 다음 VLAN을 지정하고 디바이스에 대한 호스트 이름을 작성하십시오. 

* ESXi 호스트 – 백엔드 VLAN: (1101)
* 백엔드 VLAN: (1101)
* 프론트 엔드 VLAN: (2101)

2. 지불 방법을 선택하고 이용 약관에 동의한 후 **주문 완료**를 클릭하십시오. **중요:** 이전 단계에서 주문된 VPN 또는 가상 서버를 통해 서버가 프로비저닝되고 액세스 가능할 때까지 5단계로 진행하지 마십시오.

## 5단계: BCS 스위치에서 VLAN 트렁킹
{: #step-5-trunking-vlans-on-bcs-switches}

기본적으로 포트는 백엔드 고객 스위치(즉, BCS(Backend Customer Switch)와 같은 팟(Pod)의 사설 네트워크 스위치)에 액세스 모드로 배치됩니다. 결과적으로 호스트가 스토리지에 액세스할 수 있고 VM이 사설 네트워크에서 통신할 수 있도록 ESXi 호스트에 연결된 포트를 트렁킹해야 합니다.

VLAN를 트렁킹하기 위해 티켓을 열기 전에 사설 네트워크에 있는 네트워크 인터페이스를 판별해야 합니다. 인터페이스를 판별하려면 각 ESXi 서버에 대한 **디바이스 세부사항**으로 이동하고 **네트워크 > 사설**로 이동하십시오. 이 환경에서는 `eth0` 및 `eth2`는 사설 네트워크 어댑터입니다.

ESXi 호스트에 대한 VLAN의 트렁크 연결 외에도 관리 및 용량 호스트에 대한 NIC를 결합 해제해야 합니다. LACP(Link Aggregation Control Protocol)는 소프트웨어 iSCSI 다중 경로와 호환되지 않으므로 NIC를 결합 해제합니다.

NIC를 트렁킹하고 NIC를 결합 해제하려면 다음과 같은 단계를 수행하여 티켓을 열어야 한다.

1. 브라우저 창을 열고 {{site.data.keyword.cloud_notm}}에 로그인하십시오.
2. **지원, 티켓 추가**를 선택하십시오.
3. 다음과 같은 정보를 입력하십시오. 
  * 주제: 사설 네트워크 질문
  * 제목: VLAN 트렁킹 및 NIC 결합 해제
  * 세부사항: 다음 호스트에 대해 VLAN `<Management VLAN>`, `<Storage VLAN>` 및 `<VM VLAN>`을 eth0 및 eth2 NIC 쌍에서 트렁킹하십시오[각 ESXi 호스트 나열]. 또한 다음 서버에서 사설 NIC(eth0 및 eth2)를 결합 해제(LACP 제거)하십시오[각 ESXi 호스트 나열].
4. **티켓 추가**를 클릭하십시오.

실제 VLAN으로 <>에 지정된 VLAN을 변경해야 합니다. 

## 6단계: 관리 호스트 네트워킹 구성
{: #step-6-configuring-management-host-networking}

서버가 프로비저닝되고 VLAN이 트렁킹된 후에는 관리 클러스터의 호스트에서 네트워킹을 설정해야 합니다. 이 구성에서는 관리 클러스터에 대해 vSphere 표준 스위치를 사용합니다. vMotion 및 결함 허용 포트 그룹을 제외하고 휴대용 IP 주소를 사용하여 VM 커널 포트 그룹을 구성합니다. **참고:** 트래픽을 라우팅할 필요가 없기 때문에 vMotion 및 결함 허용에 대한 자체 IP 체계를 사용합니다. 그러나 모든 호스트는 vMotion 및 결함 허용 기능을 사용하기 위해 클러스터의 다른 호스트와 동일한 서브넷에 있어야 합니다. 서브넷을 라우팅해야 하는 경우, {{site.data.keyword.cloud_notm}} 휴대용 IP 주소를 사용하는 것이 좋습니다. 

포트 그룹을 구성하려면 vSphere 클라이언트가 {{site.data.keyword.cloud_notm}} 관리 VPN를 통해 호스트에 액세스하는 유틸리티 가상 서버 또는 워크스테이션에 설치되어야 합니다.

1. 브라우저 창을 열고 {{site.data.keyword.cloud_notm}}에 로그인하십시오.
2. 유틸리티 서버 또는 {{site.data.keyword.cloud_notm}} VPN에 연결한 후에 vSphere Client를 시작하십시오. 
3. 관리 ESXi 호스트 중 하나의 IP 주소, 사용자 이름 및 비밀번호를 입력하십시오. **디바이스 세부사항 > 비밀번호**를 선택하여 ESXi 호스트에 대한 루트 비밀번호를 찾을 수 있습니다.
4. **구성 > 네트워킹**으로 이동하여 vSphere 표준 스위치에서 다음 포트 그룹을 수정하십시오(vSwitch0과 유사함).

관리 클러스터의 각 호스트에 대해 다음 단계를 완료하십시오.

### vSwitch0 특성
{: #vswitch0-properties}

<table border="1" cellpadding="0" cellspacing="0">
<caption>표 4. vSwitch0 특성</caption>
<tbody>
<tr><th>네트워크 어댑터</th><th>vmnicX 및 vmnicY</th></tr>
<tr><td>로드 밸런싱</td><td>원래 가상 포트 ID를 기반으로 한 라우터 </td></tr>
<tr><td>활성 어댑터</td><td>vmnicX 및 vmnicY</td></tr>
</tbody>
</table>

### 기존 가상 머신 포트 그룹 편집
{: #edit-existing-virtual-machine-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>표 5. vmPG-Management 포트 그룹</caption>
<tbody>
<tr><th>네트워크 레이블</th><th>*vmPG-Management*</th></tr>
</tbody>
</table>

### 기존 VM 커널 포트 그룹 편집
{: #edit-existing-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>표 6. vmkPG-Management 포트 그룹</caption>
<tbody>
<tr><th>네트워크 레이블</th><th>*vmkPG-Management*</th></tr>
</tbody>
</table>

### vMotion VM 커널 포트 그룹 추가
{: #add-vmotion-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>표 7. vMotion 포트 그룹</caption>
<tbody>
<tr><th>연결 유형 </th><th>VMKernel</th></tr>
<tr><td>네트워크 레이블</td><td>*vmkPG-vMotion*</td></tr>
<tr><td>VLAN ID</td><td>*&lt;Storage VLAN&gt; (1102)*</td></tr>
<tr><td>vMotion 트래픽</td><td>사용 가능</td></tr>
<tr><td>IP 주소</td><td>*172.16.10.X/24*<br/><br/>*다른 서브넷이 될 수 있는 사용자 정의 주소. 각 호스트의 다른 vMotion 주소가 동일한 서브넷에 있는지 확인하십시오. *</td></tr>
<tr><td>서브넷 마스크</td><td>255.255.255.0</td></tr>
</tbody>
</table>

### 결함 허용 VM 커널 포트 그룹 추가
{: #add-fault-tolerance-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>표 8. FT 포트 그룹</caption>
<tbody>
<tr><th>연결 유형</th><th>VM 커널</th></tr>
<tr><td>네트워크 레이블</td><td>*vmkPG-FT*</td></tr>
<tr><td>VLAN ID</td><td>*&lt;Storage VLAN&gt; (1102)*</td></tr>
<tr><td>결함 허용 로깅</td><td>사용 가능</td></tr>
<tr><td>IP 주소</td><td>*172.16.20.X/24*<br/><br/>*필요한 경우 다른 서브넷이 될 수 있는 사용자 정의 주소. 각 호스트의 다른 FT 주소가 동일한 서브넷에 있는지 확인하십시오. *</td></tr>
<tr><td>넷마스크</td><td>255.255.255.0</td></tr>
</tbody>
</table>

### 스토리지 VM 커널 포트 그룹 추가
{: #add-storage-vm-kernel-port-group}

호스트 및 VM 커널 포트의 이름이 지정된 각 휴대용 IP 주소의 **참고** 섹션을 업데이트하십시오. **참고** 섹션은 {{site.data.keyword.slportal_full}}에 이동하여 **네트워크 > IP 관리 > 서브넷 > [서브넷]**을 선택하여 찾을 수 있습니다.

<table border="1" cellpadding="0" cellspacing="0">
<caption>표 9. 스토리지 포트 그룹</caption>
<tbody>
<tr><th>연결 유형</th><th>VM 커널</th></tr>
<tr><td>네트워크 레이블</td><td>*vmkPG-Storage*</td></tr>
<tr><td>VLAN ID</td><td>*&lt;Storage VLAN&gt; (1102)*</td></tr>
<tr><td>IP 주소</td><td>*휴대용 사설 주소*<br/><br/>*스토리지 VLAN에 바인드된 휴대용 사설 주소에서 선택된 IP 주소*</td></tr>
<tr><td>넷마스크</td><td>*IP 범위와 관련된 서브넷 마스크*</td></tr>
</tbody>
</table>

### 공용 주소 포트 그룹 추가
{: #add-public-address-port-group}

호스트 및 VM 커널 포트의 이름이 지정된 각 휴대용 IP 주소의 **참고** 섹션을 업데이트하십시오. **참고** 섹션은 {{site.data.keyword.slportal}}에 있으며 **네트워크 > IP 관리 > 서브넷 > [서브넷]**을 선택하면 됩니다.

<table border="1" cellpadding="0" cellspacing="0">
<caption>표 10. 공용 포트 그룹</caption>
<tbody>
<tr><th>연결 유형</th><th>가상 머신</th></tr>
<tr><td>네트워크 레이블</td><td>vmPG-Public</td></tr>
<tr><td>VLAN ID</td><td>&lt;기본 공용 VLAN&gt;(예: 2101)</td></tr>
</tbody>
</table>

## 7단계: ISO 이미지 및 vCenter Virtual Appliance 다운로드 또는 업로드
{: #step-7-download-or-upload-iso-images-and-vcenter-virtual-appliance}

이제 환경은 VMware vCenter Virtual Appliance를 배치하고 DNS, Active Directory 또는 BIND용 가상 머신을 설치할 준비가 되었습니다. 그러나 배치하기 전에 이미지를 다운로드해야 합니다. 이미지를 다운로드하려면 원격 데스크탑을 이전에 프로비저닝된 가상 서버로 다운로드하고 사용자 환경에 맞게 가상 서버에서 적절한 이미지를 다운로드하십시오.

* Active Directory / Windows DNS: Windows Server 2008R2/Windows Server 2012 ISO 이미지(라이센스가 부여된 매체)
* Linux BIND: [CentOS 설치 이미지 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://isoredirect.centos.org/centos/6/isos/x86_64/){: new_window}
* [vCenter Virtual Appliance ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://my.vmware.com/web/vmware/info/slug/datacenter_cloud_infrastructure/vmware_vsphere/5_5){: new_window}(유효한 VMware 가입 필요)

ISO를 다운로드한 후에는 가상 머신에 연결할 수 있도록 이미지를 관리 호스트 데이터 저장소에 업로드해야 합니다. vSphere 클라이언트를 사용하여 관리 호스트에 연결하고 로컬 데이터 저장소에서 ISO 디렉토리를 작성하십시오. 

## 8단계: DNS 배치
{: #step-8-deploying-dns}

관리 호스트에 VM을 배치하고 DNS 서비스를 설치해야 합니다. 일반 vSphere 클라이언트를 사용하여 Windows 또는 Linux ISO가 위치해 있는 관리 ESXi 호스트에 VM을 작성하십시오. VM에 DNS 서버를 배치하려면 적절한 ISO(Windows 또는 CentOS)를 연결하십시오. 이전 단계에서 작성된 VM 포트 그룹(vmpg) 관리 포트 그룹에 VM을 연결해야 합니다.

VM이 설치된 후에는 휴대용 사설 서브넷 그룹에서 IP 주소와 기본 게이트웨이를 지정하십시오. 부록 A, 관리 VM 서브넷에 있는 VLAN 워크시트를 사용하는 경우 VM의 이름이 지정된 각 휴대용 IP 주소의 **참고** 섹션을 업데이트하십시오. **참고** 섹션은 {{site.data.keyword.slportal}}에 이동하여 **네트워크 > IP 관리 > 서브넷 > [서브넷]**을 선택하여 찾을 수 있습니다.

이 문서에서는 DNS 사용 설정에 필요한 단계를 자세히 설명하지 않지만 다음 지침이 제공됩니다.

1. **DNS 전달**을 service.softlayer.com 로컬 DNS 호스트로 설정하십시오.
  * `rs1.service.softlayer.com 10.0.80.11`
  * `rs2.service.softlayer.com 10.0.80.12`
2. DNS를 설정한 후에 프로비저닝된 모든 휴대용 및 기본 서브넷에 대해 로컬 DNS 영역(dal06.mycompany.local) 및 역방향 검색 영역을 작성하십시오. 
3. 각 호스트의 관리 IP 주소에 대한 HOST 레코드를 추가하십시오(vmkPG-management의 경우 vmk0).
4. vCenter Virtual Appliance에 대한 관리 VLAN에 바인드된 휴대용 사설 서브넷에서 HOST 레코드를 추가하십시오.
5. vCenter에 지정한 휴대용 IP 서브넷의 **참고** 섹션을 업데이트하십시오.

자세한 정보는 다음 링크를 참조하십시오.
* [Windows DNS 및 Active Directory ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://social.technet.microsoft.com/wiki/contents/articles/12370.step-by-step-guide-for-setting-up-a-windows-server-2012-domain-controller.aspx){: new_window}.
<!--* [CentOS BIND ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.centos.org/docs/2/rhl-rg-en-7.2/s1-bind-configuration.html){: new_window}.-->

## 9단계: vCenter Virtual Appliance 배치 및 구성 
{: #step-9-deploying-and-configuring-vcenter-virtual-appliance}

이제 DNS가 구성되었으므로 vCenter Server Appliance를 배치 및 구성할 수 있습니다. 어플라이언스를 배치하려면 다음 단계를 수행하십시오.

1. 원격 데스크탑을 가상 서버로 이동하고 vSphere Client를 여십시오.
2. 관리 호스트에 연결하여 **파일, OVF 템플리트 배치**를 선택하십시오. 
3. 마법사를 따라 배치를 완료하십시오.

<!--For more information about OVF Template deployment, see [VMware documentation ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://pubs.vmware.com/vsphere-55/index.jsp?topic=%2Fcom.vmware.vsphere.hostclient.doc%2FGUID-6C847F77-8CB2-4187-BD7F-E7D3D5BD897B.html&resultof=%22thick%22%20%22thin%22%20){: new_window}.-->

전원 공급 시 vCenter 가상 어플라이언스를 IP 주소에 지정하는 데 DHCP(Dynamic Host Configuration Protocol) 서버를 사용할 수 없으므로 루트 콘솔을 사용하여 어플라이언스를 구성해야 합니다. **참고:** `NO NETWORKING DETECTED` 메시지가 vCenter Virtual Appliance Console에 표시됩니다.

어플라이언스를 구성하려면 다음 단계를 따르십시오.

1. **사용자 이름**이 'root'이고 **비밀번호**가 'vmware'인 콘솔에 로그인하십시오.
2. `/opt/vmware/share/vami/vamiconfig_net`을 실행하고 텍스트 마법사를 따라 IP, 서브넷 및 DNS 특성을 구성하십시오. **8단계: DNS 배치**에서 작성된 DNS 또는 BIND 서버의 IP 주소를 사용할 것을 유념하십시오.
3. 네트워크 설정을 저장하고 콘솔을 종료하고 가상 서버에서 브라우저를 여십시오.
4. 포트 5480<!-- (https://:5480)-->과 함께 첨부된 vCenter 가상 어플라이언스(VCVA)에 제공한 IP 주소로 이동하십시오.
5. 마법사에서 EULA를 수락하고 **고객 환경 개선 프로그램**에 응답하고, **옵션 구성, 사용자 정의 구성 설정**을 선택하십시오.
6. **다음**을 클릭하고 다음 값을 입력하십시오. 
<table border="1" cellpadding="0" cellspacing="0"><caption>표 11. VCVA 설정 마법사</caption><tbody><tr><th>마법사 메뉴</th><th>옵션 </th><th>값</th></tr><tr><td>데이터베이스 설정</td><td>데이터베이스 유형</td><td>임베드됨</td></tr><tr><td>SSO 설정</td><td>SSO 배치 유형</td><td>임베드됨</td></tr><tr><td>SSO 설정</td><td>새 관리자 비밀번호*(administrator@vsphere.local의 경우)*</td><td>&lt;비밀번호 입력&gt;</td></tr><tr><td>SSO 설정</td><td>새 비밀번호 다시 입력</td><td>&lt;이전에 사용한 것과 동일한 비밀번호 입력&gt;</td></tr><tr><td>시간 동기화</td><td>NTP 동기화</td><td>servertime.service.softlayer.com</td></tr></tbody></table>
7. **시작**을 클릭하십시오. VCVA가 구성됩니다.
8. **관리** 아래의 옵션을 사용하여 루트 비밀번호를 변경하십시오.
9. VCVA 구성 웹 페이지에서 로그아웃하십시오.
10. `9443/vsphere-client`<!-- (https://:9443/vsphere-client)-->와 함께 첨부된 VCVA의 IP 주소를 입력하여 vSphere 웹 클라이언트로 이동하십시오.
11. 루트에 로그인하여 **관리, 라이센스**를 선택하십시오.
12. VMware vCenter 라이센스를 입력하십시오. 

## 10단계: vCenter 클러스터 및 분산 가상 스위치 작성
{ #step-10-create-vcenter-clusters-and-distributed-virtual-switch}

VCVA가 구성되고 라이센스가 부여되었으므로, 용량 클러스터에 대한 데이터 센터 및 클러스터 구성 및 분산 가상 스위치를 작성할 수 있습니다.

### 데이터 센터 및 클러스터 작성
{: #creating-data-center-and-clusters}

1. vSphere Client에서 **홈** 화면으로 이동하십시오.
2. **시작하기**를 선택하고 **여기를 클릭**하십시오.
3. **데이터 센터 작성**을 클릭하십시오.
4. 데이터 센터 이름을 입력하십시오. (예제 환경은 Toronto 01 데이터 센터입니다. 데이터 센터 이름으로 `Toronto 01`을 사용하십시오.)
5. 데이터 센터가 작성되고 나면 **시작하기** 페이지에서 **클러스터 작성**을 클릭하십시오.
6. 첫 번째 클러스터 `관리`의 이름을 지정하십시오. 모든 다른 옵션을 선택 취소한 후 **확인**을 클릭하십시오.
7. 관리 클러스터와 동일한 프로세스를 사용하여 수행된 경우 다른 클러스터를 작성하십시오.

이제 **호스트 추가**를 사용하여 관리 및 용량 클러스터에 관리 및 용량 호스트를 추가할 수 있습니다. 각 호스트가 추가된 경우 DNS가 사용되므로 IP 주소가 아닌 서버의 완전한 도메인 이름(FQDN)을 사용하도록 하십시오.

ESXi 호스트를 vCenter에 추가할 경우, 각 ESXi 호스트의 쉘 및 SSH의 인에이블먼트에 대한 여러 경고 메시지가 표시될 수 있습니다. 이러한 경고가 표시되지 않도록 하려면 팝업 창에서 **경고 억제 > 예**를 클릭하십시오. 경고 억제 링크가 표시되지 않으면 이러한 단계를 따르십시오. 

1. ESXi 호스트 **관리** 탭으로 이동하십시오. 
2. **설정**을 선택하고 **고급 시스템 설정**을 클릭하십시오.
3. **UserVars.SupressShellWarning** 키를 찾고 값을 **1**로 변경하십시오.
4. **확인**을 클릭하십시오. 

관리 및 용량 호스트가 각 클러스터에 추가되고 나면 각 호스트로 이동하여 DNS 및 NTP를 설정하십시오. DNS를 설정하려면 다음 단계를 수행하십시오. 

1. 호스트를 클릭하고 **관리 > 네트워킹**을 선택하십시오.
2. 기본 시스템 스택을 선택하고(**TCP/IP 구성**) 연필 아이콘을 클릭하십시오.
3. 이전에 작성한 DNS 서버의 IP 주소 및 호스트와 도메인 이름을 입력하십시오.

NTP 설정의 경우:

1. **관리, 설정, 시간 구성**으로 이동하십시오. 
* NTP 서버로 `servertime.service.softlayer.com`을 입력하십시오.
* **NTP 서비스 시작 정책**을 `Start and stop with host`로 설정하십시오.

***연결 호스트에 대한 분산 가상 스위치 작성***

1. vSphere 웹 클라이언트를 사용하여 **네트워킹**으로 이동하고 데이터 센터 이름을 마우스 오른쪽 단추로 클릭하십시오. 
2. **새 분산 스위치**를 선택하십시오.
3. 분산 스위치의 이름을 지정하고 **다음**을 클릭하십시오.
4. 적절한 분산 스위치 버전을 선택하고 **다음**을 클릭하십시오.
5. **설정 편집** 화면에서 `2`를 업링크 수로 입력하고 **기본 포트 그룹 작성** 선택을 취소하십시오.
6. **다음 > 완료**를 클릭하고 분산 가산 스위치를 작성하십시오. 

***분산 가상 스위치에 대한 포트 그룹 작성***

분산 가상 스위치가 있으므로 vMotion, 결함 허용, VM 및 스토리지에 대한 포트 그룹을 작성해야 합니다. 

***dvpg 작성-사설-VM 관리 포트 그룹***

1. vSphere 웹 클라이언트를 사용하여 네트워킹 섹션으로 이동하십시오. 
2. 분산 가상 스위치(DVS)를 마우스 오른쪽 단추로 클릭하십시오.
3. **새 분산 포트 그룹…**을 클릭하고 첫 번째 포트 그룹의 경우 표 14에 정보를 입력하십시오.
4. 테이블에 지정되지 않은 옵션에 대한 기본값을 그대로 두십시오.

| 새 분산 포트 그룹 메뉴 | 필드 |값|
| --- | --- | --- |
| 이름 및 위치 |이름| dvpg-Private-VM Management |
| 설정값 구성 | 고급 |기본 정책 구성 사용자 정의 확인|
|정책 구성(팀 구성 및 장애 복구)|로드 밸런싱|실제 NIC 로드를 기반으로 하는 라우트|
|정책 구성(팀 구성 및 장애 복구)|장애 복구 순서|활성 업링크: 업링크 1 & 업링크 2|
<caption>표 12. DVS VM 관리 포트 그룹</caption>

첫 번째 포트 그룹을 작성한 후에 다음 구성 옵션과 함께 나머지 포트 그룹을 작성하십시오(표 15 - 표 19).

***dvpg-Private-vMotion 포트 그룹 작성***

| 새 분산 포트 그룹 메뉴 |필드|값|
|---|---|---|
|이름 및 위치|이름|dvpg-Private-vMotion|
|설정값 구성|VLAN 유형|VLAN|
|설정값 구성|VLAN ID|&lt;Storage VLAN&gt;|
|설정값 구성|고급|기본 정책 구성 사용자 정의 확인|
|정책 구성(팀 구성 및 장애 복구)|로드 밸런싱|실제 NIC 로드를 기반으로 하는 라우트|
|정책 구성(팀 구성 및 장애 복구)|장애 복구 순서|활성 업링크: 업링크 1 & 업링크 2|
<caption>표 13. DVS vMotion 포트 그룹</caption>

***dvpg-Private-Fault Tolerance 포트 그룹 작성***

| 새 분산 포트 그룹 메뉴 |필드|값|
|---|---|---|
|이름 및 위치|이름|dvpg-Private-Fault Tolerance|
|설정값 구성|VLAN 유형|VLAN|
|설정값 구성|VLAN ID|&lt;Storage VLAN&gt;|
|설정값 구성|고급|기본 정책 구성 사용자 정의 확인|
|정책 구성(팀 구성 및 장애 복구)|로드 밸런싱|실제 NIC 로드를 기반으로 하는 라우트|
|정책 구성(팀 구성 및 장애 복구)|장애 복구 순서|활성 업링크: 업링크 1 & 업링크 2|
<caption>표 14. DVS FT 포트 그룹</caption>

***dvpg 작성-사설-VM 액세스 포트 그룹***

|새 분산 포트 그룹 메뉴 |필드|값|
|---|---|---|
|이름 및 위치|이름|dvpg-Private-VM Access|
|설정값 구성|VLAN 유형|VLAN|
|설정값 구성|VLAN ID|&lt;가상 머신 VLAN&gt;|
|설정값 구성|고급|기본 정책 구성 사용자 정의 확인|
|정책 구성(팀 구성 및 장애 복구)|로드 밸런싱|실제 NIC 로드를 기반으로 하는 라우트|
|정책 구성(팀 구성 및 장애 복구)|장애 복구 순서|활성 업링크: 업링크 1 & 업링크 2|
<caption>표 15. DVS VM 액세스 포트 그룹</caption>

***dvpg 작성-사설-스토리지***

|새 분산 포트 그룹 메뉴 |필드|값|
|---|---|---|
|이름 및 위치|이름|dvpg-사설-스토리지 경로 A|
|설정값 구성|VLAN 유형|VLAN|
|설정값 구성|VLAN ID|&lt;Storage VLAN&gt;|
|설정값 구성|고급|기본 정책 구성 사용자 정의 확인|
|정책 구성(팀 구성 및 장애 복구)|로드 밸런싱|실제 NIC 로드를 기반으로 하는 라우트|
|정책 구성(팀 구성 및 장애 복구)|장애 복구 순서|활성 업링크: 업링크 1 & 업링크 2|
<caption>표 16. DVS 스토리지 포트 그룹</caption>

***dvpg 작성-기본-공용***

|새 분산 포트 그룹 메뉴|필드|값|
|이름 및 위치|이름|dvpg-Private-Storage|
|설정 구성|VLAN 유형|VLAN
|설정 구성|VLAN ID|&lt;기본 공용 VLAN&gt;|
|설정 구성|고급|기본 정책 구성 사용자 정의 확인|
|정책 구성(팀 구성 및 장애 복구)|로드 밸런싱|실제 NIC 로드를 기반으로 하는 라우트|
|정책 구성(팀 구성 및 장애 복구)|장애 복구 주문|활성 업링크: 업링크 1 & 업링크 2|
<caption>표 17. DVS 스토리지 경로 B 포트 그룹</caption>

## 11단계: 용량 클러스터의 ESXi 호스트를 분산 가상 스위치로 마이그레이션 
{: #step-11-migrating-esxi-hosts-in-capacity-cluster-to-distributed-virtual-switch}

용량 호스트가 용량 클러스터에 추가되었으므로 *10단계: vCenter Clusters & 분산 가상 스위치 작성*에서 작성한 분산 가상 스위치로 가상 표준 스위치 구성을 마이그레이션할 수 있습니다. 한 호스트에 대한 마이그레이션이 완료되었으며, 클러스터의 나머지를 나중에 구성하기 위해 호스트 프로파일을 적용합니다.

VMkernel 어댑터 추가를 시작하기 전에 분산 가상 스위치의 업링크에 vmnic을 지정하십시오.

1. **vCenter 인벤토리 목록, 분산 스위치**를 클릭하십시오.
2. 용량 호스트에 대한 적절한 분산 스위치를 선택하십시오. 
3. **시작하기** 페이지에서 **호스트 추가 및 관리**를 클릭하십시오. 
4. 다음 설정을 사용하여 업링크를 추가하고 기존 호스트의 관리와 연관된 기존 VMkernel을 마이그레이션하십시오.
  - <table border="1" cellpadding="0" cellspacing="0"><caption>표 18. DVS 추가 호스트</caption><tbody><tr><th>메뉴</th><th>필드</th><th>값</th></tr><tr><td>태스크 선택</td><td>태스크 선택</td><td>호스트 추가</td></tr><tr><td>호스트 선택</td><td>**새 호스트**를 클릭하십시오.</td><td>용량 호스트를 클릭하십시오.</td></tr><tr><td>네트워크 어댑터 태스크 선택</td><td>네트워크 어댑터 태스크 선택</td><td>실제 어댑터 관리 및 VMkernel 어댑터 관리 선택</td></tr></tbody></table>
5. 사설 vmnic 중 하나를 선택하고 **실제 네트워크 어댑터 관리 > 업링크 지정**을 클릭하십시오.
6. 팝업 화면에서 `uplink1`을 선택하고 **확인**을 클릭하십시오.
7. 다른 사설 vmnic에 대해 이러한 단계를 완료하고 이를 `uplink2`로 지정하십시오.
8. **다음**을 클릭하고 vmk0 VMkernel 어댑터를 강조표시하고 **포트 그룹 지정**을 클릭하십시오.
9. 팝업 창에서 **dvpg-Private-VM Management**를 선택하고 **확인**을 클릭하십시오.
10. **다음**을 두 번 클릭한 후 **완료**를 클릭하여 분산 가상 스위치로의 마이그레이션을 완료하십시오. **참고:** 호스트에 대한 네트워크 연경을 잠시 잃을 수 있습니다. 연결이 빠르게 재설정됩니다.

vmk0 어댑터를 분산 가상 스위치로 마이그레이션하고 나면 DVS의 각 포트 그룹에 VM 커널을 추가할 수 있습니다. 

11. vCenter 내의 호스트에서 **관리 > 네트워킹**을 클릭하십시오. 
12. **VM 커널 어댑터 > 호스트 네트워킹 추가**로 이동하여 표 19 및 21에 있는 VM 커널 어댑터를 추가하십시오. 이러한 어댑터를 추가하기 위해서는 vCenter 내의 호스트에서 **관리 > 네트워킹** 탭에 있는 "VM 커널 어댑터" 메뉴로 마이그레이션합니다. 그런 다음 "호스트 네트워킹 추가" 아이콘을 클릭하고 다음 VM 커널 어댑터를 추가하십시오. 

***vMotion용 vmk1 추가***

|메뉴|필드|값|
|---|---|---|
|연결 유형 선택|연결 유형 선택|VMKernel Network Adapter|
|대상 디바이스 선택|기존 분산 포트 그룹 선택|dvpg-Private-vMotion|
|네트워크 어댑터 태스크 선택|네트워크 어댑터 태스크 선택|**실제 어댑터 관리** 및 **VM 커널 어댑터 관리**를 선택하십시오.|
|포트 특성|서비스 사용|vMotion 트래픽 확인|
|IPv4 설정|IPv4 주소|*172.16.10.X/24*<br/><br/>*이는 사용자 정의 주소이며, 필요한 경우 다른 서브넷에 있을 수 있습니다. 각 호스트의 다른 vMotion 주소가 동일한 서브넷에 있는지 확인하십시오. *|
|IPv4 설정|서브넷 마스크|255.255.255.0|
<caption>표 19. 호스트 네트워킹 vMotion</caption>

***결함 허용에 대한 vmk2 추가***

|메뉴|필드|값|
|---|---|---|
|연결 유형 선택|연결 유형 선택|VMKernel Network Adapter|
|대상 디바이스 선택|기존 분산 포트 그룹 선택|dvpg-Private-Fault Tolerance|
|네트워크 어댑터 태스크 선택|네트워크 어댑터 태스크 선택|**실제 어댑터 관리** 및 **VMKernel 어댑터 관리**를 선택하십시오.|
|포트 특성|서비스 사용|결함 허용 로깅 확인|
|IPv4 설정|IPv4 주소|*172.16.20.X/24*<br/><br/>*이는 사용자 정의 주소이며, 필요한 경우 다른 서브넷에 있을 수 있습니다. 각 호스트의 다른 FT 주소가 동일한 서브넷에 있는지 확인하십시오. *|
|IPv4 설정|서브넷 마스크|255.255.255.0|
<caption>표 20. 호스트 네트워킹 결함 허용</caption>

***스토리지용 vmk3 추가***

|메뉴|필드|값|
|---|---|---|
|연결 유형 선택|연결 유형 선택|VMkernel Network Adapter|
|대상 디바이스 선택|기존 분산 포트 그룹 선택|dvpg-Private-Storage|
|네트워크 어댑터 태스크 선택|네트워크 어댑터 태스크 선택|실제 어댑터 관리 및 VMkernel 어댑터 관리 선택|
|IPv4 설정|IPv4 주소|*휴대용 사설 주소*<br/><br/>*이 IP 주소는 스토리지 VLAN에 바인드된 휴대용 사설 주소에서 선택됩니다. 이 주소는 스토리지 경로 B와 다른 서브넷에 있어야 합니다.*|
|IPv4 설정|서브넷 마스크|IP 범위와 연관된 서브넷 마스크|
<caption>표 21. 호스트 네트워킹 스토리지</caption>

***호스트 프로파일 작성***

호스트 프로파일을 작성하려면 이전에 구성한 단일 용량 호스트에서 이를 캡처해야 합니다. 

1. vSphere Web Client 홈 화면으로 이동하여 **호스트 프로파일** 아이콘을 클릭하십시오. 
2. 초록색 더하기(+) 기호 **호스트에서 프로파일 추출**을 클릭하고 팝업 창에서 이전에 구성된 용량 호스트를 선택하십시오.
3. **다음**을 클릭하십시오. 
4. 호스트 프로파일(Capacity01 호스트 프로파일)에 적절한 이름을 제공하고 설명을 입력하고 **다음**을 클릭하십시오.
5. 설정을 검토하고 **완료**를 클릭하십시오. 

호스트 프로파일을 작성하고 나면 프로파일이 용량 클러스터의 나머지 호스트에 적용될 때 MAC 주소에 대해 프롬프트하지 않도록 이를 수정해야 합니다.

1. 작성한 호스트 프로파일을 마우스 오른쪽 단추로 클릭하고 **설정 편집 > 호스트 프로파일, 호스트 가상 NIC 편집**을 선택하십시오.
2. 오른쪽 분할창에서 **vmknic용 MAC 주소 결정 방법 판별**을 **사용자가 정책 옵션을 명시적으로 선택해야 함**으로 변경하십시오.
3. **다음**을 클릭한 후 **완료**를 클릭하십시오.

***용량 클러스터에 호스트 프로파일 연결***

이제 호스트 프로파일을 작성했으므로 호스트 프로파일을 클러스터에 연결해야 합니다. 용량 호스트에 적용될 수 있도록 클러스터가 연결됩니다. 

1. vSphere Web Client에서 **호스트 및 클러스터** 보기로 이동하십시오.
2. 클러스터에서 각 호스트에 대한 유지보수 모드를 입력하십시오. **참고:** 프로파일은 유지보수에 있는 호스트에만 적용될 수 있습니다.
3. 용량 클러스터를 마우스 오른쪽 단추로 클릭하고 팝업 메뉴에서 **호스트 프로파일 첨부**를 선택하십시오. 
4. 작성한 호스트 프로파일을 선택하고 **다음**을 클릭하십시오.
5. **호스트 사용자 정의** 화면에서 적절한 정보를 입력하고 **완료**를 클릭하십시오.
6. 유지보수 모드(비유지보수 모드)에서 호스트를 제거하십시오.

## 12단계: 공유 스토리지 주문, 구성 및 연결
{: #step-12-order-configure-and-attach-shared-storage}

계속하기 전에 환경 내에서 관리 및 용량 클러스터와 호스트와 함께 사용할 공유 스토리지를 주문, 구성 및 연결하는 것이 긴급합니다. {{site.data.keyword.cloud_notm}} 다중 테넌트 공유 스토리지 솔루션(파일 스토리지)을 사용하려는 경우, [Architecture Guide for IBM File Storage for {{site.data.keyword.cloud_notm}} with VMware](/docs/infrastructure/FileStorage?topic=FileStorage-architectureguide)의 내용을 참조하십시오.

## 13단계 HA/DRS 및 svMotion vCVA 사용 
{: #step-13-enabling-ha-drs-and-svmotion-vcva}

***관리 및 용량 클러스터에서 HA/DRS 사용***

공유 스토리지가 설정되었으므로 추가 보호 및 로드 밸런싱 기능을 관리 클러스터의 VM에 제공하도록 HA 및 DRS를 사용으로 설정해야 합니다. 

1. vSphere 웹 클라이언트로 이동하십시오. 
* 관리 클러스터에 대한 관리, 설정을 선택하십시오.
* vSphere DRS를 선택하고 편집을 클릭하십시오. 다음 설정이 선택되어 있는지 확인하십시오. **vSphere DRS 켜기**, 자동화 레벨 - **전체 자동화됨**, 마이그레이션 임계값 슬라이더 설정 - 미드웨이, 가상 머신 자동화 - **개별 가상 머신 자동화 레벨 사용**, 전원 관리 - **꺼짐**.  
* vSphere HA 설정 화면에서 다음 설정이 선택되어 있는지 확인하십시오. **vSphere HA 켜기**, **호스트 모니터링**, VM 다시 시작 우선순위 - **중간**, 호스트 격리 응답 - **켜두기**.  
***vCenter Virtual Appliance의 Storage vMotion***

스토리지가 관리 클러스터에서 설정되었고 HA 및 DRS가 사용으로 설정된 경우, 공유 스토리지에 대한 vCenter Virtual Appliance를 설정해야 합니다. 

1. 적절한 어플라이언스를 마우스 오른쪽 단추로 클릭하고 팝업 메뉴에서 **마이그레이션**을 선택하십시오.
2. **데이터 저장소 변경**을 선택하고 **다음**을 클릭하십시오.
3. 이전에 관리 클러스터에 마운트한 iSCSI 볼륨을 선택하고 **다음**을 클릭하십시오.
4. 선택 사항을 검토한 후 **다음**을 클릭하십시오.

고급 단일 사이트 VMware 환경이 완료되었습니다.

## 요약


{: #summary}

이제 IBM Cloud 데이터 센터에서 실행 중인 VMware 환경이 있습니다. VMware 환경은 프로덕션 워크로드를 실행하고 온프레미스 IBM Cloud 배치를 보완할 수 있습니다. 환경은 VMware 우수 사례를 지원하고 VMware DRS, HA, Storage DRS 및 네트워킹 이중화와 같은 기능을 사용할 수 있습니다. 더 많은 용량 또는 관리 호스트와 더 많은 스토리지를 사용하여 이 참조 아키텍처 구현을 확장할 수 있습니다.

VMware에 대한 자세한 정보는 [VMware 배치](/docs/infrastructure/vmware?topic=VMware-using-cookbooks-for-vmware-deployments) 및 [VMware FAQ](/docs/infrastructure/vmware?topic=VMware-faqs-vmware)를 참조하십시오. 

## 부록 A: VLAN 워크시트
{: #appendix-a-vlan-worksheet}

|VLAN 유형|VLAN 번호|IP 범위|게이트웨이|용도|
|---|---|---|---|---|
|기본 사설||||관리|
|기본 사설||||스토리지|
|기본 사설||||가상 머신|
|기본 공용||||공용 액세스|
|휴대용 사설||||관리 VM|
|휴대용 사설||||스토리지|
|휴대용 사설||||가상 머신|
|vMotion|||해당사항 없음||
|결함 허용|||해당사항 없음|||
<caption>VLAN 워크시트</caption>
