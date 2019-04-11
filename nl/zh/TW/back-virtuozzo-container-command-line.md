---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-18"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 在指令行中備份 Virtuozzo 容器
{: #backing-up-a-virtuozzo-container-in-the-command-line}

請使用下列步驟，在 Virtuozzo 伺服器上備份容器。

1. 檢查主機上的容器清單。

        [root@virtuozzo01 ~]# vzlist

          CTID      NPROC STATUS    IP_ADDR         HOSTNAME

             1         86 running   192.168.151.2   ServiceCT

           101          5 running   75.126.27.32    VZTest01.softlayer.local

           201          9 running   12.34.234.142   vz201.domain.com

         [root@virtuozzo01 ~]#

2. 在驗證與備份硬體節點的連線功能之後，請起始備份。

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

關於上述指令，有兩件事情需要注意：
1. 您必須登入並在「目的地」伺服器（您想要備份位於其上的伺服器）上執行備份指令。在執行指令之後，會要求原始硬體節點中的容器 ID。
2. 選項 `-e` 用來指定單一容器。若沒有這個選項，指令會將主機伺服器上的所有容器備份至新位置。

        [root@virtuozzo00 vz]# vzabackup virtuozzo01.softlayer.local

        Operation backup_env with the Env(s) vz201.domain.com,VZTest01.softlayer.local is started


您可以使用下列指令，檢視伺服器上所進行並儲存的備份：

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
