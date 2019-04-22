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

# 回復 VMware vSphere 環境
{: #recovering-your-vmware-vsphere-environment}

資料備份目前是維護資料安全及完整性的主要方法。在理想的完美狀態下，將完全不需要用到資料備份。不幸的是，現實的情況是確實會發生事故及災難。如果真的發生，則重要的是讓系統關閉時間最少，並儘快回復正常運作。

在過去，將備份儲存在高效能儲存空間或磁帶是合適的解決方案。現今的企業逐漸利用 Object Storage 來擴增甚至取代它們，試圖降低與實體內部部署儲存空間相關聯的資金和營運成本。但在使用 Object Storage 的情況下，備份已不再位於內部部署儲存空間，因此要從備份還原，已不再只是找出必要備份並從中還原這麼單純的作業。幸而，利用儲存在 Object Storage 上的備份進行還原的各項作業，大多都可利用一個混合式解決方案來執行。此解決方案包括

* {{site.data.keyword.cos_full}}
* NetApp 的 NetApp AltaVault 雲端儲存空間閘道（前身為 Riverbed SteelStore）
* Veeam Backup & Replication 軟體

如果發生災難性事件，並讓主要環境離線，則可以在 {{site.data.keyword.cloud}} 內建立次要回復站台予以回復。您可以部署 Veeam Backup & Replication 軟體及 AltaVault 雲端整合儲存空間應用裝置以與 {{site.data.keyword.cos_full_notm}} 通訊，來存取先前的主要環境備份。此軟體會與儲存空間應用裝置進行連接，以將備份還原至新的位置，並讓主要環境回到線上，以及避免任何進一步的執行時間損失。

在 {{site.data.keyword.cloud_notm}} 回復站台還原虛擬機器 (VM) 時，會保留內部部署 IP 位址。因此，請務必在順利回復或設計 BYOIP 基礎架構時變更 IP 位址。如需任一情況的協助，請聯絡 IBM 業務代表。

下列程序著重於協調使用「NetApp AltaVault 雲端儲存空間閘道」應用裝置、{{site.data.keyword.cloud_notm}} 基礎架構及 Veeam Backup & Replication 來完整還原故障的 VMware vSphere 環境。此軟體假設環境至少有一個使用前述三種技術所建立的備份，並且在回復時位於 {{site.data.keyword.cos_full_notm}}。

有兩種不同的方式可以識別「AltaVault 應用裝置」。第一種是「內部部署 AltaVault 應用裝置」，是指已故障而要從其回復或還原備份的原始內部部署 AltaVault 應用裝置。第二種是「{{site.data.keyword.cloud_notm}} AltaVault 應用裝置」，是指用來從故障的內部部署 AltaVault 應用裝置回復備份的 AltaVault 應用裝置。{{site.data.keyword.cloud_notm}} AltaVault 應用裝置使用公用程式伺服器來部署至 vSphere 環境。

## 混合式解決方案簡介
{: #introducing-a-hybrid-solution}

Veeam Backup & Replication 可讓混合式解決方案包括 NetApp AltaVault 雲端整合儲存空間應用裝置及 {{site.data.keyword.cos_full_notm}}。主要考量是從虛擬環境的備份中建立、維護及還原。NetApp AltaVault 雲端整合儲存空間應用裝置是一種軟體解決方案，可無縫地整合內部部署環境與專用或公用雲端。一起使用時，Veeam Backup & Replication 會建立 AltaVault 雲端整合儲存空間應用裝置在本端內部部署儲存空間上所儲存的備份，並且同時抄寫至 {{site.data.keyword.cos_full_notm}}。此外，{{site.data.keyword.cloud}} 隨收隨付制定價模型以及與內容遞送網路 (CDN) 的完整整合，可讓您在 24 個地理位置不同的節點上儲存及配送資料。

### IBM Cloud Object Storage
{: #ibm-cloud-object-storage}

若要存取備份至 {{site.data.keyword.cos_full_notm}} 的資料檔，請以下列方式要求資料檔：

* 已在內部部署的 AltaVault 應用裝置
* 位於 {{site.data.keyword.cloud_notm}} 內的次要 AltaVault 應用裝置

遵循此程序，在 {{site.data.keyword.cloud_notm}} 內部署次要 AltaVault 應用裝置，以回復故障的主要內部部署 AltaVault 應用裝置。

{{site.data.keyword.cloud_notm}} 環境包含具有本端儲存空間的單一 ESXi 主機，來存放及執行次要 AltaVault 應用裝置。此基礎架構代表具有單一 ESXi 主機的基本單一站台架構，該主機是由「{{site.data.keyword.cloud_notm}} 虛擬伺服器實例 (VSI)」內的 vCenter 伺服器所管理。

如果您有更可觀的基礎架構需要共用儲存空間或支援這些特性：

* vSphere 高可用性 (HA)
* vSphere 分散式資源排程器 (DRS)
* vSphere vMotion

#### 必要條件
{: #prerequisites-ibm-cloud-object-storage}

請先驗證已符合下列必要條件，再繼續進行：**附註：**在此範例中，AltaVault AVA-v8 應用裝置的試用版已與 AltaVault Cloud-Integrated Storage 4.1 搭配使用。

* 確定現有環境包含 {{site.data.keyword.cloud_notm}} VSI 內 vCenter 伺服器所管理的單一 ESXi 主機。
* 熟知 VMware Sphere 術語以及管理 vSphere ESXi 環境的方法。此知識包括（但不限於）使用 vSphere Web Client、vSphere Client 及硬體資源指派，以包括網路和儲存空間。

#### 訂購兩個可攜式專用網路
{: #order-two-portable-private-networks}

AltaVault 需要在環境內位於不同網路上的網路介面。使用下列步驟，透過 {{site.data.keyword.slportal_full}} 訂購兩個可攜式專用網路。

1. 使用您的唯一認證來存取 [{{site.data.keyword.slportal_full}} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://control.softlayer.com/){: new_window}。
2. 按一下**帳戶 > 訂購**。
3. 選取**網路**區段，然後按一下**子網路/IP > 訂單**。
4. 從下拉功能表中，選取**可攜式專用**。選取「可攜式專用」會觸發選項來選取要顯示的可攜式專用 IP 位址數目。**附註：**{{site.data.keyword.cloud_notm}} 會在每個可攜式專用網路的位址區塊中自動保留至少三個 IP 位址。保留位址適用於網址、閘道位址及播送位址。這些位址必須直接連結至虛擬 LAN (VLAN)。<!--If your IBM Cloud pod supports Hot Standby Ready Protocol (HSRP), then the number of usable IP addresses is reduced. For instance, if you select **4 Portable Private IP Addresses** will yield only one usable IP address or zero if the pod supports HSRP.-->如需可攜式 IP 位址的相關資訊，請參閱[開始使用子網路及 IP](/docs/infrastructure/subnets?topic=subnets-getting-started-subnets-ips#getting-started-subnets-ips)。
5. 選取可攜式專用 IP 位址數目之後，請按一下**繼續**。
6. 選取其中要遞送可攜式專用 IP 位址的 VLAN，然後按一下**繼續**。
7. 完成必要的聯絡資訊，然後按一下**下訂單**。
8. 重複訂購處理程序，以取得第二個必要的可攜式專用網路。
9. 從 {{site.data.keyword.slportal}} 頁面中按一下**網路 > IP 管理 > 子網路**，以在可攜式專用網路及 IP 位址配置之後進行檢視。建議追蹤 IP 位址指派。對於每個 IP 位址皆應使用**子網路**頁面的**附註**區段，以反映該位址所指派到的機器。

#### 配置 vSphere
{: #configuring-vsphere}

您需要配置 vSphere 以反映可攜式專用網路的新增，並確定它們邏輯地隔離。

1. 移至 vSphere 環境並建立虛擬機器埠群組，以反映其中一個可攜式 IP 區塊的新增（共兩個）。

虛擬機器埠群組的標籤為**共用網路**。其中一個可攜式 IP 區塊用於放置在預設（主要）虛擬機器埠群組上的 VM。另一個用於 AltaVault 應用裝置與所匯出「共用網際網路檔案系統 (CIFS)/伺服器訊息區塊 (SMB)」裝載點之間的資料傳送。

## AltaVault 雲端儲存空間閘道
{: #altavault-cloud-storage-gateway}

您可以使用 AltaVault 來整合內部部署環境與雲端，而不需要使用 {{site.data.keyword.cos_full_notm}} 的 REST API 來撰寫 Script 或應用程式。AltaVault 會在前端公開 CIFS/SMB 或「網路檔案系統 (NFS)」裝載點，並在後端安全地連接至 {{site.data.keyword.cos_full_notm}} 介面。然後，您可以裝載或指向裝載點，並將資料安全地複製至雲端，或從雲端安全地回復/還原環境。<!-- , as shown in Figure 1.-->

<!-- ![Figure 1](images/veeam_restore_fig2.png)
<br/>`Figure 1: AltaVault end-to-end flow with Veeam and IBM Cloud`-->

### 在災難回復配置中部署 AltaVault
{: #deploying-altavault-in-a-disaster-recovery-configuration}

遵循下列步驟，以使用 {{site.data.keyword.cos_full_notm}} 部署 AltaVault 作為災難回復解決方案。

#### 開始之前
{: #before-you-get-started-deploying-altavault-in-a-disaster-recovery-configuration}

請先驗證已符合下列必要條件，再繼續進行：

* 取得「AltaVault 虛擬應用裝置」的副本，並確定它位於公用程式伺服器上。它是一個具有 OVA 副檔名的單一檔案。請聯絡 NetApp 代表以取得應用裝置，或從 [NetApp AltaVault 網站 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://www.netapp.com/us/products/protection-software/altavault/){: new_window} 下載 90 天試用版。
* 備妥 CPU、記憶體和磁碟空間都符合 AltaVault 應用裝置最低需求的現有內部部署 vSphere ESXi 5.5 環境。如果您使用試用版，則以下是資源需求：四個虛擬 CPU (vCPU)、24 GB 的記憶體及最多 8 TB 的磁碟空間。
* 在 vSphere 環境內，可使用兩個 10 Gbps 網路介面控制器 (NIC)。一個 NIC 用於資料存取，另一個則用於資料抄寫至 {{site.data.keyword.cos_full_notm}}。
* 有兩個可攜式專用網路對應至 vSphere 環境內定義的兩個 NIC。無法將抄寫網路指派給與資料存取網路相同的網路，若這樣做可能會產生遞送迴圈。
* 具有 {{site.data.keyword.cos_full_notm}} 認證。這些認證包括 {{site.data.keyword.cloud_notm}} 使用者名稱、{{site.data.keyword.cos_full_notm}} 使用者名稱，以及與 {{site.data.keyword.cloud_notm}} 使用者名稱相關聯的 API 金鑰。
* 在內部部署 AltaVault 應用裝置上暫停資料抄寫，或中斷它與用來存取 {{site.data.keyword.cloud_notm}} 中備份保存檔的容器/儲存區的連線（如果內部部署環境仍然完整）。
  * 使用下列兩種方法之一，來暫停或停止抄寫：
    * 關閉內部部署 AltaVault 應用裝置。
    * 移至 AltaVault 應用裝置 Web 管理主控台，然後按一下**儲存空間 > 雲端設定 > 抄寫**。選取**暫停抄寫**。
    * 取得內部部署 AltaVault 應用裝置的配置檔副本，或可透過 URL 來存取它。配置檔為 .tar 檔案。
  * 移至 AltaVault 應用裝置 Web 管理主控台，然後按一下**配置 > 設定精靈 > 匯出配置 > 匯出配置**。儲存配置檔（.tar 檔案），然後將其傳送至公用程式伺服器，或使其可透過 URL 來存取。
    * 熟知 VMware Sphere 術語以及管理 vSphere ESXi 環境的方法。此知識包括（但不限於）使用 vSphere Web Client、vSphere Client 及硬體資源指派，例如網路和儲存空間。

### 部署 AltaVault OVA
{: #deploying-altavault-ova-disaster}

符合所有必要條件之後，使用公用程式伺服器將 AltaVault OVA 部署至 vSphere 環境。OVA 部署的指示位於 [NetApp AltaVault 安裝及服務手冊 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window} 中。AltaVault 試用版已配置四個虛擬 CPU、6 GB 的記憶體及 150 GB 的主要磁碟。使用《安裝及服務手冊》來執行下列步驟：

1. 將記憶體數量增加到 24 GB。
2. 新增大小小於或等於 8 TB 的次要磁碟儲存裝置，以儲存刪除重複備份資料。
3. 變更記憶體及磁碟配置之後，將不同的可攜式專用網路指派給 AltaVault 應用裝置。

NIC 將獲指派下列介面功能：

* **主要**：用於 AltaVault 應用裝置管理，以及將資料抄寫至雲端。它在範例環境中獲指派**主要網路**埠群組。
* **e0a**：用來將資料從 AltaVault 應用裝置抄寫至雲端的選用介面。
* **e0b**：用來匯出 SMB/CIFS 或 NFS 共用裝載點的介面。它在範例環境中獲指派**共用網路**埠群組。
* **e0c**：用來匯出 SMB/CIFS 或 NFS 共用裝載點的選用介面。

在此程序的範例配置中，AltaVault 應用裝置使用**主要**介面作為「抄寫至雲端」介面，以及使用 **e0b** 介面來匯出 CIFS/SMB 裝載點。**附註：**您無法同時使用 CIFS/SMB 共用及 NFS 共用來存取相同的資料。所使用的通訊協定必須與內部部署 AltaVault 應用裝置搭配使用以將資料抄寫至雲端的通訊協定相同。

<!-- ![Figure 2](images/veeam_restore_fig3.png)
<br/>`Figure 2: IBM Cloud setup with VMware`-->

如需部署 AltaVault 應用裝置以及配置 VM 設定的相關資訊，請參閱 [NetApp AltaVault 安裝及服務手冊 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}。

### 配置 AltaVault 應用裝置
{: #configuring-the-altavault-appliance}

1. 您可以在 AltaVault VM 配置之後開啟它的電源。可能需要一些時間來啟動 VM，因為 AltaVault 應用裝置正在將輔助儲存空間磁碟格式化。

2. 在應用裝置完成開機處理程序之後，請使用 **admin** 作為**使用者名稱**及 **password** 作為**密碼** 來登入 AltaVault 主控台。您可以在完成起始配置之後變更這些認證。請參閱「表 1：AltaVault 起始配置值」

3. 登入應用裝置之後，系統會詢問您是否要使用精靈進行配置。請輸入 *y*。
4. 進入精靈之後，請使用表 1 中的資訊。
5. 按 **Enter** 鍵儲存變更。

|問題|答案|
|---|---|
|步驟 1：管理者 (admin) 密碼？|輸入新的管理者 (admin) 密碼（不能是 **password**）。|
|步驟 2：主機名稱？|輸入適當的主機名稱。|
|步驟 3：在主要介面上使用 DHCP？|輸入 **n** 或 **no**。|
|步驟 4：主要 IP 位址？|輸入主要網路 IP 位址。在範例配置中，這是用於管理介面的網路（例如 10.120.108.132）。|
|步驟 5：網路遮罩？|輸入網路遮罩（例如 255.255.255.192）。|
|步驟 6：預設閘道？|輸入預設閘道（例如 10.120.108.129）。|
|步驟 7：主要 DNS 伺服器？|輸入您環境中的主要「網域名稱系統 (DNS)」伺服器。|
|步驟 8：網域名稱？|輸入您環境的網域名稱（例如 testenv.org）。|
{: caption="表 1. AltaVault 起始配置值" caption-side="top"}

### 配置 IBM Cloud Object Storage 的 AltaVault
{: #configuring-altavault-for-ibm-cloud-object-storage}

AltaVault 應用裝置在設定之後必須連接至 {{site.data.keyword.cos_full_notm}} 服務。在內部部署配置中，會使用公用 DNS 來連接至「Object Storage 服務」。在此配置中，會改為使用內部 DNS 名稱。

1. 開啟 Web 瀏覽器，然後輸入 AltaVault 應用裝置的 IP 位址。
2. 使用 admin 認證登入主控台。起始登入時，將會顯示**精靈儀表板**。
3. 選取**系統設定**，然後驗證所有網路設定都正確無誤，並調整時區以反映您環境的時區。
4. 選取**下一步 > 儲存並套用 > 結束**。您會回到**精靈儀表板**，並已登出現行瀏覽器階段作業。
5. 重新登入 AltaVault 應用裝置，然後按一下**設定 > 設定精靈 > 匯入配置**，並指定內部部署 AltaVault 應用裝置的配置檔位置。
6. 選取**僅匯入共用資料**，僅匯入一般共用的設定，例如雲端及電子郵件設定。
7. 針對在建立內部部署 AltaVault 應用裝置期間指定的加密金鑰（如果已設定），在**金鑰通行詞組**文字框中輸入其通行詞組。
8. 按一下**匯入配置 > 結束**。

<!--The AltaVault appliance web console displays a message indicating a successful import. If the import was not successful, verify that the configuration file is in the correct format and that the passphrase is correct before trying again.-->

您已將內部部署 AltaVault 應用裝置配置匯入至 {{site.data.keyword.cloud_notm}} AltaVault 應用裝置。

使用下列步驟來修改 {{site.data.keywrod.cloud_notm}} AltaVault 應用裝置的雲端設定，讓它透過專用網路來存取 {{site.data.keyword.cos_full_notm}} 服務。

1. 在 AltaVault 中，移至**儲存空間 > 雲端設定 > 雲端**，並修改主機名稱以反映 {{site.data.keyword.cos_full_notm}} 服務的專用位址。
  * 專用網路名稱語法為 <location>.objectstorage.service.networklayer.com，其中 *<location>* 指定縮短的資料中心名稱（例如：mel01 代表墨爾本 01 資料中心）。
2. 按一下**套用**。AltaVault 應用裝置嘗試連接至 {{site.data.keyword.cos_full_notm}} 服務。如果此連線嘗試失敗，請先驗證雲端提供者設定正確無誤，再嘗試重新連接。

連接至 {{site.data.keyword.cos_full_notm}} 之後，應用裝置必須進入回復模式，以從 {{site.data.keyword.cos_full_notm}} 同步原始備份資料的 meta 資料內容。

請使用下列步驟，將 AltaVault 應用裝置置於回復模式。

AltaVault 應用裝置會從 {{site.data.keyword.cos_full_notm}} 同步資料。**附註：**完成同步所花費的時間視正在還原的備份大小及數目而定。

1. 移至 AltaVault 應用裝置，並登入，然後輸入「圖 3」中顯示的指令：<br/>![圖 3](images/veeam_restore_fig5.png)<br/>`圖 5：回復模式指令`
2. 在同步處理程序完成之後，會回到 AltaVault 應用裝置 Web 主控台。
3. 驗證**儲存空間最佳化服務**為**執行中**，且其**狀態**為**備妥**。**狀態**可能需要數分鐘，才能變更為**備妥**。

### 在 AltaVault 中配置 CIFS/SMB 裝載點
{: #configure-the-cifs-smb-mount-point-in-altavault}

建立與 {{site.data.keyword.cos_full_notm}} 的內部連線之後，即已配置 **e0b** 介面來存取 CIFS/SMB 裝載點。

1. 在 AltaVault Web 主控台中，移至**設定 > 資料介面**。
2. 展開 **e0b** 介面，然後選取**啟用資料介面**（必要的話）。
3. 輸入您要用來裝載 CIFS/SMB 共用的 IP 位址、子網路遮罩及閘道。請務必使用與用於主要介面的子網路不同的子網路。
4. 保留 **MTU** 的預設值 **1500 個位元組**。
  * 雖然預設最大傳輸單位 (MTU) 為 1,500，但是如果您使用巨大訊框，則可以將它變更為 9,000。ESXi 主機及實體基礎架構需要支援巨大訊框。依預設，{{site.data.keyword.cloud_notm}} 支援 9,000 個位元組的 MTU 大小，而且不需要變更任何配置。
5. 按一下**套用**。

AltaVault 應用裝置現在已最低配置成容許在自己、{{site.data.keyword.cos_full_notm}} 與 Veeam Backup & Replication 之間進行通訊。

## Veeam Backup & Replication
{: #veeam-backup-replication-recover}

Veeam Backup & Replication 軟體為 VM 及其資料提供完整備份、抄寫及回復功能。Veeam Backup & Replication 可以與「AltaVault 雲端閘道應用裝置」完全整合，以進行無縫備份及回復體驗。

### 部署 Veeam Backup & Replication
{: #deploy-veeam-backup-replication}

範例中使用 Veeam Backup & Replication 第 8 版的試用版。

#### *必要條件*
{: #prerequisites-deploy-veeam-backup-replication}

繼續進行部署之前，請確保滿足下列必要條件：

* 具有已配置為要與 IBM Cloud Object Storage 及 Veeam Backup & Replication 搭配使用的現有 AltaVault 應用裝置。
* 取得 VMware 環境的 Veeam Backup & Replication 副本，這是一個單一執行檔。請聯絡「Veeam 代表」以取得副本，或下載 [30 天試用版 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}。
* 取得與 Veeam Backup & Replication 搭配使用的授權檔。在大部分情況下，會透過電子郵件將此檔案傳送至用來下載 Veeam Backup & Replication 的電子郵件位址。如果未收到此檔案，請與您的「Veeam 代表」聯絡。<br/><br/>授權檔用來啟動完整的 Veeam Backup & Replication 功能。如果在程式安裝期間未提供此檔案，則所有特性及功能都會回復為 30 天試用版的特性及功能。
* 使用表 2 中的規格，在 IBM Cloud 中佈建虛擬伺服器實例 (VSI)。**附註：**已安裝的作業系統必須是 64 位元版本。

||最小值|建議選項|
|---|---|---|
|**OS**|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|<ul><li>Windows Servers 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>WIndows 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|
|**核心或 vCPU 的數目**|2|4|
|**記憶體**|4 GB 的基礎 RAM，並針對每一個並行備份及還原工作外加 500 MB。|16 GB 的基礎 RAM，並針對每一個並行備份及還原工作外加 4 GB。|
|**磁碟空間**|2 GB 用於產品安裝，每 100 個 VM 有 10 GB 用於來賓系統型錄資料夾（持續資料）。|2 GB 用於產品安裝，每 100 個 VM 有 10 GB 用於來賓系統型錄資料夾（持續資料）。|
|**網路**|1 Gbps LAN 用於站上備份及抄寫，1 Mbps WAN 用於異地備份及抄寫。|1 Gbps LAN 用於站上備份及抄寫，1 Mbps WAN 用於異地備份及抄寫。|
{: caption="表 2. {{site.data.keyword.cloud_notm}} VSI 的系統需求" caption-side="top"}

在與 AltaVault 應用裝置及 vSphere 環境相同的資料中心內佈建 VSI。VSI 必須具有 AltaVault 應用裝置及 vCenter 的存取權。

### 安裝 Veeam Backup & Replication
{: #installing-veeam-backup-replication-disaster}

符合所有必要條件之後，請使用下列步驟以將 Veeam Backup & Replication 安裝至 {{site.data.keyword.cloud_notm}} VSI。

1. 按兩下程式執行檔，然後按一下 **Veeam Backup & Replication - 安裝**。即會開啟設定精靈。
* 按**下一步**，然後選取**我接受授權合約中的條款**。
* 按**下一步**，然後指定在「部署 Veeam Backup & Replication」下取得的授權檔位置。
* 按**下一步**，然後在**程式特性**畫面上選取您要安裝的 Veeam Backup & Replication 元件及其安裝位置。**附註：****Veeam Backup & Replication** 及 **Veeam Backup Catalog** 是必要元件。
* 按**下一步**。<br/>設定精靈會執行一系列的檢查，確定已安裝所有必要程式架構及支援元件。如果遺漏任何元件，則會提供設定精靈予以安裝。<!--Click **Install** if this is the case.-->
2. 驗證所有元件皆已**通過**系統檢查，然後按**下一步**。
3. 選取您要 Veeam Backup Service 執行的**服務（使用者）帳戶**。預設服務帳戶為 **LOCAL SYSTEM 帳戶**。按**下一步**。
4. 選取您要用來建立及儲存 Veeam Backup & Replication 資料庫的 **SQL Server 實例**。如需相關資訊，請聯絡資料庫管理者。按**下一步**。
5. 輸入**型錄服務埠** (9393) 及 **Veeam Backup 服務埠** (9392)。按**下一步**。
6. 選取要在其中儲存來賓檔案系統型錄（持續資料）及 vPower NFS 寫入快取（非持續資料）的目錄。按**下一步**。
7. 驗證所有設定及值都正確無誤，然後按一下**安裝**。安裝完成之後，請按一下**完成**。

### 配置 Veeam Backup & Replication
{: #configuring-veeam-backup-replication}

安裝 Veeam Backup & Replication 之後，您已準備好與 AltaVault 應用裝置連接。

1. 啟動 Veeam Backup & Replication。
2. 在畫面左下方，按一下**備份基礎架構**。
3. 在**備份基礎架構**視窗中，按一下**受管理伺服器 > 受管理伺服器**。
4. 從上方的功能區功能表中，按一下**新增伺服器**，然後按兩下 **VMware vSphere**。
5. 輸入 vSphere 伺服器的專用 IP 位址，然後按**下一步**。
6. 針對具有先前指定 vSphere 伺服器之管理者專用權的本端帳戶，輸入其帳戶認證。**附註：**帳戶 username 必須是網域帳戶的 DOMAIN\USER 格式，或是本端帳戶的 HOST\USER 格式。若要新增帳戶，請按一下**新增**，然後輸入適當的使用者名稱及密碼。除非您的網路管理者要求您變更預設 VMware Web 服務埠，否則請不要變更。<!-- otherwise during [Veeam Backup & Replication installation](#_Installing_Veeam_Backup).-->按**下一步**。
  * Veeam Backup & Replication 會嘗試連接至 vSphere 伺服器。如果連線嘗試失敗，請在重試之前檢查帳戶是否存在，以及是否具有 vSphere 伺服器的管理者專用權。
7. 按一下**完成**。
8. 按一下**受管理伺服器 > VMware vSphere**，以驗證是否已順利新增 vSphere 伺服器。

### 將備份儲存庫新增至 Veeam Backup & Replication
{: #adding-a-backup-repository-to-veeam-backup-replication-disaster}

請遵循下列步驟來建立新的備份儲存庫，以在 AltaVault 應用裝置上儲存已還原的備份。**附註：**CIFS/SMB 共用是一種範例，因為 Veeam Backup & Replication 備份伺服器必須位於執行 Windows 的機器上。用於還原 {{site.data.keyword.cloud_notm}} AltaVault 應用裝置中備份的共用通訊協定必須與用於內部部署 AltaVault 應用裝置中相同備份的共用通訊協定相同，而且會使用 CIFS/SMB 共用。

1. 在左下角，按一下**備份基礎架構 > 備份儲存庫**。
2. 從上方的功能區功能表中，按一下**新增儲存庫**。
3. 提供儲存庫的唯一**名稱**，並提供適當的**說明**。按**下一步**。
4. 選取共用類型，其對應至內部部署 AltaVault 應用裝置中所使用的共用類型。範例：如果內部部署 AltaVault 應用裝置中所使用的共用類型為 CIFS/SMB，則請選取**共用資料夾**。
5. 指定您要在 AltaVault 應用裝置上還原 CIFS/SMB 共用備份的位置。該共用與在內部部署 AltaVault 應用裝置上處理備份的共用具有相同的名稱。例如，如果名稱為 `cifs_test1` 的共用用於內部部署 AltaVault 應用裝置中的備份，則請指定 {{site.data.keyword.cloud_notm}} AltaVault 應用裝置上的 `cifs_test1` 共用位置。
  * 開啟 Web 瀏覽器，然後輸入 AltaVault 應用裝置的 IP 位址，以判定位置。
  * 移至**儲存空間 > CIFS**，並記下共用的**共用路徑**。**附註：**這與共用的本端路徑不同。共用路徑格式為 `\\<AltaVault appliance hostname>\<share name>`。將所需「共用路徑」中的 AltaVault 應用裝置主機名稱取代為 AltaVault 應用裝置的 e0b 網路介面（共用的裝載點）的 IP 位址。<br/>
   * 按一下 AltaVault 應用裝置管理視窗中的**設定 > 資料介面**，以尋找 **e0b** 介面的 IP 位址。如需配置 IFS/SMB 裝載的相關資訊，請參閱「在 AltaVault 中配置 CIFS/SMB 裝載點」。
   * Veeam Backup & Replication 中指定的共用路徑會是 `\\10.120.108.133\cifs_test1`，而非 `\\restore-appliance\cifs_test1`。
6. 回到 Veeam Backup & Replication，並在**共用資料夾**欄位中輸入裝載點的共用路徑，然後按**下一步**。Veeam Backup & Replication 會嘗試建立與裝載點的連線。如果連線失敗，則請返回並先驗證 AltaVault 應用裝置的網路設定正確無誤，再重試。
7. 把**將並行作業數上限限制為**的值輸入為**新建備份儲存庫**畫面上的可用資源數目。此值是備份 Proxy 可傳送至所選共用的作業數目上限。並行作業的預設數目是 4。AltaVault 建議您從 5 個並行作業開始，並根據資源容許的值來增加或減少此值。
8. 按**下一步**。
9. 在 **vPower NFS** 畫面上指定選用 vPower NFS 設定。如果**啟用 vPower NFS 伺服器**勾選框保留為未選取，則 Veeam Backup & Replication 會使用 vPower 進行回復及回復驗證。按**下一步**。
10. 除非您使用手動選取 VM 備份，否則請選取**自動匯入現有備份**。
11. 驗證所有設定都正確，然後按**下一步**。
* 按一下**完成**以結束精靈。
12. 按一下**是**以繼續進行。

已配置 Veeam Backup & Replication，您可以開始還原備份。

### 還原環境
{: #restoring-the-environment}

請遵循下列步驟，以完整還原 VM 還原。

1. 在畫面左下方，按一下**備份 & 抄寫**。
2. 從上方的功能區功能表中，按一下**還原**。
3. 選取**整個 VM（包括登錄）**。**附註：**請不要在**從抄本還原**下選取任何項目。按**下一步**。
4. 按一下**新增 VM > 從備份**。您可以透過內部部署 AltaVault 應用裝置及所選取的 CIFS/SMB 共用，來查看先前抄寫至 {{site.data.keyword.cos_full_notm}} 的 VM。按一下**新增 > 下一步**。
5. 選取**還原至新位置，或使用不同的設定**，以將 VM 還原到新位置 <!--[because the original location of the VM(s) failed]-->。按**下一步**。
6. 從**主機**畫面中，選取您要還原的每個 VM 的目標主機。選取 VM，然後按一下**主機**。
7. 從**選取主機**中選取目標主機，然後按一下**確定**。<br/>Veeam Backup & Replication 會嘗試聯絡目標主機，確保它們處於線上狀態並備妥可接收資料。如果此嘗試失敗，請驗證所有網路設定皆正確，再重新嘗試。檢查完成之後，請按**下一步**。
8. 為每個 VM 選取選用**資源儲存區**。按**下一步**。
9. 為您要還原的每個 VM 選取目標資料儲存庫及磁碟類型。按**下一步**。<br/>Veeam Backup & Replication 會嘗試驗證目標資料儲存庫。如果驗證失敗，請檢查資料儲存庫是否有足夠的容量可供正在還原的 VM 使用，以及先驗證所有網路設定，再嘗試重新連接。
10. 針對您要還原的每個 VM，選取目標資料儲存庫上的目的地資料夾。按**下一步**。
11. 指定每個 VM 的網路連線及設定。按**下一步**。
12. 輸入選用的**還原原因**，並提供您執行還原作業的原因。按**下一步**。<br/>Veeam Backup & Replication 會嘗試驗證您正在還原的 VM。請等待此驗證順利完成，再繼續進行。
13. 驗證**備份 & 抄寫**視窗中的還原作業和設定，然後按一下**完成**。Veeam Backup & Replication 會自動開始還原選取的 VM。

## 後續步驟
{: #next-steps-recovering-your-vmware-vsphere-environment}

<!--The recent rise in Object Storage can be heavily attributed to enterprises' desire to reduce or even eliminate the operational and capital expenditures associated with comparatively expensive on-premises storage. While Object Storage is a highly cost-effective solution, it introduces a new problem of how to access stored files. In the case of stored data backups, the issue is how to use them to recover an environment that has failed for whatever reason.-->

<!--This problem is handily resolved by Veeam Backup & Replication, along with a NetApp AltaVault Cloud Gateway appliance, and {{site.data.keyword.cos_full_notm}}. When used together, they create a seamless backup and recovery experience that can be tailored to fit a wide variety of environmental requirements and constraints. By following the steps outlined in this procedure, enterprises can quickly realize the benefits of cloud-based storage, while reducing the complexities involved in shifting from on-premises storage to Object Storage using the three aforementioned technologies.-->

如需此混合式解決方案元件的相關資訊，請參閱：

* [NetApp AltaVault 網站 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://www.netapp.com/us/products/protection-software/altavault/){: new_window}
* [Veeam Backup & Replication 網站 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}
* [IBM Cloud Object Storage 網站 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://www.ibm.com/cloud/storage){: new_window}

*公用程式伺服器* 是指一部伺服器，其中包含用來存取專用環境的 VMware vSphere Client，以及部署用於資料回復作業的 AltaVault 應用裝置。
