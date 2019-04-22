---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-14"

subcollection: virtualization

keywords: Citrix XenServer
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 將硬碟機新增至 Citrix XenServer
{: #adding-a-hard-disk-drive-to-citrix-xenserver}

在 XenServer 中新增硬碟機與傳統 Linux 處理程序不同。若為 XenServer，您需要建立一個稱為「儲存空間儲存庫」的容器，來定義特定的儲存空間目標（例如硬碟）。此容器是儲存虛擬磁碟映像檔 (VDI) 的位置。VDI 是抽象的儲存空間，作為 VM 的硬碟。

Xen 儲存空間儲存庫在本端連接時支援 IDE、SATA、SCSI 及 SAS 磁碟機，但用於遠端儲存的 iSCSI、NFS、SAS 及「光纖通道」除外。

## 在 XenServer 中建立儲存空間儲存庫
{: #creating-a-storage-repository-in-a-xenserver}

1. 以 root 身分 SSH 至 XenServer。

2. 使用下列指令，尋找新裝置的磁碟 ID ：

       # cat /proc/partitions

  此指令會列出所有 HDD 及分割區。尋找新的本端磁碟，其可能是"sdx"（最可能是 sdb）或 '/cciss/c0d1p0'。請使用下列指令，來列出伺服器中呈現之所有分割區/HDD 的磁碟 ID。

       # ll /dev/disk/by-id

  尋找"sdx"或 'cciss/c0d1' 磁碟的磁碟 ID。"scsi-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"或"cciss-xxxxxxxxxxxxxxxxxxxxxxxxxx"是您需要的格式。

3. 使用下列指令，在 XenServer 中尋找 'host-uuid'：

        #xe host-list

  uuid (RO) 是您需要的 'host-uuid'。

4. 建立儲存空間儲存庫 (SR)：

  > **附註：**[sr-create ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://support.citrix.com/article/CTX121313){: new_window} 指令用於新增硬碟機。建立新的硬碟機是分割及格式化磁碟機的破壞性處理程序，磁碟機上的任何資料都會遺失。如果您想要重新引進一顆具有現有資料的磁碟機，請使用 [sr-introduce ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://support.citrix.com/article/CTX121896){: new_window}。

  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label="Local Storage 2" shared=false type=lvm`<br/>
  \- 或 -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label="Local Storage 2" shared=false type=lvm`<br/>
  \- 或 -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/ host-uuid= name-label="Local Storage 2" shared=false type=lvm`

## 驗證 XenCenter 中的儲存空間儲存庫
{: #verifying-the-storage-repository-in-xencenter}

您可以遵循下列步驟，從 XenCenter 驗證「儲存空間儲存庫」：

1. 連接至 XenCenter。

2. 移至**儲存空間**，以尋找所有儲存空間儲存庫的詳細資料。您可以在清單中看到儲存空間 'Local Storage2'，顯示已新增磁碟。現在，您可以開始在其上建立 VDI。
