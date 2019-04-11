---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 建立新的 Virtuozzo 容器 (CLI)
{: #creating-a-new-virtuozzo-container-cli-}

## 如何建立新的 Virtuozzo 容器 (CLI)
{: #how-to-create-a-new-virtuozzo-container-cli-}

請完成下列步驟，在 Virtuozzo 伺服器上建立新容器。

1. 檢查主機上的容器清單。

        [root@vztrain01 ~]# vzlist -a

          CTID      NPROC STATUS    IP_ADDR         HOSTNAME

             1         93 running   192.168.135.58  ServiceCT

           100          9 running   67.228.128.93   vztest01.softlayer.local

2. 建立一個具有開放 ID# 的容器。此號碼必須高於 100 且尚未使用中。在此範例中，使用 122。

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

在您建立容器之後，VZ 處於已停止狀態。如果不確定已安裝哪些套件，您可以使用 `vzpkgs` 指令。此指令會顯示一個清單，列出伺服器上已安裝的 Virtuozzo 作業系統及應用程式套件。此範例使用 RedHat 範本，但 Virtuozzo 支援數個不同的作業系統：RedHat、CentOS、Debian、Fedora Core 及 SUSE。

3. 檢閱容器的預設配置，並進行必要的變更。

        [root@vztrain01 ~]# less /vz/private/122/ve.conf

4. 啟動容器。

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

稍後容器便會執行。您可以使用 `vzctl` status 122 指令來重新檢查狀態。

5. 新增管理容器所需的參數。

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

使用指令行，為新的 Virtuozzo 容器進行基本設定，已完成。
