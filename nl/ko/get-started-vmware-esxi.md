---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-14"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# VMware ESXi
{: #getting-started-with-vmware-esxi}

ESX 서버 프로비저닝 후에 서버를 지정하는 첫 번째 단계는 {{site.data.keyword.cloud}} 사설 네트워크 VPN에 연결하는 것입니다. 기본적으로 모든 {{site.data.keyword.cloud_notm}} 배치 VMware 서버에는 IP 주소로 구성된 사설 네트워크 인터페이스나 결합만 있습니다. 이 배치는 새 ESX 서버가 공용 인터넷에 노출되는 것을 제한하기 위해 배치되는 추가 보안 수단입니다. 이 추가 보안의 결과로서, 서버는 {{site.data.keyword.cloud_notm}} VPN 또는 새 VMware 서버가 상주하는 동일한 사설 VLAN에 액세스할 수 있는 기존 {{site.data.keyword.cloud_notm}} 호스트 중 다른 하나 {{site.data.keyword.cloud_notm}} 사설 10.x.x.x IP 주소에서만 수신합니다.

{{site.data.keyword.cloud_notm}} VPN에 대한 자세한 정보는 [VPN(Virtual Private Networking) 시작하기](/docs/infrastructure/iaas-vpn?topic=VPN-gettingstarted-with-virtual-private-networking#gettingstarted-with-virtual-private-networking)의 내용을 참조하십시오.

## ESX 호스트 액세스
{: #accessing-your-esx-host}

{{site.data.keyword.cloud_notm}} VPN에 연결하고 나면 새 VMware 서버와 통신할 수 있습니다. ESX는 vSphere Client에서 관리됩니다. 사설 네트워크 IP 주소를 웹 브라우저에 입력하여 새 VMware 서버에서 검색하고 결과 페이지에서 "vSphere Client" 링크를 클릭할 수 있습니다.

vSphere Client 다운로드 및 로컬 워크스테이션에 이를 설치한 후에는 애플리케이션을 시작하고 서버 사설용 주소와 로그인 인증 정보를 입력할 수 있습니다. 로그인 인증 정보는 사용자가 비밀번호 탭을 클릭한 후 {{site.data.keyword.slportal_full}}의 디바이스 페이지에 표시됩니다. 

1. vSphere 클라이언트의 해당 필드에 서버의 사설 IP 주소, 루트 사용자 및 비밀번호를 입력하고 **로그인**을 클릭하여 연결하십시오. 
2. 이제 서버에 연결했으므로 **인벤토리**를 선택하여 새 ESX 호스트로 이동하십시오.
3. ESX 호스트는 결과 페이지에 구성에 사용할 수 있는 노드로 표시됩니다. 이제 가상 머신을 배치하는 여러 가지 방법이 있습니다. 그 중 하나는 선호하는 운영 체제의 설치 ISO를 서버 로컬 데이터 저장소로 업로드하는 것입니다. ISO가 업로드되면 VM이 작성될 때 설치 매체로 ISO를 선택할 수 있습니다.  

## ISO 업로드
{: #uploading-an-iso}

다음 단계를 완료하여 ISO를 서버의 데이터 저장소에 업로드하십시오. 

1. 왼쪽 분할창에서 서버를 강조표시하고(서버 아이콘 및 해당 사설 IP 주소로 표시됨) 오른쪽 분할창에서 **구성** 탭을 선택하십시오.
2. **하드웨어 > 스토리지**를 선택하십시오. **보기**가 **데이터 저장소**로 설정되어 있는지 확인하십시오.
3. 해당 데이터 저장소를 마우스 오른쪽 단추로 클릭하고 **데이터 저장소 찾아보기**를 클릭하십시오.
4. 결과 페이지에는 데이터 저장소에서 파일을 찾아보고 업로드/다운로드하는 파일 관리자가 있습니다.  
  * ISO를 업로드하려면 위쪽 화살표가 있는 볼륨 아이콘을 클릭하고 **파일 업로드**를 선택하십시오.
5. 데이터 저장소에 업로드할 ISO 파일을 로컬 파일 시스템에서 선택하고 **열기**를 클릭하십시오.
6. 선택한 ISO는 이제 데이터 저장소에 있습니다.
7. 이제 설치 매체가 VMWare 서버에 있으므로 [가상 머신 작성](/docs/infrastructure/vmware?topic=VMware-create-esx-vm#create-esx-vm)을 진행할 수 있습니다.
