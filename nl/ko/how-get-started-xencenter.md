---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-24"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# XenCenter 시작하기
{: #getting-started-with-xencenter}

## 시작하기 전에
{: #before-you-begin-xencenter}

시작하기 전에 다음 전제조건을 검토하십시오. 

- "VLAN의 보조"로 라우팅되는 휴대용 IP 주소(공용 및 사설)의 새 범위. 이 예제 솔루션은 새 VM을 설정하기 위해 사설 네트워크에서 사용할 수 있는 사용 가능한 IP를 필요로 합니다. [{{site.data.keyword.slportal_full}} ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://control.softlayer.com/network/subnets/order)에서 서브넷을 주문할 수 있습니다.
- VPN을 통해 {{site.data.keyword.cloud}} 사설 네트워크에 연결할 수 있는 기능. VPN 액세스에 대한 자세한 정보는 [{{site.data.keyword.cloud_notm}} 인프라 사설 네트워크에 대한 액세스 사용](/docs/customer-portal?topic=customer-portal-getting-started#enable-private-network)을 참조하십시오.
- Citrix XenCenter가 로컬 시스템에 설치됩니다. <!-- . https://downloads.service.softlayer.com/citrix/xen/-->

**참고:** 서버는 XenServer를 빠르게 시작하고 실행하는 데 도움이 되는 다양한 템플리트로 사전 구성됩니다.

## XenCenter로 가상 머신 작성
{: #creating-a-virtual-machine-with-xencenter}

XenCenter를 사용하는 가상 머신을 작성하는 데 도움이 되도록 다음 단계를 사용하십시오.

1. VPN을 통해 {{site.data.keyword.cloud_notm}} 사설 네트워크에 연결되어 있는지 확인하십시오. SSL VPN 또는 PPTP를 통해 연결을 설정하십시오.
2. XenCenter를 열고 **"XenServer 추가"**를 클릭하십시오.
3. 서버 호스트 이름, 사용자 이름 및 비밀번호를 입력하십시오. 서버의 사설 IP 주소(예: 10.x.x.x), 사용자 이름 `root` 및 서버의 루트 비밀번호를 사용해야 합니다. [디바이스 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://control.softlayer.com/devices){: new_window}로 이동한 후 XenServer의 이름을 클릭하면 이 정보를 {{site.data.keyword.slportal}}에서 사용할 수 있습니다. **연결**을 클릭하십시오.
4. 무료 라이센스 활성화 화면을 수신하면 이 정보를 입력하십시오.
5. {{site.data.keyword.cloud_notm}} 이름을 마우스 오른쪽 단추로 클릭하고 **새 VM...**을 선택하십시오. < ! -- 이제 첫 번째 가상 머신을 만들 수 있다. 디스크가 10 GB인 CentOS 가상 머신을 작성하고 공용 및 사설 네트워크 기능을 모두 갖추고 있음-> 
6. 새 가상 머신에 사용할 운영 체제를 선택한 후 **다음**을 클릭하십시오. **참고:** 일부 템플리트를 사용하려면 사용자가 자체 매체를 제공해야 합니다.<!--Because you are using CentOS, you can use {{site.data.keyword.BluSoftlayer_notm}} private mirrors for CentOS to get our installation going.Select a version of CentOS and then click **Next**.-->
7. 새 가상 머신에 대한 적절한 이름 및 설명을 입력하십시오. 
8. 게스트 운영 체제 설치 매체의 위치를 입력하고 **다음**을 클릭하십시오. <!-- In the example, {{site.data.keyword.BluSoftlayer_notm}} a CentOS mirror is used as installation media. Provide the Install URL of: https://mirrors.service.softlayer.com/centos/5/os/x86_64 and click **Next**.
  *A trailing ‘/’ at the end of the URL can sometimes break the installation.*
  *This mirror is available only on the {{site.data.keyword.BluSoftlayer_notm}} Private Network. The full mirror's contents are  available here: https://mirrors.service.softlayer.com/.-->
9. CPU 수 및 메모리 할당량을 선택하십시오. (간소화된 시스템을 빌드 중이므로 지나치게 많은 RAM은 필요하지 않습니다. 따라서 512MB이면 충분합니다.) **다음**을 클릭하십시오. 
10. 가상 디스크를 선택하여 디스크 공간을 VM에 할당하십시오.<!--Remember that this is like adding hard disks, it is not like partitioning your system. Partitioning is done during the installation of the OS.--> 기본 크기는 이 템플리트의 경우 8GB입니다. 이 양은 사용자가 빌드 중인 서버에 충분한 양입니다. *선택사항: 디스크를 강조표시하고 **편집...**을 클릭하여 사용자 요구에 맞게 디스크를 더 크게 변경할 수도 있습니다.* **다음**을 클릭하십시오. 
11. 가상 머신에 대한 가상 네트워크 인터페이스를 추가 또는 제거하십시오. 시스템을 사설 네트워크에서 통신할 수 없도록 하지 않는 한 기본값이 허용 가능합니다. 이 예에서는 시스템에 인터페이스를 둘 다 남겨 두어야 하지만, 숙련된 Xen 관리자는 필요에 따라 해당 인터페이스 중 하나를 제거할 수 있습니다. 이 경우, 원하는 경우에는 인터페이스를 강조표시하고 삭제할 수 있습니다. **다음**을 클릭하십시오. 
12. 구성이 완료되었습니다. "자동으로 VM 시작"을 선택한 상태로 두고 **완료**를 클릭하십시오. 설치가 시작됩니다. 기본 화면으로 돌아갑니다. 새 VM이 왼쪽에 나열된 것을 볼 수 있습니다.
13. 새 VM을 선택하고 **콘솔** 탭을 클릭하십시오. <!--You can now see that your system is booted into the CentOS installer awaiting your input.-->
14. <!--All of the parameters of a CentOS installation are outside of the scope of this article and will need to be customized by your System Administrator, but this article will provide some specific pieces of information that you need to complete the installation. Select your language to get started. The CentOS installer will then ask you for assistance in configuring the Networking Devices in the system.--> **eth0 - xen 가상 이더넷**을 선택하고 **확인**을 클릭하십시오.
  <!--![14](images/14.png)-->
15. <!--In the pre-requisite notes, we made sure that we already had a set of Portable IP Addresses routed as "Secondary on VLAN" ready for this installation.--> TCP/IP를 구성할 준비가 된 정보를 가지고 있는지 확인하십시오. IPV4 지원으로 인터페이스를 수동으로 구성하고 IPV6 지원을 사용 안함으로 설정해야 합니다. 
  <!--[15](images/15.png)-->
16. 사설 IP 서브넷(10.17.37.240/29) 사용 방법에 대해 알고 있는지 확인하고 **확인**<!-- to go to the CentOS installer-->을 클릭하십시오. 사용자 및 시스템 관리자는 자체 가이드라인에 따라 게스트 운영 체제를 설치할 수 있습니다. 정적 및 휴대용 IP 블록에 대한 자세한 정보는 [서브넷 및 IP 시작하기](/docs/infrastructure/subnets?topic=subnets-getting-started-subnets-ips#getting-started-subnets-ips)를 참조하십시오.

새 VM이 작성되었습니다.
