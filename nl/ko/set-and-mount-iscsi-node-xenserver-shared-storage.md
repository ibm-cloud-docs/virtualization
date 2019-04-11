---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-16"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# XenServer 공유 스토리지에서 iSCSI 노드 설정 및 마운트
{: #setting-up-and-mounting-an-iscsi-node-in-xenserver-shared-storage}

공유 스토리지는 XenServer에서 사용할 수 있는 스토리지의 양식인 스토리지 저장소(SR)입니다. 공유 및 비공유라는 두 개의 SR 범주에서 선택할 수 있습니다. 공유 SR을 통해 다중 노드는 동일한 풀에서 스토리지 위치를 공유할 수 있습니다. {{site.data.keyword.cloud}}는 SR, NFS 및 LVMoiSCSI(LVM over iSCSI)의 두 가지 옵션을 지원합니다.

NFS 서버를 설치하고 다른 고객 시스템에서 또는 {{site.data.keyword.cloud_notm}} QuantaStor 서버를 사용하여 이를 관리해야 합니다. XenCenter에서 NFS 저장소를 마운트하려면 다음 단계를 따르십시오. 

1. XenCenter 콘솔을 여십시오. 
2. **새 스토리지**를 클릭하십시오.
* 새 대화 상자에서 **가상 디스크 스토리지 > NFS**를 선택하십시오.
* 새 SR의 적절한 이름을 제공하십시오.
* **공유 이름** 아래에 server:/path를 입력하십시오. 
* **스캔**을 클릭하십시오.

이 프로세스는 이전 SR에 대한 NFS 공유를 스캔합니다.

LVMoiSCSI에 {{site.data.keyword.blockstoragefull}}를 사용할 수 있습니다. iSCSI는 고객 관리 스토리지 서버 또는 {{site.data.keyword.blockstoragefull}} 오퍼링에 있을 수 있습니다. 성능 및 중복 Block Storage의 경우, **스토리지 > Block Storage > Lun 이름 선택**으로 이동하여 {{site.data.keyword.slportal}}에서 IQN을 검색하십시오. XenCenter를 통해 iSCSI 노드를 마운트하려면 다음 단계를 따르십시오. 

1. XenCenter 콘솔을 여십시오. 
2. **일반 > 특성**으로 이동하여 IQN을 설정하십시오.
3. **새 스토리지**를 클릭하십시오.
4. 새 대화 상자에서 **가상 디스크 스토리지 > 소프트웨어 iSCSI**로 이동하십시오.
5. 새 SR에 대한 적절한 이름을 입력하십시오.
6. iSCSI 대상의 호스트 이름 또는 IP를 입력하고 기본 3260으로 포트를 유지하십시오.
7. **CHAP 사용**을 선택하십시오.
8. Lun에 대한 사용자 이름 및 비밀번호를 입력하십시오. 
9. **대상 호스트 스캔**을 클릭하십시오.
* **대상 IQN** 옵션을 선택하십시오. 
* **대상 Lun** 옵션을 선택하십시오. 
10. IQN 및 Lun 필드를 채우고 나면 **완료**를 클릭하십시오. 대상이 이전 SR을 확인하기 위해 스캔됩니다. SR이 존재하면 설치 프로그램이 새 SR을 작성할지 이전 SR에 첨부할지를 묻습니다. 

서버가 풀에 함께 있는 경우, iSCSI 저장소가 자동으로 공유됩니다. 

다중 경로에 대해 iSCSI를 구성하는 방법에 대한 정보는 [iSCSI 다중 경로 Multipath 구성 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus1000/sw/5_x/sys_mgmt_config/b_Cisco_N1KV_VMware_Sys_Mgmt_Config_5x/b_Cisco_N1KV_VMware_Sys_Mgmt_Config_5x_chapter_01110.html?dtid=osscdc000283){: new_window}의 내용을 참조하십시오. 
