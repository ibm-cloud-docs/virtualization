---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: QuantaStor
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# QuantaStor StorageLink Xenserver 설치
{: #installing-quantastor-storagelink-xenserver}

## Citrix StorageLink용 QuantaStor 어댑터 설치
{: #installing-the-quantastor-adapter-for-citrix-storagelink}

1. XenServer의 설치가 StorageLink용으로 적절하게 라이센스가 부여되었는지 확인하십시오.

Citrix를 통해 "Enterprise" 또는 "Platinum" 라이센스에 가입한 고객만 StorageLink 기능을 사용할 수 있습니다. "Advanced" Citrix XenServer 라이센스가 있는 고객은 StorageLink 기능에 액세스할 수 없습니다.
{:tip}

2. 관리자 프롬프트에서 다음 명령을 입력하여 StorageLink 서비스를 중지하십시오. 또는 XenServer의 서비스 패널에서 StorageLink 서비스를 중지할 수 있습니다.

        C:\> net stop storagelink

3. [OSNexus ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://www.osnexus.com/trynow/){: new_window}에서 사용할 수 있는 StorageLink 어댑터를 다운로드하여 클릭하십시오.
4. 서비스가 중지되면 다음 명령을 실행하여 QuantaStor StorageLink 어댑터를 설치할 수 있습니다.

        osn_quantastor_csladapter.exe

5. XML의 이 블록을 StorageLink 구성의 끝에 추가하십시오. 
  * `C:\Program Files\Citrix\StorageLink\Server\scsi_device_id_config.xml`
  * `OSNEXUS`
  * `QUANTASTOR1`
  * 이 파일의 시작 부분에 _2.0.0.4_와 유사한 버전 번호가 표시됩니다. 버전 번호를 늘리십시오(예: 2.0.0.5). 버전 번호는 이러한 새 디바이스 식별 규칙으로 XenServer 시스템을 업그레이드하도록 StorageLink에 알립니다.
6. Citrix StorageLink 서비스를 다시 시작하십시오.

        C:\> net start storagelink

## StorageLink가 제대로 로드되었는지 확인
{: #verifying-that-storagelink-loaded-properly}

1. Citrix StorageLink Manager에 로그인하고, 왼쪽에 있는 "관리" 트리를 선택하고, OS NEXUS QuantaStor를 [QuantaStor ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://svn.osnexus.com/mediawiki/images/thumb/c/c8/Storagelink_admin.png/640px-Storagelink_admin.png){: new_window} 목록에서 찾아보십시오.
2. StorageLink가 QuantaStor 스토리지 시스템을 위한 스토리지 볼륨을 동적으로 구성 및 할당할 수 있도록 스토리지 시스템 인증 정보를 추가하십시오.

## XenServer 다중 경로 구성 설정 설치
{: #installing-the-xenserver-multipath-configuration-settings}

XenServer는 하드웨어 다중 경로에 대한 지원을 사용으로 설정하도록 Linux 디바이스 맵퍼 다중 경로 드라이버(`dmmp`)를 사용합니다. `dmmp` 드라이버는 각 공급업체에 대한 다중 경로 모드 및 장애 복구 규칙이 포함된 `multipath.conf`라는 구성 파일을 사용합니다.

QuantaStor에는 `multipath.conf` 파일에 추가해야 하는 일부 특별한 규칙이 있습니다. 각 XenServer `dom0` 노드에 있는 `/etc/multipath-enabled.conf` 파일을 편집하십시오. 해당 파일에서 마지막 디바이스 { } 섹션 뒤에 다음 스탠자를 추가하십시오. 

    device {
          vendor                  "OSNEXUS"
          product                 "QUANTASTOR"
          getuid_callout          "/sbin/scsi_id -g -u -s /block/%n"
          path_selector           "round-robin 0"
          path_grouping_policy    multibus
          failback                immediate
          rr_weight               uniform
          rr_min_io               100
          path_checker            tur
    }
