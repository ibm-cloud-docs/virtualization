---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 하이퍼바이저 설정
{: #setting-up-a-hypervisor}

다음 단계에 따라 하이퍼바이저를 설정하십시오. 

1. 고유 인증 정보를 사용하여 [{{site.data.keyword.slportal_full}} ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](http://control.softlayer.com/){: new_window}에 로그인하십시오.
2. **디바이스** 메뉴에서 **디바이스 목록**을 선택하고 하이퍼바이저를 찾으십시오. 
3. 하이퍼바이저에 액세스하려면 [VPN 보안 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](http://www.softlayer.com/vpn-access){: new_window}을 통해 사설 네트워크에 연결하십시오.

**참고:** {{site.data.keyword.cloud}} 하이퍼바이저 제공자에는 XenServer, VMware 및 Hyper-V가 포함됩니다. 각 제공자는 따로 액세스할 수 있는 고유 관리 콘솔을 가집니다. 관리 콘솔에 대한 액세스 및 작업에 대한 자세한 정보는 다음 링크를 참조하십시오. 

   * [XenServer ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://support.citrix.com/en/products/xenserver){: new_window}
   * [VMware ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://www.vmware.com/support/vsphere-hypervisor.html){: new_window}
   * [Hyper-V ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](http://technet.microsoft.com/en-us/windowsserver/dd448604){: new_window}

4. 가상 머신에 대한 휴대용 IP를 확보하십시오.
    * VM에는 휴대용 IP 주소가 필요합니다. 공용 및 사설 휴대용 IP의 블록은 {{site.data.keyword.slportal}}을 통해 주문할 수 있습니다.
    * IP 주소 할당에 대한 자세한 정보는 [서브넷 및 IP 시작하기](/docs/infrastructure/subnets?topic=subnets-getting-started-with-subnets-and-ips)를 참조하십시오.

5. 사설 네트워크에서 VM을 위한 라우팅을 설정하십시오. VM은 사설 네트워크를 통해 다른 VM으로 라우팅하기 위해 다음과 같은 스펙을 필요로 합니다.
    * 휴대용 사설 IP
    * 10.0.0.0/8 네트워크 범위와 관련된 정적 라우트

VM 라우팅 프로세스에 대한 자세한 정보는 [가상 머신 네트워크 설정](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-a-virtual-machine-network)을 참조하십시오. VM 네트워크를 설정한 후에는 동일한 VLAN에 있는 머신이 통신할 수 있습니다. 다른 VLAN에 있는 머신이 통신해야 하는 경우 [VLAN Spanning을 사용](/docs/infrastructure/vlans?topic=vlans-vlan-spanning)하십시오.

## ISO에 액세스하고 안전하게 저장
{: #access-and-securely-store-isos}

{{site.data.keyword.cloud_notm}} 네트워크의 VM은 사전 구성 또는 사용자 정의 ISO를 실행할 수 있습니다.  {{site.data.keyword.cloud_notm}} 사용자에 대해 배타적으로 사용할 수 있고 가장 공통으로 사용된 운영 체제의 가장 인기 있는 구성을 제공하는 {{site.data.keyword.cloud_notm}} 네트워크의 VM은 내부 미러 사이트에 액세스할 수 있습니다. 특정 운영 체제에 대한 특수 버전 또는 구성이 필요한 경우, OS 공급업체 웹 사이트를 참조하십시오. 

VM에서 사용자 정의 ISO를 실행할 경우, ISO를 보안 위치에 업로드하여 디바이스가 실패할 때 이를 검색할 수 있습니다. 대다수의 사용자는 SSH 또는 RDP를 사용하여 디바이스의 로컬 시스템에 사용자 정의 ISO를 저장하도록 선택합니다. 또는 다양한 기능이 있는 스토리지 서비스를 통해 공간이 제공됩니다.
