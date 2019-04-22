---

copyright:
years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: vsphere, Veeam
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# 使用 Veeam 备份 VMware vSphere 环境
{: #backing-up-your-vmware-vsphere-environment-by-using-veeam}

您可以使用由以下服务组成的混合解决方案来备份 VMware vSphere 环境：

* {{site.data.keyword.cloud}} Object Storage 服务
* NetApp AltaVault 云存储网关
* Veeam Backup & Replication 软件

Veeam Backup & Replication 支持包含 NetApp AltaVault 云集成存储设备和 {{site.data.keyword.cos_full}} 的混合解决方案。软件通过备份来创建、维护和复原虚拟环境。与 NetApp AltaVault 云集成存储设备一起使用时，可创建本地存储的备份（内部部署）。备份还会同时复制到 {{site.data.keyword.cos_full_notm}}。使用此混合解决方案，会创建两个备份副本，但其中只有一个位于本地。

## AltaVault 云集成存储网关
{: #altavault-cloud-integrated-storage-gateway}

可以使用 AltaVault 云存储网关将内部部署环境与云相集成，而不必使用 {{site.data.keyword.cos_full_notm}} 的 REST API 来编写脚本或应用程序。可以安装或指向安装点，然后开始安全地将数据复制到云中。

### 部署 AltaVault 内部部署
{: #deploying-altavault-on-premises}

执行以下步骤将 AltaVault 作为内部部署备份解决方案部署到 {{site.data.keyword.cos_full_notm}}。

购买 AltaVault 时可将其作为物理设备或虚拟设备。此过程中涵盖了基于 VMware vSphere ESXi 的 AltaVault 虚拟设备试用版的部署。
{:tip}

<a name="prerequisites"></a>
#### 先决条件

验证是否满足以下先决条件：

* AltaVault 虚拟设备的副本。这是一个具有 OVA 文件扩展名的文件。请联系 NetApp 代表以获取设备，或者从 [NetApp AltaVault Web 站点 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://www.netapp.com/us/products/protection-software/altavault/){: new_window} 下载 90 天试用版。
* 为 AltaVault 设备提供了满足最低 CPU、内存和磁盘空间需求的现有内部部署 vSphere ESXi 5.5 环境。如果使用试用版，那么这些需求为 4 个虚拟 CPU (vCPU)、24 GB 内存和最多 8 TB 磁盘空间。
* vSphere 环境中有 2 个 10 Gbps 网络接口控制器 (NIC) 可用。一个 NIC 用于数据输入，另一个 NIC 用于将数据复制到 {{site.data.keyword.cos_full_notm}}。
* 2 个网络，对应于 vSphere 环境中定义的 2 个 NIC (VLAN)。不能为复制网络分配与数据输入网络相同的网络，这样做会创建路由回路。
* 一组 {{site.data.keyword.cos_full_notm}} 凭证。这些凭证包含 {{site.data.keyword.cloud_notm}} 用户名、{{site.data.keyword.cos_full_notm}} 用户名以及与 {{site.data.keyword.cloud_notm}} 用户名关联的 API 密钥。
* 了解 VMware Sphere 术语并管理 vSphere ESXi 环境。这些知识包括（但不限于）使用 vSphere Web Client 和 vSphere Client 以及分配包含联网和存储器的硬件资源。

### 部署 AltaVault OVA
{: #deploying-altavault-ova}

在满足所有先决条件后，可以将 AltaVault OVA 部署到 vSphere 环境。有关 OVA 部署的指示信息可以在 [NetApp AltaVault Installation and Service Guide ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window} 中找到。

1. 在 OVA 部署完成后，编辑 AltaVault 虚拟机 (VM)。
2. 将分配的内存更改为与编辑窗口中的 AltaVault 版本相匹配。如果使用的是试用版，请分配 24 GB 内存，并添加一个小于或等于 8 TB 的磁盘。**注：**此辅助磁盘存储设备用于存储已去重的备份数据。
* 确保在修改内存和磁盘配置后，将不同的网络 (VLAN) 分配给 AltaVault 设备。

向 NIC 分配了以下接口功能：

* 主接口。用作管理接口。
* e0a。用于将数据从 AltaVault 设备复制到云的接口。
* e0b。用于导出 SMB/CIFS 或 NFS 共享的安装点的接口。
* e0c。用于导出 SMB/CIFS 或 NFS 共享的安装点的可选接口。

在此示例配置中，AltaVault 设备使用 **e0a** 接口作为复制到云的接口，使用 **e0b** 接口来导出 CIFS/SMB 安装点。**注：**CIFS/SMB 共享和 NFS 共享不能同时用于访问相同的数据。换句话说，如果数据位于 CIFS/SMB 共享中，那么无法通过 NFS 共享进行访问，反之亦然。

有关部署 AltaVault 设备以及配置设备的 VM 设置的更多信息，请参阅 [NetApp AltaVault Installation and Service Guide ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}。

### AltaVault 设备的初始配置<!--initial configuration?-->
{: #initial-configuration-of-the-altavault-appliance-initial-configuration-}

为 AltaVault VM 配置了相应的硬件后，可以打开该 VM 的电源。**注：**AltaVault VM 初始启动需要一些时间，因为 AltaVault 设备将对辅助元数据高速缓存磁盘进行格式化。

1. 设备完成启动过程后，请登录到 AltaVault 控制台。使用 ``*admin`` 作为**用户名**，使用 ``password`` 作为**密码**。您可以在初始配置完成后更改这些凭证。
2. 登录后，系统会询问您是否要使用向导来进行初始配置。输入 **y**，然后按 **Enter** 键以保存更改。

向导打开后，使用表 1 中的信息。

|问题|回答|
|---|---|
|步骤 1：管理员密码？|输入新的管理员密码（不能是“password”）|
|步骤 2：主机名？|输入要使用的主机名|
|步骤 3：在主接口上使用 DHCP？|输入 **n**|
|步骤 4：主 IP 地址？|输入主网络 IP 地址。在示例配置中，主 IP 地址是用于云复制和设备管理的网络 (192.168.50.15)|
|步骤 5：网络掩码？|输入网络掩码 (255.255.255.0)|
|步骤 6：缺省网关？|输入缺省网关 (192.168.50.1)|
|步骤 7：主 DNS 服务器？|输入环境中的主域名系统 (DNS) 服务器|
|步骤 8：域名？|输入环境的域名 (testenv.org)|
{: caption="表 1. AltaVault 初始配置值" caption-side="top"}

### 配置 AltaVault 用于 Object Storage
{: #configuring-altavault-for-object-storage}

使用以下步骤将设备配置为连接到 {{site.data.keyword.cos_full_notm}} 服务。

1. 打开 Web 浏览器，并输入 AltaVault 设备主接口的 IP 地址（请参阅先前的步骤）。
2. 使用管理员凭证登录到控制台。首次登录时，将显示“向导仪表板”。
3. 选择**系统设置**，然后验证下一个屏幕上的信息是否正确，并调整时区以反映出环境的时区。
4. 单击**下一步 > 保存并应用 > 退出**。您将返回到“向导仪表板”。
5. 选择**云设置**，然后单击**提供者**。选择 **IBM Cloud Object Storage**。
6. 选择相应的 **Object Storage 区域**。**注：**并非所有区域都会显示（如墨尔本）。但是，使用**主机名**字段可修改 {{site.data.keyword.cos_short}} 服务的主机名。例如，如果要使用“墨尔本”作为区域，那么可以从**区域**下拉菜单中选择**圣何塞 1**，然后将**主机名**字段修改为 **mel01.objectstorage.softlayer.net**。
7. 在**用户名**字段中输入 {{site.data.keyword.cos_full_notm}} 凭证。用户名的格式必须为 `object_storage_username:IBM_Cloud_username`。例如：**ABC-DE123456-7:user**。您可以在 [{{site.data.keyword.slportal_full}} ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://control.softlayer.com/){: new_window} 中的**存储 > Object Storage** 下找到您的 Object Storage 用户名。
8. 输入要存储数据的**存储区名称**。存储区名称是 {{site.data.keyword.cos_full_notm}} 中要存储数据的容器的名称。
* 不要修改缺省端口，除非网络管理员指示您这样做。**启用归档**字段缺省为**否**，然后单击**下一步**。
9. 根据需要输入**许可证请求令牌**，然后单击**下一步**。
10. 输入**加密密钥**。可以允许 AltaVault 生成新的加密密钥，或者输入要用于对数据进行加密和解密的现有密钥。单击**下一步**。
11. 验证是否所有设置都正确，然后单击**完成并应用**。现在，AltaVault 将尝试使用“云设置向导”中的输入和设置来联系 {{site.data.keyword.cos_full_notm}} 服务。如果连接失败，请复查设置并确保您具有对该服务的相应访问权。
12. 建立连接后，单击**退出**以返回到“向导仪表板”，然后单击**退出向导**以返回到 AltaVault 设备状态页面。
13. 验证*存储优化服务*是否正在运行以及状态是否为就绪。**注：**可能需要几分钟时间，状态才会更改为*就绪*。

AltaVault 设备已配置为与 {{site.data.keyword.cos_full_notm}} 服务通信。

### 在 AltaVault 中配置 CIFS/SMB 安装点
{: #configuring-the-cifs-smb-mount-point-in-altavault}

**e0b** 接口需要配置为创建 CIFS/SMB 安装点。使用以下步骤来配置 **e0b**。

1. 转至**设置 > 数据接口**，然后展开 **e0b** 接口。选中**启用数据接口**，然后输入用于安装 CIFS/SMB 共享的 **IP 地址、子网掩码**和**网关**。
2. 保留 **MTU** 缺省值 **1500 字节**。<br/><br/>虽然缺省最大传输单元 (MTU) 设置为 1,500，但如果使用的是巨型帧，那么可以将其更改为 9,000。**注：**ESXi 主机和物理基础架构需要支持巨型帧。缺省情况下，{{site.data.keyword.cloud_notm}} 已支持 MTU 大小 9,000 字节，因此无需更改任何配置。
3. 单击**应用**。安装点已准备好进行配置。
4. 选择**存储 > CIFS > 添加 CIFS 共享**，然后输入唯一名称。
5. 单击**锁定共享**下拉菜单，然后选择**是**。**注：**对于未锁定的共享，Veeam Backup & Replication 备份可能会失败。
6. 在**路径**字段中输入共享的唯一路径。最好使用共享名称作为路径，即，如果共享名称为 `cifs_share0`，请输入 /cifs_share0 作为路径。
7. 如果安全不是问题，请清除**允许所有人访问**。最好将使用 CIFS/SMB 共享的客户机列入白名单。否则，如果安全是问题，请使**允许所有人访问**保持选中状态，然后单击**添加共享**。
8. 单击**添加 CIFS 用户**为授权用户创建帐户，然后填写**用户名**和**密码**字段。
9. 展开新的 CIFS 共享，然后单击**添加用户或组**以添加授权用户帐户。
10. 转至**全局 CIFS 设置**，单击**侦听接口**下拉菜单并选择 **e0b**，然后单击**应用**。

AltaVault 设备已配置为允许自身、{{site.data.keyword.cos_full_notm}} 和运行 Veeam Backup & Replication 的计算机之间进行通信。建议导出 AltaVault 设备的配置，以加快未来部署速度（如果需要）。

要导出 AltaVault 设备配置，请执行以下步骤。

1. 单击**设置 > 设置向导**以在内部部署 AltaVault 设备的 Web 管理控制台中访问“向导仪表板”。
2. 单击**导出配置**，然后单击**导出配置**。
3. 将配置文件 (tarball/.tar) 保存在安全位置。

## Veeam Backup & Replication
{: #veeam-backup-replication-backup}

Veeam Backup & Replication 软件为 VM 及其数据提供了完整备份、复制和恢复功能。备份可以完全与 AltaVault 云网关设备集成。

### 在新服务器上供应 Veeam
{: #provisioning-veeam-on-a-new-server}

可以在供应新的 VSI 或裸机服务器时订购 Veeam。供应时，请使用以下信息。
  * Veeam 仅可用于按月计费
  * Veeam 仅可用于 Windows 操作系统

要添加 Veeam，请执行以下操作：
  * 在“系统附加组件”部分的“特定于操作系统的附加组件”下，单击 Veeam 选项卡，然后选择其中一个 Veeam 选项。
  * 在“系统附加组件”部分的“CDP 附加组件”下，选择要添加的其他任何 Veeam 选项。<br><br>**注**：如果选择 *Veeam Backup and Replication 9.5 Update 3*，那么需要在 CDP 附加组件列表中选择至少一个选项。

### 在现有服务器上订购 Veeam
{: #ordering-veeam-on-an-existing-server}

可以使用[操作系统重装](/docs/infrastructure/software?topic=software-reloading-the-os)过程将 Veeam 添加到现有服务器。服务器必须运行的是 Windows 操作系统，并且必须设置为按月计费。

要添加 Veeam，请执行以下操作：
1. 在“系统附加组件”部分的“特定于操作系统的附加组件”下，单击 Veeam 选项卡，然后选择其中一个 Veeam 选项。
2. 在“系统附加组件”部分的“CDP 附加组件”下，选择要添加的其他任何 Veeam 选项。<br><br>**注**：如果选择 *Veeam Backup and Replication 9.5 Update 3*，那么需要在 CDP 附加组件列表中选择至少一个选项。

### 部署 Veeam Backup & Replication
{: #deploying-veeam-backup-replication}

示例中使用的是 Veeam Backup & Replication V8 试用版。

#### 先决条件
{: #prerequisites-deploying-veeam-backup-replication}

继续部署之前，请验证是否满足以下先决条件：

* 拥有配置用于 {{site.data.keyword.cos_full_notm}} 和 Veeam Backup & Replication 的现有 AltaVault 设备。
* 获取 Veeam Backup & Replication for VMware 环境的副本，这是单个可执行文件。请联系 Veeam 代表以获取副本，或者下载 [30 天试用版 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}。
* 获取要用于 Veeam Backup & Replication 的许可证文件。在大多数情况下，此文件会通过电子邮件发送到用于下载 Veeam Backup & Replication 的电子邮件地址。如果未收到此文件，请联系 Veeam 代表。<br/><br/>许可证文件用于激活所有 Veeam Backup & Replication 功能。如果在程序安装期间未提供此文件，那么所有功能部件和功能都会还原为 30 天试用版。
* 拥有现有备份服务器（可以是现场或非现场备份服务器），其规范可以在表 2 中找到。安装的操作系统必须是 64 位版本。

||最低需求|建议需求|
|---|---|---|
|**操作系统**|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>WIndows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|
|**内核或 vCPU 数**|2|4|
|**内存**|4 GB 基本 RAM，外加每个并发备份作业 500 MB。|16 GB 基本 RAM，外加每个并发备份作业 4 GB。|
|**磁盘空间**|2 GB 用于产品安装；每 100 个 VM 10 GB，用于访客文件系统目录数据（持久数据）。|2 GB 用于产品安装；100 个 VM 10 GB，用于访客文件系统目录（持久数据）。|
|**网络**|1 Gbps LAN，用于现场备份和复制；1 Mbps WAN，用于非现场备份和复制。|1 Gbps LAN，用于现场备份和复制；1 Mbps WAN，用于非现场备份和复制。|
{: caption="表 2. Veeam Backup & Replication 备份服务器的系统需求" caption-side="top"}

#### 安装 Veeam Backup & Replication
{: #installing-veeam-backup-replication}

在满足所有先决条件后，使用以下步骤将 Veeam Backup & Replication 安装到备份服务器。

1. 要启动设置向导，请双击 Veeam 可执行文件，然后单击 **Veeam Backup & Replication - 安装**。
2. 单击**下一步**，并接受许可协议中的条款。
3. 单击**下一步**，然后针对 **Veeam Backup & Replication**，单击**安装**。
4. 输入先前获取的许可证文件的位置，然后单击**下一步**。
5. 在 **Veeam Backup & Replication 设置**屏幕上，选择要安装的 Veeam Backup & Replication 组件，并提供安装位置。**Veeam Backup & Replication 和 Veeam Backup Catalog** 是必需组件。单击**下一步**。设置向导会运行一系列检查，以确保所有必需的程序框架和支持组件都已安装。如果缺少任何项，设置向导会主动自动进行安装。如果需要安装缺少的框架或组件，请单击**安装**。
6. 验证所有组件是否均**已通过**系统检查，然后单击**下一步**。
7. 选择运行 Veeam Backup 服务的**服务（用户）帐户**。**注：**缺省服务帐户是 *LOCAL SYSTEM 帐户*。单击**下一步**。
8. 选择用于创建和存储 Veeam Backup & Replication 数据库的 **SQL Server 实例**。<!--Contact your database administrator for more information, if necessary. -->单击**下一步**。
9. 输入**目录服务端口**和 **Veeam Backup 服务端口**（缺省端口为 **9393** 和 **9392**）。<!--Contact your network administrator for more information, if necessary.-->单击**下一步**。
10. 选择存储访客文件系统目录（持久数据）和 vPower NFS 写高速缓存（非持久数据）的目录。单击**下一步**。
11. 验证是否所有设置和值都正确，然后单击**安装**以开始安装。安装完成后，单击**完成**。

### 配置 Veeam Backup & Replication 进行备份
{: #configuring-veeam-backup-replication-for-backups}

安装 Veeam Backup & Replication 后，即可随时将其连接到包含 AltaVault 虚拟设备的 vSphere ESXi 主机。

1. 启动 Veeam Backup & Replication。
2. 单击屏幕左下角的**备份基础架构 > 受管服务器**。
3. 单击**添加服务器**，然后双击 **VMware vSphere**。
4. 输入 vCenter Server 的 **DNS 名称或 IP 地址**和**描述**，然后单击**下一步**。
5. 输入在 vSphere 服务器上具有管理员特权的本地帐户的**凭证**。**注：**对于域帐户，帐户用户名必须为 **DOMAIN\USER** 格式，对于本地帐户，必须为 **HOST\USER** 格式。要添加帐户，请单击**添加**，然后输入帐户用户名和密码。<br/>在 Veeam Backup & Replication 安装期间，不要更改**缺省 VMware Web Service 端口**，除非网络管理员指示您这样做。
6. 单击**下一步**。Veeam Backup & Replication 会连接到 VMware vSphere 服务器。如果连接尝试失败，请检查帐户是否存在，以及是否在 VMware vSphere 服务器上具有管理员特权，然后重试连接。
7. 单击**摘要**窗口上的**完成**，然后通过单击**受管服务器 > VMware vSphere** 来验证 vSphere 服务器是否已成功添加。

### 向 Veeam Backup & Replication 添加备份存储库
{: #adding-a-backup-repository-to-veeam-backup-replication}

缺省情况下，在程序安装期间 Veeam Backup & Replication 会在 Veeam Backup & Replication 备份服务器的 C:\ 盘上创建本地备份存储库。

使用以下步骤来创建备份存储库，以存储 AltaVault 设备上的所有备份。如果要使用缺省备份存储库，请跳过以下步骤。

1. 在**备份基础架构**屏幕的左下角，单击**备份基础架构 > 备份存储库 > 添加存储库**。
2. 在**名称**字段中输入唯一的存储库名称。（可选）可以提供**描述**。单击**下一步**。
3. 选择**共享文件夹**。创建的 CIFS/SMB 共享是此备份存储库使用的共享。单击**下一步**。
4. 指定 AltaVault 设备上 CIFS/SMB 共享的位置。要确定位置，请打开 Web 浏览器，并输入 AltaVault 设备的 IP 地址。转至**存储 > CIFS**，并记下共享的**共享路径**。**注：**这与共享的本地路径不同。<br/><br/>共享路径格式为 `\\<AltaVault appliance hostname>\<share name>`。将“共享路径”中的 AltaVault 设备主机名替换为 AltaVault 设备的 **e0b** 网络接口（共享的安装点）的 IP 地址。<br/><br/>要查找 **e0b** 接口的 IP 地址，请单击 AltaVault 设备管理窗口中的**设置 > 数据接口**。Veeam Backup & Replication 中指定的共享路径为 `\\192.168.50.16\cifs_test2`。
5. 返回到 Veeam Backup & Replication，在**共享文件夹**字段中输入安装点的共享路径，然后单击**下一步**。Veeam Backup & Replication 会尝试建立与该安装点的连接。如果连接尝试失败，请返回并验证 AltaVault 设备的网络设置是否正确，然后重试。
6. 输入值，用于将最大并发任务数限制为可用的资源数。此值是备份代理可以发送到所选共享的最大任务数。缺省并发任务数为 4。AltaVault 建议从 5 个并发任务开始，并根据资源的允许情况来增大或减小此值。创建备份存储库后，可以调整此值。
7. 单击**下一步**。
8. 如果需要，请指定 **vPower NFS 设置**。如果使**启用 vPower NFS 服务器**选项保持未选中状态，那么 Veeam Backup & Replication 将使用 vPower 进行恢复并执行恢复验证。单击**下一步**。
9. 在**复查**屏幕上，确认是否所有设置都正确，然后单击**下一步**。
10. 单击**完成**以退出向导。现在，可以开始备份数据。

### 备份环境
{: #backing-up-the-environment}

执行以下步骤来创建完整虚拟环境的备份。

1. 在**备份基础架构**屏幕中，单击**备份和复制**。
2. 在**备份和复制**窗口中，单击**作业 > 备份作业**。
3. 在**名称**字段中输入唯一名称。（可选）可以输入**描述**。单击**下一步**。
4. 通过单击**添加对象**并单击树结构来选择 VM，以选择要备份的 VM。选择相应的 VM 后，单击**添加**。<br/>如果要仅备份 VM 的特定部分（启动磁盘），请单击**排除**并指定部分。否则，请单击**下一步**。
5. 使用**备份存储库**下拉菜单选择您创建的备份存储库。

**注：**为了获得最佳性能，请确保更改了数据去重和压缩设置。使用以下步骤来优化性能。

1. 单击**高级**，选择**存储**选项卡，然后清除**启用内联数据去重**。这将提高性能，因为 AltaVault 设备会对通过它的 Veeam Backup & Replication 备份执行块级别去重。
2. 在**压缩级别**下拉菜单下选择**无**，然后在**存储优化**下拉菜单下选择 **LAN 目标**。<br/>**注：**如果 CIFS/SMB 共享的网络位置拥堵，使**启用内联数据去重**保持选中状态可能会缓解网络性能问题，但代价是 AltaVault 设备上的数据去重比率下降。
3. 单击**下一步**。
4. 如果需要应用程序感知处理和/或访客文件系统索引建立，请选中相应的复选框。根据需要，设置**访客操作系统凭证** [正在备份的 VM 的访客操作系统用户名和密码]。单击**下一步**。
5. 如果备份要定期运行，请选中**自动运行作业**复选框，并设置所需的时间间隔。否则，请单击**创建**，再单击**完成**。

#### 启动手动备份
{: starting-a-manual-backup}
要手动启动备份，请右键单击备份作业，并选择**启动**。或者，如果需要新备份，请选择**活动完全**。

**注：**Veeam Backup & Replication 可以从备份复原虚拟环境。有关复原虚拟环境的更多信息，请参阅 [Veeam Backup & Replication ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window} Web 站点。
