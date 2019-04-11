---
copyright:
  years: 2014, 2018
lastupdated: "2018-06-22"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# 迁移 Hyper-V 虚拟机
{: #migrating-a-hyper-v-virtual-machine}

## 开始之前
{: #before-you-begin-migrating-a-hyper-v-virtual-machine}

开始之前，请复查以下先决条件。

* 兼容服务器（Windows 2008 Server DC Edition，带 Hyper-V）。
* 用于从源服务器连接到目标服务器的方法。
* 源和目标服务器必须位于同一 VLAN 上。如果不位于同一 VLAN 上，那么需要重新发出虚拟机的 IP（需要开具支持凭单）。
* 虚拟机具有有效网络上行链路，并已安装有效/受支持的软件。

## 为什么可能需要重新定位 Hyper-V 虚拟机？
可能需要重新定位 Hyper-V 虚拟机的常见原因有两个：
* VM 位于运行不正常的硬件上，
* 当前主机服务器的资源不足。
对于任一种情况，如果您满足先前提及的需求，那么可以快速完成 Hyper-V 迁移，具体请完成以下步骤。

1. 登录到源服务器，并打开 Hyper-V 管理器。选择要迁移到目标服务器的虚拟机。
2. 关闭要迁移的虚拟机。然后，在“服务器操作”列表下选择**导出**，以输入导出文件的位置。
3. 现在，在源服务器上使用 RDP 时，可以登录到安装有 C: 盘的目标服务器，以将文件置于其上。

您将使用 RDP 通过资源安装来传输文件。对于此过程，可以选择最适合您的传输方法（Windows 共享、通过 RDP 的资源安装、FTP 和其他传输方法）。
{:tip}

4. 将文件导出到目标服务器后，请确保该文件位于 Hyper-V 虚拟硬盘 (VHD) 的缺省位置中，然后将其导入。缺省位置为 `C:\Users\Public\Documents\Hyper-V\Virtual hard disks`。如果更改了此缺省位置，并且不确定缺省位置，那么可以选择 **Hyper-V 设置 > 导入虚拟机**来查看该位置。
5. 找到导出的文件并单击**导入**后，虚拟机将使用先前具有的所有配置和文件填充到 Hyper-V 管理器中。服务器现在已联机并正常运行。如果服务器与需求不匹配，并且目标服务器位于其他 VLAN 中，那么需要使用可移植子网（作为“VLAN 上的辅助子网”路由）对虚拟机重新设定 IP。
