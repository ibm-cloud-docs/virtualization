---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Xenserver
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# 공용 인터페이스를 통해 XenServer에 연결
{: #connecting-to-xenserver-over-a-public-interface}

기본적으로 XenServer의 {{site.data.keyword.BluSoftlayer}} 설치는 모든 관리 연결을 위해 사설 네트워크 인터페이스를 사용하도록 구성됩니다. 사설 네트워크에 연결 문제가 있는 경우, 지원 티켓을 여십시오.

이 구성 변경에는 보안 위협이 따르므로 권장되지 않습니다.
{:tip}

공용 인터페이스를 사용하도록 XenServer를 다시 구성하려면 다음 명령을 호스트에서 루트로 실행하십시오(호스트를 풀링할 수 없음).

1. `xe pif-list`

2. 사용하려는 인터페이스의 UUID를 기록해 두십시오(eth1 또는 bond1).

3. 다음 명령을 실행하십시오.

        `xe host-management-reconfigure pif-uuid=`

이러한 변경사항은 즉시 발생하며 다시 시작할 필요가 없습니다.
