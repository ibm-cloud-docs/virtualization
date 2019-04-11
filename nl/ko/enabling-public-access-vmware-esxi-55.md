---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization

keywords: ESXi, VMware
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# VMware ESXi에 대한 공용 액세스 사용
{: #enabling-public-access-to-vmware-esxi}

기본적으로 ESXi 호스트는 사설 네트워크에만 서비스 콘솔과 함께 설치됩니다. 따라서 ESXi 호스트에서 공용 네트워크 간에 공용 트래픽을 방지합니다.

시작하려면 서버의 사설 인터페이스에서 VMware vSphere 클라이언트를 사용하여 서버에 연결해야 합니다.

설정을 완료하려면 서버의 '기본' 공인 IP 정보가 있어야 합니다. 서버의 공인 IP에 관한 정보는 [{{site.data.keyword.slportal_full}} ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://control.softlayer.com/){: new_window}의 내용을 참조하십시오.
{:tip}

ESXi 호스트에 로그인한 후에 다음 단계에 따라 공용 인터페이스를 사용으로 설정하십시오. 

1. 라우팅을 정정하기 위해 사설 IP를 사용하여 SSH를 통해 서버에 로그인해야 할 수 있습니다.
* 현재 라우트를 판별하려면 `esxcfg-route -l`를 실행하십시오. 
* `esxcfg-route -a 10.0.0.0/8 [your servers private gateway ip]`를 실행하십시오. 
* 공용 게이트웨이가 기본값인지 확인하려면 `esxcfg-route [your servers public gateway]`를 실행하십시오. 

vSphere에 로그인하고 다음 단계를 수행하십시오. 

1. 구성 탭을 클릭하고 **네트워킹**을 클릭하십시오.
2. vSwitch1의 경우, **특성 > 추가**를 클릭하십시오. 
3. **VM 커널**을 선택하고 **다음**을 클릭하십시오.
* 설정은 그대로 남아 있을 수 있습니다. 식별을 위해 네트워크를 "VMKernelPublic"에 다시 레이블할 수 있습니다.
4. 서버 공인 IP 정보를 입력하십시오. 
5. VM 커널 기본 게이트웨이의 경우 **편집**을 클릭하고 공용 게이트웨이 IP를 입력하고 **확인**을 클릭하십시오.
* **확인**을 클릭하십시오. **참고:** 연결 해제가 예상됩니다(정상).*

이제 공용 및 사설 IP 주소 둘 다를 사용하여 서버에 액세스할 수 있습니다.

ESXi에 대한 공인 IP 액세스 사용에는 고유한 보안 위험이 포함됩니다. ESXi에 대한 액세스를 이를 필요로 하는 사용자로만 제한하려면 적절한 단계를 수행해야 합니다. {:tip}
