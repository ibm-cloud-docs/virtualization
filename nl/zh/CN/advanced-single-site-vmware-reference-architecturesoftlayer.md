---
copyright:
  years: 2014, 2019
lastupdated: "2019-01-15"

subcollection: virtualization

keywords: vmware, ESXi
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# 高级单站点 VMware 参考体系结构
{: #advanced-single-site-vmware-reference-architecture}

使用以下步骤可创建高级单站点 vSphere 环境。以下步骤使用 VMware 建议的最佳实践定义了 {{site.data.keyword.cloud}} 参考体系结构部署。

参考体系结构旨在供应使用共享存储器、VMware 高可用性 (HA) 和 vSphere 分布式资源调度程序 (DRS) 以及 {{site.data.keyword.cloud_notm}} 网关或防火墙的环境。高级单站点 VMware 参考体系结构适用于大多数生产部署，可根据工作负载的指示进行缩放，并且可以替换内部部署实现，或扩展到混合 IT 场景中。

## 环境概述
{: #environment-overview}

所概述的高级代表性 VMware 环境包含一个数据中心，用于管理两个不同的集群：管理集群和容量集群。可以使用一个四节点集群来设置配置，具体取决于需求。管理集群包含用于管理基础架构的以下虚拟机 (VM)：

* VMware vCenter Server 5.5 或 6.0 设备
* Microsoft Windows 2012 R2 Standard，具有 Active Directory 和域名系统 (DNS) 角色

容量集群包含创建和运行 VM 所需的资源和基础架构。对于联网，环境包含三个专用内部 VLAN 以及一个用于外部通信的公用 VLAN。表 1 指定了在整个环境中使用的 VLAN 类型和 VLAN 名称。

|VLAN 类型|VLAN 名称|描述|                                                                             |
|---|---|---|
|主专用|Management|分配用于管理和访问物理 ESXi 主机和虚拟服务器。|
|主专用|Storage|分配用于管理和访问连接到每个 ESXi 主机的共享存储器。|
|主专用|VM Access|分配给在每个 ESXi 主机上运行的虚拟机。|
|主公用|Public|分配给需要从公用网络访问的虚拟机或其他设备。|
<caption>表 1. 主 VLAN</caption>

对于共享存储器，可以使用 OS Nexus QuantaStor（一种单租户共享存储服务器）或者 {{site.data.keyword.cloud_notm}} 耐久性或性能存储服务。无论使用哪种类型，共享存储设备都用于存储管理和容量集群上的 VM。有关存储选项的更多信息，请参阅 [{{site.data.keyword.cloud_notm}} Storage solutions ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://www.ibm.com/cloud/storage){: new_window}。

存储环境已配置为支持 NFS 卷。

## 步骤 1：订购主公用和专用 VLAN
{: #step-1-ordering-primary-public-and-private-vlans}

**注：**VLAN 订单须经过复查和核准。没有任何特定条件或需求可将 VLAN 订单标记为自动核准。VLAN 订单可能因各种原因而被拒绝。

在此步骤中将创建四个 VLAN，分别用于管理、存储、VM 和公共访问。订购服务器之前，建议您先创建这四个 VLAN。要订购 VLAN，请首先确保服务器已放置在正确的 VLAN 上和正确的数据中心内。

环境由五个 ESXi 主机（两个用于管理集群，三个用于容量集群）和一个虚拟服务器组成。专用管理 VLAN 包含一个具有 16 个 IP 地址的子网，用于为该 VLAN 提供支持。用于存储和 VM 流量的主专用 VLAN 包含 8 个地址，因为环境包含一个存储服务器，并且多个 VM 使用一个可移植子网。如果需要将更大的子网范围用于管理网络，请调整范围，计算方法是 ESXi 主机数乘以 2。此外，请确保指定为其创建这些 VLAN 的数据中心。

登录到 {{site.data.keyword.cloud_notm}} 后，通过选择**支持 > 添加凭单**来开具针对管理和虚拟机 VLAN 的支持凭单。使用**表 2** 中的信息来填写相应字段。

|字段|值|
|---|---|
|主题|专用网络问题|
|标题|订购 VLAN|
|详细信息|供应三个主专用 VLAN 和一个主公用 VLAN。针对每个 VLAN，关联以下寻址方案：<br/><ul><li>对于主专用 VLAN，关联 1 个 18（16 个地址）</li><li>对于主专用 VLAN，关联 2 个 29（8 个地址）</li><li>对于主公用 VLAN，关联 1 个 29（8 个地址）</li></ul>|
|为什么需要这些额外的 VLAN？|将主机、存储器和 VM 放置在 VMware 环境的其他网络上。
|需要公用和/或专用 VLAN 吗？|专用和公用|
|需要多少个 VLAN？|专用 = 3 个，公用 = 1 个|
|需要多少个 IP 地址？|每个 VLAN 8 到 16 个|
|VLAN 需要位于哪种路由器后面？|这无关紧要，因为 VLAN 全部位于同一 pod 中。|
|VLAN 需要位于哪个 pod 中？|VLAN 全部位于 <DATA CENTER NAME> 中的同一 pod 中。|
<caption>表 2. 支持凭单信息</caption>

供应 VLAN 后，请记下 VLAN 编号、子网范围和网关，然后将其分配给逻辑 vSphere 网络。可以使用“附录 A：VLAN 工作表”中的工作表来记录 VLAN 和关联的信息。例如：

|VLAN 类型|VLAN 编号|IP 范围|网关|用途|
|---|---|---|---|---|
|主专用|1101|10.X.Y.Z/28|10.X.Y.1|管理|
|主专用|1102|10.A.B.C/29|10.A.B.1|存储|
|主专用|1103|10.Q.R.S/29|10.Q.R.1|虚拟机|
|主公用|2101|75.S.T.U/29|75.S.T.1|公共访问|
<caption>表 3. 主 VLAN 样本</caption>

**注：**在供应 VLAN 之前，不要继续执行下一步。

## 步骤 2：订购可移植专用 IP 地址
{: #step-2-ordering-portable-private-ip-addresses}

在步骤 2 中，发出请求来创建可移植专用子网，以用于容量集群中的管理 VM、VM 内核存储器访问和 VM。您需要确定每个 VLAN 子网需要的地址数。在代表性环境中，订购以下地址：

* 管理 VLAN - 1 个 /29，含 8 个地址 - 一个地址用于 vCenter 设备；一个地址用于 DNS 和 Active Directory。
* 存储 VLAN - 1 个 /28，含 16 个地址 - 在同一 VLAN 上创建两个子网用于存储，每个 ESXi 主机上两个 VM 内核端口，ESXi 主机使用不同的子网来访问共享存储设备。
* VM VLAN - 1 个 /27，含 32 个地址 - 这些地址用于将 IP 地址分配给容量集群中的 VM。

订购某个数量时，请确保考虑在接下来的 30 天和 6 个月内需要多少个 IP 地址。另外值得注意的是，{{site.data.keyword.cloud_notm}} 针对每个可移植子网块保留 3 到 4 个 IP 地址。因此，订购 4 个 IP 地址实际可用的是 1 个 IP 地址，或者如果 pod 支持热备用路由器协议，那么没有任何 IP 地址可用。

要针对您希望创建的每个子网的每个 VLAN 订购一个可移植 IP 地址块，请使用以下步骤：

1. 打开浏览器窗口并登录到 {{site.data.keyword.cloud_notm}}。
2. 选择**帐户 > 下订单**。
3. 在弹出窗口中，转至**网络 > 子网/IP > 订购**。
4. 从下拉菜单中，选择**可移植专用**。
5. 选择 **XX 个可移植专用 IP 地址**，然后单击**继续**。**注：**_XX_ 指定 IP 地址数。
6. 选择要与 IP 地址块关联的 VLAN，然后单击**继续**。
7. 填写屏幕上的信息，然后单击**继续**。

创建 IP 地址的速度相当快，然后可通过从**网络 > IP 管理**中选择**子网**来显示这些地址。您可以在“附录 A：VLAN 工作表”中找到的工作表中记录这些 IP 地址。

## 步骤 3：订购虚拟服务器
{: #step-3-ordering-a-virtual-server}

在此步骤中，将供应 Windows 2012 R2 标准虚拟服务器，以用作 ISO 的实用程序服务器，通过该服务器，还可以访问环境。

1. 打开浏览器窗口并登录到 {{site.data.keyword.cloud_notm}}。
2. 选择**帐户 > 下订单**。
3. 转至**虚拟服务器 > 按小时或按月**。
4. 选择相应的数据中心（在其中创建了 VLAN）以供应虚拟服务器，并为每个选项指定以下规范：
  * 计算实例 - 1 个 2.0 GHz 核心
  * RAM：4 GB
  * 操作系统：Windows Server 2012 R2 Standard Edition（64 位）
  * 第一个磁盘：100 GB (SAN)
  * 上行端口速度 - 1 Gbps 公用和专用网络上行链路
5. 单击**继续处理订单**，然后在**订单摘要和计费**屏幕上，选择后端和前端 VLAN。**注：**选择 VLAN 非常重要，这样实用程序才可以放置到数据中心内的正确 pod 中。对于示例环境，后端 VLAN 是管理 VLAN (1101)，前端 VLAN 是公用 VLAN (2101)。
6. 输入服务器的主机名和域名，然后单击**下订单**。

## 步骤 4：订购 ESXi 主机和网关/防火墙
{: #step-4-ordering-esxi-hosts-and-gateway-firewall}

供应虚拟服务器时，需要订购 ESXi 主机、Brocade vRouter (Vyatta) 网关和防火墙设备。请同时订购所有这些服务器，以便可以同时放置到同一 pod 中。在不同时间订购硬件可能会导致主机和防火墙位于 {{site.data.keyword.cloud_notm}} 数据中心内的不同 pod 中。

### ESXi 主机
{: #esxi-hosts}

对于为环境订购的每个 ESXi 主机，操作系统是 VMware ESXi 5.5。如果要使用 {{site.data.keyword.cloud_notm}} vSphere 许可证，那么将根据使用量产生每月费用。

另一个选项是使用您自己的 ISO 来安装 ESXi。此过程的指示信息可以在[通过远程控制台和虚拟介质安装 VMware vSphere ESXi](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi) 中找到。如果要使用自己的 ISO 来安装 ESXi，请确保在订购过程中，对于管理和容量主机的操作系统，选择**无操作系统**。

**注：**此实现需要 Enterprise Plus 许可才能使用 vSphere 分布式虚拟交换机。如果您的许可证对于 Enterprise Plus 无效，那么需要使用 {{site.data.keyword.cloud_notm}} 提供的 VMware 服务提供者计划 (VSPP) 许可证。

### 订购管理主机
{: #ordering-management-hosts}

使用以下步骤来订购管理主机服务器。

1. 打开浏览器窗口并登录到 {{site.data.keyword.cloud_notm}}。
2. 选择**帐户 > 下订单**。
3. 选择**裸机服务器 > 按月**。
4. 选择符合服务器列表屏幕上管理集群需求的相应服务器。**注：**ESXi 5.5 至少需要一个双核处理器、4 GB RAM 和 1 Gb 以太网控制器。
5. 选择相应的数据中心（在其中创建了 VLAN）以供应 ESXi 服务器，并为每个选项指定以下规范：
  * 数量：2
  * RAM：32 GB
  * 操作系统：VMware ESXi 5.5（如果使用的是[通过远程控制台和虚拟介质安装 VMware vSphere ESXi](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi)，请选择“无操作系统”）
  * 硬盘：磁盘 1 和 2：500 GB SATA（RAID 1 模式）
  * 公共带宽：仅限专用网络 -> 0 GB 带宽
  * 上行端口速度：10 Gbps 冗余专用网络上行链路
6. 单击**继续处理订单**。
7. 单击**添加服务器**以开始向订单添加容量集群的 ESXi 主机。

### 订购容量主机
{: #ordering-capacity-hosts}

1. 选择符合服务器列表屏幕上容量集群主机需求的相应服务器。**注：**ESXi 5.5 至少需要一个双核处理器、4 GB RAM 和一个 1 GB 以太网控制器。
2. 选择相应的数据中心（在其中创建了 VLAN）以供应 ESXi 服务器，并为每个选项指定以下规范：
  * 数量：3
  * RAM：128 GB
  * 操作系统：VMware ESXi 5.5（如果使用的是[通过远程控制台和虚拟介质安装 VMware vSphere ESXi](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi)，请选择“无操作系统”）
  * 硬盘：磁盘 1 和 2：500 GB SATA（RAID 1 模式）
  * 公共带宽：仅限专用网络 > 0 GB 带宽
  * 上行端口速度：10 Gbps 冗余专用网络上行链路
3. 单击**继续处理订单**。

### 完成配置
{: #completing-configuration}

现在，您的购物车中已有 ESXi 主机。要正确供应设备，您需要为设备分配公用 VLAN（如果适用）、专用 VLAN、主机名和域。

1. 为设备分配以下 VLAN 并创建主机名：

* ESXi 主机 - 后端 VLAN：(1101)
* 后端 VLAN：(1101)
* 前端 VLAN：(2101)

2. 选择付款方式，同意条款和条件，然后单击**最终完成订单**。**重要信息：**在供应了服务器，并且可通过上一步中订购的 VPN 或虚拟服务器访问服务器之前，不要继续执行步骤 5。

## 步骤 5：在 BCS 交换机上中继 VLAN
{: #step-5-trunking-vlans-on-bcs-switches}

缺省情况下，端口会以访问方式放置在后端客户交换机（即，pod 中的专用网络交换机，例如后端客户交换机 (BCS)）上。因此，需要中继连接到 ESXi 主机的端口，以便主机可以访问存储器，并且 VM 可以在专用网络上进行通信。

在开具凭单以中继 VLAN 之前，您需要确定哪些网络接口位于专用网络上。要确定接口，请转至每个 ESXi 服务器的**设备详细信息**，然后转至**网络 > 专用**。对于此环境，`eth0` 和 `eth2` 是专用网络适配器。

除了对 ESXi 主机的 VLAN 进行中继外，还必须取消绑定管理主机和容量主机的 NIC。之所以要取消绑定 NIC，是因为链路聚集控制协议 (LACP) 与软件 iSCSI 多路径功能不兼容。

要中继 VLAN 并取消绑定 NIC，需要通过执行以下步骤来开具凭单：

1. 打开浏览器窗口并登录到 {{site.data.keyword.cloud_notm}}。
2. 单击**支持 > 添加凭单**。
3. 输入以下信息：
  * 主题：专用网络问题
  * 标题：中继 VLAN 并取消绑定 NIC
  * 详细信息：对以下主机的 eth0 和 eth2 NIC 对的 VLAN（`<Management VLAN>`、`<Storage VLAN>` 和 `<VM VLAN>`）进行中继：[列出每个 ESXi 主机]。此外，在以下服务器上取消绑定（除去 LACP）专用 NIC（eth0 和 eth2）：[列出每个 ESXi 主机]
4. 单击**添加凭单**。

确保将 <> 中指定的 VLAN 更改为实际 VLAN

## 步骤 6：配置管理主机联网
{: #step-6-configuring-management-host-networking}

供应服务器并中继 VLAN 后，需要在管理集群中的主机上设置联网。对于此配置，请将 vSphere 标准交换机用于管理集群。使用可移植 IP 地址来配置 VM 内核端口组，但 vMotion 和容错端口组除外。**注：**您可以将自己的 IP 方案用于 vMotion 和容错，因为流量不需要进行路由。但是，所有主机都需要与集群中的其他主机位于同一子网上，才能使用 vMotion 和容错功能。如果需要对子网进行路由，建议使用 {{site.data.keyword.cloud_notm}} 可移植 IP 地址。

要配置端口组，vSphere Client 必须安装在实用程序虚拟服务器上，或安装在通过 {{site.data.keyword.cloud_notm}} 管理 VPN 访问主机的工作站上。

1. 打开浏览器窗口并登录到 {{site.data.keyword.cloud_notm}}。
2. 连接到实用程序服务器或 {{site.data.keyword.cloud_notm}} VPN 后，启动 vSphere Client。
3. 输入其中一个管理 ESXi 主机的 IP 地址、用户名和密码。（通过选择**设备详细信息 > 密码**，可以找到 ESXi 主机的 root 用户密码。
4. 转至**配置 > 联网**，然后在 vSphere 标准交换机（最有可能是 vSwitch0）上创建或修改以下端口组。

针对管理集群中的每个主机，完成以下步骤。

### vSwitch0 属性
{: #vswitch0-properties}

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 4. vSwitch0 属性</caption>
<tbody>
<tr><th>网络适配器</th><th>vmnicX 和 vmnicY</th></tr>
<tr><td>负载均衡</td><td>基于发起虚拟端口标识的路由器</td></tr>
<tr><td>活动适配器</td><td>vmnicX 和 vmnicY</td></tr>
</tbody>
</table>

### 编辑现有虚拟机端口组
{: #edit-existing-virtual-machine-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 5. vmPG-Management 端口组</caption>
<tbody>
<tr><th>网络标签</th><th>*vmPG-Management*</th></tr>
</tbody>
</table>

### 编辑现有 VM 内核端口组
{: #edit-existing-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 6. vmkPG-Management 端口组</caption>
<tbody>
<tr><th>网络标签</th><th>*vmkPG-Management*</th></tr>
</tbody>
</table>

### 添加 vMotion VM 内核端口组
{: #add-vmotion-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 7. vMotion 端口组</caption>
<tbody>
<tr><th>连接类型</th><th>VMKernel</th></tr>
<tr><td>网络标签</td><td>*vmkPG-vMotion*</td></tr>
<tr><td>VLAN 标识</td><td>*&lt;Storage VLAN&gt; (1102)*</td></tr>
<tr><td>vMotion 流量</td><td>已启用</td></tr>
<tr><td>IP 地址</td><td>*172.16.10.X/24*<br/><br/>*用户定义的地址，可以是其他子网。确保每个主机上的其他 vMotion 地址位于同一子网上。*</td></tr>
<tr><td>子网掩码</td><td>255.255.255.0</td></tr>
</tbody>
</table>

### 添加容错 VM 内核端口组
{: #add-fault-tolerance-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 8. FT 端口组</caption>
<tbody>
<tr><th>连接类型</th><th>VM 内核</th></tr>
<tr><td>网络标签</td><td>*vmkPG-FT*</td></tr>
<tr><td>VLAN 标识</td><td>*&lt;Storage VLAN&gt; (1102)*</td></tr>
<tr><td>容错日志记录</td><td>已启用</td></tr>
<tr><td>IP 地址</td><td>*172.16.20.X/24*<br/><br/>*用户定义的地址，可以是其他子网（如果需要）。确保每个主机上的其他 FT 地址位于同一子网上。*</td></tr>
<tr><td>网络掩码</td><td>255.255.255.0</td></tr>
</tbody>
</table>

### 添加存储 VM 内核端口组
{: #add-storage-vm-kernel-port-group}

使用分配的主机和 VM 内核端口的名称，更新每个可移植 IP 地址的**注释**部分。可以通过转至 {{site.data.keyword.slportal_full}}，并选择**网络 > IP 管理 > 子网 > [子网]** 来找到**注释**部分。

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 9. 存储器端口组</caption>
<tbody>
<tr><th>连接类型</th><th>VM 内核</th></tr>
<tr><td>网络标签</td><td>*vmkPG-Storage*</td></tr>
<tr><td>VLAN 标识</td><td>*&lt;Storage VLAN&gt; (1102)*</td></tr>
<tr><td>IP 地址</td><td>*可移植专用地址*<br/><br/>*从绑定到存储 VLAN 的可移植专用地址中选择的 IP 地址。*</td></tr>
<tr><td>网络掩码</td><td>*与 IP 范围关联的子网掩码*</td></tr>
</tbody>
</table>

### 添加公共地址端口组
{: #add-public-address-port-group}

使用分配的主机和 VM 内核端口的名称，更新每个可移植 IP 地址的**注释**部分。在 {{site.data.keyword.slportal}} 中，可以通过选择**网络 > IP 管理 > 子网 > [子网]** 来找到**注释**部分。

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 10. 公共端口组</caption>
<tbody>
<tr><th>连接类型</th><th>虚拟机</th></tr>
<tr><td>网络标签</td><td>vmPG-Public</td></tr>
<tr><td>VLAN 标识</td><td>&lt;Primary Public VLAN&gt;（例如，2101）</td></tr>
</tbody>
</table>

## 步骤 7：下载或上传 ISO 映像和 vCenter 虚拟设备
{: #step-7-download-or-upload-iso-images-and-vcenter-virtual-appliance}

现在，环境已准备好部署 VMware vCenter 虚拟设备，并为 DNS、Active Directory 或 BIND 安装虚拟机。但是，部署之前，需要下载映像。要下载映像，请通过远程桌面访问先前供应的虚拟服务器，并在该虚拟服务器上下载与您的环境相应的映像：

* Active Directory/Windows DNS：Windows Server 2008R2/Windows Server 2012 ISO 映像（您的许可介质）
* Linux BIND：[CentOS 安装映像 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://isoredirect.centos.org/centos/6/isos/x86_64/){: new_window}
* [vCenter 虚拟设备 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://my.vmware.com/web/vmware/info/slug/datacenter_cloud_infrastructure/vmware_vsphere/5_5){: new_window}（需要有效的 VMware 预订）

下载 ISO 后，需要将映像上传到管理主机数据存储，以便可以将其连接到虚拟机。使用 vSphere Client 连接到管理主机，并在本地数据存储上创建 ISO 目录。

## 步骤 8：部署 DNS
{: #step-8-deploying-dns}

您需要部署 VM（在管理主机上）并安装 DNS 服务。使用传统 vSphere Client 在 Windows 或 Linux ISO 所在的管理 ESXi 主机上创建 VM。连接相应的 ISO（Windows 或 CentOS）以在该 VM 上部署 DNS 服务器。确保将该 VM 与先前步骤中创建的 VM 端口组（vmpg-management 端口组）相关联。

安装 VM 后，从可移植专用子网组中分配 IP 地址和缺省网关。如果使用的是“附录 A”中的 VLAN 工作表，请从管理 VM 子网中进行分配。使用分配的 VM 的名称来更新每个可移植 IP 地址的**注释**部分。可以通过转至 {{site.data.keyword.slportal}}，并选择**网络 > IP 管理 > 子网 > [子网]** 来找到**注释**部分。

虽然详细说明启用 DNS 所需的步骤超出了本文档的范围，但还是提供了以下指导信息：

1. 将 **DNS 转发**设置为 service.softlayer.com 本地 DNS 主机：
  * `rs1.service.softlayer.com 10.0.80.11`
  * `rs2.service.softlayer.com 10.0.80.12`
2. 设置 DNS 后，创建本地 DNS 区域 (dal06.mycompany.local) 和逆向查找区域，以用于供应的所有可移植子网和主子网。
3. 为每个主机的管理 IP 地址（vmkPG-management 上的 vmk0）添加 A HOST 记录。
4. 从绑定到 vCenter 虚拟设备管理 VLAN 的可移植专用子网添加 A HOST 记录。
5. 更新分配给 vCenter 的可移植 IP 子网的**注释**部分。

有关更多信息，请参阅以下链接：
* [Windows DNS 和 Active Directory ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://social.technet.microsoft.com/wiki/contents/articles/12370.step-by-step-guide-for-setting-up-a-windows-server-2012-domain-controller.aspx){: new_window}。
<!--* [CentOS BIND ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.centos.org/docs/2/rhl-rg-en-7.2/s1-bind-configuration.html){: new_window}.-->

## 步骤 9：部署和配置 vCenter 虚拟设备
{: #step-9-deploying-and-configuring-vcenter-virtual-appliance}

既然已配置 DNS，现在可以部署和配置 vCenter Server Appliance。要部署该设备，请执行以下步骤：

1. 通过远程桌面访问虚拟服务器，并打开 vSphere Client。
2. 连接到管理主机，然后选择**文件 > 部署 OVF 模板**。
3. 遵循向导来完成部署。

<!--For more information about OVF Template deployment, see [VMware documentation ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://pubs.vmware.com/vsphere-55/index.jsp?topic=%2Fcom.vmware.vsphere.hostclient.doc%2FGUID-6C847F77-8CB2-4187-BD7F-E7D3D5BD897B.html&resultof=%22thick%22%20%22thin%22%20){: new_window}.-->

由于没有动态主机配置协议 (DHCP) 服务器可用于在打开电源时为 vCenter 虚拟设备分配 IP 地址，因此必须使用根控制台来配置设备。**注：**vCenter 虚拟设备控制台上会显示`未检测到联网`消息。

要配置设备，请执行以下步骤：

1. 使用**用户名**（“root”）**密码**（“vmware”）登录到控制台。
2. 运行 `/opt/vmware/share/vami/vami_config_net`，并遵循文本向导来配置 IP、子网和 DNS 属性。请记住使用在**步骤 8：部署 DNS** 中创建的 DNS 或 BIND 服务器的 IP 地址。
3. 保存网络设置，退出控制台，然后在虚拟服务器上打开浏览器。
4. 转至向 vCenter 虚拟设备 (VCVA) 提供的附加有端口 5480 的 IP 地址<!-- (https://:5480)-->。
5. 接受向导中的 EULA，回答**客户改进体验计划**问题，然后选择**配置选项 > 设置定制配置**。
6. 单击**下一步**，然后输入以下值：
<table border="1" cellpadding="0" cellspacing="0"><caption>表 11. VCVA 设置向导</caption><tbody><tr><th>向导菜单</th><th>选项</th><th>值</th></tr><tr><td>数据库设置</td><td>数据库类型</td><td>已嵌入</td></tr><tr><td>SSO 设置</td><td>SSO 部署类型</td><td>已嵌入</td></tr><tr><td>SSO 设置</td><td>新管理员密码*（对于 administrator@vsphere.local）*</td><td>&lt;输入密码&gt;</td></tr><tr><td>SSO 设置</td><td>重新输入新密码</td><td>&lt;输入先前使用的相同密码&gt;</td></tr><tr><td>时间同步</td><td>NTP 同步</td><td>servertime.service.softlayer.com</td></tr></tbody></table>
7. 单击**启动**。这将配置 VCVA。
8. 使用**管理员**下的选项来更改 root 用户密码。
9. 从 VCVA 配置 Web 页面注销。
10. 通过输入附加有 `9443/vsphere-client` 的 VCVA IP 地址<!-- (https://:9443/vsphere-client)-->，转至 vSphere Web Client。
11. 以 root 用户身份登录，然后选择**管理 > 许可证**。
12. 输入 VMware vCenter 许可证。

## 步骤 10：创建 vCenter 集群和分布式虚拟交换机
{ #step-10-create-vcenter-clusters-and-distributed-virtual-switch}

既然 VCVA 已配置且获得许可，现在可以为容量集群创建数据中心和集群构造以及分布式虚拟交换机。

### 创建数据中心和集群
{: #creating-data-center-and-clusters}

1. 在 vSphere Client 上，转至**主页**屏幕。
2. 选择**入门**，然后单击**单击此处**。
3. 单击**创建数据中心**。
4. 输入数据中心名称（示例环境是多伦多 01 数据中心。请使用`多伦多 01` 作为数据中心名称）。
5. 创建数据中心后，单击**入门**页面上的**创建集群**。
6. 将第一个集群命名为`管理`。使其他所有选项均保持未选中状态，然后单击**确定**。
7. 使用与创建管理集群相同的过程来创建其他集群。

现在，可以使用**添加主机**将管理和容量主机添加到管理和容量集群。确保使用服务器的标准域名 (FQDN)，而不是使用 IP 地址，以便在添加每个主机时使用 DNS。

将 ESXi 主机添加到 vCenter 后，您可能会注意到有关在每个 ESXi 主机上启用 shell 和 SSH 的一些警告消息。要禁止显示这些警告，请在弹出窗口上单击**禁止显示警告 > 是**。如果未显示“禁止显示警告”链接，请执行以下步骤：

1. 转至 ESXi 主机的**管理**选项卡。
2. 选择**设置**，然后单击**高级系统设置**。
3. 找到 **UserVars.SupressShellWarning** 键，并将值更改为 **1**。
4. 单击**确定**。

将管理和容量主机添加到其各自的集群后，转至每个主机并设置 DNS 和 NTP。要设置 DNS，请执行以下步骤：

1. 单击主机，然后选择**管理 > 联网**。
2. 选择缺省系统堆栈（**TCP/IP 配置**），然后单击“画笔”图标。
3. 输入先前创建的 DNS 服务器的 IP 地址以及主机和域名。

对于 NTP 设置：

1. 转至**管理 > 设置 > 时间配置**。
* 对于 NTP 服务器，输入 `servertime.service.softlayer.com`。
* 将 **NTP 服务启动策略**设置为`使用主机启动和停止`。

***为容量主机创建分布式虚拟交换机***

1. 使用 vSphere Web Client 转至**联网**，然后右键单击数据中心名称。
2. 选择**新建分布式交换机**。
3. 命名分布式交换机，然后单击**下一步**。
4. 选择相应的分布式交换机版本，然后单击**下一步**。
5. 在**编辑设置**屏幕上，对于上行链路数，输入 `2`，并清除**创建缺省端口组**选项。
6. 单击**下一步 > 完成**，然后创建分布式虚拟交换机。

***为分布式虚拟交换机创建端口组***

既然分布式虚拟交换机已存在，现在必须创建用于 vMotion、容错、VM 和存储器的端口组。

***创建 dvpg-Private-VM Management 端口组***

1. 使用 vSphere Web Client 转至“联网”部分。
2. 右键单击分布式虚拟交换机 (DVS)。
3. 单击**新建分布式端口组...**，然后对于第一个端口组，输入表 14 中的信息。
4. 对于表中未指定的任何选项，保留缺省值。

|新建分布式端口组菜单|字段|值|
| --- | --- | --- |
|名称和位置|名称|dvpg-Private-VM Management|
|配置设置|高级|选中“定制缺省策略配置”|
|配置策略（组队和故障转移）|负载均衡|基于物理 NIC 负载的路由|
|配置策略（组队和故障转移）|故障转移顺序|活动上行链路：上行链路 1 和上行链路 2|
<caption>表 12. DVS VM Management 端口组</caption>

创建第一个端口组后，使用以下配置选项来创建其余端口组（表 15 至表 19）。

***创建 dvpg-Private-vMotion 端口组***

|新建分布式端口组菜单|字段|值|
|---|---|---|
|名称和位置|名称|dvpg-Private-vMotion|
|配置设置|VLAN 类型|VLAN|
|配置设置|VLAN 标识|&lt;Storage VLAN&gt;|
|配置设置|高级|选中“定制缺省策略配置”|
|配置策略（组队和故障转移）|负载均衡|基于物理 NIC 负载的路由|
|配置策略（组队和故障转移）|故障转移顺序|活动上行链路：上行链路 1 和上行链路 2|
<caption>表 13. DVS vMotion 端口组</caption>

***创建 dvpg-Private-Fault Tolerance 端口组***

|新建分布式端口组菜单|字段|值|
|---|---|---|
|名称和位置|名称|dvpg-Private-Fault Tolerance|
|配置设置|VLAN 类型|VLAN|
|配置设置|VLAN 标识|&lt;Storage VLAN&gt;|
|配置设置|高级|选中“定制缺省策略配置”|
|配置策略（组队和故障转移）|负载均衡|基于物理 NIC 负载的路由|
|配置策略（组队和故障转移）|故障转移顺序|活动上行链路：上行链路 1 和上行链路 2|
<caption>表 14. DVS FT 端口组</caption>

***创建 dvpg-Private-VM Access 端口组***

|新建分布式端口组菜单|字段|值|
|---|---|---|
|名称和位置|名称|dvpg-Private-VM Access|
|配置设置|VLAN 类型|VLAN|
|配置设置|VLAN 标识|&lt;Virtual Machine VLAN&gt;|
|配置设置|高级|选中“定制缺省策略配置”|
|配置策略（组队和故障转移）|负载均衡|基于物理 NIC 负载的路由|
|配置策略（组队和故障转移）|故障转移顺序|活动上行链路：上行链路 1 和上行链路 2|
<caption>表 15. DVS VM Access 端口组</caption>

***创建 dvpg-Private-Storage***

|新建分布式端口组菜单|字段|值|
|---|---|---|
|名称和位置|名称|dvpg-Private-Storage Path A|
|配置设置|VLAN 类型|VLAN|
|配置设置|VLAN 标识|&lt;Storage VLAN&gt;|
|配置设置|高级|选中“定制缺省策略配置”|
|配置策略（组队和故障转移）|负载均衡|基于物理 NIC 负载的路由|
|配置策略（组队和故障转移）|故障转移顺序|活动上行链路：上行链路 1 和上行链路 2|
<caption>表 16. DVS Storage 端口组</caption>

***创建 dvpg-Primary-Public***

|新建分布式端口组菜单|字段|值|
|名称和位置|名称|dvpg-Private-Storage|
|配置设置|VLAN 类型|VLAN
|配置设置|VLAN 标识|&lt;主公共 VLAN&gt;|
|配置设置|高级|选中“定制缺省策略配置”|
|配置策略（组队和故障转移）|负载均衡|基于物理 NIC 负载的路由|
|配置策略（组队和故障转移）|故障转移顺序|活动上行链路：上行链路 1 和上行链路 2|
<caption>表 17. DVS Storage Path B 端口组</caption>

## 步骤 11：将容量集群中的 ESXi 主机迁移到分布式虚拟交换机
{: #step-11-migrating-esxi-hosts-in-capacity-cluster-to-distributed-virtual-switch}

既然容量主机已添加到容量集群，现在可以将虚拟标准交换机配置迁移到*步骤 10：创建 vCenter 集群和分布式虚拟交换机*中创建的分布式虚拟交换机。迁移操作只对一个主机执行，您随后可应用主机概要文件来配置集群的其余主机。

开始添加 VMkernel 适配器之前，请为分布式虚拟交换机上的上行链路分配 vmnic。

1. 单击 **vCenter 清单列表 > 分布式交换机**。
2. 为容量主机选择相应的分布式交换机。
3. 单击**入门**页面上的**添加和管理主机**。
4. 使用以下设置来添加上行链路，并迁移与主机管理关联的现有 VMkernel。
  - <table border="1" cellpadding="0" cellspacing="0"><caption>表 18. DVS 添加主机</caption><tbody><tr><th>菜单</th><th>字段</th><th>值</th></tr><tr><td>选择任务</td><td>选择任务</td><td>添加主机</td></tr><tr><td>选择主机</td><td>单击**新建主机**</td><td>单击“容量主机”</td></tr><tr><td>选择网络适配器任务</td><td>选择网络适配器任务</td><td>选择“管理物理适配器”和“管理 VMkernel 适配器”</td></tr></tbody></table>
5. 选择其中一个专用 vmnic，然后单击**管理物理网络适配器 > 分配上行链路**。
6. 在弹出屏幕上选择 `uplink1`，然后单击**确定**。
7. 对另一个专用 vmnic 重复上述步骤，并将其分配给 `uplink2`。
8. 单击**下一步**，突出显示 vmk0 VMkernel 适配器，然后单击**分配端口组**。
9. 在弹出窗口中选择 **dvpg-Private-VM Management**，然后单击 **确定**。
10. 单击**下一步**两次，然后单击**完成**以完成迁移到分布式虚拟交换机的操作。**注：**您可能会短时间失去与主机的网络连接。但连接会迅速重新建立。

将 vmk0 适配器迁移到分布式虚拟交换机后，可以将 VM 内核添加到 DVS 中的每个端口组。

11. 单击 vCenter 中主机上的**管理 > 联网**。
12. 转至 **VM 内核适配器 > 添加主机联网**，然后添加表 19 和 21 中的 VM 内核适配器。要添加这些适配器，请转至 vCenter 中主机上**管理 > 联网**选项卡中的“VM 内核适配器”菜单。然后，单击“添加主机联网”图标，并添加以下 VM 内核适配器。

***添加 vmk1 用于 vMotion***

|菜单|字段|值|
|---|---|---|
|选择连接类型|选择连接类型|VMKernel 网络适配器|
|选择目标设备|选择现有分布式端口组|dvpg-Private-vMotion|
|选择网络适配器任务|选择网络适配器任务|选择**管理物理适配器**和**管理 VM 内核适配器**|
|端口属性|启用服务|选中 vMotion 流量|
|IPv4 设置|IPv4 地址|*172.16.10.X/24*<br/><br/>*这是用户定义的地址，可以是其他子网（如果需要）。确保每个主机上的其他 vMotion 地址位于同一子网上。*|
|IPv4 设置|子网掩码|255.255.255.0|
<caption>表 19. 主机联网 - vMotion</caption>

***添加 vmk2 用于容错***

|菜单|字段|值|
|---|---|---|
|选择连接类型|选择连接类型|VMKernel 网络适配器|
|选择目标设备|选择现有分布式端口组|dvpg-Private-Fault Tolerance|
|选择网络适配器任务|选择网络适配器任务|选择**管理物理适配器**和**管理 VMKernel 适配器**|
|端口属性|启用服务|选中容错日志记录|
|IPv4 设置|IPv4 地址|*172.16.20.X/24*<br/><br/>*这是用户定义的地址，可以是其他子网（如果需要）。确保每个主机上的其他 FT 地址位于同一子网上。*|
|IPv4 设置|子网掩码|255.255.255.0|
<caption>表 20. 主机联网 - 容错</caption>

***添加 vmk3 用于存储器***

|菜单|字段|值|
|---|---|---|
|选择连接类型|选择连接类型|VMKernel 网络适配器|
|选择目标设备|选择现有分布式端口组|dvpg-Private-Storage|
|选择网络适配器任务|选择网络适配器任务|选择“管理物理适配器”和“管理 VMkernel 适配器”|
|IPv4 设置|IPv4 地址|*可移植专用地址*<br/><br/>*这是从绑定到存储 VLAN 的可移植专用地址中选择的 IP 地址。此地址需要位于与 Storage Path B 不同的子网上。*|
|IPv4 设置|子网掩码|与 IP 范围关联的子网掩码|
<caption>表 21. 主机联网 - 存储器</caption>

***创建主机概要文件***

要创建主机概要文件，必须从先前配置的单个容量主机中捕获该概要文件。

1. 转至 vSphere Web Client 主屏，单击**主机概要文件**图标。
2. 单击绿色加号 (+) 并选择**从主机中抽取概要文件**，然后在弹出窗口中选择先前配置的容量主机。
3. 单击**下一步**。
4. 为主机概要文件（Capacity01 主机概要文件）提供相应名称，输入描述，然后单击**下一步**。
5. 复查设置，然后单击**完成**。

创建主机概要文件后，需要对其进行修改，以便在将概要文件应用于容量集群中的其余主机时，不会提示输入 MAC 地址。

1. 右键单击已创建的主机概要文件，并选择**编辑设置 > 编辑主机概要文件 > 主机虚拟 NIC**。
2. 在右侧窗格中，将**确定如何决定 vmknic 的 MAC 地址**更改为**用户必须显式选择策略选项**。
3. 单击**下一步**，然后单击**完成**。

***将主机概要文件连接到容量集群***

既然已创建主机概要文件，现在需要将主机概要文件连接到集群。集群已连接，因此可以将其应用于容量主机。

1. 转至 vSphere Web Client 中的**主机和集群**视图。
2. 针对集群中的每个主机，进入维护方式。**注：**概要文件只能应用于处于维护方式的主机。
3. 右键单击容量集群，然后从弹出菜单中选择**连接主机概要文件**。
4. 选择已创建的主机概要文件，然后单击**下一步**。
5. 在**定制主机**屏幕上输入相应的信息，然后单击**完成**。
6. 从维护方式除去主机（进入非维护方式）。

## 步骤 12：订购、配置和连接共享存储器
{: #step-12-order-configure-and-attach-shared-storage}

继续之前，您必须订购、配置和连接共享存储器，以用于环境中的管理和容量集群与主机。如果要使用 {{site.data.keyword.cloud_notm}} 多租户共享存储解决方案（文件存储器），请参阅[通过 VMware 供应 {{site.data.keyword.cloud_notm}} 的 IBM File Storage 的体系结构指南](/docs/infrastructure/FileStorage?topic=FileStorage-architectureguide)。

## 步骤 13：启用 HA/DRS 和 svMotion vCVA
{: #step-13-enabling-ha-drs-and-svmotion-vcva}

***在管理和容量集群上启用 HA/DRS***

既然已设置共享存储，现在需要启用 HA 和 DRS，以便为管理集群上的 VM 提供额外的保护和负载均衡功能。

1. 转至 vSphere Web Client。
* 对于管理集群，选择“管理”>“设置”。
* 选择 vSphere DRS，然后单击“编辑”。确保选择以下设置：**开启 vSphere DRS**，自动化级别 - **完全自动化**，迁移阈值滑块设置 - 中间，虚拟机自动化 - **启用不同虚拟机自动化级别**，电源管理 - **关闭**。  
* 在“vSphere HA 设置”屏幕上，确保选择以下设置：**开启 vSphere HA**，**主机监视**，VM 重新启动优先级 - **中等**，主机隔离响应 - **使电源保持打开**。  
***Storage vMotion 和 vCenter 虚拟设备***

既然已在管理集群上设置了存储器，并且已启用了 HA 和 DRS，现在需要将 vCenter 虚拟设备设置为共享存储器。

1. 右键单击相应的设备，并从弹出菜单中选择**迁移**。
2. 选择**更改数据存储**，然后单击**下一步**。
3. 选择先前安装到管理集群的 iSCSI 卷，然后单击**下一步**。
4. 复查选项，然后单击**下一步**。

高级单站点 VMware 环境已完成。

## 摘要
{: #summary}

现在，您的 VMware 环境正在 IBM Cloud 数据中心运行。该 VMware 环境可以运行生产工作负载，并补充内部部署 IBM Cloud 部署。该环境制定了 VMware 最佳实践，并启用 VMware DRS、HA、Storage DRS 和联网冗余等功能。可以使用更多容量或管理主机以及更多存储器来扩展此参考体系结构实现。

有关 VMware 的更多信息，请参阅[部署 VMware](/docs/infrastructure/vmware?topic=VMware-using-cookbooks-for-vmware-deployments) 和 [VMware 常见问题](/docs/infrastructure/vmware?topic=VMware-faqs-vmware)。

## 附录 A：VLAN 工作表
{: #appendix-a-vlan-worksheet}

|VLAN 类型|VLAN 编号|IP 范围|网关|用途|
|---|---|---|---|---|
|主专用||||管理|
|主专用||||存储|
|主专用||||虚拟机|
|主公用||||公共访问|
|可移植专用||||管理 VM|
|可移植专用||||存储|
|可移植专用||||虚拟机|
|vMotion|||不适用||
|容错|||不适用|||
<caption>VLAN 工作表</caption>
