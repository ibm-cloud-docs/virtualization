---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 新しい Virtuozzo コンテナーの作成 (CLI)
{: #creating-a-new-virtuozzo-container-cli-}

## 新しい Virtuozzo コンテナーの作成方法 (CLI)
{: #how-to-create-a-new-virtuozzo-container-cli-}

以下の手順を実行して、Virtuozzo サーバー上に新しいコンテナーを作成します。

1. ホスト上のコンテナーのリストを確認します。

        [root@vztrain01 ~]# vzlist -a

          CTID      NPROC STATUS    IP_ADDR         HOSTNAME

             1         93 running   192.168.135.58  ServiceCT

           100          9 running   67.228.128.93   vztest01.softlayer.local

2. 空き ID 番号を持つコンテナーを作成します。 この番号は、101 以上で未使用である必要があります。 この例では、122 を使用します。

       [root@vztrain01 ~]# vzctl create 122 --pkgset redhat-as3-minimal-x86_64  --config basic

        Creating Container private area (redhat-as3-minimal-x86_64/20080630)

        Container is mounted

        Postcreate action done

        Container is unmounted

        Container private area created

        Container registered succesfully

        [root@vztrain01 ~]# vzlist -a

          CTID      NPROC STATUS    IP_ADDR         HOSTNAME

             1         93 running   192.168.135.58  ServiceCT

           100          9 running   67.228.128.93   vztest01.softlayer.local

           122          - stopped   -               -

コンテナーを作成したら、VZ は停止状態になります。 インストールされているパッケージが不明な場合は、`vzpkgls` コマンドを使用して確認できます。 このコマンドを実行すると、サーバーにインストールされている Virtuozzo OS とアプリケーションのパッケージが一覧表示されます。 この例では、RedHat テンプレートが使用されていますが、Virtuozzo は RedHat、CentOS、Debian、Fedora Core、および SUSE という複数のオペレーティング・システムをサポートしています。

3. コンテナーのデフォルト構成を確認して、必要な変更を加えます。

        [root@vztrain01 ~]# less /vz/private/122/ve.conf

4. コンテナーを始動します。

        [root@vztrain01 ~]# vzctl start 122

        Starting Container ...

        Container is mounted

        Setup slm memory limit

        Setup slm subgroup (default)

        Setting devperms 20002 dev 0x7d00

        Adding port redirection to Container(1): 4643 8443

        Adding IP address(es):

        Configure meminfo: 65536

        Container start in progress...

しばらくすると、コンテナーが実行されます。 ステータスをもう一度確認するには、`vzctl` status 122 コマンドを使用します。

5. コンテナーを管理するために必要なパラメーターを追加します。

        [root@vztrain01 ~]# vzctl set 122 --ipadd 12.34.234.142 --hostname vz122.domain.com --userpasswd root:password123 --save

        Hostname for Container set: vz122.domain.com

        Adding IP address(es) to pool: 12.34.234.142

        Adding IP address(es): 12.34.234.142

        Shutting down loopback interface:  [  OK  ]

        Setting network parameters:  [  OK  ]

        Bringing up loopback interface:  [  OK  ]

        Bringing up interface venet0:  [  OK  ]

        Changing password for user root.

        passwd: all authentication tokens updated successfully.

        Saved parameters for Container 122

これで、コマンド・ラインを使用して新しい Virtuozzo コンテナーの基本的なセットアップを完了できました。
