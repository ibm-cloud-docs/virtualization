---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-14"

subcollection: virtualization

keywords: Citrix XenServer
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 向 Citrix XenServer 添加硬盘驱动器
{: #adding-a-hard-disk-drive-to-citrix-xenserver}

在 XenServer 中添加新硬盘驱动器的过程不同于传统的 Linux 过程。对于 XenServer，您需要创建称为“存储库”的容器，用于定义特定存储目标（例如，硬盘）。VM 的虚拟盘映像 (VDI) 存储在此容器中。VDI 是一个抽象的存储空间，充当 VM 的硬盘。

Xen 存储库在本地连接时支持 IDE、SATA、SCSI 和 SAS 驱动器，但用于远程存储的 iSCSI、NFS、SAS 和光纤通道除外。

## 在 XenServer 中创建存储库
{: #creating-a-storage-repository-in-a-xenserver}

1. 以 root 用户身份通过 SSH 登录到 XenServer。

2. 使用以下命令查找新设备的磁盘标识：

       # cat /proc/partitions

  此命令会列出所有 HDD 和分区。查找新的本地磁盘，可能是“sdx”（最有可能是 sdb）或“/cciss/c0d1p0”。使用以下命令可列出服务器中存在的所有分区/HDD 的磁盘标识。

       # ll /dev/disk/by-id

  查找“sdx”或“cciss/c0d1”磁盘的磁盘标识。格式为“scsi-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx”或“cciss-xxxxxxxxxxxxxxxxxxxxxxxxxx”的标识就是您需要的标识。

3. 使用以下命令在 XenServer 中查找“host-uuid”：

        #xe host-list

  UUID (RO) 就是您需要的 “host-uuid”。

4. 创建存储库 (SR)：

  > **注：**[sr-create ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://support.citrix.com/article/CTX121313){: new_window} 命令用于添加新的硬盘驱动器。创建新的硬盘驱动器是一种破坏性过程，会对驱动器进行分区和格式化，因此驱动器上的所有数据都会丢失。如果要重新声明具有现有数据的驱动器，请使用 [sr-introduce ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://support.citrix.com/article/CTX121896){: new_window}。

  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`<br/>
  \- 或 -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`<br/>
  \- 或 -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`

## 验证 XenCenter 中的存储库
{: #verifying-the-storage-repository-in-xencenter}

可以通过执行以下步骤，验证 XenCenter 中的存储库：

1. 连接到 XenCenter。

2. 转至**存储**，以查找所有存储库的详细信息。您可以在列表中看到存储器“Local Storage2”，这表明该磁盘已添加。现在，您可以开始在该磁盘上创建 VDI。
