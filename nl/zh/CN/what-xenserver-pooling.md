---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# XenServer 池
{: #xenserver-pooling}

XenServer 池可以视为包含最多 16 个服务器，作为一个集群运行。此集群不仅支持共享资源，还支持使用 XenMotion 实时迁移虚拟机。其中一个服务器充当“主节点”，而其余服务器充当从属节点。通过 XenCenter 打开池中的任何服务器都将打开整个池的管理信息。节点加入池时，其管理密码会更改为与主节点的管理密码相匹配。但从池中除去节点时，不会更改此密码。

节点中的所有系统都必须来自同一处理器系列，例如 Intel 或 AMD。理想情况下，硬件必须完全相同，才能成功池化。对于异构 CPU 池，不一定会有兼容性问题。您需要自行执行兼容性测试，以了解硬件是否兼容。有关更多信息，请参阅 [Understanding heterogeneous CPU pooling ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](http://support.citrix.com/article/CTX127059){: new_window}。

有两种方法可用于创建 XenServer 池。一种是通过 XenCenter 控制台使用“新建池”选项。请遵循简单的逐步屏幕指示信息来创建系统并将系统添加到池。另一种方法是使用命令行。在新的从属节点上，可以执行以下命令：

        `xe pool-join master-address= master-username= master-password=`

**注**：如果使用的是缺省安装，那么管理 IP 地址是服务器的 IP 地址 10.x.x.x。

使用绑定网络时，新连接的从属节点可能会将其接口连接到不正确的绑定网络。要更正此问题，请通过命令行来销毁从属节点上的绑定，然后使用正确的 PIF 和网络来重新创建绑定。

有两种方法可从池中逐出从属节点。第一种方法是使用 XenCenter。转至**池**，然后选择**除去服务器**。请遵循提供的简单逐步屏幕指示信息进行操作。另一种方法是使用命令行。可以从池中的任何节点执行除去节点操作。您需要找到要逐出的节点的 _uuid_，为此，请运行以下命令：

        `xe host-list`

然后，运行以下命令：

        `xe pool-eject uuid=`

**注**：逐出节点后，该节点会重新引导到全新安装中，这包括除去用于提供公共和专用连接的联网配置。从池中逐出从属节点后，该从属节点的密码仍与主节点相同。您可能必须通过 IPMI 来重新配置联网，因为除去操作可能会破坏从属节点的设置。
