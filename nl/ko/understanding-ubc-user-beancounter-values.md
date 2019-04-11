---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# UBC/사용자 Bean 카운터 값 이해하기
{: #understanding-ubc-user-beancounter-values}

UBC를 이해하지 못하는 경우 SLM 모드를 사용하십시오. SLM이 더 쉬우면서도 동일한 제어 유형을 제공합니다.

|이름|유형|설명|
|---|---|---|
|*기본 매개변수*|||
|`numproc`|*시스템*|프로세스 및 스레드의 수|
|`numtcpsock`|*시스템*|TCP 소켓의 수|
|`numothersock`|*시스템*|TCP 이외의 소켓 수|
|`vmguardpages`|*시스템*|메모리 할당 보장|
|`cpuunits`|*cpu*|CPU 전원|
|`diskspace`|*디스크*|디스크 공간 할당량|
|`rate`|*네트워크*|네트워크 대역폭|
|`ratebound`|*네트워크*|네트워크 대역폭 제한 여부|
|*추가 매개변수*|||
|`kmemsize`|*시스템*|이 컨테이너의 프로세스용으로 할당된 스왑 불가능한 커널 메모리의 크기|
|`tcpsndbuf`|*시스템*|TCP 송신 버퍼의 총 크기|
|`tcprcvbuf`|*시스템*|TCP 수신 버퍼의 총 크기|
|`othersockbuf`|*시스템*|UNIX 도메인 소켓 버퍼, UDP 및 기타 데이터그램 프로토콜 송신 버퍼의 총 크기|
|`dgramrcvbuf`|*시스템*|UDP 및 기타 데이터그램 프로토콜 수신 버퍼|
|`oomguardpages`|*시스템*|메모리가 초과 예약되었을 경우 보장된 메모리의 양(메모리 부족 시 보장이 강제 종료됨)|
|`privvmpages`|*시스템*|메모리 할당 한계|
|`lockedpages`|*시스템*|스왑 아웃이 허용되지 않은 프로세스 페이지([mlock(2) ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](http://linux.die.net/man/2/mlock)에 의해 잠긴 페이지)|
|`shmpages`|*시스템*|페이지의 공유 메모리의 총 크기(프로세스 간 통신 또는 IPC, 공유 익명 맵핑 및 tmpfs 오브젝트)|
|`physpages`|*시스템*|프로세스에 사용되는 총 RAM 페이지 수|
|`numfile`|*시스템*|열린 파일 수|
|`numflock`|*시스템*|파일 잠금 수|
|`numpty`|*시스템*|의사 터미널 수|
|`numsiginfo`|*시스템*|서명 정보 구조의 수|
|`dcachesize`|*시스템*|메모리에서 잠긴 dentry 및 inode 구조의 총 크기|
|`numiptent`|*시스템*|NETFILTER 항목 수(IP 패킷 필터링)|
|`cpulimit`|*cpu*|CPU 이용 제한|
|`diskinodes`|*디스크*|디스크 inode(파일 시스템 오브젝트) 할당량|
|`quotatime`|*디스크*|디스크 할당량 유예 기간|
|`quotaugidlimit`|*디스크*|uid 및 gid 계정 항목의 수 제한|
<caption>표 1. SLM 매개변수 설명</caption>

**관리 > 지원 > 다운로드 > UBC 리소스 관리 관리자 안내서**로 이동하여
서버의 PIM 내에서 VzLinuxUBCMgmt.pdf에 액세스하거나 HTML로 이를 볼 수 있습니다. 

UBC에 대한 자세한 정보는 [OpenVZ Virtuozzo 컨테이너, 카테고리: UBC ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](http://wiki.openvz.org/Category:UBC)를 참조하십시오.**참고:** OpenVZ의 일부 항목은 Virtuozzo와 동일합니다.
