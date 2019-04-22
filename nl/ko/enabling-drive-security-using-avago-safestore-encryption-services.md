---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Avago SafeStore, encryption
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Avago SafeStore 암호화 서비스를 사용하여 드라이브 보안 사용
{: #enabling-drive-security-by-using-avago-safestore-encryption-services}

드라이브 보안을 설정하면 보안 키 없이 제거된 디스크에 있는 저장된 데이터에 대한 액세스를 방지하는 데 도움이 됩니다. 이 키를 사용하지 않으면 드라이브 데이터를 복구할 수 없습니다. {{site.data.keyword.cloud}}는 Bare Metal Server에서 구입할 수 있는 드라이브의 선택 데이터 센터에서 자체 암호화 드라이브(SED)를 제공합니다. US 데이터 센터에서는 10개의 TB SATA 드라이브를 사용할 수 있습니다.

## 전제조건
{: #prerequisites-enabling-drive-security-by-using-avago-safestore-encryption-services}

* SED 드라이브가 있는 Bare Metal Server - 10 TB SATA
* LSI/AVAGO MegaRAID SAS 9361 -8i 또는 유사 LSI/AVAGO RAID 카드
* 설치된 Mega RAID Storage Manager 소프트웨어

## MSM(MegaRAID Storage Manager)을 사용하여 드라이브 보안 사용 
{: #enabling-drive-security-by-using-megaraid-storage-manager-msm-}

MegaRAID Storage Manager를 사용하여 보안 키 및 세이프가드 데이터를 설정할 수 있습니다. 또한 서버 시작 시에 MegaRAID 카드 BIOS를 입력하여 드라이브 보안 설정을 구성해야 하는 WebBIOS 인터페이스도 사용할 수 있습니다. MegaRAID Controller Card SAS 9361-8i에 대한 자세한 정보는 Broadcom 사이트([MegaRAID SAS 9361-8i ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://www.broadcom.com/products/storage/raid-controllers/megaraid-sas-9361-8i#documentation))를 참조하십시오.

### 사전 설치된 SED 드라이브 식별
{: #identifying-preinstalled-sed-drives}

MegaRAID Storage Manager는 지원되는 대부분의 운영 체제에 사전 설치되어 제공됩니다. 없는 경우, Broadcom 사이트에서 수동으로 설치할 수 있습니다. 자세한 정보는 [MegaRAID SAS 9361-8i 다운로드](https://www.broadcom.com/products/storage/raid-controllers/megaraid-sas-9361-8i#downloads)를 참조하십시오.

시스템 인증 정보를 사용하여 MegaRAID Storage Manager를 열 수 있습니다. 예를 들어, Windows 머신이 사용되고 MSM이 사전 설치되어 있습니다.

MSM을 시작할 경우, 권한 부여된 사용자(관리자) 및 비밀번호인 **사용자 이름** 및 **비밀번호**를 입력해야 합니다.

<!--![Figure 1](images/1_adapter_login.jpg)-->

**실제** 탭을 클릭하십시오. 그런 다음 시스템에서 사용할 수 있는 드라이브를 클릭하십시오. **특성** 분할창에는
**전체 디스크 암호화 가능** 필드를 나타내는 **드라이브 보안 특성**이 있으며, 이는 **예**를 표시합니다. 사용된 예제에서는 2개의 비 SED 디스크와 4개의 SED 디스크가 사용됩니다.

<!--![Figure 2](images/1_fde_capable_drives.jpg)-->

### 제어기에서 드라이브 보안 사용
{: #enabling-drive-security-at-the-controller}

1. 드라이브 보안을 사용으로 설정하려면 **실제** 탭에서 **Controller 0 :AVAGO MegaRAID SAS 9361-8i**를 마우스 오른쪽 단추로 클릭하고
**드라이브 보안 사용**을 선택하십시오.
  * 이제 **보안 키 ID** 및 **보안 키**를 입력할 수 있습니다. 보안 키가 여러 개인 경우, 보안 키 ID는 사용할 보안 키를 식별하는 데 도움이 될 수 있습니다. 보안 키를 안전한 위치에 기록해 두어야 합니다. 드라이브 제거나 재삽입 등 드라이브를 재구성할 때 보안 키가 필요합니다. 보안 키가 없으면 SED에서 작성되는 볼륨에 저장된 데이터를 검색할 수 없습니다. 보안 키를 잊을 경우 검색할 수 없습니다. 시작 시간 비밀번호를 설정할 수도 있습니다. 이 비밀번호는 여기에 입력할 비밀번호 세트에 대한 시스템 일시정지를 보유합니다. 시작 시간 비밀번호는 선택사항이며, 설정된 경우 시스템이 재부팅될 때마다 IPMI에 로그인하여 시작 비밀번호를 입력해야 합니다. 아래로 스크롤하여 **나중에 참조하도록 보안 설정을 기록함**이라는 상자를 선택하고 **예**를 클릭하여 드라이브 보안을 사용으로 설정하십시오.
  * 드라이브 보안이 사용으로 설정된 경우, **Controller 0 AVAGO MegaRAID SAS 9361-8i**에 대해 노란색 키 이미지가 표시됩니다.
2. 이제 SED를 사용하여 보안 볼륨을 작성하십시오. **논리** 탭에서 **Controller0**를 마우스 오른쪽 단추로 클릭하여 선택할 수 있습니다.  
**가상 드라이브를 작성**하십시오.
3. **고급** 옵션을 선택하십시오. 이 화면에서는 **RAID 레벨**을 지정하고 **드라이브 보안 방법**을 **전체 디스크 암호화(FDE)**로 지정해야 합니다. 필요한 FDE 드라이브를 선택하고 **추가** > **드라이브 그룹 작성** > **다음**을 클릭하십시오.
4. 가상 드라이브 설정을 검토하고 필요한 변경을 수행하십시오. **읽기 정책**에 대해 제안되는 설정은 **항상 먼저 읽기**입니다. **쓰기 정책**에 대해 제안되는 설정은 **답장**입니다. **가상 드라이브 작성**을 클릭하십시오. **예**를 클릭하여 BBU로 인한 다시 쓰기 정책 영향을 승인하십시오. **다음**을 클릭하고 요약 화면을 검토하십시오. **완료**를 클릭하십시오.
5. 가상 디스크에 대한 보안이 설정되었는지 확인하려면 **논리** 탭을 클릭하고 작성된 가상 드라이브를 클릭하십시오. **드라이브 보안 특성**에 **보안됨** 필드가 **예**로 표시되어 있습니다.

<!--![Figure 3](images/2_enable_drive_security.jpg)-->
<!--![Figure 4](images/3_security_key_details_page.jpg)-->
<!--![Figure 5](images/4_security_key_set_0.jpg)-->
<!--![Figure 6](images/9_create_vd_with_fde_drives.jpg)-->
<!--![Figure 7](images/10_create_vd_advanced_select_raid_drive_encryption_0.jpg)-->
<!--![Figure 8](images/create_vd_settings.jpg)-->
<!--![Figure 9](images/6_vd_secured_confirmation_0.jpg)-->

서버가 SED 드라이브를 사용하여 이미 작성된 RAID 볼륨과 함께 제공되는 경우, 다음 단계를 수행하여 볼륨을 안전하게 설정할 수 있습니다.

**논리** 탭에서 **드라이브 그룹**을 마우스 오른쪽 단추로 클릭하고 **FDE를 사용하여 보안**을 선택하십시오. 볼륨에 대한
혼합 FDE 및 비 FDE 드라이브가 있는 경우, 이 옵션이 표시되지 않습니다. 

<!--![Figure 10](images/5_secure_existing_vd_with_fde_drives_0.jpg)-->

드라이브 보안을 제거하려면 먼저 보안 가상 디스크를 삭제하고 Controller 0을 마우스 오른쪽 단추로 클릭하여 **드라이브 보안 사용 안 함**을 선택하십시오. 이 기능은 내부의 데이터를 안전하게 지우고 드라이브 보안을 제거합니다.

또한 WebBIOS를 사용하여 시작 시에 IPMI를 통해 로그인하고 RAID BIOS를 입력하여 드라이브 보안을 설정할 수도 있습니다. 자세한 정보는 **12Gb/s MegaRAID SAS 소프트웨어 사용자 안내서**의 **Avago SafeStore 암호화 서비스**를 참조하십시오.
