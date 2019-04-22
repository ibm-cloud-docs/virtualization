---

copyright:
years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: vsphere, Veeam
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# Veeam을 사용하여 VMware vSphere 환경 백업
{: #backing-up-your-vmware-vsphere-environment-by-using-veeam}

다음 서비스로 구성된 하이브리드 솔루션을 사용하여 VMware vSphere 환경을 백업할 수 있습니다.

* {{site.data.keyword.cloud}} Object Storage 서비스
* NetApp AltaVault 클라우드 스토리지 게이트웨이
* Veeam Backup & Replication 소프트웨어

Veeam Backup & Replication은 NetApp AltaVault 클라우드 통합 스토리지 어플라이언스 및 {{site.data.keyword.cos_full}}를 포함하는 하이브리드 솔루션을 가능하게 합니다. 소프트웨어는 백업으로부터 가상 환경을 작성, 유지보수 및 복원합니다. NetApp AltaVault 클라우드 통합 스토리지 어플라이언스와 함께 사용할 경우 로컬로 저장된(온프레미스) 백업을 작성합니다. 또한 백업은 {{site.data.keyword.cos_full_notm}}에도 동시에 복제됩니다. 이 하이브리드 솔루션을 통해 두 개의 백업 사본이 작성되지만 이 중 하나만 로컬에 존재합니다. 

## AltaVault 클라우드 통합 스토리지 게이트웨이
{: #altavault-cloud-integrated-storage-gateway}

AltaVault 클라우드 스토리지 게이트웨이를 사용하면 {{site.data.keyword.cos_full_notm}}용 REST API를 사용하여 스크립트 또는 애플리케이션을 작성하지 않고도 온프레미스 환경을 클라우드와 통합할 수 있습니다. 마운트하거나 마운트 위치를 가리키고 데이터를 클라우드에 안전하게 복사하기 시작할 수 있습니다.

### AltaVault On-Premises 배치
{: #deploying-altavault-on-premises}

다음 단계에 따라 AltaVault를 {{site.data.keyword.cos_full_notm}}에 대한 온프레미스 백업 솔루션으로 배치하십시오.

실제 또는 가상 어플라이언스 중 하나로 AltaVault를 구매할 수 있습니다. 평가판 VMware vSphere ESXi 기반 AltaVault 가상 어플라이언스의 배치는 이 프로시저에서 다룹니다.
{:tip}

<a name="prerequisites"></a>
#### 전제조건

다음 전제조건을 충족하는지 확인하십시오. 

* AltaVault 가상 어플라이언스의 사본. OVA 파일 확장자를 가진 단일 파일입니다. 어플라이언스의 NetApp 담당자에게 문의하거나 [NetApp AltaVault 웹 사이트 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://www.netapp.com/us/products/protection-software/altavault/){: new_window}에서 90일 평가판을 다운로드하십시오.
* AltaVault 어플라이언스에 대해 최소 CPU, 메모리, 디스크 공간 요구사항에 맞는 기존의 온프레미스 vSphere ESXi 5.5 환경. 평가판을 사용하는 경우, 4개의 가상 CPU(vCPU), 24GB의 메모리, 그리고 최대 8TB의 디스크 공간이 필요합니다.
* vSphere 환경 내에서 사용할 수 있는 두 개의 10Gbps 네트워크 인터페이스 컨트롤러(NIC). 한 NIC는 데이터 입력에 사용되고, 다른 하나는 {{site.data.keyword.cos_full_notm}} 복제에 사용됩니다.
* vSphere 환경 내에 정의된 두 개의 NIC(VLAN)에 해당하는 두 개의 네트워크. 복제 네트워크는 데이터 입력 네트워크와 동일한 네트워크에 지정될 수 없으므로 이렇게 할 경우 라우팅 루프를 작성할 수 있습니다.
* {{site.data.keyword.cos_full_notm}} 인증 정보 세트. 이러한 인증 정보에는 {{site.data.keyword.cloud_notm}} 사용자 이름과 연관된 {{site.data.keyword.cloud_notm}} 사용자 이름, {{site.data.keyword.cos_full_notm}} 사용자 이름 및 API 키가 포함됩니다. 
* VMware Sphere 기술과 vSphere ESXi 환경 관리에 대한 지식이 있어야 합니다. 이러한 지식에는 네트워킹 및 스토리지를 포함하는 하드웨어 리소스의 지정, vSphere 웹 클라이언트, vSphere 클라이언트의 사용 및 이외의 많은 지식이 포함됩니다.

### AltaVault OVA 배치
{: #deploying-altavault-ova}

모든 전제조건이 충족되고 나면 AltaVault OVA를 vSphere 환경에 배치할 수 있습니다. OVA 배치에 대한 지시사항은 [NetApp AltaVault 설치 및 서비스 안내서 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}의 내용을 참조하십시오.

1. OVA 배치가 완료되고 나면 AltaVault 가상 머신(VM)을 편집하십시오.
2. 편집 창에 있는 AltaVault의 버전과 일치하도록 할당된 메모리를 변경하십시오. 평가판을 사용하는 경우, 24GB의 메모리를 지정하고 8TB 이하의 디스크 하나를 추가하십시오. **참고:** 이 보조 디스크 스토리지 디바이스는 중복 백업 데이터를 저장하는 데 사용됩니다.
* 메모리 및 디스크 구성이 수정된 후 AltaVault 어플라이언스에 다른 네트워크(VLAN)를 지정하도록 하십시오. 

NIC에 다음과 같은 인터페이스 기능이 지정됩니다.

* 기본. 관리 인터페이스로 사용됩니다. 
* e0a. AltaVault 어플라이언스에서 클라우드로 데이터를 복제하는 데 사용되는 인터페이스입니다.
* e0b. SMB/CIFS 또는 NFS 공유를 위해 마운트 지점을 내보내는 데 사용되는 인터페이스입니다.
* e0c. SMB/CIFS 또는 NFS 공유를 위해 마운트 지점을 내보내는 데 사용되는 선택적 인터페이스입니다.

이 예제 구성에서는 CIFS/SMB 마운트 지점을 내보내기 위해 AltaVault 어플라이언스가 **e0a** 인터페이스를 복제-클라우드 인터페이스 및 **e0b** 인터페이스로 사용합니다. **참고:** CIFS/SMB 공유 및 NFS 공유 중 하나는 동일한 데이터에 액세스하는 데 사용할 수 없습니다. 즉, 데이터가 CIFS/SMB 공유에 있는 경우에는 NFS 공유를 통해 액세스할 수 없으며 그 반대도 마찬가지입니다 .

AltaVault 어플라이언스 및 이 어플라이언스에 대한 VM 설정의 구성 배치에 대한 자세한 정보는 [NetApp AltaVault 설치 및 서비스 안내서 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}을 참조하십시오.

### AltaVault 어플라이언스 <!--initial configuration?-->의 초기 구성
{: #initial-configuration-of-the-altavault-appliance-initial-configuration-}

적절한 하드웨어와 함께 구성되고 나면 AltaVault VM을 켤 수 있습니다. **참고:** AltaVault 어플라이언스가 보조 보조 메타데이터 캐시 디스크를 형식화함에 따라 AltaVault VM을 처음에 시작하려면 약간의 시간이 걸립니다.

1. 어플라이언스가 시작 프로세스를 완료하고 나면 AltaVault 콘솔에 로그인하십시오. ``*admin``을 **사용자 이름**으로 사용하고, ``password``를 **비밀번호**로 사용하십시오. 초기 구성이 완료되고 나면 이러한 인증 정보를 변경할 수 있습니다. 
2. 로그인하고 나면 초기 구성을 위해 마법사를 사용할 것인지 묻습니다. **y**를 입력하고 **Enter**를 누르면 변경사항이 저장됩니다. 

마법사가 열리면 표 1의 정보를 사용하십시오. 

|질문|답변|
|---|---|
|1단계: 관리 비밀번호|새 관리 비밀번호 입력("password"일 수 없음).|
|2단계: 호스트 이름|사용하려는 호스트 이름 입력|
|3단계: 기본 인터페이스에서 DHCP 사용|**n** 입력|
|4단계: 기본 IP 주소|기본 네트워크 IP 주소 입력. 예제 구성에서는 기본 IP 주소가 클라우드 복제 및 어플라이언스 관리에 사용되는 네트워크입니다(192.168.50.15).|
|5단계: 넷마스크|넷마스크 입력(255.255.255.0)|
|6단계: 기본 게이트웨이|기본 게이트웨이 입력(192.168.50.1)|
|7단계: 기본 DNS 서버|사용자 환경의 기본 도메인 이름 시스템(DNS) 서버 입력|
|8단계: 도메인 이름|사용자 환경의 도메인 이름 입력(testenv.org)|
{: caption="표 1. AltaVault 초기 구성 값" caption-side="top"}

### Object Storage에 대한 AltaVault 구성
{: #configuring-altavault-for-object-storage}

어플라이언스가 {{site.data.keyword.cos_full_notm}} 서비스에 연결되도록 구성하려면 다음 단계를 사용하십시오.

1. 웹 브라우저를 열고 AltaVault 어플라이언스 기본 인터페이스의 IP 주소를 입력하십시오(이전 단계 참조).
2. admin 인증 정보를 사용하여 콘솔에 로그인하십시오. 처음 로그인하면 마법사 대시보드가 표시됩니다.
3. **시스템 설정**을 선택하고 다음 화면에서 정보가 올바른지 확인하고 사용자 환경의 시간대를 반영하도록 시간대를 조정하십시오.
4. **다음 > 저장 및 적용 > 종료**를 클릭하십시오. 마법사 대시보드로 돌아갑니다. 
5. **클라우드 설정**을 선택하고 **제공자**를 클릭하십시오. **IBM Cloud Object Storage**를 클릭하십시오.
6. 적절한 **Object Storage 영역**을 선택하십시오. **참고:** 일부 영역은 표시되지 않습니다(예: 멜버른). 그러나 {{site.data.keyword.cos_short}} 서비스의 호스트 이름은 **호스트 이름** 필드를 사용하여 수정됩니다. 예를 들어 멜버른을 영역으로 사용하려면 **Region** 드롭 다운 메뉴에서 **San Jose 1**을 선택하고 **호스트 이름** 필드를 **mel01.objectstorage.softlayer.net**으로 수정할 수 있습니다.
7. **사용자 이름** 필드에 {{site.data.keyword.cos_full_notm}} 인증 정보를 입력하십시오. 사용자 이름의 형식은 `object_storage_username:IBM_Cloud_username`이어야 합니다. 예를 들면 **ABC-DE123456-7:user**입니다. [{{site.data.keyword.slportal_full}} ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://control.softlayer.com/){: new_window}의 **스토리지 > Object Storage** 아래에서 Object Storage 사용자 이름을 찾을 수 있습니다.
8. 데이터를 저장할 **버킷 이름**을 입력하십시오. 버켓 이름은 데이터를 {{site.data.keyword.cos_full_notm}}에 저장할 컨테이너 이름입니다. 
* 네트워크 관리자가 지시하지 않는 한 기본 포트를 수정하지 마십시오. **아카이브 사용** 필드를 **아니오**로 기본값을 설정하고 **다음**을 클릭하십시오.
9. 필요한 경우 **라이센스 요청 토큰**을 입력하고 **다음**을 입력하십시오.
10. **암호화 키**를 입력하십시오. 새 암호화 키를 생성하거나 데이터를 암호화 및 복호화하는 데 사용할 기존 키를 입력하도록 AltaVault를 허용할 수 있습니다. **다음**을 클릭하십시오. 
11. 모든 설정이 올바른지 확인한 후 **완료 및 적용**을 클릭하십시오. AltaVault는 이제 클라우드 설정 마법사에 있는 입력 및 설정을 사용하여 {{site.data.keyword.cos_full_notm}} 서비스에 대한 연결을 시도합니다. 연결에 실패하면 설정을 검토하고 서비스에 대한 적절한 액세스 권한이 있는지 확인하십시오.
12. 연결이 설정되고 나면 **종료**를 클릭하여 마법사 대시보드로 돌아가고 **마법사 종료**를 클릭하여 AltaVault 어플라이언스 상태 페이지로 돌아가십시오. 
13. *스토리지 최적화 서비스*가 실행 중이고 상태가 준비 상태인지 확인하십시오. **참고:** 상태를 *준비*로 변경하는 데는 몇 분 정도 걸릴 수 있습니다.

AltaVault 어플라이언스는 {{site.data.keyword.cos_full_notm}} 서비스와 통신하도록 구성되어 있습니다.

### AltaVault에서 CIFS/SMB 마운트 지점 구성
{: #configuring-the-cifs-smb-mount-point-in-altavault}

**e0b** 인터페이스는 CIFS/SMB 마우스 지점을 작성하도록 구성되어야 합니다. 다음 단계를 사용하여 **e0b**를 구성하십시오.

1. **설정 > 데이터 인터페이스 **로 이동하여 **e0b** 인터페이스를 확장하십시오. **데이터 인터페이스 사용**을 선택하고 CIFS/SMB 공유를 마운트하는 데 사용하는 **IP 주소, 서브넷 마스크** 및 **게이트웨이**를 입력하십시오. 
2. 기본 **MTU** 값 **1500바이트**를 그대로 두십시오.<br/><br/>기본 최대 전송 단위(MTU)는 1,500으로 설정되어 있지만 점보 프레임을 사용하는 경우에는 9,000으로 변경할 수 있습니다. **참고:** ESXi 호스트 및 실제 인프라는 점보 프레임을 지원해야 합니다. 기본적으로 {{site.data.keyword.cloud_notm}}는 이미 구성 변경이 필요하지 않은 9,000바이트의 MTU 사이즈를 지원합니다.
3. **적용**을 클릭하십시오. 마운트 지점 구성 준비가 되었습니다.
4. **스토리지 > CIFS > CIFS 공유 추가**를 선택하고 고유 이름을 입력하십시오. 
5. **핀 공유** 드롭 다운 메뉴를 클릭하고 **예**를 선택하십시오. **참고:** Veeam Backup & Replication 백업은 고정되지 않은 공유로 실패할 수 있습니다.
6. **경로** 필드에 공유에 대한 고유 경로를 입력하십시오. 공유 이름이 경로로 `cifs_share0, enter /cifs_share0`인 경우 공유 이름을 경로로 사용하는 것이 좋습니다. 
7. 보안이 문제가 되지 않는 경우 **모든 사용자 액세스 허용**을 선택 취소하십시오. CIFS/SMB 공유를 사용하는 클라이언트를 화이트리스트 처리하는 것이 좋습니다. 그렇지 않고 보안이 문제가 되는 경우 **모든 사용자 액세스 허용**을 선택한 상태로 두고 **공유 추가**를 클릭하십시오.
8. 권한 부여된 사용자에 대한 권한을 작성하려면 **CIFS 사용자 추가**를 클릭하고 **사용자 이름** 및 **비밀번호** 필드를 채우십시오. 
9. 새 CIFS 공유를 펼치고 **사용자 또는 그룹 추가**를 클릭하여 권한 부여된 사용자 계정을 추가하십시오. 
10. **글로벌 CIFS 설정**으로 이동하여 **인터페이스 청취** 드롭 다운 메뉴를 클릭하고 **e0b**를 선택하고 **적용**을 클릭하십시오.

AltaVault 어플라이언스는 자체 {{site.data.keyword.cos_full_notm}} 및 Veeam Backup & Replication를 실행 중인 컴퓨터 간의 통신을 허용하도록 구성됩니다. 필요한 경우 나중에 배치할 수 있도록 AltaVault 어플라이언스의 구성을 내보내는 것이 좋습니다.

AltaVault 어플라이언스 구성을 내보내려면 다음 단계를 따르십시오.

1. **설정 > 설정 마법사**를 클릭하여 온프레미스 AltaVault 어플라이언스의 웹 관리 콘솔에서 마법사 대시보드에 액세스하십시오.
2. **구성 내보내기**를 클릭하고 **구성 내보내기**를 클릭하십시오.
3. 안전한 위치에 구성 파일(tarball/.tar)을 저장하십시오. 

## Veeam Backup & Replication
{: #veeam-backup-replication-backup}

Veeam Backup & Replication 소프트웨어는 VM 및 해당 데이터에 대한 전체 백업, 복제 및 복구 기능을 제공합니다. 백업은 AltaVault Cloud Gateway Appliance와 완전히 통합할 수 있습니다.

### 새 서버에서 Veeam 프로비저닝
{: #provisioning-veeam-on-a-new-server}

새 VSI 또는 Bare Metal Server를 프로비저닝할 때 Veeam을 주문할 수 있습니다. 프로비저닝 시 다음 정보를 사용하십시오.
  * Veeam은 월별 청구와만 사용할 수 있습니다.
  * Veeam은 Windows 운영 체제에서만 사용할 수 있습니다.

Veeam을 추가하려면 다음을 수행하십시오.
  * 시스템 추가 기능 섹션의 OS별 추가 기능 아래에서 Veeam 탭을 클릭하고 Veeam 옵션 중 하나를 선택하십시오. 
  * 시스템 추가 기능 섹션의 CDP 추가 기능 아래에서 추가할 Veeam 옵션을 선택하십시오. <br><br>**참고**: *Veeam Backup and Replication 9.5 Update 3*을 선택하면 CDP 추가 기능 목록에서 최소한 하나의 옵션을 선택해야 합니다. 

### 기존 서버에서 Veeam 주문
{: #ordering-veeam-on-an-existing-server}

[OS 다시 로드](/docs/infrastructure/software?topic=software-reloading-the-os) 프로시저로 Veeam을 기존 서버에 추가할 수 있습니다. 서버는 Windows OS를 실행 중이어야 하며 월별 비용 청구용으로 설정해야 합니다.

Veeam을 추가하려면 다음을 수행하십시오.
1. 시스템 추가 기능 섹션의 OS별 추가 기능 아래에서 Veeam 탭을 클릭하고 Veeam 옵션 중 하나를 선택하십시오. 
2. 시스템 추가 기능 섹션의 CDP 추가 기능 아래에서 추가할 Veeam 옵션을 선택하십시오. <br><br>**참고**: *Veeam Backup and Replication 9.5 Update 3*을 선택하면 CDP 추가 기능 목록에서 최소한 하나의 옵션을 선택해야 합니다. 

### Veeam Backup & Replication
{: #deploying-veeam-backup-replication}

Veeam Backup & Replication 버전 8의 평가판이 예에서 사용됩니다. 

#### 전제조건
{: #prerequisites-deploying-veeam-backup-replication}

배치를 계속하기 전에 다음 전제조건이 충족되었는지 확인하십시오.

* {{site.data.keyword.cos_full_notm}} 및 Veeam Backup & Replication과 함께 사용하도록 구성된 기존 AltaVault 어플라이언스를 확보하십시오.
* 단일 실행 파일인 VMware 환경에 대한 Veeam Backup & Replication의 사본을 확보하십시오. [30일 평가판 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}의 복사 또는 다운로드는 Veeam 담당자에게 문의하십시오.
* Veeam Backup & Replication과 함께 사용할 라이센스 파일을 가져오십시오. 대부분의 경우, 이 파일은 Veeam Backup & Replication을 다운로드하는 데 사용된 이메일 주소로 이메일로 보내집니다. 이 파일을 수신하지 못한 경우, Veeam 담당자에게 문의하십시오.<br/><br/>라이센스 파일은 모든 Veeam Backup & Replication 기능을 활성화하는 데 사용됩니다. 이 파일이 프로그램 설치 동안 제공되지 않으면 모든 기능을 30일 평가판으로 되돌립니다.
* 표 2에 있는 스펙과 함께 온사이트와 오프사이트가 될 수 있는 기존 백업 서버가 있어야 합니다. 설치된 운영 체제는 64비트 버전이어야 합니다. 

||최소값|권장사항|
|---|---|---|
|**OS**|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>WIndows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|
|**코어 또는 vCPU 수**|2|4|
|**메모리**|각 동시 백업 작업에 대해 4GB 기반 RAM 및 500MB|각 동시 백업 작업에 대해 16GB 기반 RAM 및 4GB|
|**디스크 공간**|2GB 제품 설치: 게스트 파일 시스템 카탈로그 데이터에 대해 100VM당 10GB(지속적 데이터).|2GB 제품 설치: 게스트 파일 시스템 카탈로그에 대해 100VM당 10GB(지속적 데이터).|
|**네트워크**|온사이트 백업 및 복제의 경우 1Gbps LAN, 오프사이트 백업 및 복제의 경우 1Mbps WAN.|온사이트 백업 및 복제의 경우 1Gbps LAN, 오프사이트 백업 및 복제의 경우 1Mbps WAN.|
{: caption="표 2. Veeam Backup & Replication 백업 서버에 대한 시스템 요구사항" caption-side="top"}

#### Veeam Backup & Replication 설치
{: #installing-veeam-backup-replication}

모든 요구사항이 충족되고 나면 다음 단계를 사용하여 Veeam Backup & Replication을 백업 서버에 설치하십시오.

1. 설정 마법사를 시작하려면 Veeam 실행 파일을 두 번 클릭하고 **Veeam Backup & Replication – 설치**를 클릭하십시오.
2. **다음**을 클릭하고 라이센스 계약의 조항에 동의하십시오. 
3. **다음**을 클릭하고 **Veeam Backup & Replication**에 대해 **설치**를 클릭하십시오.
4. 이전에 확보한 라이센스 파일의 위치를 입력하고 **다음**을 클릭하십시오.
5. **Veeam Backup & Replication 설정** 화면에서 설치하고 설치 위치를 제공할 Veeam Backup & Replication 컴포넌트를 선택하십시오. **Veeam Backup & Replication 및 Veeam Backup 카탈로그**는 필수 컴포넌트입니다. **다음**을 클릭하십시오. 설정 마법사는 모든 필수 프로그램 프레임워크 및 지원 컴포넌트가 설치되는지 확인하기 위해 일련의 검사를 실행합니다. 누락된 항목이 있으면 이를 자동으로 설치하도록 설정 마법사가 제공됩니다. 누락된 프레임워크 또는 컴포넌트를 설치해야 하는 경우, **설치**를 클릭하십시오.
6. 모든 컴포넌트가 시스템 검사를 **통과**했는지 확인하고 **다음**을 클릭하십시오.
7. Veeam Backup 서비스가 실행되는 **서비스(사용자) 계정**을 선택하십시오. **참고:** 기본 서비스 계정은 *LOCAL SYSTEM 계정*입니다. **다음**을 클릭하십시오. 
8. Veeam Backup & Replication 데이터베이스를 작성 및 저장하는 데 사용되는 **SQL 서버 인스턴스**를 선택하십시오.<!--Contact your database administrator for more information, if necessary. --> **다음**을 클릭하십시오. 
9. **카탈로그 서비스 포트** 및 **Veeam Backup 서비스 포트**를 입력하십시오(기본 포트는 **9393** 및 **9392**임).<!--Contact your network administrator for more information, if necessary.--> **다음**을 클릭하십시오. 
10. 게스트 파일 시스템 카탈로그(지속적 데이터) 및 vPower NFS 쓰기 캐시(비지속적 데이터)가 저장되는 디렉토리를 선택하십시오. **다음**을 클릭하십시오. 
11. 모든 설정 및 값이 올바른지 확인하고 **설치**를 클릭하여 설치를 시작하십시오. 설치가 완료되고 나면 **완료**를 클릭하십시오.

### 백업용으로 Veeam Backup & Replication 구성
{: #configuring-veeam-backup-replication-for-backups}

Veeam Backup & Replication을 설치하고 나면 이를 AltaVault 가상 어플라이언스가 포함된 vSphere ESXi 호스트에 연결할 준비가 됩니다. 

1. Veeam Backup & Replication을 시작하십시오.
2. 화면의 왼쪽 하단에서 **백업 인프라 > 관리 서버**를 클릭하십시오.
3. **서버 추가**를 클릭하고 **VMware vSphere**를 두 번 클릭하십시오.
4. vCenter 서버의 **DNS 이름 또는 IP 주소** 및 **설명**을 입력하고 **다음**을 클릭하십시오.
5. vSphere 서버에 대한 관리자 권한을 가진 로컬 계정의 **인증 정보**를 입력하십시오. **참고:** 계정 사용자 이름은 도메인 계정의 경우 **DOMAIN\USER** 형식이고 로컬 계정의 경우 **HOST\USER** 형식이어야 합니다. 계정을 추가하려면 **추가**를 클릭하고 계정 사용자 이름 및 비밀번호를 입력하십시오. <br/>사용자 네트워크 관리자가 달리 지시하지 않는 한, Veeam Backup & Replication 설치 동안 **Default VMware web 서비스 포트**를 변경하지 마십시오. 
6. **다음**을 클릭하십시오. Veeam Backup & Replication은 VMware vSphere 서버에 연결됩니다. 연결 시도에 실패하면 연결을 다시 시도하기 전에 계정이 존재하고 VMware vSphere 서버에 관리자 권한이 있는지 확인하십시오.
7. **요약** 창의 **완료**를 클릭하고 **관리 서버 > VMware vSphere**를 클릭하여 vSphere 서버가 성공적으로 추가되었는지 확인하십시오.

### 백업 저장소를 Veeam Backup & Replication에 추가 
{: #adding-a-backup-repository-to-veeam-backup-replication}

기본적으로 Veeam Backup & Replication은 프로그램 설치 중에 Veeam Backup & Replication 백업 서버의 C:\ 드라이브에 로컬 백업 저장소를 작성합니다.

다음 단계를 사용하여 AltaVault 어플라이언스에 모든 백업을 저장할 백업 저장소를 작성하십시오. 기본 백업 저장소를 사용하려면 다음 단계를 건너뛰십시오. 

1. **백업 인프라** 화면의 왼쪽 하단에서 **백업 인프라 > 백업 저장소 > 저장소 추가**를 클릭하십시오.
2. **이름** 필드에 고유 저장소 이름을 입력하십시오. 또는 **설명**을 제공할 수 있습니다. **다음**을 클릭하십시오. 
3. **공유 폴더**를 선택하십시오. 작성한 CIFS/SMB 공유는 이 백업 저장소가 사용하는 공유입니다. **다음**을 클릭하십시오. 
4. AltaVault 어플라이언스에 CIFS/SMB 공유 위치를 지정하십시오. 위치를 판별하려면 웹 브라우저를 열고 AltaVault 어플라이언스의 IP 주소를 입력하십시오. **스토리지 > CIFS**로 이동하고 공유의 **경로 공유**를 기록해 두십시오. **참고:** 이는 공유의 로컬 경로와 동일하지 않습니다.<br/><br/>공유 경로 형식은 `\\<AltaVault appliance hostname>\<share name>`입니다. 공유 경로의 AltaVault 어플라이언스 호스트 이름을 AltaVault 어플라이언스의 **e0b** 네트워크 인터페이스(공유의 마운트 지점)의 IP 주소로 대체하십시오. <br/><br/>**e0b** 인터페이스의 IP 주소를 찾으려면 AltaVault 어플라이언스 관리 창에서 **설정 > 데이터 인터페이스**를 클릭하십시오. Veeam Backup & Replication에서 지정된 공류 경로는 `\\192.168.50.16\cifs_test2`입니다.
5. Veeam Backup & Replication으로 돌아가 마우스 지점의 공유 경로를 **공유 폴더** 필드에 입력하고 **다음**을 클릭하십시오. Veeam Backup & Replication은 마운트 지점과의 연결을 설정하려고 시도합니다. 연결 시도에 실패하면 다시 시도하기 전에 다시 돌아가서 AltaVault 어플라이언스에 대한 네트워크 설정이 올바른지 확인하십시오.
6. 최대 동시 태스크를 사용 가능한 리소스 수로 제한하도록 값을 입력하십시오. 이 값은 백업 프록시가 선택된 공유에 보낼 수 있는 최대 태스크 수입니다. 기본 동시 태스크 수는 4입니다. AltaVault의 경우 사용자가 5개의 동시 태스크로 시작하여 리소스가 허용하는 대로 이를 늘리거나 줄이도록 제안합니다. 백업 저장소가 작성된 후에 이 값을 조정할 수 있습니다.
7. **다음**을 클릭하십시오. 
8. 필요한 경우, **vPower NFS 설정**을 지정하십시오. **vPower NFS 서버 사용** 선택란을 선택하지 않은 경우, Veeam Backup & Replication이 복구 및 복구 확인을 위해 vPower를 사용합니다. **다음**을 클릭하십시오. 
9. **검토** 화면에서 모든 설정이 올바른지 확인하고 **다음**을 클릭하십시오.
10. 마법사를 종료하려면 **완료**를 클릭하십시오. 이제 데이터 백업을 시작할 수 있습니다.

### 환경 백업
{: #backing-up-the-environment}

다음 단계에 따라 전체 가상 환경의 백업을 작성하십시오.

1. **백업 인프라** 화면에서 **Backup & Replication**을 클릭하십시오.
2. **Backup & Replication** 화면에서 **작업 > 백업 작업**을 클릭하십시오.
3. **이름** 필드에 고유한 이름을 입력하십시오. 또는 **설명**을 입력할 수 있습니다. **다음**을 클릭하십시오. 
4. **오브젝트 추가**를 클릭하여 백업하려는 VM을 선택하고 VM을 선택할 트리 구조를 클릭하십시오. 적절한 VM을 선택한 후에 **추가**를 클릭하십시오. <br/>VM의 특정 파트만 백업되도록 할 경우(시작 디스크), **제외**를 클릭하고 파트를 지정하십시오. 그렇지 않으면 **다음**을 클릭하십시오. 
5. **백업 저장소** 드롭 다운 메뉴를 사용하여 작성한 백업 저장소를 선택하십시오. 

**참고:**최적의 성능을 위해서는 데이터 중복 제거 및 압축 설정을 변경해야 합니다. 다음 단계를 사용하여 성능을 최적화하십시오.

1. **고급**을 클릭하고 **스토리지** 탭을 선택하고 **인라인 데이터 복제 사용**을 선택 취소하십시오. AltaVault 어플라이언스가 이를 통과하는 Veeam Backup & Replication 백업의 블록 레벨 중복 제거를 수행하므로 성능이 향상됩니다. 
2. **압축 레벨** 드롭 다운 메뉴 아래에서 **없음**을 선택하고 **스토리지 최적화** 드롭 다운 메뉴에서 **LAN 대상**을 선택하십시오. <br/>**참고:** CIFS/SMB 공유의 네트워크 위치가 혼잡한 경우, **인라인 데이터 중복 제거 사용**을 선택한 상태로 두면 AltaVault 어플라이언스에서 더 작은 데이터 중복 제거 비율의 비용으로도 네트워크 성능 문제를 완화할 수 있습니다. 
3. **다음**을 클릭하십시오. 
4. 애플리케이션 인식 처리 및/또는 게스트 파일 시스템 인덱싱을 원할 경우, 적절한 선택란을 선택하십시오. 필요한 경우 **게스트 OS 인증 정보**[백업되고 있는 VM의 게스트 OS의 사용자 이름 및 비밀번호]를 설정하십시오. **다음**을 클릭하십시오. 
5. 백업이 정기적으로 실행될 경우 **자동으로 작업 실행** 선택란을 선택하고 원하는 간격을 설정하십시오. 그렇지 않으면 **작성** 및 **완료**를 클릭하십시오.

#### 수동 백업 시작
{: starting-a-manual-backup}
백업을 수동으로 시작하려면 백업 작업을 마우스 오른쪽 단추로 클릭하고 **시작**을 선택하십시오. 또는 새 백업을 원하는 경우 **활성 전체**를 선택하십시오.

**참고:** Veeam Backup & Replication은 백업에서 가상 환경을 복원할 수 있습니다. 가상 환경 복원에 대한 자세한 정보는 [Veeam Backup & Replication ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window} 웹 사이트를 참조하십시오.
