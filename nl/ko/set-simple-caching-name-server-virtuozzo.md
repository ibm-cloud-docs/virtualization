---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-05"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Virtuozzo로 단순 캐싱 이름 서버 사용
{: #using-a-simple-caching-name-server-with-virtuozzo}

Virtuozzo는 컨테이너의 공인 IP 주소를 사용하여 하드웨어 노드의 내부 인터페이스에서 패킷을 전송하려고 시도합니다. 이제 사설 인터페이스에서만 사용할 수 있는 하드웨어 노드에서 캐싱 이름 서버를 실행하기만 하면 됩니다.

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


또는 Virtuozzo를 사용하여 브릿지된 네트워크 인터페이스를 추가하고 내부 네트 블록에서 고유 내부 IP 주소를 지정하고 정적 라우트를 추가할 수 있습니다. 내부 라우팅은 내부 인프라에 대한 액세스를 제공하므로, 내부 네트워크의 서버에 혼합 네트워크와 이완된 필터링이 있는 경우 이 대체 솔루션을 염두에 두십시오. 
