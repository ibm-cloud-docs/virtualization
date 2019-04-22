---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-14"

subcollection: virtualization

keywords: Citrix XenServer
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Citrix XenServer에 하드 디스크 추가
{: #adding-a-hard-disk-drive-to-citrix-xenserver}

XenServer에서 새 하드 디스크 드라이브를 추가하는 것은 일반적인 Linux 프로세스와는 다릅니다. XenServer의 경우, 하드 디스크와 같은 특정 스토리지 대상을 정의하기 위해 "스토리지 저장소"라고 하는 컨테이너를 작성해야 합니다. 이 컨테이너는 VM의 가상 디스크 이미지가 저장되는 위치입니다. VDI는 VM의 하드 디스크 역할을 하는 추상화된 스토리지 영역입니다.

Xen 스토리지 저장소는 로컬로 연결된 경우 IDE, SATA, SCSI및 SAS 드라이브(iSCSI, NFS, SAS, 원격 스토리지용 파이버 채널)를 지원합니다.

## XenServer에서 스토리지 저장소 작성
{: #creating-a-storage-repository-in-a-xenserver}

1. XenServer에 루트로 SSH 처리하십시오.

2. 다음 명령을 사용하여 새 디바이스의 디스크 ID를 찾으십시오.

       # cat /proc/partitions

  모든 HDD 및 파티션의 명령 목록. 새 로컬 디스크를 찾으십시오. 대부분 "sdx"이거나, 또는 '/cciss/c0d1p0'입니다. 서버에 있는 모든 파티션/HDD에 대한 디스크 ID를 나열하려면 다음 명령을 사용하십시오.

       # ll /dev/disk/by-id

  "sdx" 또는 'cciss/c0d1' 디스크에 대한 디스크 ID를 찾으십시오. 필요한 형식은 "scsi-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" 또는 "cciss-xxxxxxxxxxxxxxxxxxxxxxxxxx"입니다.

3. 다음 명령을 사용하여 XenServer에서 'host-uuid'를 찾으십시오. 

        #xe host-list

  UUID(RO)는 필요한 'host-uuid'입니다.

4. 스토리지 저장소(SR)를 작성하십시오. 

  > **참고:** [sr-create ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://support.citrix.com/article/CTX121313){: new_window} 명령은 새 하드 디스크 드라이브 추가용입니다. 새 하드 디스크 드라이브 작성은 드라이브를 파티셔닝하고 포맷하는 파괴적인 프로세스이며 드라이브의 모든 데이터가 유실됩니다. 기존 데이터가 있는 드라이브를 다시 도입하려면 [sr-introduce ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://support.citrix.com/article/CTX121896){: new_window}를 사용하십시오.

  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`<br/>
  \- 또는 -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`<br/>
  \- 또는 -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`

## XenCenter의 스토리지 저장소 확인
{: #verifying-the-storage-repository-in-xencenter}

다음 단계를 수행하여 XenCenter에서 스토리지 저장소를 확인할 수 있습니다.

1. XenCenter에 연결하십시오.

2. 모든 스토리지 저장소의 세부사항을 찾으려면 **스토리지**로 이동하십시오. 디스크가 추가되었음을 표시하는 스토리지 '로컬 스토리지 2'가 목록에 표시됩니다. 이제 여기에서 VDI 작성을 시작할 수 있습니다. 
