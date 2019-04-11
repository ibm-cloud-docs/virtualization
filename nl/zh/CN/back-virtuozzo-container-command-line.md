---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-18"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 在命令行中备份 Virtuozzo 容器
{: #backing-up-a-virtuozzo-container-in-the-command-line}

使用以下步骤来备份 Virtuozzo 服务器上的容器。

1. 检查主机上的容器列表。

        [root@virtuozzo01 ~]# vzlist

          CTID      NPROC STATUS    IP_ADDR         HOSTNAME

             1         86 running   192.168.151.2   ServiceCT

           101          5 running   75.126.27.32    VZTest01.softlayer.local

           201          9 running   12.34.234.142   vz201.domain.com

         [root@virtuozzo01 ~]#

2. 验证与备份硬件节点的连接后，启动备份。

        [root@virtuozzo01 ~]# ping virtuozzo00.softlayer.local

        PING virtuozzo00.softlayer.local (75.126.77.100) 56(84) bytes of data.

        64 bytes from (75.126.77.100): icmp_seq=1 ttl=64 time=0.149 ms

        64 bytes from (75.126.77.100): icmp_seq=2 ttl=64 time=0.142 ms

        [root@virtuozzo00 ~]# vzabackup virtuozzo01.softlayer.local -e 201

        * 对环境 vz201.domain.com 的操作 backup_env 已启动

        * 正在将环境 vz201.domain.com 备份到 10.4.9.85

        * 正在检查参数

        * 正在转储配额

        * 正在创建备份 5daa073c-2dd0-0a41-8274-a1ba1ee0d693/20081029151602

        * 正在调整备份类型（完全）

        * 备份存储器：正在接收备份文件

        * 正在准备备份操作

        * 正在备份专用区域

        100% |**************************************************************|

        * 正在发送专用备份数据

        * 备份存储器：正在存储专用备份数据

        * 备份存储器：正在填充生成的备份信息

        * 对环境 vz201.domain.com 的操作 backup_env 成功完成

        .

        对节点“virtuozzo01.softlayer.local”的备份操作成功完成。

关于上述命令，须注意两点：
1. 您必须登录到“目标”服务器（您希望备份所在的服务器）并运行备份命令。运行该命令后，将对来自原始硬件节点的容器标识发出请求。
2. `-e` 选项用于指定单个容器。如果没有此选项，命令会将主机服务器上的所有容器备份到新位置。

        [root@virtuozzo00 vz]# vzabackup virtuozzo01.softlayer.local

        对环境 vz201.domain.com,VZTest01.softlayer.local 的操作 backup_env 已启动


可以使用以下命令来查看在服务器上创建和存储的备份：

    [root@virtuozzo00 vz]# vzarestore --list --full

    显示现有备份...

    CTID：100

    标题：migrateme

    类型：完全

    备份标识：ab8743f4-d436-3a44-b35d-090f2bd2ea35/20081023173802

    描述：这是对 virtuozzo00 上 migrateme 的初始完全备份。

    父环境：virtuozzo00.softlayer.local

    大小：97.78 Mb

    创建日期：2008-10-23T123802-005

    CTID：101

    标题：VZTest01.softlayer.local

    类型：完全

    备份标识：f440f67b-76e7-684b-9f1b-f64f755803f7/20081029153257

    描述：

    父环境：virtuozzo01.softlayer.local

    大小：98.22 Mb

    创建日期：2008-10-29T103257-005

    CTID：201

    标题：vz201.domain.com

    类型：完全

    备份标识：5daa073c-2dd0-0a41-8274-a1ba1ee0d693/20081029151602

    描述：

    父环境：virtuozzo01.softlayer.local

    大小：3.80 Mb

    创建日期：2008-10-29T101602-005

    CTID：201

    标题：vz201.domain.com

    类型：完全

    备份标识：5daa073c-2dd0-0a41-8274-a1ba1ee0d693/20081029153047

    描述：

    父环境：virtuozzo01.softlayer.local

    大小：3.80 Mb

    创建日期：2008-10-29T103047-005
