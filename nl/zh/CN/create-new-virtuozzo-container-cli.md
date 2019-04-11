---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 创建新的 Virtuozzo 容器 (CLI)
{: #creating-a-new-virtuozzo-container-cli-}

## 如何创建新的 Virtuozzo 容器 (CLI)
{: #how-to-create-a-new-virtuozzo-container-cli-}

完成以下步骤在 Virtuozzo 服务器上创建新容器。

1. 检查主机上的容器列表。

        [root@vztrain01 ~]# vzlist -a

          CTID      NPROC STATUS    IP_ADDR         HOSTNAME

             1         93 running   192.168.135.58  ServiceCT

           100          9 running   67.228.128.93   vztest01.softlayer.local

2. 使用 Open ID 号创建容器。编号必须大于 100 并且尚未在使用中。在此示例中，使用 122。

       [root@vztrain01 ~]# vzctl create 122 --pkgset redhat-as3-minimal-x86_64  --config basic

        正在创建容器专用区域 (redhat-as3-minimal-x86_64/20080630)

        容器已安装

        Postcreate 操作已完成

        容器已卸装

        容器专用区域已创建

        容器已成功注册

        [root@vztrain01 ~]# vzlist -a

          CTID      NPROC STATUS    IP_ADDR         HOSTNAME

             1         93 running   192.168.135.58  ServiceCT

           100          9 running   67.228.128.93   vztest01.softlayer.local

           122          - stopped   -               -

创建容器后，VZ 会处于 stopped 状态。如果不确定安装了哪些包，那么可以使用 `vzpkgls` 命令。此命令显示服务器上已安装的 Virtuozzo 操作系统和应用程序包的列表。在示例中，使用的是 RedHat 模板，但 Virtuozzo 支持多种不同的操作系统：RedHat、CentOS、Debian、Fedora Core 和 SUSE。

3. 复查容器的缺省配置，并进行必要的更改。

        [root@vztrain01 ~]# less /vz/private/122/ve.conf

4. 启动容器。

        [root@vztrain01 ~]# vzctl start 122

        正在启动容器...

        容器已安装

        设置 slm 内存限制

        设置 slm 子组（缺省值）

        正在设置 devperms 20002 dev 0x7d00

        正在向容器 (1) 添加端口重定向：4643 8443

        正在添加 IP 地址：

        配置内存信息：65536

        容器正在启动...

稍候片刻，容器会运行。可以使用 `vzctl` status 122 命令再次检查状态。

5. 添加管理容器所需的参数。

        [root@vztrain01 ~]# vzctl set 122 --ipadd 12.34.234.142 --hostname vz122.domain.com --userpasswd root:password123 --save

        容器的主机名已设置：vz122.domain.com

        正在向池添加 IP 地址：12.34.234.142

        正在添加 IP 地址：12.34.234.142

        正在关闭回送接口：[  正常  ]

        正在设置网络参数：[  正常  ]

        正在启动回送接口：[  正常  ]

        正在启动接口 venet0：[  正常  ]

        正在更改用户 root 的密码。

        passwd：所有认证令牌都已成功更新。

        已保存容器 122 的参数

使用命令行对新 Virtuozzo 容器进行基本设置已完成。
