---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Parallels Server 4 Bare Metal 服务器概述
{: #overview-of-parallels-server-4-bare-metal}

Parallels Server Bare Metal 是一种虚拟化解决方案，提供 Virtuozzo 软件虚拟化和硬件虚拟化，从而提供适用于虚拟机和容器的技术*。
{:shortdesc}

**命令行界面**

除了通常的 Virtuozzo 命令外，还提供了其他命令：`pctl`、`pmigrate`、`pstat` 和 `prl_disk_tool`。

**迁移**

现在，可以将 Virtuozzo Containers 迁移到虚拟机，将物理服务器迁移到 Virtuozzo Containers，以及将物理服务器迁移到虚拟机，从而允许合并服务。此功能还支持在裸机服务器之间迁移容器或 VM，转换在不同虚拟环境上创建的 VM (V2V)，以及通过模板克隆或部署 Windows VM 时更改 Windows SID。

**IP 地址使用和 VLAN**

可以使用 VM 中的 Parallels Tools 将 VM 从物理服务器分配给 IP。此外，PSBM 还支持在 VM 中创建虚拟交换机和 VLAN，以更好地保护 VM 间的网络流量。

**执行命令**

可以直接通过 Virtuozzo Containers 中的物理服务器来运行原始命令，现在还可以在 VM 中运行原始命令（如果在 VM 中安装了 Parallels Tools），包括用户密码重置。

**进程记帐**

可以使用 Parallels Server Bare Metal 快速增加和减少为 VM 分配的资源的优先级（CPU 和磁盘 I/O 优先级）。

**备份**

Parallels Server Bare Metal 提供了在本地裸机服务器或远程裸机服务器上备份和复原 VM 及容器的功能，以包含完整和增量备份。

**网络记帐**

使用 Parallels Server Bare Metal 可基于当前和历史网络吞吐量，轻松查看和查找 VM 或容器。

**网络**

Parallels Server Bare Metal 使用可移植 IP 地址，而 Virtuozzo 使用可移植或静态 IP 地址（取决于配置）。

\* {{site.data.keyword.BluSoftlayer_full}} 仅许可 Parallels Server 4 Bare Metal 上的硬件虚拟机，除非在订购表单上另有指示。_VM_ = 虚拟机。_V_ = VPS 或容器。
