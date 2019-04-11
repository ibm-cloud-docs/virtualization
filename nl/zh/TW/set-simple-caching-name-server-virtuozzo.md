---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-05"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 使用簡單快取名稱伺服器與 Virtuozzo 搭配
{: #using-a-simple-caching-name-server-with-virtuozzo}

Virtuozzo 會嘗試使用容器的公用 IP 位址，從硬體節點的內部介面傳送封包。您只需在硬體節點上執行快取名稱伺服器即可，而此伺服器只能在專用介面上使用。

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


或者，您也可以使用 Virtuozzo 來新增橋接網路介面，從內部網路區塊指派唯一的內部 IP 位址，然後新增靜態路徑。內部遞送提供對內部基礎架構的存取權，因此，如果您在內部網路的伺服器上有混合的網路及輕鬆的過濾方式，請將此替代解決方案謹記於心。
