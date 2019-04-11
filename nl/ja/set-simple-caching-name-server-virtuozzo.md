---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-05"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Virtuozzo で単純なキャッシング・ネーム・サーバーを使用する
{: #using-a-simple-caching-name-server-with-virtuozzo}

Virtuozzo は、コンテナーのパブリック IP アドレスを使用して、ハードウェア・ノードの内部インターフェースからパケットを送信しようとします。 ユーザーが行う作業は、プライベート・インターフェースでのみ使用可能なキャッシング・ネーム・サーバーをハードウェア・ノード上で実行することだけです。

```
    [root@virtuozzo ~]# yum install bind

    Loading "security" plugin

    Loading "rhnplugin" plugin

    rhel-x86_64-server-vt-5   100% |=========================| 1.2 kB    00:00

    rhel-x86_64-server-5      100% |=========================| 1.2 kB    00:00

    Excluding Packages in global exclude list

    Finished

    Setting up Install Process

    Parsing package install arguments

    Resolving Dependencies

    --> Running transaction check

    ---> Package bind.x86_64 30:9.3.4-6.0.2.P1.el5_2 set to be updated

    --> Finished Dependency Resolution

    Dependencies Resolved

    =============================================================================

     Package                 Arch       Version          Repository        Size

    =============================================================================

    Installing:

     bind                    x86_64     30:9.3.4-6.0.2.P1.el5_2  rhel-x86_64-server-5  965 k

    Transaction Summary

    =============================================================================

    Install      1 Package(s)

    Update       0 Package(s)

    Remove       0 Package(s)

    Total download size: 965 k

    Is this ok [y/N]: y

    Downloading Packages:

    (1/1): bind-9.3.4-6.0.2.P 100% |=========================| 965 kB    00:00

    Running rpm_check_debug

    Running Transaction Test

    Finished Transaction Test

    Transaction Test Succeeded

    Running Transaction

      Installing: bind                         ######################### [1/1]

    Installed: bind.x86_64 30:9.3.4-6.0.2.P1.el5_2

    Complete!

    [root@virtuozzo ~]# cat > /etc/named.conf <_EOF_

    options

    {

            directory  "/var/named";

            forwarders { 10.0.80.11; 10.0.80.12; };

            listen-on  { 10/8; };

    };

    _EOF_

    [root@virtuozzo ~]# chkconfig named on

    [root@virtuozzo ~]# chkconfig --list named

    named           0:off   1:off   2:on    3:on    4:on    5:on    6:off

    [root@virtuozzo ~]# service named start

    Starting named:                                            [  OK  ]

    [root@virtuozzo ~]#

```


この代わりに、Virtuozzo を使用してブリッジ・ネットワーク・インターフェースを追加し、内部ネット・ブロックから固有の内部 IP アドレスを割り当てて、静的ルートを追加することもできます。 内部ルーティングでは内部インフラストラクチャーにアクセスできるようになるので、この代替ソリューションは、混合ネットワークがあり、内部ネットワーク内のサーバーのフィルタリングが緩い場合に検討してください。
