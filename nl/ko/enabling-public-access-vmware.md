---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-11"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# VMware에 대한 공용 액세스 사용
{: #enabling-public-access-to-vmware}

기본적으로 ESX 호스트는 사설 네트워크에서만 서비스 콘솔과 함께 설치되며, 이는 공용 네트워크와의 공용 트래픽을 ESX 호스트에서 막을 수 있다는 것을 의미합니다.

시작하려면 다음 단계에 따라 서버의 사설 인터페이스에서 VMware vSphere 클라이언트를 사용하여 서버에 연결해야 합니다.

1. **ESX 호스트 > 구성 탭 > 네트워킹 > 네트워킹 추가**를 클릭하십시오. 
2. 서비스 콘솔을 선택하십시오.
3. 사용할 vSwitch를 선택하십시오. 이 경우에는 외부/공용 인터페이스입니다. 따라서 **vSwitch1**를 선택하십시오(vmnic1 및 vmnic3).
4. 서비스 콘솔의 이름을 쉽게 이해할 수 있는 이름으로 바꾸십시오(예: "공용 서비스 콘솔"). 
5. 올바른 서브넷 마스크로 서버에 지정된 기본 외부 IP 주소를 입력하십시오.
6. **편집**을 클릭하고 서버에 지정된 기본 게이트웨이를 추가하십시오.
