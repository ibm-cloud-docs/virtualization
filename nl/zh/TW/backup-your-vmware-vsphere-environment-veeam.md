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

# 使用 Veeam 備份 VMware vSphere 環境
{: #backing-up-your-vmware-vsphere-environment-by-using-veeam}

您可以使用由下列服務組成的混合式解決方案，來備份 VMware vSphere 環境：

* {{site.data.keyword.cloud}} Object Storage 服務
* NetApp AltaVault 雲端儲存空間閘道
* Veeam Backup & Replication 軟體

Veeam Backup & Replication 會啟用混合式解決方案，其中包括 NetApp AltaVault 雲端整合式儲存空間應用裝置及 {{site.data.keyword.cos_full}}。此軟體會從備份建立、維護及還原虛擬環境。與 NetApp AltaVault 雲端整合式儲存空間應用裝置搭配使用時，您可以建立本端儲存的備份（內部部署）。此備份也會同步抄寫至 {{site.data.keyword.cos_full_notm}}。有了這個混合式解決方案，就會建立兩個備份副本，但其中只有一個存在於本端。

## AltaVault 雲端整合式儲存空間閘道
{: #altavault-cloud-integrated-storage-gateway}

您可以使用「AltaVault 雲端儲存空間閘道」，將內部部署環境與雲端整合，而不必使用 {{site.data.keyword.cos_full_notm}} 的 REST API 來撰寫 Script 或應用程式。您可以裝載或指向裝載點，並開始將資料安全地複製至雲端。

### 部署 AltaVault 內部部署
{: #deploying-altavault-on-premises}

請遵循下列步驟，將 AltaVault 當作內部部署備份解決方案部署至 {{site.data.keyword.cos_full_notm}}。

您可以購買 AltaVault 作為實體或虛擬應用裝置。此程序涵蓋了試用版 VMware vSphere ESXi 型 AltaVault 虛擬應用裝置的部署。
{:tip}

<a name="prerequisites"></a>
#### 必要條件

驗證是否符合下列必要條件：

* 「AltaVault 虛擬應用裝置」的副本。它是副檔名為 OVA 的單一檔案。請聯絡您的 NetApp 代表以取得應用裝置，或從 [NetApp AltaVault 網站 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](http://www.netapp.com/us/products/protection-software/altavault/){: new_window} 中下載 90 天試用版。
* 現有的內部部署 vSphere ESXi 5.5 環境，其中具有適用於 AltaVault 應用裝置的最低 CPU、記憶體及磁碟空間需求。如果您使用試用版，則這些需求為四個虛擬 CPU (vCPU)、24 GB 記憶體，以及最高 8 TB 的磁碟空間。
* 兩個可在 vSphere 環境內使用的 10-Gbps 網路介面控制器 (NIC)。一個 NIC 用於資料輸入，另一個則用於將資料抄寫至 {{site.data.keyword.cos_full_notm}}。
* 兩個網路，對應於 vSphere 環境內定義的兩個 NIC (VLAN)。無法將抄寫網路指派給與資料輸入網路相同的網路，若這樣做可能會產生遞送迴圈。
* 一組 {{site.data.keyword.cos_full_notm}} 認證。這些認證包括 {{site.data.keyword.cloud_notm}} 使用者名稱、{{site.data.keyword.cos_full_notm}} 使用者名稱，以及與 {{site.data.keyword.cloud_notm}} 使用者名稱相關聯的 API 金鑰。
* 讓您了解 VMware Sphere 術語及如何管理 vSphere ESXi 環境的知識。此知識包括（但不限於）如何使用 vSphere Web 用戶端、vSphere 用戶端，以及如何指派硬體資源，其中包括網路及儲存空間。

### 部署 AltaVault OVA
{: #deploying-altavault-ova}

在符合所有必要條件之後，您可以將 AltaVault OVA 部署至 vSphere 環境。可在 [NetApp AltaVault Installation and Service Guide ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window} 中找到部署 OVA 的指示。

1. 在 OVA 的部署完成之後，編輯 AltaVault 虛擬機器 (VM)。
2. 變更配置的記憶體，以符合編輯視窗中的 AltaVault 版本。如果您使用的是試用版，請指派 24 GB 記憶體，並新增一顆小於或等於 8 TB 的磁碟。**附註：**這個次要磁碟儲存裝置用來儲存刪除重複的備份資料。
* 請確定在修改記憶體及磁碟配置之後，將不同的網路 (VLAN) 指派給 AltaVault 應用裝置。

NIC 獲指派下列介面功能：

* 主要。用作管理介面。
* e0a。此介面用來將 AltaVault 應用裝置中的資料抄寫至雲端。
* e0b。此介面用來匯出 SMB/CIFS 或 NFS 共用的裝載點。
* e0c。此選用介面用來匯出 SMB/CIFS 或 NFS 共用的裝載點。

在此範例配置中，AltaVault 應用裝置會使用 **e0a** 介面作為「抄寫至雲端」介面，以及使用 **e0b** 介面來匯出 CIFS/SMB 裝載點。**附註：**無法同時使用 CIFS/SMB 共用及 NFS 共用來存取相同的資料。換言之，如果資料放在 CIFS/SMB 共用中，則無法透過 NFS 共用來存取它，反之亦然。

如需部署 AltaVault 應用裝置，以及配置應用裝置之 VM 設定的相關資訊，請參閱 [NetApp AltaVault Installation and Service Guide ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}。

### AltaVault 應用裝置的起始配置<!--initial configuration?-->
{: #initial-configuration-of-the-altavault-appliance-initial-configuration-}

您可以在 AltaVault VM 配置了適當的硬體之後，開啟 AltaVault VM 的電源。**附註：**AltaVault VM 初次啟動需要一些時間，因為 AltaVault 應用裝置正在格式化次要 meta 資料快取磁碟。

1. 在應用裝置完成啟動處理程序之後，請登入 AltaVault 主控台。使用 ``*admin`` 作為**使用者名稱**，並使用 ``password`` 作為**密碼**。您可以在起始配置完成之後變更這些認證。
2. 登入之後，系統會詢問您是否要使用精靈進行起始配置。請輸入 **y**，然後按 **Enter** 鍵以儲存您的變更。

在精靈開啟之後，使用表 1 中的資訊。

|問題|答案|
|---|---|
|步驟 1：管理者 (admin) 密碼？|輸入新的管理者 (admin) 密碼（不得為 "password"）|
|步驟 2：主機名稱？|輸入您要使用的主機名稱|
|步驟 3：在主要介面上使用 DHCP？|輸入 **n**|
|步驟 4：主要 IP 位址？|輸入主要網路 IP 位址。在範例配置中，主要 IP 位址是用於雲端抄寫及應用裝置管理的網路（192.168.50.15）|
|步驟 5：網路遮罩？|輸入網路遮罩 (255.255.255.0)|
|步驟 6：預設閘道？|輸入預設閘道 (192.168.50.1)|
|步驟 7：主要 DNS 伺服器？|輸入您環境中的主要「網域名稱系統 (DNS)」伺服器|
|步驟 8：網域名稱？|輸入您環境的網域名稱 (testenv.org)|
{: caption="表 1. AltaVault 起始配置值" caption-side="top"}

### 配置 AltaVault for Object Storage
{: #configuring-altavault-for-object-storage}

請使用下列步驟來配置應用裝置，以連接至 {{site.data.keyword.cos_full_notm}} 服務。

1. 開啟 Web 瀏覽器，然後輸入 AltaVault 應用裝置主要介面的 IP 位址（請參閱前一個步驟）。
2. 使用管理者認證登入主控台。在第一次登入時，會顯示「精靈儀表板」。
3. 選取**系統設定**，然後驗證下一個畫面上的資訊是否正確，並調整時區以反映環境的時區。
4. 按**下一步 > 儲存並套用 > 結束**。您會回到「精靈儀表板」。
5. 選取**雲端設定**，然後按一下**提供者**。選擇 **IBM Cloud Object Storage**。
6. 選取適當的 **Object Storage 地區**。**附註：**並非所有地區都會顯示出來（例如墨爾本）。不過，{{site.data.keyword.cos_short}} 服務的主機名稱會藉由使用**主機名稱**欄位來修改。比方說，如果您想要使用墨爾本作為地區，則可以從**地區**下拉功能表中選取**聖荷西 1**，並將**主機名稱**欄位修改為 **mele01.objectstorage.softlayer.net**。
7. 在**使用者名稱**欄位中輸入您的 {{site.data.keyword.cos_full_notm}} 認證。使用者名稱的格式必須是 `object_storage_username:IBM_Cloud_username`。例如：**ABC-DE123456-7:user**。您可以在 [{{site.data.keyword.slportal_full}} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://control.softlayer.com/){: new_window} 的**儲存空間 > Object Storage** 下，找到 Object Storage 使用者名稱。
8. 輸入**儲存區名稱**來儲存資料。儲存區名稱是您要在其中儲存 {{site.data.keyword.cos_full_notm}} 資料的容器名稱。
* 除非您的網路管理者另有指示，否則請不要修改預設埠。**啟用保存**欄位預設為**否**，然後按**下一步**。
9. 必要的話，輸入**授權要求記號**，然後按**下一步**。
10. 輸入**加密金鑰**。您可以容許 AltaVault 產生新的加密金鑰，或輸入您要用來加密及解密資料的現有金鑰。按**下一步**。
11. 驗證您的所有設定是否都正確，然後按一下**完成並套用**。AltaVault 現在會使用「雲端設定精靈」中的輸入及設定，嘗試聯絡 {{site.data.keyword.cos_full_notm}} 服務。如果連線失敗，請檢閱您的設定，並確定您具有服務的適當存取權。
12. 在建立連線以回到「精靈儀表板」之後，按一下**結束**，然後按一下**結束精靈**，以回到 AltaVault 應用裝置狀態頁面。
13. 驗證*儲存空間最佳化服務*是否執行中，且狀態是否為備妥。**附註：**可能需要數分鐘的時間，才能讓狀態變更為*備妥*。

AltaVault 應用裝置會配置為與 {{site.data.keyword.cos_full_notm}} 服務通訊。

### 在 AltaVault 中配置 CIFS/SMB 裝載點
{: #configuring-the-cifs-smb-mount-point-in-altavault}

需要配置 **e0b** 介面，才能建立 CIFS/SMB 裝載點。請使用下列步驟來配置 **e0b**。

1. 移至**設定 > 資料介面**，然後展開 **e0b** 介面。勾選**啟用資料介面**，然後輸入您用來裝載 CIFS/SMB 共用的 **IP 位址、子網路遮罩**及**閘道**。
2. 保留 **MTU** 的預設值 **1500 個位元組**。<br/><br/>雖然預設最大傳輸單位 (MTU) 設為 1,500，但是您可以將它變更為 9,000，如果使用巨大訊框的話。**附註：**需要您的 ESXi 主機和實體基礎架構，才能支援巨大訊框。依預設，{{site.data.keyword.cloud_notm}} 已支援 9,000 個位元組的 MTU 大小，不需要任何配置變更。
3. 按一下**套用**。裝載點已備妥，可進行配置。
4. 選取**儲存空間 > CIFS > 新增 CIFS 共用**，然後輸入唯一名稱。
5. 按一下**固定共用**下拉功能表，然後選取**是**。**附註：**Veeam Backup & Replication 備份可能由於未固定的共用而失敗。
6. 在**路徑**欄位中輸入共用的唯一路徑。最好使用共用名稱作為路徑，亦即，如果共用名稱是 `cifs_share0`，請輸入 /cifs_share0 作為路徑。
7. 如果安全不是問題，請清除**容許每個人存取**。最好將使用 CIFS/SMB 共用的用戶端列入白名單。否則，如果安全是問題，請持續選取**容許每個人存取**，然後按一下**新增共用**。
8. 按一下**新增 CIFS 使用者**，以建立授權使用者的帳戶，並完成**使用者名稱**及**密碼**欄位。
9. 展開新的 CIFS 共用，然後按一下**新增使用者或群組**，以新增授權使用者帳戶。
10. 移至**廣域 CIFS 設定**，並按一下**接聽介面**下拉功能表，然後選取 **e0b**，並按一下**套用**。

AltaVault 應用裝置即會配置為容許在本身、{{site.data.keyword.cos_full_notm}} 及執行 Veeam Backup & Replication 的電腦之間進行通訊。必要的話，建議您匯出 AltaVault 應用裝置的配置，以加快未來的部署。

若要匯出您的 AltaVault 應用裝置配置，請遵循下列步驟。

1. 按一下**設定 > 設定精靈**，以在內部部署 AltaVault 應用裝置的 Web 管理主控台中存取「精靈儀表板」。
2. 按一下**匯出配置**，然後按一下**匯出配置**。
3. 將配置檔 (Tarball/.tar) 儲存在安全位置中。

## Veeam Backup & Replication
{: #veeam-backup-replication-backup}

Veeam Backup & Replication 軟體可為 VM 及其資料提供完整備份、抄寫及回復功能。此備份可以完全與「AltaVault 雲端閘道應用裝置」整合。

### 在新伺服器上佈建 Veeam
{: #provisioning-veeam-on-a-new-server}

在佈建新的 VSI 或裸機伺服器時，您可以訂購 Veeam。佈建時請使用下列資訊。
  * Veeam 只能搭配按月計費使用
  * Veeam 只能搭配「Windows 作業系統」使用

若要新增 Veeam，請執行下列動作：
  * 在「系統附加程式」區段的「作業系統專用附加程式」下，按一下 Veeam 標籤，然後選取其中一個 Veeam 選項。
  * 在「系統附加程式」區段的「CDP 附加程式」下，選取任何其他要新增的 Veeam 選項。<br><br>**附註**：如果您選取 *Veeam Backup and Replication 9.5 Update 3*，則需要在「CDP 附加程式」清單中至少選取一個選項。

### 在現有伺服器上訂購 Veeam
{: #ordering-veeam-on-an-existing-server}

您可以使用[作業系統重新載入](/docs/infrastructure/software?topic=software-reloading-the-os)程序，將 Veeam 新增至現有伺服器。此伺服器必須正在執行 Windows 作業系統，而且它必須設為按月計費。

若要新增 Veeam，請執行下列動作：
1. 在「系統附加程式」區段的「作業系統專用附加程式」下，按一下 Veeam 標籤，然後選取其中一個 Veeam 選項。
2. 在「系統附加程式」區段的「CDP 附加程式」下，選取任何其他要新增的 Veeam 選項。<br><br>**附註**：如果您選取 *Veeam Backup and Replication 9.5 Update 3*，則需要在「CDP 附加程式」清單中至少選取一個選項。

### 部署 Veeam Backup & Replication
{: #deploying-veeam-backup-replication}

範例中使用 Veeam Backup & Replication 第 8 版的試用版。

#### 必要條件
{: #prerequisites-deploying-veeam-backup-replication}

在繼續進行部署之前，請驗證是否滿足下列必要條件：

* 具有已配置要與 {{site.data.keyword.cos_full_notm}} 及 Veeam Backup & Replication 搭配使用的現有 AltaVault 應用裝置。
* 取得適用於 VMware 環境的 Veeam Backup & Replication 副本，這是單一執行檔。請聯絡您的 Veeam 代表以取得副本，或下載 [30 天試用版本 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](http://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}。
* 取得要與 Veeam Backup & Replication 搭配使用的授權檔。在大部分情況下，此檔案會透過電子郵件傳送至用來下載 Veeam Backup & Replication 的電子郵件位址。如果未收到此檔案，請與您的 Veeam 代表聯絡。<br/><br/>授權檔用來啟動所有 Veeam Backup & Replication 功能。如果在程式安裝期間未提供此檔案，則所有特性及功能都會回復為 30 天試用版。
* 具有現有的備用伺服器（可為站上或異地），其中的規格可在表 2 找到。安裝的作業系統必須是 64 位元版本。

||下限|建議|
|---|---|---|
|**作業系統**|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>WIndows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|
|**核心或 vCPU 的數目**|2|4|
|**記憶體**|4 GB 的基礎 RAM，並針對每一個並行備份工作外加 500 MB。|16 GB 的基礎 RAM，並針對每一個並行備份工作外加 4 GB。|
|**磁碟空間**|2 GB 用於產品安裝；每 100 個 VM 有 10 GB 用於來賓檔案系統型錄資料（持續資料）。|2 GB 用於產品安裝；每 100 個 VM 有 10 GB 用於來賓檔案系統型錄（持續資料）。|
|**網路**|1 Gbps LAN 用於站上備份及抄寫；1 Mbps WAN 用於異地備份及抄寫。|1 Gbps LAN 用於站上備份及抄寫；1 Mbps WAN 用於異地備份及抄寫。|
{: caption="表 2. Veeam Backup & Replication 備份伺服器的系統需求" caption-side="top"}

#### 安裝 Veeam Backup & Replication
{: #installing-veeam-backup-replication}

在符合所有必要條件之後，請使用下列步驟將 Veeam Backup & Replication 安裝至備用伺服器。

1. 若要啟動設定精靈，請按兩下 Veeam 執行檔，然後按一下 **Veeam Backup & Replication - 安裝**。
2. 按**下一步**，並接受授權合約中的條款。
3. 按**下一步**，然後對 **Veeam Backup & Replication** 按一下**安裝**。
4. 輸入先前取得之授權檔的位置，然後按**下一步**。
5. 選取您要安裝的 Veeam Backup & Replication 元件，並在 **Veeam Backup & Replication 設定**畫面上提供安裝位置。**Veeam Backup & Replication 及 Veeam Backup 型錄**是必要元件。按**下一步**。設定精靈會執行一系列檢查，確定已安裝所有必要的程式架構及支援元件。如果遺漏任何項目，則設定精靈會自動安裝它們。如果您需要安裝遺漏的架構或元件，請按一下**安裝**。
6. 驗證所有元件皆已**通過**系統檢查，然後按**下一步**。
7. 選取「Veeam Backup 服務」執行所在的**服務（使用者）帳戶**。**附註：**預設服務帳戶為 *LOCAL SYSTEM 帳戶*。按**下一步**。
8. 選取用來建立及儲存 Veeam Backup & Replication 資料庫的 **SQL Server 實例**。<!--Contact your database administrator for more information, if necessary. -->按**下一步**。
9. 輸入**型錄服務埠**及 **Veeam Backup 服務埠**（預設埠是 **9393** 及 **9392**）。<!--Contact your network administrator for more information, if necessary.-->按**下一步**。
10. 選取儲存來賓檔案系統型錄（持續資料）及 vPower NFS 寫入快取（非持續資料）的目錄。按**下一步**。
11. 驗證所有設定及值皆正確，然後按一下**安裝**來開始安裝。在安裝完成之後，請按一下**完成**。

### 配置 Veeam Backup & Replication 進行備份
{: #configuring-veeam-backup-replication-for-backups}

在安裝 Veeam Backup & Replication 之後，您可以將它連接至包含 AltaVault 虛擬應用裝置的 vSphere ESXi 主機。

1. 啟動 Veeam Backup & Replication。
2. 在畫面左下方，按一下**備份基礎架構 > 受管理伺服器**。
3. 按一下**新增伺服器**，然後按兩下 **VMware vSphere**。
4. 輸入 vCenter 伺服器的 **DNS 名稱或 IP 位址**及**說明**，然後按**下一步**。
5. 輸入在 vSphere 伺服器具有管理者專用權之本端帳戶的**認證**。**附註：**帳戶使用者名稱的格式須為 **DOMAIN\USER**（若為網域帳戶），或為 **HOST\USER**（若為本端帳戶）。若要新增帳戶，請按一下**新增**，然後輸入帳戶使用者名稱及密碼。<br/>除非您的網路管理者另有告知，否則請不要在 Veeam Backup & Replication 安裝期間變更**預設 VMware Web 服務埠**。
6. 按**下一步**。Veeam Backup & Replication 即會連接至 VMware vSphere 伺服器。如果連線嘗試失敗，請檢查帳戶是否存在，以及是否在 VMware vSphere 伺服器上具有管理者專用權，然後再嘗試重新連接。
7. 在**摘要**視窗上按一下**完成**，然後按一下**受管理伺服器 > VMware vSphere**，來驗證是否已順利新增 vSphere 伺服器。

### 將備份儲存庫新增至 Veeam Backup & Replication
{: #adding-a-backup-repository-to-veeam-backup-replication}

依預設，Veeam Backup & Replication 會在程式安裝期間，於 Veeam Backup & Replication 備用伺服器的 C:\ 磁碟機上建立本端備份儲存庫。

請使用下列步驟來建立備份儲存庫，以儲存 AltaVault 應用裝置上的所有備份。如果您想要使用預設備份儲存庫，請跳過下列步驟。

1. 在**備份基礎架構**畫面的左下方，按一下**備份基礎架構 > 備份儲存庫 > 新增儲存庫**。
2. 在**名稱**欄位中輸入唯一的儲存庫名稱。您可以選擇性地提供**說明**。按**下一步**。
3. 選取**共用資料夾**。您所建立的 CIFS/SMB 共用是此備份儲存庫所使用的共用。按**下一步**。
4. 指定 AltaVault 應用裝置上 CIFS/SMB 共用的位置。若要判定位置，請開啟 Web 瀏覽器，並輸入 AltaVault 應用裝置的 IP 位址。移至**儲存空間 > CIFS**，並記下共用的**共用路徑**。**附註：**這與共用的本端路徑不同。<br/><br/>共用路徑格式為 `\\<AltaVault appliance hostname>\<share name>`。將所需「共用路徑」中的 AltaVault 應用裝置主機名稱取代為 AltaVault 應用裝置的 **e0b** 網路介面（共用的裝載點）的 IP 位址。<br/><br/>若要尋找 **e0b** 介面的 IP 位址，請按一下 AltaVault 應用裝置管理視窗中的**設定 > 資料介面**。在 Veeam Backup & Replication 中指定的共用路徑為 `\\192.168.50.16\cifs_test2`。
5. 回到 Veeam Backup & Replication、在**共用資料夾**欄位中輸入裝載點的共用路徑，然後按**下一步**。Veeam Backup & Replication 會嘗試建立與裝載點的連線。如果連線嘗試失敗，請返回並驗證 AltaVault 應用裝置的網路設定是否正確，然後再試一次。
6. 輸入一值，將並行作業數上限限制為可用的資源數目。此值是備份 Proxy 可以傳送至所選共用的作業數上限。預設並行作業數目為 4。AltaVault 建議您從五個並行作業開始，並在資源容許時增加或減少此值。在建立備份儲存庫之後，可以調整此值。
7. 按**下一步**。
8. 必要的話，請指定 **vPower NFS 設定**。如果**啟用 vPower NFS 伺服器**選項保持未選取狀態，則 Veeam Backup & Replication 會使用 vPower 進行回復及回復驗證。按**下一步**。
9. 在**檢閱**畫面上，確認所有設定都正確，然後按**下一步**。
10. 按一下**完成**來結束精靈。您現在可以開始備份資料。

### 備份環境
{: #backing-up-the-environment}

請遵循下列步驟，以建立完整虛擬環境的備份。

1. 從**備份基礎架構**畫面中，按一下**備份及抄寫**。
2. 在**備份及抄寫**視窗中，按一下**工作 > 備份工作**。
3. 在**名稱**欄位中輸入唯一名稱。您可以選擇性地輸入**說明**。按**下一步**。
4. 按一下**新增物件**來選取您要備份的 VM，然後按一下樹狀結構來選取 VM。在選取適當的 VM 之後，按一下**新增**。<br/>如果只備份 VM 的特定部分（啟動磁碟），請按一下**排除**，然後指定哪些部分。否則，按**下一步**。
5. 選取您使用**備份儲存庫**下拉功能表建立的備份儲存庫。

**附註：**如需最佳效能，請確定您變更刪除重複資料及壓縮設定。請使用下列步驟來最佳化效能。

1. 按一下**進階**、選取**儲存空間**標籤，然後清除**啟用行內資料複製**。效能會得到改善，因為 AltaVault 應用裝置會對通過其的 Veeam Backup & Replication 備份執行區塊層刪除重複。
2. 在**壓縮層次**下拉功能表下選取**無**，然後在**儲存空間最佳化**下拉功能表下選取 **LAN 目標**。<br/>**附註：**如果 CIFS/SMB 共用的網路位置壅塞，將**啟用行內刪除重複資料**保留勾選狀態可能舒緩網路效能問題，但代價是在 AltaVault 應用裝置上經歷較小的刪除重複資料比例。
3. 按**下一步**。
4. 如果您想要進行應用程式察覺處理及（或）來賓檔案系統檢索，請選取適當的勾選框。必要的話，設定**來賓作業系統認證** [正在備份的 VM 來賓作業系統的使用者名稱及密碼]。按**下一步**。
5. 如果備份定期執行並設定您想要的間隔，請選取**自動執行工作**勾選框。否則，請按一下**建立**及**完成**。

#### 啟動手動備份
{: starting-a-manual-backup}
若要手動啟動備份，請用滑鼠右鍵按一下備份工作，然後選取**啟動**。或者，如果您想要新的備份，請選取**作用中的完整**。

**附註：**Veeam Backup & Replication 可以從備份還原虛擬環境。如需還原虛擬環境的相關資訊，請參閱 [Veeam Backup & Replication ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window} 網站。
