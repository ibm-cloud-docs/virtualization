---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-16"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 在 XenServer 共享存储器中设置和安装 iSCSI 节点
{: #setting-up-and-mounting-an-iscsi-node-in-xenserver-shared-storage}

共享存储器是一种存储库 (SR)，可以是 XenServer 可使用的任何形式的存储器。您可以从两种 SR 类别中进行选择：共享和非共享。共享 SR 允许多个节点共享同一池中的存储位置。{{site.data.keyword.cloud}} 支持两个 SR 选项：NFS 和 LVMoiSCSI（基于 iSCSI 的 LVM）。

您需要安装 NFS 服务器，并通过其他客户系统或使用 {{site.data.keyword.cloud_notm}} QuantaStor 服务器对其进行管理。请执行以下步骤在 XenCenter 中安装 NFS 存储库：

1. 打开 XenCenter 控制台。
2. 单击**新建存储器**。
* 在新建对话框中，选择**虚拟盘存储器 > NFS**。
* 为新 SR 提供适用名称。
* 在**共享名称**下，输入格式为“服务器:/路径”的名称。
* 单击**扫描**。

此过程会扫描 NFS 共享以查找任何先前的 SR。

可以将 {{site.data.keyword.blockstoragefull}} 用于 LVMoiSCSI。iSCSI 可以来自客户管理存储服务器或 {{site.data.keyword.blockstoragefull}} 产品。对于性能和冗余块存储器，请在 {{site.data.keyword.slportal}} 中，通过转至**存储 > 块存储器 > 选择 LUN 名称**来检索 IQN。要通过 XenCenter 安装 iSCSI 节点，请执行以下步骤：

1. 打开 XenCenter 控制台。
2. 转至**常规 > 属性**，然后设置 IQN。
3. 单击**新建存储器**。
4. 在新建对话框中，转至**虚拟盘存储器 > 软件 iSCSI**。
5. 为新 SR 输入适用名称。
6. 输入 iSCSI 目标的主机名或 IP，并使端口保留缺省值 3260。
7. 选择**使用 CHAP**。
8. 输入 LUN 的用户名和密码。
9. 单击**扫描目标主机**。
* 选择**目标 IQN** 选项。
* 选择**目标 LUN** 选项。
10. 填充 IQN 和 LUN 字段后，单击**完成**。这将扫描目标以检查先前的 SR。如果存在 SR，那么安装程序会询问您是要创建新的 SR，还是连接到先前的 SR。

如果服务器都位于一个池中，那么会自动共享 iSCSI 存储库。

有关配置 iSCSI 以用于多路径的信息，请参阅 [Configuring iSCSI Multipath ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus1000/sw/5_x/sys_mgmt_config/b_Cisco_N1KV_VMware_Sys_Mgmt_Config_5x/b_Cisco_N1KV_VMware_Sys_Mgmt_Config_5x_chapter_01110.html?dtid=osscdc000283){: new_window}。
