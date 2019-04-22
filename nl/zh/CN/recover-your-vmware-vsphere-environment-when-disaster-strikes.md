---
copyright:
  years: 2014, 2018
lastupdated: "2018-06-22"

subcollection: virtualization

keywords: disaster recovery
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# 恢复 VMware vSphere 环境
{: #recovering-your-vmware-vsphere-environment}

目前，数据备份是维护数据安全和完整性的主要方法。在理想的完美世界中，从不需要使用数据备份。但遗憾的是，在现实中会发生事故和灾难。在发生这些情况时，最重要的是尽可能缩短系统停机时间，并尽快恢复启动和正常运行。

在过去，在高性能存储器或磁带上存储备份都是可行的解决方案。现今，企业通过扩充内部部署物理存储器甚至是将其更换为 Object Storage，以求降低与此类存储器关联的资本和运营成本。但是，如果使用 Object Storage，那么备份将不再位于内部部署存储器中，因此从备份进行复原就不再只是找到必需的备份并进行复原这么简单。幸运的是，现在提供了一种混合解决方案，可用于执行从 Object Storage 上存储的备份进行复原时所需完成的大部分繁琐工作。该解决方案由以下部分组成：

* {{site.data.keyword.cos_full}}
* NetApp 的 NetApp AltaVault 云存储网关（原先称为 Riverbed SteelStore）
* Veeam Backup & Replication 软件

如果发生灾难性事件，导致主环境处于脱机状态，那么可以在 {{site.data.keyword.cloud}} 中创建辅助恢复站点以恢复该环境。可以部署 Veeam Backup & Replication 软件和 AltaVault 云集成存储设备，用于与 {{site.data.keyword.cos_full_notm}} 通信以访问主环境的先前备份。该软件与存储设备相连接，以将备份复原到新位置，使主环境恢复联机状态，并避免任何进一步的正常运行时间损失。

在 {{site.data.keyword.cloud_notm}} 恢复站点复原虚拟机 (VM) 时，会保留内部部署 IP 地址。因此，务必在成功恢复后更改 IP 地址或设计 BYOIP 基础架构。如果需要关于其中任何一种情况的帮助，请联系 IBM 代表。

以下过程侧重于协调使用 NetApp AltaVault 云存储网关设备、{{site.data.keyword.cloud_notm}} 基础架构和 Veeam Backup & Replication，以完全复原发生故障的 VMware vSphere 环境。软件假定已使用前述三种技术创建了环境的至少一个备份，并且该备份在恢复时位于 {{site.data.keyword.cos_full_notm}} 上。

“AltaVault 设备”通过两种不同的方式进行识别。第一种是“内部部署 AltaVault 设备”，这指的是发生故障并从中恢复和/或复原备份的原始内部部署 AltaVault 设备。第二种是“{{site.data.keyword.cloud_notm}} AltaVault 设备”，这指的是用于从发生故障的内部部署 AltaVault 设备恢复备份的 AltaVault 设备。{{site.data.keyword.cloud_notm}} AltaVault 设备使用实用程序服务器部署到 vSphere 环境。

## 混合解决方案简介
{: #introducing-a-hybrid-solution}

Veeam Backup & Replication 支持包含 NetApp AltaVault 云集成存储设备和 {{site.data.keyword.cos_full_notm}} 的混合解决方案。主要的关注事项是创建和维护虚拟环境的备份以及从这些备份进行复原。NetApp AltaVault 云集成存储设备是一种软件解决方案，可将内部部署环境与私有云或公共云无缝集成。一起使用时，Veeam Backup & Replication 创建备份，AltaVault 云集成存储设备在本地内部部署存储器上存储这些备份，并同时将这些备份复制到 {{site.data.keyword.cos_full_notm}}。此外，通过 {{site.data.keyword.cloud}} 现收现付定价模型以及与内容交付网络 (CDN) 的完全集成，能够在位于不同地理位置的 24 个节点上存储和分布数据。

### IBM Cloud Object Storage
{: #ibm-cloud-object-storage}

要访问备份到 {{site.data.keyword.cos_full_notm}} 的数据文件，请通过以下方式请求数据文件：

* 位于内部部署的已有 AltaVault 设备
* 位于 {{site.data.keyword.cloud_notm}} 中的辅助 AltaVault 设备

遵循此过程在 {{site.data.keyword.cloud_notm}} 中部署辅助 AltaVault 设备，以恢复发生故障的主内部部署 AltaVault 设备。

{{site.data.keyword.cloud_notm}} 环境包含具有本地存储器的单个 ESXi 主机，用于容纳和运行辅助 AltaVault 设备。该基础架构代表了具有单个 ESXi 主机的基本单站点体系结构，该主机由 {{site.data.keyword.cloud_notm}} 虚拟服务器实例 (VSI) 中的 vCenter Server 进行管理。

如果您有更重要的基础架构需要共享存储器和/或支持以下功能：

* vSphere 高可用性 (HA)
* vSphere 分布式资源调度程序 (DRS)
* vSphere vMotion

#### 先决条件
{: #prerequisites-ibm-cloud-object-storage}

继续之前，请验证是否满足以下先决条件：**注：**对于此示例，AltaVault AVA-v8 设备试用版与 AltaVault Cloud-Integrated Storage 4.1 一起使用。

* 确保现有环境包含由 {{site.data.keyword.cloud_notm}} VSI 中的 vCenter Server 管理的单个 ESXi 主机。
* 了解 VMware Sphere 术语并管理 vSphere ESXi 环境。这些知识包括（但不限于）使用 vSphere Web Client 和 vSphere Client 以及分配要包含联网和存储器的硬件资源。

#### 订购两个可移植专用网络
{: #order-two-portable-private-networks}

AltaVault 要求网络接口位于环境中的不同网络中。使用以下步骤通过 {{site.data.keyword.slportal_full}} 订购两个可移植专用网络。

1. 使用您的唯一凭证访问 [{{site.data.keyword.slportal_full}} ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://control.softlayer.com/){: new_window}。
2. 单击**帐户 > 下订单**。
3. 选择**网络**部分，然后单击**子网/IP > 订购**。
4. 从下拉菜单中，选择**可移植专用**。选择“可移植专用”将触发一个选项，用于选择要显示的可移植专用 IP 地址数。**注：**在每个可移植专用网络的地址块中，{{site.data.keyword.cloud_notm}} 会自动保留至少三个 IP 地址。保留的地址用于网络地址、网关地址和广播地址。这些地址必须直接绑定到虚拟 LAN (VLAN)。<!--If your IBM Cloud pod supports Hot Standby Ready Protocol (HSRP), then the number of usable IP addresses is reduced. For instance, if you select **4 Portable Private IP Addresses** will yield only one usable IP address or zero if the pod supports HSRP.--> 有关可移植 IP 地址的更多信息，请参阅[子网和 IP 入门](/docs/infrastructure/subnets?topic=subnets-getting-started-subnets-ips#getting-started-subnets-ips)。
5. 选择可移植专用 IP 地址数后，单击**继续**。
6. 选择可移植专用 IP 地址要路由到的 VLAN，然后单击**继续**。
7. 填写必需的联系人信息，然后单击**下订单**。
8. 重复订购过程以获取第二个必需的可移植专用网络。
9. 在分配了可移植专用网络和 IP 地址后，单击 {{site.data.keyword.slportal}} 页面中的**网络 > IP 管理 > 子网**可查看这些可移植专用网络和 IP 地址。建议您跟踪 IP 地址分配。使用每个 IP 地址的**子网**页面的**注释**部分来反映该地址所分配给的机器。

#### 配置 vSphere
{: #configuring-vsphere}

您需要配置 vSphere 以反映出添加的可移植专用网络，并确保这些网络在逻辑上是隔离的。

1. 转至 vSphere 环境，然后创建虚拟机端口组来反映添加的其中一个可移植 IP 块（总计有两个）。

虚拟机端口组已标注为**共享网络**。一个可移植 IP 块用于位于缺省（主）虚拟机端口组上的 VM。另一个可移植 IP 块用于 AltaVault 设备与导出的通用因特网文件系统 (CIFS)/服务器消息块 (SMB) 安装点之间的数据传输。

## AltaVault 云存储网关
{: #altavault-cloud-storage-gateway}

可以使用 AltaVault 将内部部署环境与云相集成，而无需使用 {{site.data.keyword.cos_full_notm}} 的 REST API 来编写脚本或应用程序。AltaVault 在前端公开 CIFS/SMB 或网络文件系统 (NFS) 安装点，并安全地连接到后端的 {{site.data.keyword.cos_full_notm}} 接口。接下来，可以安装或指向安装点，然后从云中安全地将数据复制到环境和/或恢复/复原环境。<!-- , as shown in Figure 1.-->

<!-- ![Figure 1](images/veeam_restore_fig2.png)
<br/>`Figure 1: AltaVault end-to-end flow with Veeam and IBM Cloud`-->

### 以灾难恢复配置部署 AltaVault
{: #deploying-altavault-in-a-disaster-recovery-configuration}

执行以下步骤通过 {{site.data.keyword.cos_full_notm}} 将 AltaVault 部署为灾难恢复解决方案。

#### 开始之前
{: #before-you-get-started-deploying-altavault-in-a-disaster-recovery-configuration}

继续之前，请验证是否满足以下先决条件：

* 获取 AltaVault 虚拟设备的副本，并确保该副本位于实用程序服务器上。这是一个具有 OVA 文件扩展名的文件。请联系 NetApp 代表以获取设备，或者从 [NetApp AltaVault Web 站点 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://www.netapp.com/us/products/protection-software/altavault/){: new_window} 下载 90 天试用版。
* 为 AltaVault 设备提供了满足最低 CPU、内存和磁盘空间需求的现有内部部署 vSphere ESXi 5.5 环境。如果使用试用版，那么资源需求如下：4 个虚拟 CPU (vCPU)、24 GB 内存和最多 8 TB 磁盘空间。
* vSphere 环境中有 2 个 10 Gbps 网络接口控制器 (NIC) 可用。一个 NIC 用于数据访问，另一个 NIC 用于将数据复制到 {{site.data.keyword.cos_full_notm}}。
* 有 2 个可移植专用网络，对应于 vSphere 环境中定义的 2 个 NIC。不能为复制网络分配与数据访问网络相同的网络，这样做会创建路由回路。
* 有 {{site.data.keyword.cos_full_notm}} 凭证。这些凭证包含 {{site.data.keyword.cloud_notm}} 用户名、{{site.data.keyword.cos_full_notm}} 用户名以及与 {{site.data.keyword.cloud_notm}} 用户名关联的 API 密钥。
* 暂挂内部部署 AltaVault 设备上的数据复制，或将其与用于访问 {{site.data.keyword.cloud_notm}} 中备份归档的容器/存储区断开连接（如果内部部署环境仍然完好）。
  * 使用下列两种方法之一来暂挂或停止复制：
    * 关闭内部部署 AltaVault 设备。
    * 转至 AltaVault 设备 Web 管理控制台，然后单击**存储 > 云设置 > 复制**。选择**暂挂复制**。
    * 获取内部部署 AltaVault 设备配置文件的副本，或使其可通过 URL 进行访问。配置文件是 .tar 文件。
  * 转至 AltaVault 设备 Web 管理控制台，然后单击**配置 > 设置向导 > 导出配置 > 导出配置**。保存配置文件（.tar 文件），并将其传输到实用程序服务器，或使其可通过 URL 进行访问。
    * 了解 VMware Sphere 术语并管理 vSphere ESXi 环境。这些知识包括（但不限于）使用 vSphere Web Client 和 vSphere Client 以及分配联网和存储器等硬件资源。

### 部署 AltaVault OVA
{: #deploying-altavault-ova-disaster}

在满足所有先决条件后，使用实用程序服务器将 AltaVault OVA 部署到 vSphere 环境。有关 OVA 部署的指示信息可以在 [NetApp AltaVault Installation and Service Guide ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window} 中找到。AltaVault 试用版配置有 4 个虚拟 CPU、6 GB 内存和 150 GB 主磁盘。使用 Installation and Service Guide 来执行以下步骤：

1. 将内存量增大到 24 GB。
2. 添加大小小于或等于 8 TB 的辅助磁盘存储设备，用于存储已去重的备份数据。
3. 确保在更改内存和磁盘配置后，将不同的可移植专用网络分配给 AltaVault 设备。

向 NIC 分配了以下接口功能：

* **主接口**：用于 AltaVault 设备管理以及将数据复制到云。在示例环境中，会将其分配给**主网络**端口组。
* **e0a**：用于将数据从 AltaVault 设备复制到云的可选接口。
* **e0b**：用于导出 SMB/CIFS 或 NFS 共享的安装点的接口。在示例环境中，会将其分配给**共享网络**端口组。
* **e0c**：用于导出 SMB/CIFS 或 NFS 共享的安装点的可选接口。

在此过程的示例配置中，AltaVault 设备使用**主**接口作为复制到云的接口，使用 **e0b** 接口来导出 CIFS/SMB 安装点。**注：**不能同时使用 CIFS/SMB 共享和 NFS 共享来访问相同的数据。使用的协议必须与用于内部部署 AltaVault 设备的协议相同，以便可将数据复制到云。

<!-- ![Figure 2](images/veeam_restore_fig3.png)
<br/>`Figure 2: IBM Cloud setup with VMware`-->

有关部署 AltaVault 设备以及配置 VM 设置的更多信息，请参阅 [NetApp AltaVault Installation and Service Guide ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}。

### 配置 AltaVault 设备
{: #configuring-the-altavault-appliance}

1. 配置了 AltaVault VM 后，可以打开该 VM 的电源。VM 启动可能需要一些时间，因为 AltaVault 设备将对辅助存储磁盘进行格式化。

2. 在设备完成引导过程后，使用 **admin** 作为 **用户名**并使用 **password** 作为**密码**来登录到 AltaVault 控制台。您可以在完成初始配置后更改这些凭证。请参阅“表 1：AltaVault 初始配置值”。

3. 登录到设备后，系统会询问您是否要使用向导来进行配置。请输入 *y*。
4. 进入向导后，请使用表 1 中的信息。
5. 按 **Enter** 键以保存更改。

|问题|回答|
|---|---|
|步骤 1：管理员密码？|输入新的管理员密码（不能是 **password**）。|
|步骤 2：主机名？|输入相应的主机名。|
|步骤 3：在主接口上使用 DHCP？|输入 **n** 或 **no**。|
|步骤 4：主 IP 地址？|输入主网络 IP 地址。在示例配置中，这是用于管理接口的网络（例如，10.120.108.132）。|
|步骤 5：网络掩码？|输入网络掩码（例如，255.255.255.192）。|
|步骤 6：缺省网关？|输入缺省网关（例如，10.120.108.129）。|
|步骤 7：主 DNS 服务器？|输入环境中的主域名系统 (DNS) 服务器。|
|步骤 8：域名？|输入环境的域名（例如，testenv.org）。|
{: caption="表 1. AltaVault 初始配置值" caption-side="top"}

### 配置 AltaVault 用于 IBM Cloud Object Storage
{: #configuring-altavault-for-ibm-cloud-object-storage}

设置 AltaVault 设备后，必须将其连接到 {{site.data.keyword.cos_full_notm}} 服务。在内部部署配置中，公共 DNS 用于连接到 Object Storage 服务。在此配置中，将改为使用内部 DNS 名称。

1. 打开 Web 浏览器，并输入 AltaVault 设备的 IP 地址。
2. 使用管理员凭证登录到控制台。初始登录时，将显示**向导仪表板**。
3. 选择**系统设置**，然后验证是否所有网络设置都正确，并调整时区以反映出环境的时区。
4. 选择**下一步 > 保存并应用 > 退出**。您将返回到**向导仪表板**并从当前浏览器会话注销。
5. 重新登录到 AltaVault 设备，然后单击**设置 > 设置向导 > 导入配置**，并指定内部部署 AltaVault 设备的配置文件的位置。
6. 选择**仅导入共享数据**，这将仅导入通用设置（例如，云和电子邮件设置）。
7. 在**密钥口令**文本框中，输入在创建内部部署 AltaVault 设备期间指定的加密密钥的口令（如果已设置）。
8. 单击**导入配置 > 退出**。

<!--The AltaVault appliance web console displays a message indicating a successful import. If the import was not successful, verify that the configuration file is in the correct format and that the passphrase is correct before trying again.-->

您已将内部部署 AltaVault 设备配置导入到 {{site.data.keyword.cloud_notm}} AltaVault 设备。

使用以下步骤来修改 {{site.data.keywrod.cloud_notm}} AltaVault 设备的云设置，以向其授予通过专用网络访问 {{site.data.keyword.cos_full_notm}} 服务的访问权。

1. 在 AltaVault 中，转至**存储器 > 云设置 > 云**，然后修改主机名以反映出 {{site.data.keyword.cos_full_notm}} 服务的专用地址。
  * 专用网络名称的语法为 <location>.objectstorage.service.networklayer.com，其中 *<location>* 指定简写的数据中心名称（示例：mel01 表示墨尔本 01 数据中心）。
2. 单击**应用**。AltaVault 设备会尝试连接到 {{site.data.keyword.cos_full_notm}} 服务。如果此连接尝试失败，请验证云提供者设置是否正确，然后重试连接。

连接到 {{site.data.keyword.cos_full_notm}} 后，需要使设备进入恢复方式，以从 {{site.data.keyword.cos_full_notm}} 同步原始备份数据的元数据内容。

使用以下步骤来使 AltaVault 设备进入恢复方式。

AltaVault 设备会从 {{site.data.keyword.cos_full_notm}} 同步数据。**注：**完成同步所需的时间取决于要复原的备份的大小和数量。

1. 转至 AltaVault 设备并登录，然后输入图 3 中显示的命令：<br/>![图 3](images/veeam_restore_fig5.png)<br/>`图 5：恢复方式命令`
2. 在同步过程完成后，返回到 AltaVault 设备 Web 控制台。
3. 验证**存储优化服务**是否**正在运行**以及其**状态**是否为**就绪**。可能需要几分钟时间，**状态**才会更改为**就绪**。

### 在 AltaVault 中配置 CIFS/SMB 安装点
{: #configure-the-cifs-smb-mount-point-in-altavault}

建立与 {{site.data.keyword.cos_full_notm}} 的内部连接后，将 **e0b** 接口配置为访问 CIFS/SMB 安装点。

1. 在 AltaVault Web 控制台中，转至**设置 > 数据接口**。
2. 展开 **e0b** 接口，并根据需要选择**启用数据接口**。
3. 输入要用于安装 CIFS/SMB 共享的 IP 地址、子网掩码和网关。确保使用的子网不同于用于主接口的子网。
4. 保留 **MTU** 缺省值 **1500 字节**。
  * 虽然缺省最大传输单元 (MTU) 设置为 1,500，但如果使用的是巨型帧，那么可以将其更改为 9,000。ESXi 主机和物理基础架构需要支持巨型帧。缺省情况下，{{site.data.keyword.cloud_notm}} 支持 MTU 大小 9,000 字节，因此无需更改任何配置。
5. 单击**应用**。

现在，AltaVault 设备已最低限度配置为允许自身、{{site.data.keyword.cos_full_notm}} 和 Veeam Backup & Replication 之间进行通信。

## Veeam Backup & Replication
{: #veeam-backup-replication-recover}

Veeam Backup & Replication 软件为 VM 及其数据提供了完整备份、复制和恢复功能。Veeam Backup & Replication 可以与 AltaVault 云网关设备完全集成，从而实现无缝备份和恢复体验。

### 部署 Veeam Backup & Replication
{: #deploy-veeam-backup-replication}

示例中使用的是 Veeam Backup & Replication V8 试用版。

#### *先决条件*
{: #prerequisites-deploy-veeam-backup-replication}

继续部署之前，请确保满足以下先决条件：

* 拥有配置用于 IBM Cloud Object Storage 和 Veeam Backup & Replication 的现有 AltaVault 设备。
* 获取 Veeam Backup & Replication for VMware 环境的副本，这是单个可执行文件。请联系 Veeam 代表以获取副本，或者下载 [30 天试用版 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}。
* 获取要用于 Veeam Backup & Replication 的许可证文件。在大多数情况下，此文件会通过电子邮件发送到用于下载 Veeam Backup & Replication 的电子邮件地址。如果未收到此文件，请联系 Veeam 代表。<br/><br/>许可证文件用于激活 Veeam Backup & Replication 的完全功能。如果在程序安装期间未提供此文件，那么所有功能部件和功能都会还原为 30 天试用版的功能部件和功能。
* 拥有在 IBM Cloud 中供应的虚拟服务器实例 (VSI)，规范如表 2 中所示。**注：**安装的操作系统必须是 64 位版本。

||最低需求|建议需求|
|---|---|---|
|**操作系统**|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>WIndows 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|
|**内核或 vCPU 数**|2|4|
|**内存**|4 GB 基本 RAM，外加每个并发备份和复原作业 500 MB。|16 GB 基本 RAM，外加每个并发备份和复原作业 4 GB。|
|**磁盘空间**|2 GB 用于产品安装；每 100 个 VM 10 GB，用于访客系统目录文件夹（持久数据）。|2 GB 用于产品安装；每 100 个 VM 10 GB，用于访客系统目录文件夹（持久数据）。|
|**网络**|1 Gbps LAN，用于现场备份和复制；1 Mbps WAN，用于非现场备份和复制。|1 Gbps LAN，用于现场备份和复制；1 Mbps WAN，用于非现场备份和复制。|
{: caption="表 2. {{site.data.keyword.cloud_notm}} VSI 的系统需求" caption-side="top"}

在 AltaVault 设备和 vSphere 环境所在的数据中心内供应 VSI。VSI 必须具有对 AltaVault 设备和 vCenter 的访问权。

### 安装 Veeam Backup & Replication
{: #installing-veeam-backup-replication-disaster}

满足所有先决条件后，使用以下步骤将 Veeam Backup & Replication 安装到 {{site.data.keyword.cloud_notm}} VSI。

1. 双击程序可执行文件，然后单击 **Veeam Backup & Replication - 安装**。这将打开设置向导。
* 单击**下一步**，然后选择**我接受许可协议中的条款**。
* 单击**下一步**，然后指定在“部署 Veeam Backup & Replication”下获取的许可证文件的位置。
* 单击**下一步**，然后在**程序功能**屏幕上选择要安装的 Veeam Backup & Replication 组件及其安装位置。**注：****Veeam Backup & Replication** 和 **Veeam Backup Catalog** 是必需组件。
* 单击**下一步**。<br/>设置向导会运行一系列检查，以确保所有必需的程序框架和支持组件都已安装。如果缺少任何组件，设置向导会主动进行安装。<!--Click **Install** if this is the case.-->
2. 验证所有组件是否均**已通过**系统检查，然后单击**下一步**。
3. 选择要运行 Veeam Backup 服务的**服务（用户）帐户**。缺省服务帐户是 **LOCAL SYSTEM 帐户**。单击**下一步**。
4. 选择要用于创建和存储 Veeam Backup & Replication 数据库的 **SQL Server 实例**。有关更多信息，请联系数据库管理员。单击**下一步**。
5. 输入**目录服务端口** (9393) 和 **Veeam Backup 服务端口** (9392)。单击**下一步**。
6. 选择要存储访客文件系统目录（持久数据）和 vPower NFS 写高速缓存（非持久数据）的目录。单击**下一步**。
7. 验证是否所有设置和值都正确，然后单击**安装**。安装完成后，单击**完成**。

### 配置 Veeam Backup & Replication
{: #configuring-veeam-backup-replication}

安装 Veeam Backup & Replication 后，即准备好与 AltaVault 设备建立连接。

1. 启动 Veeam Backup & Replication。
2. 单击屏幕左下角的**备份基础架构**。
3. 在**备份基础架构**窗口中，单击**受管服务器 > 受管服务器**。
4. 在上方的功能区菜单中，单击**添加服务器**，然后双击 **VMware vSphere**。
5. 输入 vSphere 服务器的专用 IP 地址，然后单击**下一步**。
6. 输入先前指定的在 vSphere 服务器上具有管理员特权的本地帐户的帐户凭证。**注：**对于域帐户，帐户用户名必须为 DOMAIN\USER 格式，对于本地帐户，必须为 HOST\USER 格式。要添加帐户，请单击**添加**，然后输入相应的用户名和密码。不要更改缺省 VMware Web Service 端口，除非网络管理员指示您这样做。<!-- otherwise during [Veeam Backup & Replication installation](#_Installing_Veeam_Backup).-->单击**下一步**。
  * Veeam Backup & Replication 会尝试连接到 vSphere 服务器。如果连接尝试失败，请检查帐户是否存在，以及是否在 vSphere 服务器上具有管理员特权，然后重试。
7. 单击**完成**。
8. 通过单击**受管服务器 > VMware vSphere** 来验证 vSphere 服务器是否已成功添加。

### 向 Veeam Backup & Replication 添加备份存储库
{: #adding-a-backup-repository-to-veeam-backup-replication-disaster}

执行以下步骤来创建新的备份存储库，以存储 AltaVault 设备上复原的备份。**注：**示例中使用的是 CIFS/SMB 共享，因为 Veeam Backup & Replication 备份服务器必须在运行 Windows 的机器上托管。用于在 {{site.data.keyword.cloud_notm}} AltaVault 设备上复原备份的共享协议必须与用于内部部署 AltaVault 设备中这些相同备份的共享协议相同，才可使用 CIFS/SMB 共享。

1. 单击左下角的**备份基础架构 > 备份存储库**。
2. 在上方的功能区菜单中，单击**添加存储库**。
3. 为存储库提供唯一**名称**，并提供相应的**描述**。单击**下一步**。
4. 选择与内部部署 AltaVault 设备中使用的共享类型对应的共享类型。示例：如果内部部署 AltaVault 设备中使用的共享类型为 CIFS/SMB，请选择**共享文件夹**。
5. 指定要在 AltaVault 设备上复原 CIFS/SMB 共享的备份的位置。该共享与在内部部署 AltaVault 设备上处理备份所通过的共享同名。例如，如果名为 `cifs_test1` 的共享用于内部部署 AltaVault 设备中的备份，请在 {{site.data.keyword.cloud_notm}} AltaVault 设备上指定 `cifs_test1` 共享的位置。
  * 打开 Web 浏览器，并输入 AltaVault 设备的 IP 地址以确定该位置。
  * 转至**存储 > CIFS**，并记下共享的**共享路径**。**注：**这与共享的本地路径不同。共享路径格式为 `\\<AltaVault appliance hostname>\<share name>`。将“共享路径”中需要的 AltaVault 设备主机名替换为 AltaVault 设备的 e0b 网络接口（共享的安装点）的 IP 地址。<br/>
   * 单击 AltaVault 设备管理窗口中的**设置 > 数据接口**，以查找 **e0b** 接口的 IP 地址。有关配置 CIFS/SMB 安装的更多信息，请参阅“在 AltaVault 中配置 CIFS/SMB 安装点”。
   * Veeam Backup & Replication 中指定的共享路径将为 `\\10.120.108.133\cifs_test1`，而不是 `\\restore-appliance\cifs_test1`。
6. 返回到 Veeam Backup & Replication，在**共享文件夹**字段中输入安装点的共享路径，然后单击**下一步**。Veeam Backup & Replication 会尝试建立与该安装点的连接。如果连接失败，请返回并验证 AltaVault 设备的网络设置是否正确，然后重试。
7. 在**将最大并发任务数限制为**中输入值，使其等于**新建备份存储库**屏幕上提供的资源数。此值是备份代理可以发送到所选共享的最大任务数。缺省并发任务数为 4。AltaVault 建议从 5 个并发任务开始，并根据资源的允许情况来增大或减小此值。
8. 单击**下一步**。
9. 在 **vPower NFS** 屏幕上指定可选的 vPower NFS 设置。如果使**启用 vPower NFS 服务器**复选框保持未选中状态，那么 Veeam Backup & Replication 将使用 vPower 进行恢复并执行恢复验证。单击**下一步**。
10. 选择**自动导入现有备份**，除非要手动选择 VM 备份。
11. 验证是否所有设置都正确，然后单击**下一步**。
* 单击**完成**以退出向导。
12. 单击**是**以继续。

Veeam Backup & Replication 已配置，现在可以开始复原备份。

### 复原环境
{: #restoring-the-environment}

执行以下步骤来完全复原 VM 复原。

1. 单击屏幕左下角的**备份和复制**。
2. 在上方的功能区菜单中，单击**复原**。
3. 选择**整个 VM（包括注册）**。**注：**不要在**从副本复原**下进行任何选择。单击**下一步**。
4. 单击**添加 VM > 从备份**。您会看到先前通过内部部署 AltaVault 设备和所选的 CIFS/SMB 共享复制到 {{site.data.keyword.cos_full_notm}} 的 VM。单击**添加 > 下一步**。
5. 通过选择**复原到新位置或使用其他设置**，将 VM 复原到新位置<!--[because the original location of the VM(s) failed]-->。单击**下一步**。
6. 在**主机**屏幕上，为要复原的每个 VM 选择目标主机。选择 VM，然后单击**主机**。
7. 在**选择主机**中选择目标主机，然后单击**确定**。<br/>Veeam Backup & Replication 会尝试联系目标主机，以确保这些主机已联机并准备好接收数据。如果此尝试失败，请验证是否所有网络设置都正确，然后重试。检查完成后，单击**下一步**。
8. 为每个 VM 选择可选的**资源池**。单击**下一步**。
9. 为要复原的每个 VM 选择目标数据存储和磁盘类型。单击**下一步**。<br/>Veeam Backup & Replication 会尝试验证目标数据存储。如果验证失败，请检查数据存储是否有足够的容量可用于要复原的 VM，并验证所有网络设置，然后重试连接。
10. 在目标数据存储上，为要复原的每个 VM 选择目标文件夹。单击**下一步**。
11. 为每个 VM 指定网络连接和设置。单击**下一步**。
12. 输入可选的**复原原因**，以提供为什么要执行复原操作的原因。单击**下一步**。<br/>Veeam Backup & Replication 会尝试验证要复原的 VM。请等待此验证成功完成，然后再继续。
13. 在**备份和复制**窗口中验证复原操作和设置，然后单击**完成**。Veeam Backup & Replication 会自动开始复原所选 VM。

## 后续步骤
{: #next-steps-recovering-your-vmware-vsphere-environment}

<!--The recent rise in Object Storage can be heavily attributed to enterprises' desire to reduce or even eliminate the operational and capital expenditures associated with comparatively expensive on-premises storage. While Object Storage is a highly cost-effective solution, it introduces a new problem of how to access stored files. In the case of stored data backups, the issue is how to use them to recover an environment that has failed for whatever reason.-->

<!--This problem is handily resolved by Veeam Backup & Replication, along with a NetApp AltaVault Cloud Gateway appliance, and {{site.data.keyword.cos_full_notm}}. When used together, they create a seamless backup and recovery experience that can be tailored to fit a wide variety of environmental requirements and constraints. By following the steps outlined in this procedure, enterprises can quickly realize the benefits of cloud-based storage, while reducing the complexities involved in shifting from on-premises storage to Object Storage using the three aforementioned technologies.-->

有关此混合解决方案的组件的更多信息，请参阅：

* [NetApp AltaVault Web 站点 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://www.netapp.com/us/products/protection-software/altavault/){: new_window}
* [Veeam Backup & Replication Web 站点 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}
* [IBM Cloud Object Storage Web 站点 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://www.ibm.com/cloud/storage){: new_window}

*实用程序服务器*是指包含 VMware vSphere Client 的服务器，该 VMware vSphere Client 用于访问专用环境，并借以部署用于数据恢复操作的 AltaVault 设备。
