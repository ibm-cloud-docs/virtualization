---



copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# FAQ: Virtuozzo
{: #faqs-virtuozzo}

## Virtuozzo에 대해 정적 또는 휴대용 IP 주소가 필요합니까?
{: #do-i-need-static-or-portable-ip-addresses-for-virtuozzo-}

* 정적 IP 주소: VLAN당 또는 전체에 하나의 하드웨어 노드만 있는 경우 정적 IP 주소를 사용하십시오. 
* 휴대용 IP 주소:
    * VLAN에 라우팅됨: 동일한 VLAN 내에 여러 호스트 노드가 있는 경우 이 메소드를 사용하십시오. 또한 이 메소드를 사용하여 컨테이너의 IP를 변경하지 않고도 동일한 VLAN 내에서 다른 하드웨어 노드로 컨테이너를 마이그레이션할 수도 있습니다.
    * VLAN의 보조: 이 메소드가 작동하지만 보조 게이트웨이가 필요하지는 않습니다. 호스트 ID, 게이트웨이 및 브로드캐스트로 인해 IP 주소 중 3개를 잃습니다.

라우팅된 유형에 따라 IP당 비용이 변경될 수 있습니다. IP 주소를 순서 지정하는 서버를 선택하고 나면 가격이 포털에 표시됩니다. 지원 티켓을 열어 언제든지 라우팅을 변경할 수 있습니다.
{:tip}

## 중단 시간 없이 Virtuozzo Container 3을 Virtuozzo Container 4 하드웨어 노드로 마이그레이션할 수 있습니까?
{: #can-i-migrate-a-virtuozzo-3-container-to-a-virtuozzo-container-4-hardware-node-with-no-downtime-}

**Linux:**

예. 기본적으로 Linux 4의 Virtuozzo 컨테이너는 호환성 모드에서 실행됩니다. Virtuozzo 컨테이너가 호환성 모드에서 실행되고 있지 않은 경우에는 SSH를 통해 하드웨어 노드에서 다음 명령을 사용하여 이를 시작할 수 있습니다.

`/usr/sbin/vzagent_compat_ctl start`

**Windows(지원되지 않음):**

Virtuozzo Containers 4 Windows를 사용하면 전달할 온라인 옵션이 없기 때문에 상태 전이 동안 중단 시간이 생깁니다(20초에서 최대 5분까지). 

Windows용 Virtuozzo 3.0을 사용하는 경우에는 하드웨어 노드를 적어도 Windows용 Virtuozzo 3.5.1로 업그레이드해야 합니다.

Windows용 Virtuozzo Containers 4는 기본적으로 호환성 모드로 실행됩니다.

또한 Virtuozzo Container 4 하드웨어 노드의 3.5.1로부터의 백업 복원은 대상 노드로 전체 백업을 복사하고 `vzbackupsync.exe`를 실행하고 백업을 정상적으로 복원하여 수행할 수 있습니다.

동일한 템플리트가 하드웨어 노드에 설치되어 있는지 확인하십시오.
{:tip}

## 컨테이너는 서로 간 및 호스트와 분리되어 있습니까?
{: #are-containers-isolated-from-each-other-and-the-host-}

컨테이너는 LKM을 로드하지 못하도록 제한되어 있으며, 자체 환경에서 `chroot(“../../../”)`를 수행하고 IPC 리소스(세마포어 세트 및 공유 메모리 세그먼트)를 공유하지 않습니다.

Virtuozzo가 리소스를 관리하고 프로세스를 분리하는 방법에 관해서는 보안 문제가 없습니다. 브릿지된 네트워크 구성을 사용하면 동일한 하드웨어 노드의 동일한 VLAN에 있는 모든 컨테이너가 서로의 트래픽을 볼 수 있습니다. 라우트된 모드를 사용하는 것이 좋습니다.

## Virtuozzo에서 RedHat 및 SUSE OS 템플리트를 캐시할 수 없는 이유는 무엇입니까?
{: #why-can-t-i-cache-redhat-and-suse-os-templates-in-virtuozzo-}

Vitruozzo를 사용하는 경우에는 RedHat 및 SUSE OS 템플리트에 캐시할 경우 문제가 발생할 수 있습니다. 이러한 OS 템플리트에는 {{site.data.keyword.cloud_notm}}에서 제공하지 않는 추가 라이센싱이 필요합니다. 다음 오류를 수신하면 각 벤더에 해당하는 자체 계정을 가지고 있어야 하며 /etc/vztt/url.map 내에서 해당 변수를 구성해야 합니다(Virtuozzo 3의 경우 /etc/vztt/vztt.conf).

||||
|---|---|---|
|TIMESTAMP|Click here to open/close the operation details.Update OS Template Cache|**Failed**|
|TIMESTAMP|Operation update with the Env(s) "virtuozzo00.softlayer.local" and package(s) .redhat-as4-x86_64 is started||
|TIMESTAMP|Operation update with the Env(s) "virtuozzo00.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $RH_SERVER/download/mirrors/redhat-as4 contents undefined variable You can define this variable in /etc/vztt/url.map. .|**Failed**|
|TIMESTAMP|Click here to open/close the operation details.Update OS Template Cache|**Failed**|
|TIMESTAMP|Operation update with the Env(s) "virtuozzo00.softlayer.local" and package(s) .redhat-el5-x86_64 is started||
|TIMESTAMP|Operation update with the Env(s) "virtuozzo00.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $RH_SERVER/download/mirrors/redhat-el5 contents undefined variable You can define this variable in /etc/vztt/url.map. See Virtuozzo Installation Guide for more details. .|**Failed**|
|TIMESTAMP|Click here to open/close the operation details.Update OS Template Cache|**Failed**|
|TIMESTAMP|Operation update with the Env(s) "virtuozzo00.softlayer.local" and package(s) .sles-10-x86_64 is started||
|TIMESTAMP|Operation update with the Env(s) "virtuozzo00.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $SLES_SERVER/download/mirrors/suse-es10 contents undefined variable You can define this variable in /etc/vztt/url.map. See Virtuozzo Installation Guide for more details. .|**Failed**|
|TIMESTAMP|Click here to open/close the operation details.Update OS Template Cache|**Failed**|
|TIMESTAMP|Operation update with the Env(s) "virtuozzo01.softlayer.local" and package(s) .redhat-as4-x86_64 is started||
|TIMESTAMP|Operation update with the Env(s) "virtuozzo01.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $RH_SERVER/download/mirrors/redhat-as4 contents undefined variable You can define this variable in /etc/vztt/url.map. See Virtuozzo Installation Guide for more details. .|**Failed**|
|TIMESTAMP|Click here to open/close the operation details.Update OS Template Cache|**Failed**|
|TIMESTAMP|Operation update with the Env(s) "virtuozzo01.softlayer.local" and package(s) .redhat-el5-x86_64 is started||
|TIMESTAMP|Operation update with the Env(s) "virtuozzo01.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $RH_SERVER/download/mirrors/redhat-el5 contents undefined variable You can define this variable in /etc/vztt/url.map. .|**Failed**|
|TIMESTAMP|Click here to open/close the operation details.Update OS Template Cache          Failed<br/><br/>Oct 29, 2008 01:16:56 PM     Operation update with the Env(s) "virtuozzo01.softlayer.local" and package(s) .sles-10-x86_64 is started||
|TIMESTAMP|Operation update with the Env(s) "virtuozzo01.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $SLES_SERVER/download/mirrors/suse-es10 contents undefined variable .|**Failed**|
|TIMESTAMP|Completing the process|**Failed**|

## ServiceVE에 대해 ping을 실행할 수 있는데 SSH(PIM/PMC)를 통해 로그인할 수는 없는 이유는 무엇입니까?
{: #why-can-i-ping-my-serviceve-but-cannot-log-in-to-via-ssh-pim-pmc-}

포털에 나열된 IP 주소가 아닌 다른 IP 주소가 호스트 노드에 지정되어 있지 않은지 확인하십시오(1개의 공인 IP 및 1개의 사설 IP [10.0.0.0/8]).

호스트 노드에 구성된 범위 파일이 있는 경우, 이러한 파일을 제거하고 네트워킹을 다시 시작해야 합니다.

    [root@virtuozzo ~]# mv –vi /etc/sysconfig/network-scripts/eth[0-9]-range[0-9]* ~/.sl-orig-configs/

    [root@virtuozzo ~]# /sbin/service network restart


`vzcp`는 서비스 VE 내에서 시작되지 않습니다. 하드웨어 노드의 서비스 내에서 `vzcp`를 시작하십시오. 

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp status

    vzcp is stopped

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp start

    Starting vzcpd: [  OK  ]

    Starting vzcp: [  OK  ]

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp status

    vzcp (pid 3775 3774 3771) is running...

    [root@virtuozzo ~]#
