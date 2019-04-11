---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-05"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 将简单高速缓存名称服务器用于 Virtuozzo
{: #using-a-simple-caching-name-server-with-virtuozzo}

Virtuozzo 尝试通过使用容器的公共 IP 地址从硬件节点的内部接口发出包。您唯一需要执行的是在硬件节点上运行高速缓存名称服务器，该服务器仅在专用接口上可用。

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


或者，可以使用 Virtuozzo 来添加桥接的网络接口，从内部网块分配唯一的内部 IP 地址，并添加静态路由。内部路由提供了对内部基础架构的访问权，因此如果您在内部网络中的服务器上有混合网络和宽松过滤，请别忘了此替代解决方案。
