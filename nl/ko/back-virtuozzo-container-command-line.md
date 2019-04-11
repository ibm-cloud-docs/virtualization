---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-18"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 명령행에서 Virtuozzo 컨테이너 백업
{: #backing-up-a-virtuozzo-container-in-the-command-line}

Virtuozzo 서버에서 컨테이너를 뱁업하려면 다음 단계를 사용하십시오.

1. 호스트에서 컨테이너의 목록을 확인하십시오. 

        [root@virtuozzo01 ~]# vzlist

          CTID      NPROC STATUS    IP_ADDR         HOSTNAME

             1         86 running   192.168.151.2   ServiceCT

           101          5 running   75.126.27.32    VZTest01.softlayer.local

           201          9 running   12.34.234.142   vz201.domain.com

         [root@virtuozzo01 ~]#

2. 백업 하드웨어 노드에 대한 연결을 확인한 후 백업을 시작하십시오. 

        [root@virtuozzo01 ~]# ping virtuozzo00.softlayer.local

        PING virtuozzo00.softlayer.local (75.126.77.100) 56(84) bytes of data.

        64 bytes from (75.126.77.100): icmp_seq=1 ttl=64 time=0.149 ms

        64 bytes from (75.126.77.100): icmp_seq=2 ttl=64 time=0.142 ms

        [root@virtuozzo00 ~]# vzabackup virtuozzo01.softlayer.local -e 201

        * Operation backup_env with the Env(s) vz201.domain.com is started

        * Backing up environment vz201.domain.com to 10.4.9.85

        * Checking parameters

        * Dumping quota

        * Creating backup 5daa073c-2dd0-0a41-8274-a1ba1ee0d693/20081029151602

        * Adjusting backup type (full)

        * Backup storage: receiving backup file

        * Preparing for backup operation

        * Backing up private area

        100% |**************************************************************|

        * Sending private backup data

        * Backup storage: storing private backup data

        * Backup storage: filling resultant backup info

        * Operation backup_env with the Env(s) vz201.domain.com is finished successfully

        .

        Backup operation for node 'virtuozzo01.softlayer.local' was finished successfully.

이전 명령에 대해 주의해야 할 두 가지 사항이 있습니다.
1. 로그인하고 "대상" 서버(백업을 저장할 서버)에서 백업 명령을 실행하십시오. 명령을 실행하고 나면 원래 하드웨어 노드에서 컨테이너 ID에 대한 요청이 작성됩니다.
2. `-e` 옵션이 단일 컨테이너를 지정하는 데 사용됩니다. 이 옵션이 없으면 명령은 호스트 서버의 모든 컨테이너를 새 위치에 백업합니다.

        [root@virtuozzo00 vz]# vzabackup virtuozzo01.softlayer.local

        Operation backup_env with the Env(s) vz201.domain.com,VZTest01.softlayer.local is started


다음 명령을 사용하여 서버에서 작성되고 저장된 백업을 볼 수 있습니다.

    [root@virtuozzo00 vz]# vzarestore --list --full

    Show existing backups...

    CTID: 100

    Title: migrateme

    Type:  full

    BackupID: ab8743f4-d436-3a44-b35d-090f2bd2ea35/20081023173802

    Description: This is the initial full backup of migrateme on virtuozzo00.

    Parent environment: virtuozzo00.softlayer.local

    Size:  97.78 Mb

    Creation date: 2008-10-23T123802-005

    CTID: 101

    Title: VZTest01.softlayer.local

    Type:  full

    BackupID: f440f67b-76e7-684b-9f1b-f64f755803f7/20081029153257

    Description:

    Parent environment: virtuozzo01.softlayer.local

    Size:  98.22 Mb

    Creation date: 2008-10-29T103257-005

    CTID: 201

    Title: vz201.domain.com

    Type:  full

    BackupID: 5daa073c-2dd0-0a41-8274-a1ba1ee0d693/20081029151602

    Description:

    Parent environment: virtuozzo01.softlayer.local

    Size:   3.80 Mb

    Creation date: 2008-10-29T101602-005

    CTID: 201

    Title: vz201.domain.com

    Type:  full

    BackupID: 5daa073c-2dd0-0a41-8274-a1ba1ee0d693/20081029153047

    Description:

    Parent environment: virtuozzo01.softlayer.local

    Size:   3.80 Mb

    Creation date: 2008-10-29T103047-005
