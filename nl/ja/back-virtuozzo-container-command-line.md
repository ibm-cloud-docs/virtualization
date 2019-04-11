---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-18"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# コマンド・ラインでの Virtuozzo コンテナーのバックアップ
{: #backing-up-a-virtuozzo-container-in-the-command-line}

以下の手順に従って、Virtuozzo サーバー上のコンテナーをバックアップします。

1. ホスト上のコンテナーのリストを確認します。

        [root@virtuozzo01 ~]# vzlist

          CTID      NPROC STATUS    IP_ADDR         HOSTNAME

             1         86 running   192.168.151.2   ServiceCT

           101          5 running   75.126.27.32    VZTest01.softlayer.local

           201          9 running   12.34.234.142   vz201.domain.com

         [root@virtuozzo01 ~]#

2. バックアップ・ハードウェア・ノードへの接続を確認した後に、バックアップを開始します。

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

上記のコマンドについて、次の 2 点に注意してください。
1. ログインして、「バックアップ先」サーバー (バックアップ内容を配置するサーバー) 上でバックアップ・コマンドを実行する必要があります。 このコマンドを実行した後に、元のハードウェア・ノードのコンテナー ID に対する要求が発行されます。
2. `-e` オプションを使用して単一のコンテナーを指定します。 このオプションを使用しない場合は、コマンドによってホスト・サーバー上のすべてのコンテナーが新しい場所にバックアップされます。

        [root@virtuozzo00 vz]# vzabackup virtuozzo01.softlayer.local

        Operation backup_env with the Env(s) vz201.domain.com,VZTest01.softlayer.local is started


作成されてサーバーに保管されたバックアップを確認するには、次のコマンドを使用します。

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
