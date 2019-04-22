---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Avago SafeStore, encryption
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 使用 Avago SafeStore 加密服务启用驱动器安全性
{: #enabling-drive-security-by-using-avago-safestore-encryption-services}

设置驱动器安全性有助于阻止在没有安全密钥的情况下，访问已除去磁盘上存储的数据。没有此密钥，无法恢复驱动器数据。{{site.data.keyword.cloud}} 在精选数据中心为可以在裸机服务器上购买的驱动器提供自加密驱动器 (SED)。美国数据中心内提供 10 TB SATA 驱动器。

## 先决条件
{: #prerequisites-enabling-drive-security-by-using-avago-safestore-encryption-services}

* 裸机服务器（带 SED 驱动器）- 10 TB SATA
* LSI/AVAGO MegaRAID SAS 9361 -8i 或类似 LSI/AVAGO RAID 卡
* 安装的 MegaRAID Storage Manager 软件

## 使用 MegaRAID Storage Manager (MSM) 启用驱动器安全性
{: #enabling-drive-security-by-using-megaraid-storage-manager-msm-}

用户可以使用 MegaRAID Storage Manager 设置安全密钥并保护其中的数据。您还可以使用 WebBIOS 接口，在服务器启动时需要该接口来进入 MegaRAID 卡 BIOS，以配置驱动器安全设置。有关 MegaRAID SAS 9361-8i 控制器卡的更多信息，请参阅 Broadcom 站点 [MegaRAID SAS 9361-8i ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://www.broadcom.com/products/storage/raid-controllers/megaraid-sas-9361-8i#documentation)。

### 识别预安装的 SED 驱动器
{: #identifying-preinstalled-sed-drives}

在大多数支持的操作系统上都预安装了 MegaRAID Storage Manager。如果不存在，您可以从 Broadcom 站点手动进行安装。有关更多信息，请参阅 [MegaRAID SAS 9361-8i 下载](https://www.broadcom.com/products/storage/raid-controllers/megaraid-sas-9361-8i#downloads)。

可以使用系统凭证来打开 MegaRAID Storage Manager。在示例中，使用的是 Windows 机器，并预安装了 MSM。

启动 MSM 时，必须输入**用户名**和**密码**，即特权用户 (Administrator) 和密码。

<!--![Figure 1](images/1_adapter_login.jpg)-->

单击**物理**选项卡。然后，单击系统上可用的驱动器。**属性**窗格具有**驱动器安全属性**，其中包含**支持全磁盘加密**字段，该字段显示**是**。在所用示例中，将使用 2 个非 SED 磁盘和 4 个 SED 磁盘。

<!--![Figure 2](images/1_fde_capable_drives.jpg)-->

### 在控制器上启用驱动器安全性
{: #enabling-drive-security-at-the-controller}

1. 要启用驱动器安全性，请在**物理**选项卡中，右键单击**控制器 0：AVAGO MegaRAID SAS 9361-8i**，然后选择**启用驱动器安全性**。
  * 现在，可以输入**安全密钥标识**和**安全密钥**。如果您有多个安全密钥，那么安全密钥标识可以帮助您确定要使用的安全密钥。必须将安全密钥记录在安全位置。重新配置驱动器（例如，卸下或重新插入驱动器）时，需要安全密钥。如果没有安全密钥，那么无法检索在 SED 之外创建的卷中存储的任何数据。无法检索忘记的安全密钥。另外，还可以设置启动密码，这可使系统暂停，等待输入在此处设置的密码。启动密码是可选的，如果设置了此密码，那么每次重新引导系统时，您都必须登录到 IPMI，并输入启动密码。向下滚动并选中显示**我已记录安全设置以供未来参考**的框，然后单击**是**以启用驱动器安全性。
  * 启用驱动器安全性后，对于**控制器 0：AVAGO MegaRAID SAS 9361-8i**，将显示黄色钥匙图像。
2. 现在，使用 SED 来创建安全卷。可以在**逻辑**选项卡中右键单击**控制器 0**，并选择  
**创建虚拟驱动器**。
3. 选择**高级**选项。屏幕需要指定 **RAID 级别**，并将**驱动器安全方法**指定为**全磁盘加密 (FDE)**。选择所需的 FDE 驱动器，然后单击**添加** > **创建驱动器组** > **下一步**。
4. 复查虚拟驱动器设置，并进行任何必要的更改。**读策略**的建议设置是**始终预读**。**写策略**的建议设置是**回写**。单击**创建虚拟驱动器**。通过单击**是**，以接受因 BBU 产生的回写策略影响。单击**下一步**，并复查摘要屏幕。单击**完成**。
5. 要确认虚拟盘是否已受保护，请单击**逻辑**选项卡，并单击创建的虚拟驱动器。在**驱动器安全属性**中，您会看到**受保护**字段标记为**是**。

<!--![Figure 3](images/2_enable_drive_security.jpg)-->
<!--![Figure 4](images/3_security_key_details_page.jpg)-->
<!--![Figure 5](images/4_security_key_set_0.jpg)-->
<!--![Figure 6](images/9_create_vd_with_fde_drives.jpg)-->
<!--![Figure 7](images/10_create_vd_advanced_select_raid_drive_encryption_0.jpg)-->
<!--![Figure 8](images/create_vd_settings.jpg)-->
<!--![Figure 9](images/6_vd_secured_confirmation_0.jpg)-->

如果服务器随附已使用 SED 驱动器创建的 RAID 卷，那么可以通过执行以下步骤来确保卷的安全。

在**逻辑**选项卡中，右键单击**驱动器组**，然后选择**使用 FDE 进行保护**。如果针对某个卷有混合的 FDE 和非 FDE 驱动器，那么此选项不会显示。

<!--![Figure 10](images/5_secure_existing_vd_with_fde_drives_0.jpg)-->

要除去驱动器安全性，必须首先删除受保护的虚拟盘，然后右键单击“控制器 0”以**禁用驱动器安全性**。此功能可安全地擦除其中的数据，并除去驱动器安全性。

您还可以通过使用 webBIOS，在启动时通过 IPMI 登录并进入 RAID BIOS 来设置驱动器安全性。有关更多信息，请参阅 **12 Gb/s MegaRAID SAS Software User Guide** 中的 **Avago SafeStore Encryption Services**。
