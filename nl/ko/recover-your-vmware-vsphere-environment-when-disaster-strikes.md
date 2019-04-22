---
copyright:
  years: 2014, 2018
lastupdated: "2018-06-22"

subcollection: virtualization

keywords: disaster recovery
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# VMware vSphere 환경 복구
{: #recovering-your-vmware-vsphere-environment}

데이터 백업은 현재 데이터 안전 및 무결성을 유지하기 위한 기본 방법입니다. 이상적이고 완벽한 세상에서는 데이터 백업이 필요하지 않을 것입니다. 그러나 불행히도 현실에서는 사고와 재해가 발생합니다. 이 경우 시스템 중단 시간을 최소화하고 가능한 한 빨리 백업 및 실행하는 것이 가장 중요합니다.

과거에는 고성능 스토리지나 테이프에 백업을 저장하는 것이 현실적인 해결책이었습니다. 오늘날 기업들은 물리적 온프레미스 스토리지를 보강하거나 Object Storage로 교체하여 물리적 온프레미스 스토리지에 소요되는 자본금과 운영 비용을 절감하는 방안을 모색하고 있습니다. 하지만 Object Storage를 사용하는 경우 백업이 온프레미스 스토리지에 저장되지 않으므로 백업에서 복원하는 것이 더 이상 간단히 필요한 백업을 찾은 후 이 백업을 바탕으로 복원하는 문제가 아닙니다. 다행스럽게도, Object Storage에 저장된 백업을 복원하는 번거로운 과정을 상당 부분 처리해 주는 하이브리드 솔루션이 있습니다. 이 솔루션은 다음으로 구성됩니다.

* {{site.data.keyword.cos_full}}
* NetApp의 NetApp AltaVault 클라우드 스토리지 게이트웨이(이전 Riverbed SteelStore)
* Veeam Backup & Replication 소프트웨어

재해 이벤트가 발생하고 기본 환경이 오프라인으로 변경되면 보조 복구 사이트가 이를 복구하기 위해 {{site.data.keyword.cloud}} 내에 작성될 수 있습니다. 기본 환경의 이전 백업에 액세스하기 위해 {{site.data.keyword.cos_full_notm}}와 통신하기 위해 Veeam Backup & Replication 소프트웨어 및 AltaVault 클라우드 통합 스토리지 어플라이언스를 배치할 수 있습니다. 소프트웨어는 저장소 어플라이언스와 연결하여 백업을 새 위치로 복원하고, 기본 환경을 다시 온라인으로 전환하여 가동 시간의 추가 손실을 방지합니다.

온프레미스 IP 주소는 {{site.data.keyword.cloud_notm}} 복구 사이트에서 가상 머신(VM)을 복원할 때 유지됩니다. 따라서 복구에 성공하면 IP 주소를 변경하거나 BYOIP 인프라를 디자인하는 것이 중요합니다. 두 경우 모두 도움이 필요하면 IBM 담당자에게 문의하십시오.

다음 프로시저는 NetApp AltaVault Cloud Storage Gateway 어플라이언스, {{site.data.keyword.cloud_notm}} 인프라 및 Veeam Backup & Replication을 조화롭게 사용하여 실패한 VMware vSphere 환경을 완전히 복원하는 데 초점을 맞춥니다. 소프트웨어는 환경에 대해 적어도 하나의 백업이 3가지 선행 기술을 사용하여 수행되었으며 복구 시에 {{site.data.keyword.cos_full_notm}}에 상주한다고 가정합니다. 

"AltaVault 어플라이언스"는 두 가지 방법으로 식별됩니다. 첫 번째는 "온프레미스 AltaVault 어플라이언스"로, 이는 실패하여 백업이 복구되거나 복원된 원래 온프레미스 AltaVault 어플라이언스를 의미합니다. 두 번째는 "{{site.data.keyword.cloud_notm}} AltaVault 어플라이언스"로, 이는 실패한 AltaVault 어플라이언스에서 백업을 복구하는 데 사용된 AltaVault 어플라이언스를 의미합니다. {{site.data.keyword.cloud_notm}} AltaVault 어플라이언스는 유틸리티 서버를 사용하여 vSphere 환경에 배치됩니다. 

## 하이브리드 솔루션 도입
{: #introducing-a-hybrid-solution}

Veeam Backup & Replication은 NetApp AltaVault 클라우드 통합 스토리지 어플라이언스 및 {{site.data.keyword.cos_full_notm}}를 포함하는 하이브리드 솔루션을 가능하게 합니다. 주요 관심사는 가상 환경의 백업을 작성, 유지보수 및 복원하는 것입니다. NetApp AltaVault 클라우드 통합 스토리지 어플라이언스는 사설 또는 공용 클라우드와 온프레미스 환경을 완벽하게 통합하기 위한 소프트웨어 솔루션입니다. 함께 사용되는 경우, Veeam Backup & Replication은 AltaVault 클라우드 통합 스토리지 어플라이언스가 로컬 온프레미스 스토리지에 저장하고 동시에 {{site.data.keyword.cos_full_notm}}에 복제하는 백업을 작성합니다. 또한 {{site.data.keyword.cloud}} 종량과금제 가격 모델 및 CDN(Content Delivery Network)과의 완전한 통합은 지리적으로 분산된 24개의 노드를 통해 데이터를 저장하고 분배하는 기능을 제공합니다.

### IBM Cloud Object Storage
{: #ibm-cloud-object-storage}

{{site.data.keyword.cos_full_notm}}에 백업된 데이터 파일에 액세스하기 위해 다음 방법으로 데이터 파일이 요청됩니다.

* 이미 온프레미스로 위치해 있는 AltaVault 어플라이언스
* {{site.data.keyword.cloud_notm}} 내에 있는 보조 AltaVault 어플라이언스

이 프로시저를 수행하면 {{site.data.keyword.cloud_notm}} 내의 보조 AltaVault 어플라이언스를 배치하여 실패한 기본 온프레미스 AltaVault 어플라이언스를 복구하십시오. 

{{site.data.keyword.cloud_notm}} 환경은 보조 AltaVault 어플라이언스를 배치 및 실행하기 위한 로컬 스토리지가 있는 단일 ESXi 호스트로 구성되어 있습니다. 인프라는 {{site.data.keyword.cloud_notm}} Virtual Server Instance(VSI) 내에서 vCenter Server에서 관리되는 단일 ESXi 호스트로 기본 단일 사이트 아키텍처를 대표합니다. 

공유 스토리지를 필요로 하는 보다 실질적인 인프라가 있거나 이러한 기능을 지원해야 하는 경우

* vSphere High Availability(HA)
* vSphere Distributed Resource Scheduler(DRS)
* vSphere vMotion

#### 전제조건
{: #prerequisites-ibm-cloud-object-storage}

계속하기 전에 다음 요구사항을 충족하는지 확인하십시오. **참고:** 이 예의 경우 AltaVault AVA-v8 어플라이언스의 평가판은 AltaVault Cloud-Integrated Storage 4.1과 함께 사용되었습니다. 

* {{site.data.keyword.cloud_notm}} VSI 내의 vCenter 서버에 의해 관리되는 단일 ESXi 호스트로 구성되어 있는지 확인하십시오. 
* VMware Sphere 기술과 vSphere ESXi 환경 관리에 대한 지식이 있어야 합니다. 이러한 지식에는 네트워킹 및 스토리지를 포함하는 하드웨어 리소스의 지정, vSphere 웹 클라이언트, vSphere 클라이언트의 사용 및 이외의 많은 지식이 포함됩니다.

#### 2개의 휴대용 사설 네트워크 주문
{: #order-two-portable-private-networks}

AltaVault는 네트워크 인터페이스가 환경 내의 서로 다른 네트워크에 있어야 합니다. 다음 단계를 사용하여 {{site.data.keyword.slportal_full}}를 통해 두 개의 휴대용 사설 네트워크를 주문하십시오.

1. 고유 인증 정보를 사용하여 [{{site.data.keyword.slportal_full}} ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://control.softlayer.com/){: new_window}에 액세스하십시오.
2. **계정 > 주문**을 클릭하십시오. 
3. **네트워크** 섹션을 선택하고 **Subnets/IPs > 주문**을 선택하십시오.
4. 드롭 다운 메뉴에서 **휴대용 사설**을 선택하십시오. 휴대용 사설을 선택하면 표시할 휴대용 사설 IP 주소의 수를 선택할 수 있는 옵션이 트리거됩니다. **참고:** {{site.data.keyword.cloud_notm}}에서는 각 휴대용 사설 네트워크에 대한 주소 블록에 적어도 3개의 IP 주소를 자동으로 예약합니다. 예약된 주소는 네트워크 주소, 게이트웨이 주소 및 브로드캐스트 주소용입니다. 이러한 주소는 가상 LAN(VLAN)에 직접 바인드되어야 합니다. <!--If your IBM Cloud pod supports Hot Standby Ready Protocol (HSRP), then the number of usable IP addresses is reduced. For instance, if you select **4 Portable Private IP Addresses** will yield only one usable IP address or zero if the pod supports HSRP.--> 휴대용 IP 주소에 대한 자세한 정보는 [서브넷 및 IP 시작하기](/docs/infrastructure/subnets?topic=subnets-getting-started-subnets-ips#getting-started-subnets-ips)의 내용을 참조하십시오.
5. 휴대용 사설 IP 주소의 수를 선택한 후에 **계속**을 클릭하십시오. 
6. 휴대용 사설 IP 주소가 라우팅되는 VLAN을 선택하고 **계속**을 클릭하십시오.
7. 필수 연락처 정보를 입력하고 **주문**을 클릭하십시오.
8. 주문 프로세스를 반복하여 두 번째 요청된 휴대용 사설 네트워크를 구입하십시오.
9. 할당된 후에 휴대용 사설 네트워크 및 IP 주소를 보려면 {{site.data.keyword.slportal}} 페이지에서 **네트워크 > IP 관리 > 서브넷**을 클릭하십시오. IP 주소 지정을 추적하는 것이 좋습니다. IP 주소가 지정된 시스템을 반영하려면 각 IP 주소에 대한 **서브넷** 페이지의 **참고** 섹션을 사용하십시오.

#### vSphere 구성
{: #configuring-vsphere}

휴대용 사설 네트워크의 추가를 반영하도록 vSphere를 구성하고 논리적으로 분리되도록 해야 합니다. 

1. vSphere 환경으로 이동하고 가상 머신 포트 그룹을 작성하여 두 개의 휴대용 IP 블록 중 하나의 추가를 반영하십시오. 

가상 머신 포트 그룹은 **공유 네트워크**로 레이블 지정됩니다. 하나의 휴대용 IP 블록은 기본(1차) 가상 머신 포트 그룹 상에 배치되는 VM용으로 사용됩니다. 다른 하나는 AltaVault 어플라이언스와 내보낸 CIFS(Common Internet File System)/SMB(Server Message Block) 마운트 지점 간의 데이터 전송에 사용됩니다. 

## AltaVault Cloud Storage Gateway
{: #altavault-cloud-storage-gateway}

AltaVault를 사용하면 {{site.data.keyword.cos_full_notm}}용 REST API를 사용하여 스크립트 또는 애플리케이션을 기록할 필요 없이 온프레미스 환경을 클라우드와 통합할 수 있습니다. AltaVault는 프론트 엔드에서 CIFS/SMB 또는 NFS(Network File System) 마운트 지점을 노출하고 백엔드의 {{site.data.keyword.cos_full_notm}} 인터페이스에 안전하게 연결합니다. 그러면 마운트 지점을 마운트하거나 가리키고 데이터를 클라우드에 복사하거나 클라우드로부터 안전하게 환경을 복구/복원할 수 있습니다. <!-- , as shown in Figure 1.-->

<!-- ![Figure 1](images/veeam_restore_fig2.png)
<br/>`Figure 1: AltaVault end-to-end flow with Veeam and IBM Cloud`-->

### 재해 복구 구성에서 AltaVault 배치
{: #deploying-altavault-in-a-disaster-recovery-configuration}

다음 단계를 수행하여 {{site.data.keyword.cos_full_notm}}와 함께 재해 복구 솔루션으로 AltaVault를 배치하십시오.

#### 시작하기 전에
{: #before-you-get-started-deploying-altavault-in-a-disaster-recovery-configuration}

진행하기 전에 다음 전제조건이 충족되는지 확인하십시오.

* AltaVault Virtual Appliance의 사본을 얻고 유틸리티 서버에 상주하는지 확인하십시오. OVA 파일 확장자를 가진 단일 파일입니다. 어플라이언스의 NetApp 담당자에게 문의하거나 [NetApp AltaVault 웹 사이트 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://www.netapp.com/us/products/protection-software/altavault/){: new_window}에서 90일 평가판을 다운로드하십시오.
* AltaVault 어플라이언스에 대해 최소 CPU, 메모리, 디스크 공간 요구사항에 맞는 기존의 온프레미스 vSphere ESXi 5.5 환경이 구현되어 있습니다. 평가판을 사용하는 경우, 4개의 가상 CPU(vCPU), 24GB의 메모리, 그리고 최대 8TB의 디스크 공간 등의 리소스 요구사항이 있습니다. 
* vSphere 환경 내에서 사용할 수 있는 두 개의 10Gbps 네트워크 인터페이스 컨트롤러(NIC)를 가집니다. 한 NIC는 데이터 액세스에 사용되고, 다른 하나는 {{site.data.keyword.cos_full_notm}} 복제에 사용됩니다.
* vSphere 환경 내에 정의되어 있는 두 개의 NIC에 해당하는 두 개의 휴대용 사설 네트워크를 가집니다. 복제 네트워크는 데이터 액세스 네트워크와 동일한 네트워크에 지정될 수 없으므로 이렇게 할 경우 라우팅 루프를 작성할 수 있습니다.
* {{site.data.keyword.cos_full_notm}} 인증 정보를 가집니다. 이러한 인증 정보에는 {{site.data.keyword.cloud_notm}} 사용자 이름, {{site.data.keyword.cloud_notm}} 사용자 이름 및 {{site.data.keyword.cos_full_notm}} 사용자 이름과 연관된 API 키가 포함됩니다. 
* 온프레미스 AltaVault 어플라이언스에서 데이터 복제를 일시중단하거나, 어플라이언스 환경이 여전히 손상되지 않은 경우 {{site.data.keyword.cloud_notm}}에서 백업 아카이브에 액세스하는 데 사용되는 컨테이너/버켓에서 연결을 끊습니다.
  * 복제를 일시중단 또는 중지하려면 다음 두 가지 방법 중 하나를 사용하십시오. 
    * 온프레미스 AltaVault 어플라이언스를 끄십시오. 
    * AltaVault 어플라이언스 웹 관리 콘솔으로 이동하여 **스토리지 > 클라우드 설정 > 복제**를 클릭하십시오. **복제 일시중단**을 선택하십시오.
    * 온프레미스 AltaVault 어플라이언스의 구성 파일 사본을 확보하거나 URL을 통해 액세스하도록 하십시오. 구성 파일은 .tar 파일입니다. 
  * AltaVault 어플라이언스 웹 관리 콘솔로 이동하고 **구성 > 설정 마법사 > 구성 내보내기 > 구성 내보내기**를 클릭하십시오. 구성 파일(.tar file)을 저장하고 유틸리티 서버에 이를 전송하거나 URL을 통해 이에 액세스할 수 있도록 하십시오. 
    * VMware Sphere 기술과 vSphere ESXi 환경 관리에 대한 지식이 있어야 합니다. 이러한 지식에는 네트워킹 및 스토리지 같은 하드웨어 리소스의 지정, vSphere 웹 클라이언트, vSphere 클라이언트의 사용 및 이외의 많은 지식이 포함됩니다.

### AltaVault OVA 배치
{: #deploying-altavault-ova-disaster}

모든 전제조건이 충족되고 나면 유틸리티 서버를 사용하여 AltaVault OVA를 vSphere 환경에 배치하십시오. OVA 배치에 대한 지시사항은 [NetApp AltaVault 설치 및 서비스 안내서 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}의 내용을 참조하십시오. AltaVault의 평가판은 4개의 가상 CPU, 6GB의 메모리 및 150GB의 기본 디스크로 구성됩니다. 설치 및 서비스 안내서를 사용하여 다음 단계를 수행하십시오.

1. 메모리 양을 24GB로 늘리십시오.
2. 중복 제거된 백업 데이터를 저장하려면 크기가 8TB 이하인 보조 디스크 스토리지 디바이스를 추가하십시오.
3. 메모리 및 디스크 구성이 변경된 후 AltaVault 어플라이언스에 다른 휴대용 사설 네트워크를 지정하십시오. 

NIC에 다음과 같은 인터페이스 기능이 지정됩니다.

* **Primary**: AltaVault 어플라이언스 관리 및 클라우드로의 데이터 복제에 사용됩니다. 예제 환경에서 **기본 네트워크** 포트 그룹이 지정됩니다.
* **e0a**: AltaVault 어플라이언스에서 클라우드로 데이터를 복제하는 데 사용되는 인터페이스입니다.
* **e0b**: SMB/CIFS 또는 NFS 공유를 위해 마운트 지점을 내보내는 데 사용되는 인터페이스입니다. 예제 환경에서 **공유 네트워크** 포트 그룹이 지정됩니다.
* **e0c**: SMB/CIFS 또는 NFS 공유를 위해 마운트 지점을 내보내는 데 사용되는 선택적 인터페이스입니다. 

이 프로시저의 예제 구성에서는 CIFS/SMB 마운트 지점을 내보내기 위해 AltaVault 어플라이언스가 **기본** 인터페이스를 복제-클라우드 인터페이스 및 **e0b** 인터페이스로 사용합니다. **참고:** 두 개의 CIFS/SMB 공유 및 NFS 공유를 사용하여 동일한 데이터에 액세스할 수 없습니다. 사용되는 프로토콜은 데이터를 클라우드에 복제하기 위해 온프레미스 AltaVault 어플라이언스와 함께 사용되는 프로토콜과 동일해야 합니다. 

<!-- ![Figure 2](images/veeam_restore_fig3.png)
<br/>`Figure 2: IBM Cloud setup with VMware`-->

VM 설정의 AltaVault 어플라이언스 및 구성의 배치에 대한 자세한 정보는 [NetApp AltaVault 설치 및 서비스 안내서 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}의 내용을 참조하십시오.

### AltaVault 어플라이언스 구성
{: #configuring-the-altavault-appliance}

1. 구성 후에 AltaVault VM의 전원을 켤 수 있습니다. AltaVault 어플라이언스가 보조 스토리지 디스크를 형식화함에 따라 VM을 시작하는 데 시간이 걸릴 수 있습니다. 

2. 어플라이언스가 부트 프로세스를 완료한 후에 **admin**을 **사용자 이름**으로, **password**를 **비밀번호**로 사용하여 AltaVault 콘솔에 로그인하십시오. 초기 구성을 완료한 후에 이러한 인증 정보를 변경할 수 있습니다. 표 1: AltaVault 초기 구성 값을 참조하십시오. 

3. 어플라이언스에 로그인하고 나면 구성을 위해 마법사를 사용할지 여부를 묻는 메시지가 표시됩니다. *y*를 입력하십시오.
4. 마법사를 입력한 후에 표 1의 정보를 사용하십시오. 
5. **Enter**를 눌러 변경사항을 저장하십시오.

|질문|답변|
|---|---|
|1단계: 관리 비밀번호|새 관리 비밀번호를 입력하십시오(**password**일 수 없음).|
|2단계: 호스트 이름|적절한 호스트 이름을 입력하십시오. |
|3단계: 기본 인터페이스에서 DHCP 사용|**n** 또는 **no**를 입력하십시오.|
|4단계: 기본 IP 주소|기본 네트워크 IP 주소 입력. 예제 구성에서 이는 관리 인터페이스에 사용되는 네트워크입니다(예: 10.120.108.132).|
|5단계: 넷마스크|넷마스크 입력(예: 255.255.255.192).|
|6단계: 기본 게이트웨이|기본 게이트웨이 입력(예: 10.120.108.129).|
|7단계: 기본 DNS 서버|사용자 환경의 기본 도메인 이름 시스템(DNS) 서버 입력|
|8단계: 도메인 이름|사용자 환경의 도메인 이름을 입력하십시오(예: testenv.org).|
{: caption="표 1. AltaVault 초기 구성 값" caption-side="top"}

### IBM Cloud Object Storage에 대한 AltaVault 구성
{: #configuring-altavault-for-ibm-cloud-object-storage}

설치 후에는 AltaVault 어플라이언스를 {{site.data.keyword.cos_full_notm}} 서비스에 연결해야 합니다. 온프레미스 구성에서 공용 DNS는 오브젝트 스토리지 서비스에 연결하는 데 사용됩니다. 이 구성에서는 내부 DNS 이름이 대신 사용됩니다.

1. 웹 브라우저를 열고 AltaVault 어플라이언스의 IP 주소를 입력하십시오.
2. admin 인증 정보를 사용하여 콘솔에 로그인하십시오. 초기 로그인 시에 **마법사 대시보드**가 표시됩니다. 
3. **시스템 설정**을 선택하고 모든 네트워크 설정이 올바른지 확인하고 시간대를 조정하여 사용자 환경의 시간대를 반영하십시오.
4. **다음 > 저장 및 적용 > 종료**를 선택하십시오. **마법사 대시보드**로 돌아가 현재 브라우저 세션에서 로그아웃하십시오. 
5. AltaVault 어플라이언스에 다시 로그인하고 **설정 > 설정 마법사 > 구성 가져오기**를 클릭하고 온프레미스 AltaVault 어플라이언스의 구성 파일 위치를 지정하십시오. 
6. 클라우드 및 이메일 설정 등 공통으로 공유되는 설정만 가져오는 **공유 데이터만 가져오기**를 선택하십시오. 
7. 설정해 둔 경우 온프레미스 AltaVault 어플라이언스를 작성하는 동안 지정된 암호화 키에 대한 비밀번호 문구를 **키 비밀번호 문구** 텍스트 상자에 입력하십시오.
8. **구성 가져오기 > 종료**를 클릭하십시오.

<!--The AltaVault appliance web console displays a message indicating a successful import. If the import was not successful, verify that the configuration file is in the correct format and that the passphrase is correct before trying again.-->

온프레미스 AltaVault 어플라이언스 구성을 {{site.data.keyword.cloud_notm}} AltaVault 어플라이언스로 가져왔습니다. 

다음 단계를 사용하여 {{site.data.keywrod.cloud_notm}} AltaVault 어플라이언스의 클라우드 설정을 수정하여 사설 네트워크를 통해 {{site.data.keyword.cos_full_notm}} 서비스에 액세스할 수 있도록 하십시오.

1. AltaVault에서 **스토리지 > 클라우드 설정 > 클라우드**로 이동하고 {{site.data.keyword.cos_full_notm}} 서비스의 사설 주소를 반영하도록 호스트 이름을 수정하십시오. 
  * 사설 네트워크 이름 구문은 <location>.objectstorage.service.networklayer.com이며, 여기서 *<location>*은 축약 데이터 센터 이름을 지정합니다(예: 멜버른 01 데이터 센터의 경우 mel01).
2. **적용**을 클릭하십시오. AltaVault 어플라이언스가 {{site.data.keyword.cos_full_notm}} 서비스에 연결을 시도합니다. 이 연결 시도가 실패하면 다시 연결을 시도하기 전에 클라우드 제공자 설정이 올바른지 확인하십시오.

{{site.data.keyword.cos_full_notm}}에 연결하고 나면 어플라이언스가 {{site.data.keyword.cos_full_notm}}에서 원래 백업의 메타데이터 컨텐츠를 동기화하기 위한 복구 모드로 배치되도록 해야 합니다.

AltaVault 어플라이언스를 복구 모드로 전환하려면 다음 단계를 사용하십시오.

AltaVault 어플라이언스는 {{site.data.keyword.cos_full_notm}}에서 데이터를 동기화합니다. **참고:** 동기화를 완료하는 데 걸리는 시간은 복원 중인 백업의 크기와 수에 따라 다릅니다. 

1. AltaVault 어플라이언스로 이동하여 로그인하고 그림 3에 표시된 명령을 입력하십시오. <br/>![그림 3](images/veeam_restore_fig5.png)<br/>`그림 5: 복구 모드 명령`
2. 동기화 프로세스가 완료된 후 AltaVault 어플라이언스 웹 콘솔로 돌아가십시오.
3. **스토리지 최적화 서비스**가 **실행 중**이고 **상태**가 **준비됨**인지 확인하십시오. **상태**가 **준비**로 변경되려면 몇 분 정도 걸릴 수 있습니다.

### AltaVault에서 CIFS/SMB 마운트 지점 구성 
{: #configure-the-cifs-smb-mount-point-in-altavault}

{{site.data.keyword.cos_full_notm}}에 대한 내부 연결이 설정되고 나면 CIFS/SMB 마운트 지점에 액세스하도록 **e0b** 인터페이스를 구성하십시오. 

1. AltaVault 웹 콘솔에서 **설정 > 데이터 인터페이스**로 이동하십시오.
2. **e0b** 인터페이스를 펼치고 필요한 경우 **데이터 인터페이스 사용**을 선택하십시오.
3. CIFS/SMB 공유를 마운트하는 데 사용할 IP 주소, 서브넷 마스크 및 게이트웨이를 입력하십시오. 기본 인터페이스에 사용되는 서브넷과는 다른 서브넷을 사용하도록 하십시오.
4. 기본 **MTU** 값 **1500바이트**를 그대로 두십시오.
  * 기본 최대 전송 단위(MTU)는 1,500이지만 점보 프레임을 사용하는 경우에는 9,000으로 변경할 수 있습니다. ESXi 호스트 및 물리적 인프라는 점보 프레임을 지원해야 합니다. 기본적으로 {{site.data.keyword.cloud_notm}}는 구성 변경이 필요하지 않은 9,000바이트의 MTU 사이즈를 지원합니다.
5. **적용**을 클릭하십시오. 

AltaVault 어플라이언스는 이제 최소한 자체 {{site.data.keyword.cos_full_notm}} 및 Veeam Backup & Replication 사이의 통신을 허용하도록 구성됩니다.

## Veeam Backup & Replication
{: #veeam-backup-replication-recover}

Veeam Backup & Replication 소프트웨어는 VM 및 해당 데이터에 대한 전체 백업, 복제 및 복구 기능을 제공합니다. Veeam Backup &TWB; Replication은 AltaVault Cloud Gateway Appliance와 완전히 통합되어 완벽한 백업 및 복구 작업을 수행할 수 있습니다.

### Veeam Backup & Replication 배치
{: #deploy-veeam-backup-replication}

Veeam Backup & Replication 버전 8의 평가판이 예에서 사용됩니다. 

#### *전제조건*
{: #prerequisites-deploy-veeam-backup-replication}

배치를 계속하기 전에 다음 전제조건이 충족되었는지 확인하십시오.

* IBM Cloud Object Storage 및 Veeam Backup & Replication과 함께 사용하도록 구성된 기존 AltaVault 어플라이언스를 확보하십시오. 
* 단일 실행 파일인 VMware 환경에 대한 Veeam Backup & Replication의 사본을 확보하십시오. [30일 평가판 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}의 복사 또는 다운로드는 Veeam 담당자에게 문의하십시오.
* Veeam Backup & Replication과 함께 사용할 라이센스 파일을 가져오십시오. 대부분의 경우, 이 파일은 Veeam Backup & Replication을 다운로드하는 데 사용된 이메일 주소로 이메일로 보내집니다. 이 파일을 수신하지 못한 경우, Veeam 담당자에게 문의하십시오.<br/><br/>라이센스 파일은 전체 Veeam Backup & Replication 기능을 활성화하는 데 사용됩니다. 이 파일이 프로그램 설치 동안 제공되지 않으면 모든 기능을 30일 평가판으로 되돌립니다.
* 표 2의 스펙과 함께 IBM Cloud에 프로비저닝된 가상 서버 인스턴스(VSI)가 있어야 합니다. **참고:** 설치된 운영 체제는 64비트 버전이어야 합니다. 

||최소값|권장사항|
|---|---|---|
|**OS**|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|<ul><li>Windows Servers 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>WIndows 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|
|**코어 또는 vCPU 수**|2|4|
|**메모리**|각 동시 백업 및 복원 작업의 경우 4GB의 기본 RAM 및 500MB.|각 동시 백업 및 복원 작업의 경우 16GB의 기본 RAM 및 4GB.|
|**디스크 공간**|2GB 제품 설치: 게스트 시스템 카탈로그 폴더에 대해 100VM당 10GB(지속적 데이터).|2GB 제품 설치: 게스트 시스템 카탈로그 폴더에 대해 100VM당 10GB(지속적 데이터).|
|**네트워크**|온사이트 백업 및 복제의 경우 1Gbps LAN, 오프사이트 백업 및 복제의 경우 1Mbps WAN.|온사이트 백업 및 복제의 경우 1Gbps LAN, 오프사이트 백업 및 복제의 경우 1Mbps WAN.|
{: caption="표 2. {{site.data.keyword.cloud_notm}} VSI에 대한 시스템 요구사항" caption-side="top"}

AltaVault 어플라이언스 및 vSphere 환경과 동일한 데이터 센터 내의 VSI를 프로비저닝하십시오. VSI는 AltaVault 어플라이언스 및 vCenter에 액세스할 수 있어야 합니다.

### Veeam Backup & Replication 설치
{: #installing-veeam-backup-replication-disaster}

모든 요구사항이 충족되고 나면 다음 단계를 사용하여 Veeam Backup & Replication을 {{site.data.keyword.cloud_notm}} VSI에 설치하십시오.

1. 프로그램 실행 파일을 두 번 클릭하고 **Veeam Backup & Replication – 설치**를 클릭하십시오. 설정 마법사가 열립니다. 
* **다음**을 클릭하고 **라이센스 계약의 조항에 동의함**을 선택하십시오.
* **다음**을 클릭하고 Veeam Backup & Replication 배치 아래에서 얻은 라이센스 파일의 위치를 지정하십시오.
* **다음**을 클릭하고 **프로그램 기능** 화면에서 설치 위치 및 설치하려는 Veeam Backup & Replication 컴포넌트를 선택하십시오. **참고:** **Veeam Backup & Replication** 및 **Veeam Backup 카탈로그**는 필수 컴포넌트입니다. 
* **다음**을 클릭하십시오. <br/>설정 마법사는 모든 필수 프로그램 프레임워크 및 지원 컴포넌트가 설치되는지 확인하기 위해 일련의 검사를 실행합니다. 컴포넌트가 누락된 경우 이를 설치하도록 설정 마법사가 제공됩니다. <!--Click **Install** if this is the case.-->
2. 모든 컴포넌트가 시스템 검사를 **통과**했는지 확인하고 **다음**을 클릭하십시오.
3. Veeam Backup Service를 실행할 **서비스(사용자) 계정**을 선택하십시오. 기본 서비스 계정은 **로컬 시스템 계정**입니다. **다음**을 클릭하십시오. 
4. Veeam Backup & Replication 데이터베이스를 작성 및 저장하는 데 사용할 **SQL 서버 인스턴스**를 선택하십시오. 자세한 정보는 데이터베이스 관리자에게 문의하십시오.**다음**을 클릭하십시오. 
5. **카탈로그 서비스 포트**(9393) 및 **Veeam Backup 서비스 포트**(9392)를 입력하십시오. **다음**을 클릭하십시오. 
6. 게스트 파일 시스템 카탈로그(지속적 데이터) 및 vPower NFS 쓰기 캐시(비지속적 데이터)를 저장할 디렉토리를 선택하십시오. **다음**을 클릭하십시오. 
7. 모든 설정 및 값이 올바른지 확인하고 **설치**를 클릭하십시오. 설치가 완료되면 **완료**를 클릭하십시오.

### Veeam Backup & Replication 구성 
{: #configuring-veeam-backup-replication}

Veeam Backup & Replication 설치 후에는 AltaVault 어플라이언스에 연결할 준비가 됩니다.

1. Veeam Backup & Replication을 실행하십시오.
2. 화면의 왼쪽 하단에서 **백업 인프라**를 클릭하십시오.
3. **백업 인프라** 창에서 **관리 서버 > 관리 서버**를 클릭하십시오.
4. 상단 리본 메뉴에서 **서버 추가**를 클릭하고 **VMware vSphere**를 두 번 클릭하십시오.
5. vSphere 서버의 사설 IP 주소를 입력하고 **다음**을 클릭하십시오.
6. 이전에 지정된 vSphere 서버에 관리자 권한을 가진 로컬 계정의 계정 인증 정보를 입력하십시오. **참고:** 계정 사용자 이름은 도메인 계정의 경우 DOMAIN\USER 형식, 로컬 계정의 경우 HOST\USER 형식이어야 합니다. 계정을 추가하려면 **추가**를 클릭하고 적절한 사용자 이름 및 비밀번호를 입력하십시오. 네트워크 관리자가 지시하지 않는 한, VMware 웹 서비스 포트를 변경하지 마십시오. <!-- otherwise during [Veeam Backup & Replication installation](#_Installing_Veeam_Backup).--> **다음**을 클릭하십시오. 
  * Veeam Backup & Replication은 vSphere 서버에 대한 연결을 시도합니다. 연결 시도에 실패하면 연결을 다시 시도하기 전에 계정이 존재하고 vSphere 서버에 관리자 권한이 있는지 확인하십시오. 
7. **완료**를 클릭하십시오.
8. **관리 서버 > VMware vSphere**를 클릭하여 vSphere 서버가 성공적으로 추가되었는지 확인하십시오.

### 백업 저장소를 Veeam Backup & Replication에 추가
{: #adding-a-backup-repository-to-veeam-backup-replication-disaster}

다음 단계를 수행하여 AltaVault 어플라이언스에 복원된 백업을 저장할 새 백업 저장소를 작성하십시오. **참고:** Veeam Backup & Replication 백업 서버는 Windows를 실행 중인 머신에 호스트되어야 하므로 CIFS/SMB 공유가 예에 사용됩니다. {{site.data.keyword.cloud_notm}} AltaVault 어플라이언스에서 백업을 복원하는 데 사용되는 공유 프로토콜은 CIFS/SMB 공유가 사용되는 온프레미스 AltaVault 어플라이언스에서 동일한 백업에 사용되는 공유 프로토콜과 동일합니다. 

1. 왼쪽 하단에서 **백업 인프라 > 백업 저장소**를 클릭하십시오.
2. 상단 리본 메뉴에서 **저장소 추가**를 클릭하십시오.
3. 저장소에 고유한 **이름**을 제공하고 적절한 **설명**을 제공하십시오. **다음**을 클릭하십시오. 
4. 온프레미스 AltaVault 어플라이언스에 사용되는 공유 유형에 해당하는 공유 유형을 선택하십시오. 예: 온프레미스 AltaVault 어플라이언스에 사용되는 공유 유형이 CIFS/SMB인 경우, **공유 폴더**를 선택하십시오.
5. CIFS/SMB 공유의 백업이 AltaVault 어플라이언스에 복원되도록 하려는 위치를 지정하십시오. 이 공유는 온프레미스 AltaVault 어플라이언스에서 처리된 백업을 통한 공유와 동일한 이름을 가집니다. 예를 들어 이름이 `cifs_test1`인 공유가 온프레미스 AltaVault 어플라이언스의 백업에 사용되는 경우, {{site.data.keyword.cloud_notm}} AltaVault 어플라이언스의 `cifs_test1` 공유 위치를 지정하십시오. 
  * 웹 브라우저를 열고 AltaVault 어플라이언스의 IP 주소를 입력하여 위치를 판별하십시오. 
  * **스토리지 > CIFS**로 이동하고 공유의 **경로 공유**를 기록해 두십시오. **참고:** 이는 공유의 로컬 경로와 동일하지 않습니다. 공유 경로 형식은 `\\<AltaVault appliance hostname>\<share name>`입니다. 공유 경로의 AltaVault 어플라이언스 호스트 이름을 AltaVault 어플라이언스의 e0b 네트워크 인터페이스(공유의 마운트 지점)의 IP 주소로 대체하십시오. <br/>
   * AltaVault 어플라이언스 관리 창에서 **설정 > 데이터 인터페이스**를 클릭하여 **e0b** 인터페이스의 IP 주소를 찾으십시오. IFS/SMB 마운트 구성에 대한 자세한 정보는 AltaVault의 CIFS/SMB 마운트 지점을 참조하십시오.
   * Veeam Backup & Replication에 지정된 공유 경로는 `\\restore-appliance\cifs_test1`이 아닌 `\\10.120.108.133\cifs_test1`이 됩니다.
6. Veeam Backup & Replication으로 돌아가 마우스 지점의 공유 경로를 **공유 폴더** 필드에 입력하고 **다음**을 클릭하십시오. Veeam Backup & Replication은 마운트 지점과의 연결을 설정하려고 시도합니다. 연결에 실패하면 다시 시도하기 전에 다시 돌아가서 AltaVault 어플라이언스에 대한 네트워크 설정이 올바른지 확인하십시오.
7. **새 백업 저장소 화면**에서 사용할 수 있는 리소스 수에 대한 **동시 최대값 제한** 태스크에 값을 입력하십시오. 이 값은 백업 프록시가 선택된 공유에 보낼 수 있는 최대 태스크 수입니다. 기본 동시 태스크 수는 4입니다. AltaVault의 경우 사용자가 5개의 동시 태스크로 시작하여 리소스가 허용하는 대로 이를 늘리거나 줄이도록 권장합니다.
8. **다음**을 클릭하십시오. 
9. **vPower NFS** 화면에서 선택적 vPower NFS 설정을 지정하십시오. **vPower NFS 서버 사용** 선택란을 선택하지 않은 경우, Veeam Backup & Replication이 복구 및 복구 확인을 위해 vPower를 사용합니다. **다음**을 클릭하십시오. 
10. VM 백업의 수동 선택을 사용하지 않는 한 **자동으로 기존 백업 가져오기**를 선택하십시오.
11. 모든 설정이 올바른지 확인하고 **다음**을 클릭하십시오.
* 마법사를 종료하려면 **완료**를 클릭하십시오.
12. 계속하려면 **예**를 클릭하십시오.

Veeam Backup & Replication이 구성되며, 백업 복원을 시작할 수 있습니다.

### 환경 복원
{: #restoring-the-environment}

VM 복원을 전체 복원하려면 다음 단계를 따르십시오. 

1. 화면의 왼쪽 하단에서 **백업 & 복원**을 클릭하십시오.
2. 상단 리본 메뉴에서 **복원**을 클릭하십시오.
3. **전체 VM(등록 포함)**을 선택하십시오. **참고:** **복제본에서 복원** 아래에서 선택하지 마십시오. **다음**을 클릭하십시오. 
4. **VM 추가 > 백업에서**를 클릭하십시오. 온프레미스 AltaVault 어플라이언스와 선택된 CIFS/SMB 공유를 통해 {{site.data.keyword.cos_full_notm}}에 이전에 복제된 VM을 볼 수 있습니다. **추가 > 다음**을 클릭하십시오.
5. **기본 위치에 다른 설정으로 복원**을 선택하여 VM을 새 위치 <!--[because the original location of the VM(s) failed]-->에 복원하십시오. **다음**을 클릭하십시오. 
6. **호스트** 화면에서 복원 중인 각 VM에 대한 대상 호스트를 선택하십시오. VM을 선택한 후 **호스트**를 클릭하십시오.
7. **호스트 선택**에서 대상 호스트를 선택하고 **확인**을 클릭하십시오.<br/>온라인 상태이고 데이터를 받을 준비가 되어 있는지 확인하기 위해 Veeam Backup & Replication은 대상 호스트에 대한 연결을 시도합니다. 이번 시도가 실패하면 모든 네트워크 설정이 올바른지 확인한 후 다시 시도하십시오. 확인이 완료되면 **다음**을 클릭하십시오. 
8. 각 VM에 대해 선택적 **리소스 풀**을 선택하십시오. **다음**을 클릭하십시오. 
9. 복원 중인 각 VM에 대한 대상 데이터 저장소 및 디스크 유형을 선택하십시오. **다음**을 클릭하십시오. <br/>Veeam Backup & Replication은 대상 데이터 저장소에 대한 유효성 검증을 시도합니다. 유효성 검증에 실패하면, 데이터 저장소에 복원 중인 VM에 대한 충분한 용량이 있는지 확인하고 다시 연결하기 전에 모든 네트워크 설정을 확인하십시오.
10. 복원할 각 VM에 대해 대상 데이터 저장소의 대상 폴더를 선택하십시오. **다음**을 클릭하십시오. 
11. 각 VM에 대한 네트워크 연결 및 설정을 지정하십시오. **다음**을 클릭하십시오. 
12. 복원 작업을 수행하는 이유를 제공하여 선택적 **복원 이유**를 입력하십시오. **다음**을 클릭하십시오. <br/>Veeam Backup & Replication은 복원 중인 VM의 검증을 시도합니다. 계속 진행하기 전에 이 유효성 검증이 완료될 때까지 기다리십시오.
13. **Backup & Replication** 창에서 복원 작업 및 설정을 확인하고 **완료**를 클릭하십시오. Veeam Backup & Replication은 선택된 VM의 복원을 자동으로 시작합니다.

## 다음 단계 
{: #next-steps-recovering-your-vmware-vsphere-environment}

<!--The recent rise in Object Storage can be heavily attributed to enterprises' desire to reduce or even eliminate the operational and capital expenditures associated with comparatively expensive on-premises storage. While Object Storage is a highly cost-effective solution, it introduces a new problem of how to access stored files. In the case of stored data backups, the issue is how to use them to recover an environment that has failed for whatever reason.-->

<!--This problem is handily resolved by Veeam Backup & Replication, along with a NetApp AltaVault Cloud Gateway appliance, and {{site.data.keyword.cos_full_notm}}. When used together, they create a seamless backup and recovery experience that can be tailored to fit a wide variety of environmental requirements and constraints. By following the steps outlined in this procedure, enterprises can quickly realize the benefits of cloud-based storage, while reducing the complexities involved in shifting from on-premises storage to Object Storage using the three aforementioned technologies.-->

이 하이브리드 솔루션에 대한 자세한 정보는 다음을 참조하십시오.

* [NetApp AltaVault 웹 사이트 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://www.netapp.com/us/products/protection-software/altavault/){: new_window}
* [Veeam Backup & Replication 웹 사이트 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}
* [IBM Cloud Object Storage 웹 사이트 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://www.ibm.com/cloud/storage){: new_window}

*유틸리티 서버*는 사설 환경에 액세스하는 데 사용되는 VMware vSphere Client가 포함되어 있고 데이터 복구 작업에 사용되는 AltaVault 어플라이언스가 배치되는 서버를 말합니다. 
