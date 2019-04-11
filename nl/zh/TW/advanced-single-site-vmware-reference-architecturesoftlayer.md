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

# 進階單一站台 VMware 參照架構
{: #advanced-single-site-vmware-reference-architecture}

請使用下列步驟，來建立進階單一站台 vSphere 環境。下列步驟使用 VMware 建議的最佳作法，來定義 {{site.data.keyword.cloud}} 參照架構部署。

參考架構旨在於佈建一個環境，以使用共用儲存空間、「VMware 高可用性 (HA)」及「vSphere 分散式資源排程器 (DRS)」，以及 {{site.data.keyword.cloud_notm}} 閘道或防火牆。進階單一站台 VMware 參照架構適用於大部分可隨著工作負載規定而調整的正式作業部署，而且這些部署可以取代內部部署實作，或延伸至混合式 IT 情境。

## 環境概觀
{: #environment-overview}

概述的高階代表 VMware 環境由一個資料中心組成，該資料中心管理兩個不同的叢集：管理及容量。您可以使用取決於需求的單一四節點叢集來設定配置。管理叢集包含下列用於管理基礎架構的虛擬機器 (VM)：

* VMware vCenter Server 5.5 或 6.0 應用裝置
* Microsoft Windows 2012 R2 Standard（含 Active Directory 及「網域名稱系統 (DNS)」角色）

容量叢集包含建立及執行 VM 所需的資源和基礎架構。對於網路，環境包含三個專用內部 VLAN，以及用於外部通訊的單一公用 VLAN。表 1 指定在整個環境中使用的 VLAN 類型及 VLAN 名稱。

|VLAN 類型|VLAN 名稱|說明|                                                                             |
|---|---|---|
|主要專用| 管理| 指派此類型用於管理及存取實體 ESXi 主機和虛擬伺服器。|
|主要專用| 儲存空間| 指派此類型用於管理及存取連接至每一個 ESXi 主機的共用儲存空間。|
|主要專用| VM 存取 | 指派給在每一個 ESXi 主機上執行的虛擬機器。|
|主要公用| 公用 | 指派給虛擬機器或其他需要從公用網路存取的裝置。|
<caption>表 1. 主要 VLAN</caption>

若為共用儲存空間，您可以使用 OS Nexus QuantaStor、單一承租戶共用的儲存空間伺服器，或 {{site.data.keyword.cloud_notm}}「耐久性」或「效能」儲存服務。在任一情況下，都會使用共用儲存裝置，將 VM 同時儲存在管理及容量叢集上。如需儲存空間選項的相關資訊，請參閱 [{{site.data.keyword.cloud_notm}} 儲存空間解決方案 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://www.ibm.com/cloud/storage){: new_window}。

儲存空間環境會配置為支援 NFS 磁區。

## 步驟 1 訂購主要公用及專用 VLAN
{: #step-1-ordering-primary-public-and-private-vlans}

**附註：**VLAN 訂單可能需要檢閱並核准。沒有任何適當的特定條件或需求，標示自動核准 VLAN 訂單。可以由於各種原因而拒絕 VLAN 訂單。

此步驟中會建立四個 VLAN：一個用於管理、一個用於儲存、一個用於 VM，以及一個用於公用存取。建議您在訂購伺服器之前，先建立這四個 VLAN。訂購 VLAN 首先確定伺服器置於正確的 VLAN 及正確的資料中心。

環境包含五個 ESXi 主機（兩個用於管理叢集，三個用於容量叢集）及一部虛擬伺服器。專用管理 VLAN 包含一個子網路，其中具有 16 個 IP 位址以支援它。用於儲存空間及 VM 資料流量的主要專用 VLAN 由八個位址組成，因為環境包含單一儲存空間伺服器，而且 VM 使用可攜式子網路。如果管理網路需要更大的子網路範圍，請計算 ESXi 主機數目並乘以 2。此外，請確定您指定為其指定這些 VLAN 的資料中心。

登入 {{site.data.keyword.cloud_notm}} 之後，請選取**支援 > 新增問題單**，來開啟管理及虛擬機器 VLAN 的支援問題單。請使用**表 2：**中的資訊填寫欄位。

|欄位|值|
|---|---|
|主旨|專用網路問題|
|標題|訂購 VLAN|
|詳細資料|佈建三個主要專用 VLAN 及一個主要公用 VLAN。為每一個 VLAN 關聯下列定址方法：<br/><ul><li>為主要專用 VLAN 關聯 1x18（16 個位址）</li><li>為主要專用 VLAN 關聯 2x29（八個位址）</li><li>關聯 1x29（八個位址）主要公用 VLAN</li></ul>|
|為何需要這些額外的 VLAN？|為了在 VMWare 環境的不同網路上放置主機、儲存空間及 VM。
|我需要公用及（或）專有 VLAN 嗎？|專用及公用|
|我需要多少個 VLAN？|專用 = 3、公用 = 1|
|我需要多少個 IP 位址？|每個 VLAN 需有 8 - 16 個 IP 位址|
|VLAN 需要在哪個路由器後面？|無所謂，VLAN 全都在同一個 Pod 中。|
|VLAN 需要在哪個 Pod 中？|它們全都位於 <DATA CENTER NAME> 的相同 Pod 中。|
<caption>表 2. 支援問題單資訊</caption>

在佈建 VLAN 之後，請記下 VLAN 號碼、子網路範圍及閘道，並將它們指派給邏輯 vSphere 網路。您可以使用「附錄 A：VLAN 工作表」中的工作表，來記錄 VLAN 及相關聯的資訊。例如：

|VLAN 類型|VLAN 號碼|IP 範圍|閘道|用途|
|---|---|---|---|---|
|主要專用|1101|10.X.Y.Z/28|10.X.Y.1|管理|
|主要專用|1102|10.A.B.C/29|10.A.B.1|儲存空間|
|主要專用|1103|10.Q.R.S/29|10.Q.R.1|虛擬機器|
|主要公用|2101|75.S.T.U/29|75.S.T.1|公用存取|
<caption>表 3. 主要 VLAN 範例</caption>

**附註：**請不要繼續下一步，直到佈建了 VLAN 為止。

## 步驟 2 訂購可攜式專用 IP 位址
{: #step-2-ordering-portable-private-ip-addresses}

在步驟 2 中，要求在容量叢集中為管理 VM、VM 核心儲存空間存取及 VM 建立可攜式專用子網路。您需要決定每一個 VLAN 子網路所需要的位址數目。在代表環境中，訂購下列位址：

* 管理 VLAN - 1x8 位址 /29 - 一個位址用於 vCenter 應用裝置；一個位址用於 DNS 及 Active Directory。
* 儲存空間 VLAN - 1x16 位址 /28 - 在相同 VLAN 上為儲存空間建立兩個子網路，並在每一個 ESXi 主機上建立兩個 VM 核心埠，方法為使用不同子網路來存取共用儲存裝置。
* VM VLAN - 1x32 位址 /27 - 這些位址用來將 IP 位址指派給容量叢集中的 VM。

當您訂購一個數量時，請確定考量到在接下來的 30 天和 6 個月內需要多少 IP 位址。也請務必注意，{{site.data.keyword.cloud_notm}} 會為每一個可攜式子網路區塊保留三到四個 IP 位址。因此，訂購四個 IP 位址會獲得一個 IP 位址，如果 Pod 支援「快速待命路由器通訊協定，Hot Standby Router Protocol」的話則是零個。

針對您要建立的每一個子網路，請使用下列步驟，為每一個 VLAN 訂購可攜式 IP 位址的區塊：

1. 開啟瀏覽器視窗，並登入 {{site.data.keyword.cloud_notm}}。
2. 選取**帳戶 > 下訂單**。
3. 在蹦現視窗中，移至**網路 > 子網路/IP > 訂購**。
4. 從下拉功能表中，選取**可攜式專用**。
5. 選取 **XX 個可攜式專用 IP 位址**，然後按一下**繼續**。**附註：**_XX_ 指定 IP 位址的數目。
6. 選取要與 IP 位址區塊相關聯的 VLAN，然後按一下**繼續**。
7. 填妥畫面上的資訊，然後按一下**繼續**。

建立 IP 位址相當快，並可從**網路 > IP 管理**中選取**子網路**來顯示。您可以在「附錄 A：VLAN 工作表」中找到的工作表中記錄這些 IP 位址。

## 步驟 3 訂購虛擬伺服器
{: #step-3-ordering-a-virtual-server}

Windows 2012 R2 Standard 虛擬伺服器會佈建為公用程式伺服器以供 ISO 使用，並在此步驟中提供對環境的存取權。

1. 開啟瀏覽器視窗，並登入 {{site.data.keyword.cloud_notm}}。
2. 選取**帳戶 > 下訂單**。
3. 移至**虛擬伺服器 > 每小時或每月**。
4. 選取適當的資料中心（已在其中建立 VLAN）來佈建虛擬伺服器，並為每一個選項指定下列規格：
  * 運算實例 - 1x2.0 GHz 核心
  * RAM：4 GB
  * 作業系統：Windows Server 2012 R2 Standard Edition（64 位元）
  * 第一顆磁碟：100 GB (SAN)
  * 上行鏈路埠速度 - 1 Gbps 公用及專有網路上行鏈路
5. 按一下**繼續訂購**，並在**訂單摘要與帳單**畫面上選取後端和前端 VLAN。**附註：**選取 VLAN 很重要，如此公用程式才能放置在資料中心內的正確 Pod 中。在範例環境中，後端 VLAN 是管理 VLAN (1101)，前端 VLAN 則是公用 VLAN (2101)
6. 輸入伺服器的主機名稱及網域名稱，然後按**下訂單**。

## 步驟 4 訂購 ESXi 主機及閘道/防火牆
{: #step-4-ordering-esxi-hosts-and-gateway-firewall}

佈建虛擬伺服器時，您需要訂購 ESXi 主機和 Brocade vRouter (Vyatta) 閘道，以及防火牆應用裝置。請同時訂購所有這些伺服器，以便同時將它們放置在相同的 Pod 中。在不同時間訂購硬體可能會導致主機及防火牆位於 {{site.data.keyword.cloud_notm}} 資料中心內的不同 Pod 中。

### ESXi 主機
{: #esxi-hosts}

對於針對環境訂購的每一個 ESXi 主機，VMware ESXi 5.5 是作業系統。如果您想要使用 {{site.data.keyword.cloud_notm}} vSphere 授權，則根據使用情形產生每月費用。

另一個選項是使用您自己的 ISO 來安裝 ESXi。此處理程序的指示可在[透過遠端主控台及虛擬媒體安裝 VMware vSphere ESXi](/docs/infrastructure/vmware?topic=VMware-installing-vmware-vsphere-esxi-via-remote-console-and-virtual-media) 中找到。如果您想要使用自己的 ISO 來安裝 ESXi，請確定在訂購過程中選取**無作業系統**，作為管理及容量主機的作業系統。

**附註：**這個實作需要 Enterprise Plus 授權，才能使用「vSphere 分散式虛擬交換器」。如果您的授權對 Enterprise Plus 無效，則您需要使用 {{site.data.keyword.cloud_notm}} 提供的 VMware Service Provider Program (VSPP) 授權。

### 訂購管理主機
{: #ordering-management-hosts}

請使用下列步驟來訂購管理主機伺服器。

1. 開啟瀏覽器視窗，並登入 {{site.data.keyword.cloud_notm}}。
2. 選取**帳戶 > 下訂單**。
3. 選取**裸機伺服器 > 每月**。
4. 在伺服器清單畫面上，選擇符合管理叢集需求的適當伺服器。**附註：**ESXi 5.5 至少需有單一雙核心處理器、4 GB RAM 及 1 GB 乙太網路控制器。
5. 選取適當的資料中心（已在其中建立 VLAN）來佈建 ESXi 伺服器，並為每一個選項指定下列規格：
  * 數量：2
  * RAM：32 GB
  * 作業系統：VMware ESXi 5.5（如果您是使用[透過遠端主控台及虛擬媒體安裝 VMware vSphere ESXi](/docs/infrastructure/vmware?topic=VMware-installing-vmware-vsphere-esxi-via-remote-console-and-virtual-media)，則為「無作業系統」）
  * 硬碟：磁碟 1 及 2：RAID 1 中有 500 GB SATA
  * 公用頻寬：僅限專用網路 -> 0 GB 頻寬
  * 上行鏈路埠速度：10 Gbps 備援專用網路上行鏈路
6. 按一下**繼續訂購**。
7. 按一下**新增伺服器**，開始將容量叢集的 ESXi 主機新增至訂單。

### 訂購容量主機
{: #ordering-capacity-hosts}

1. 在伺服器清單畫面上，選擇符合容量叢集主機需求的適當伺服器。**附註：**ESXi 5.5 至少需有單一雙核心處理器、4 GB RAM 及一個 1 GB 乙太網路控制器。
2. 選取適當的資料中心（已在其中建立 VLAN）來佈建 ESXi 伺服器，並為每一個選項指定下列規格：
  * 數量：3
  * RAM：128 GB
  * 作業系統：VMware ESXi 5.5（如果您是使用[透過遠端主控台及虛擬媒體安裝 VMware vSphere ESXi](/docs/infrastructure/vmware?topic=VMware-installing-vmware-vsphere-esxi-via-remote-console-and-virtual-media)，則為「無作業系統」）
  * 硬碟：磁碟 1 及 2：RAID 1 中有 500 GB SATA
  * 公用頻寬：僅限專用網路 > 0 GB 頻寬
  * 上行鏈路埠速度：10 Gbps 備援專用網路上行鏈路
3. 按一下**繼續訂購**。

### 完成配置
{: #completing-configuration}

現在，您的購物車中有 ESXi 主機。若要正確佈建裝置，您需要將公用（如果適用的話）VLAN、專用 VLAN、主機名稱及網域指派給裝置。

1. 指派下列 VLAN 並建立裝置的主機名稱：

* ESXi 主機 - 後端 VLAN：(1101)
* 後端 VLAN：(1101)
* 前端 VLAN：(2101)

2. 選取您的付款方法、同意條款，然後按一下**完成您的訂單**。**重要事項：**請不要繼續執行步驟 5，直到佈建伺服器，且可以透過前一個步驟中訂購的 VPN 或虛擬伺服器存取這些伺服器為止。

## 步驟 5 透過幹線連接 BCS 交換器上的 VLAN
{: #step-5-trunking-vlans-on-bcs-switches}

依預設，埠會放置在後端客戶交換器上（亦即，Pod 中的專用網路交換器，例如存取模式下的「後端客戶交換器 (BCS)」）。因此，您需要透過幹線連接已連接至 ESXi 主機的埠，讓主機可以存取儲存空間，並讓 VM 可在專用網路上進行通訊。

在您開啟問題單，透過幹線連接 VLAN 之前，您需要判定專用網路上有哪些網路介面。若要判定介面，請移至每一部 ESXi 伺服器的**裝置詳細資料**，並移至**網路 > 專用**。對於此環境，`eth0` 和 `eth2` 是專用網路配接卡。

除了透過幹線連接 ESXi 主機的 VLAN 之外，您也須解除結合管理及容量主機的 NIC。您解除結合 NIC 的原因是「鏈結聚集控制通訊協定 (LACP)」與軟體 iSCSI 多重路徑不相容。

若要透過幹線連接 VLAN，並解除結合 NIC，您需要遵循下列步驟來開啟問題單：

1. 開啟瀏覽器視窗，並登入 {{site.data.keyword.cloud_notm}}。
2. 選取**支援、新增問題單**。
3. 輸入下列資訊：
  * 主旨：專用網路問題
  * 標題：透過幹線連接 VLAN 並解除結合 NIC
  * 詳細資料：針對下列主機 [列出每一個 ESXi 主機]，透過幹線連接 eth0 及 eth2 NIC 配對上的 VLAN（`<Management VLAN>`、`<Storage VLAN>` 及 `<VM VLAN>`）。此外，解除結合（移除 LACP）下列伺服器上的專用 NIC（eth0 及 eth2）：[列出每一個 ESXi 主機]
4. 按一下**新增問題單**。

確定將 <> 中指定的 VLAN 變更為您的實際 VLAN

## 步驟 6 配置管理主機網路
{: #step-6-configuring-management-host-networking}

在佈建伺服器且透過幹線連接 VLAN 之後，您需要在管理叢集的主機上設定網路。如需此配置，您可對管理叢集使用 vSphere 標準交換器。除了 vMotion 及容錯埠群組之外，您可以使用可攜式 IP 位址來配置 VM 核心埠群組。**附註：**您可以使用自己的 IP 方法進行 vMotion 及容錯，因為不需要遞送資料流量。不過，所有主機必須位於與叢集中其他主機相同的子網路上，才能使用 vMotion 及容錯功能。如果需要遞送子網路，建議您使用 {{site.data.keyword.cloud_notm}} 可攜式 IP 位址。

若要配置埠群組，必須在公用程式虛擬伺服器或透過 {{site.data.keyword.cloud_notm}} 管理 VPN 存取主機的工作站上安裝 vSphere 用戶端。

1. 開啟瀏覽器視窗，並登入 {{site.data.keyword.cloud_notm}}。
2. 在連接至公用程式伺服器或 {{site.data.keyword.cloud_notm}} VPN 之後，請啟動 vSphere 用戶端。
3. 輸入其中一個管理 ESXi 主機的 IP 位址、使用者名稱和密碼。（您可以選取**裝置詳細資料 > 密碼**，來尋找 ESXi 主機的 root 密碼。
4. 移至**配置 > 網路**，然後在 vSphere 標準交換器（最可能為 vSwitch0）上建立或修改下列埠群組。

請對管理叢集中的每一個主機完成下列步驟。

### vSwitch0 內容
{: #vswitch0-properties}

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 4. vSwitch0 內容</caption>
<tbody>
<tr><th>網路配接卡</th><th>vmnicX 及 vmnicY</th></tr>
<tr><td>負載平衡</td><td>根據原始虛擬埠 ID 的路由器</td></tr>
<tr><td>作用中的配接卡</td><td>vmnicX 及 vmnicY</td></tr>
</tbody>
</table>

### 編輯現有的虛擬機器埠群組
{: #edit-existing-virtual-machine-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 5. vmPG-Management 埠群組</caption>
<tbody>
<tr><th>網路標籤</th><th>*vmPG-Management*</th></tr>
</tbody>
</table>

### 編輯現有的 VM 核心埠群組
{: #edit-existing-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 6. vmkPG-Management 埠群組</caption>
<tbody>
<tr><th>網路標籤</th><th>*vmkPG-Management*</th></tr>
</tbody>
</table>

### 新增 vMotion VM 核心埠群組
{: #add-vmotion-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 7. vMotion 埠群組</caption>
<tbody>
<tr><th>連線類型</th><th>VMKernel</th></tr>
<tr><td>網路標籤</td><td>*vmkPG-vMotion*</td></tr>
<tr><td>VLAN ID</td><td>*&lt;儲存空間 VLAN&gt; (1102)*</td></tr>
<tr><td>vMotion 資料流量</td><td>已啟用</td></tr>
<tr><td>IP 位址</td><td>*172.16.10.X/24*<br/><br/>*使用者定義的位址，可以是不同的子網路。請確定每一個主機上的其他 vMotion 位址都位於相同的子網路上。*</td></tr>
<tr><td>子網路遮罩</td><td>255.255.255.0</td></tr>
</tbody>
</table>

### 新增容錯 VM 核心埠群組
{: #add-fault-tolerance-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 8. FT 埠群組</caption>
<tbody>
<tr><th>連線類型</th><th>VM 核心</th></tr>
<tr><td>網路標籤</td><td>*vmkPG-FT*</td></tr>
<tr><td>VLAN ID</td><td>*&lt;儲存空間 VLAN&gt; (1102)*</td></tr>
<tr><td>容錯記載</td><td>已啟用</td></tr>
<tr><td>IP 位址</td><td>*172.16.20.X/24*<br/><br/>*使用者定義的位址，可以是不同的子網路（必要的話）。請確定每一個主機上的其他 FT 位址都位於相同的子網路上。*</td></tr>
<tr><td>網路遮罩</td><td>255.255.255.0</td></tr>
</tbody>
</table>

### 新增儲存空間 VM 核心埠群組
{: #add-storage-vm-kernel-port-group}

將每一個可攜式 IP 位址的**附註**區段更新為已指派的主機名稱及 VM 核心埠。若要找出**附註**區段，可以移至 {{site.data.keyword.slportal_full}}，然後選取**網路 > IP 管理 > 子網路 > [子網路]**。

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 9. 儲存空間埠群組</caption>
<tbody>
<tr><th>連線類型</th><th>VM 核心</th></tr>
<tr><td>網路標籤</td><td>*vmkPG-Storage*</td></tr>
<tr><td>VLAN ID</td><td>*&lt;儲存空間 VLAN&gt; (1102)*</td></tr>
<tr><td>IP 位址</td><td>*可攜式專用位址*<br/><br/>*從可攜式專用位址中選取的 IP 位址，而這些可攜式專用位址會連結至儲存空間 VLAN。*</td></tr>
<tr><td>網路遮罩</td><td>*與 IP 範圍相關聯的子網路遮罩*</td></tr>
</tbody>
</table>

### 新增公用位址埠群組
{: #add-public-address-port-group}

將每一個可攜式 IP 位址的**附註**區段更新為已指派的主機名稱及 VM 核心埠。**附註**區段位於 {{site.data.keyword.slportal}} 中，且可以透過選取**網路 > IP 管理 > 子網路 > [子網路]** 來找到此區段。

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 10. 公用埠群組</caption>
<tbody>
<tr><th>連線類型</th><th>虛擬機器</th></tr>
<tr><td>網路標籤</td><td>vmPG-Public</td></tr>
<tr><td>VLAN ID</td><td>&lt;主要公用 VLAN&gt;（例如 2101）</td></tr>
</tbody>
</table>

## 步驟 7 下載或上傳 ISO 映像檔及 vCenter 虛擬應用裝置
{: #step-7-download-or-upload-iso-images-and-vcenter-virtual-appliance}

現在，環境已準備好部署「VMware vCenter 虛擬應用裝置」，並安裝 DNS、Active Directory 或 BIND 的虛擬機器。不過，在部署之前，您需要下載映像檔。若要下載映像檔，透過遠端桌面移至先前佈建的虛擬伺服器，然後在虛擬伺服器上針對您的環境下載適當的映像檔：

* Active Directory / Windows DNS：Windows Server 2008R2/Windows Server 2012 ISO 映像檔（您的授權媒體）
* Linux BIND：[CentOS 安裝映像檔 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](http://isoredirect.centos.org/centos/6/isos/x86_64/){: new_window}
* [vCenter 虛擬應用裝置 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://my.vmware.com/web/vmware/info/slug/datacenter_cloud_infrastructure/vmware_vsphere/5_5){: new_window}（需要有效的 VMware 訂閱）

在下載 ISO 之後，您需要將映像檔上傳至管理主機資料儲存庫，以便它可以連接至虛擬機器。請使用 vSphere 用戶端來連接至管理主機，並在本端資料儲存庫上建立 ISO 目錄。

## 步驟 8 部署 DNS
{: #step-8-deploying-dns}

您需要部署 VM（在管理主機上）並安裝 DNS 服務。請使用傳統 vSphere 用戶端，在 Windows 或 Linux ISO 所在的「管理 ESXi」主機上建立 VM。連接適當的 ISO（Windows 或 CentOS），以在 VM 上部署 DNS 伺服器。請確定將 VM 與在前一個步驟中建立的 VM 埠群組 (vmpg)-management 埠群組建立關聯。

在安裝 VM 之後，請從可攜式專用網路群組中指派一個 IP 位址及預設閘道。如果您是使用「附錄 A」中的 VLAN 工作表，則為「管理 VM」子網路。將每一個可攜式 IP 位址的**附註**區段更新為已指派的 VM 名稱。若要找出**附註**區段，可以移至 {{site.data.keyword.slportal}}，然後選取**網路 > IP 管理 > 子網路 > [子網路]**。

雖然詳述啟用 DNS 所需步驟超出本文件的範圍，但會提供下列指引：

1. 將 **DNS 轉遞**設為 service.softlayer.com 本端 DNS 主機：
  * `rs1.service.softlayer.com 10.0.80.11`
  * `rs2.service.softlayer.com 10.0.80.12`
2. 在設定 DNS 之後，請為佈建的所有可攜式及主要子網路建立一個本端 DNS 區域 (dal06.mycompany.local) 及一個反向查閱區域。
3. 為每一個主機的「管理」IP 位址（vmkPG-management 上的 vmk0）新增 A HOST 記錄。
4. 從連結至管理 VLAN 的可攜式專用網路中，為「vCenter 虛擬應用裝置」新增 A HOST 記錄。
5. 更新「可攜式 IP 子網路」的**附註**區段，而您已將這個子網路指派給 vCenter。

如需相關資訊，請參閱下列鏈結：
* [Windows DNS 及 Active Directory ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://social.technet.microsoft.com/wiki/contents/articles/12370.step-by-step-guide-for-setting-up-a-windows-server-2012-domain-controller.aspx){: new_window}。
<!--* [CentOS BIND ![External link icon](../../icons/launch-glyph.svg "External link icon")](http://www.centos.org/docs/2/rhl-rg-en-7.2/s1-bind-configuration.html){: new_window}.-->

## 步驟 9 部署及配置 vCenter 虛擬應用裝置
{: #step-9-deploying-and-configuring-vcenter-virtual-appliance}

既然已配置 DNS，您就可以部署及配置「vCenter 伺服器應用裝置」。若要部署應用裝置，請遵循下列步驟：

1. 透過遠端桌面移至虛擬伺服器，然後開啟「vSphere 用戶端」。
2. 連接至管理主機，並選取**檔案、部署 OVF 範本**。
3. 遵循精靈來完成部署。

<!--For more information about OVF Template deployment, see [VMware documentation ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://pubs.vmware.com/vsphere-55/index.jsp?topic=%2Fcom.vmware.vsphere.hostclient.doc%2FGUID-6C847F77-8CB2-4187-BD7F-E7D3D5BD897B.html&resultof=%22thick%22%20%22thin%22%20){: new_window}.-->

因為在開啟電源時，可以使用「動態主機配置通訊協定 (DHCP)」伺服器，將 IP 位址指派給 vCenter 虛擬應用裝置，所以您必須使用根主控台來配置應用裝置。**附註：**`NO NETWORKING DETECTED` 訊息會顯示在「vCenter 虛擬應用裝置主控台」上。

請遵循下列步驟來配置應用裝置：

1. 以**使用者名稱** 'root' 及**密碼** 'vmware' 登入主控台。
2. 執行 `/opt/vmware/share/vami/vami_config_net`，並遵循文字精靈來配置 IP、子網路及 DNS 內容。請記住，使用在**步驟 8：部署 DNS** 中建立的 DNS 或 BIND 伺服器 IP 位址。
3. 儲存網路設定、結束主控台，並在虛擬伺服器上開啟瀏覽器。
4. 移至您提供給 vCenter 虛擬應用裝置 (VCVA) 並附加埠 5480<!-- (https://:5480)--> 的 IP 位址。
5. 接受精靈中的 EULA、回答**客戶改善體驗方案**問題，然後選取**配置選項、設定自訂配置**。
6. 按**下一步**，然後輸入下列值：
<table border="1" cellpadding="0" cellspacing="0"><caption>表 11. VCVA 設定精靈</caption><tbody><tr><th>精靈功能表</th><th>選項</th><th>值</th></tr><tr><td>資料庫設定</td><td>資料庫類型</td><td>內嵌</td></tr><tr><td>SSO 設定</td><td>SSO 部署類型</td><td>內嵌</td></tr><tr><td>SSO 設定</td><td>新建管理者密碼*（適用於 administrator@vsphere.local）*</td><td>&lt;輸入密碼&gt;</td></tr><tr><td>SSO 設定</td><td>重新輸入新密碼</td><td>&lt;輸入您先前使用的同一個密碼&gt;</td></tr><tr><td>時間同步化</td><td>NTP 同步化</td><td>servertime.service.softlayer.com</td></tr></tbody></table>
7. 按一下**啟動**。即會配置 VCVA。
8. 使用**管理**下的選項來變更 root 密碼。
9. 登出 VCVA 配置網頁。
10. 移至「vSphere Web 用戶端」，方法為輸入附加 `9443/vsphere-client`<!-- (https://:9443/vsphere-client)--> 的 VCVA IP 位址。
11. 登入 root，並選取**管理、授權**。
12. 輸入您的 VMware vCenter 授權。

## 步驟 10 建立 vCenter 叢集及分散式虛擬交換器
{ #step-10-create-vcenter-clusters-and-distributed-virtual-switch}

既然已配置且授權 VCVA，您就可以為容量叢集建立資料中心及叢集的建構及分散式虛擬交換器。

### 建立資料中心及叢集
{: #creating-data-center-and-clusters}

1. 在 vSphere 用戶端上，移至**首頁**畫面。
2. 選取**開始使用**，然後按一下**按一下這裡**。
3. 按一下**建立資料中心**。
4. 輸入資料中心名稱（範例環境為 Toronto 01 資料中心。請使用 `Toronto 01` 作為資料中心名稱）。
5. 在建立資料中心之後，請按一下**開始使用**頁面上的**建立叢集**。
6. 將第一個叢集命名為 `Management`。清除所有其他選項，然後按一下**確定**。
7. 完成時，請使用與管理叢集相同的處理程序來建立另一個叢集。

現在您可以使用**新增主機**，將管理及容量主機新增至「管理」及「容量」叢集。請務必使用伺服器的完整網域名稱 (FQDN)，而不是 IP 位址，以便在新增每一個主機時使用 DNS。

在將 ESXi 主機新增至 vCenter 之後，您可能會注意到一些警告訊息，警告在每一個 ESXi 主機上啟用了 Shell 及 SSH。若要抑制這些警告，請在蹦現視窗上按一下**抑制警告 > 是**。如果未呈現「抑制警告」鏈結，請遵循下列步驟：

1. 移至 ESXi 主機的**管理**標籤。
2. 選取**設定**，然後按一下**進階系統設定**。
3. 找出 **UserVars.SuressShellWarning** 機碼，並將值變更為 **1**。
4. 按一下**確定**。

在將管理及容量主機新增至其各自的叢集之後，請移至每一個主機，並設定 DNS 及 NTP。若要設定 DNS，請遵循下列步驟：

1. 按一下主機，然後選取**管理 > 網路**。
2. 選取預設系統堆疊（**TCP/IP 配置**），然後按一下鉛筆圖示。
3. 輸入您先前建立的 DNS 伺服器 IP 位址，以及主機與網域名稱。

若為 NTP 設定：

1. 移至**管理、設定、時間配置**.
* 輸入 `servertime.service.softlayer.com` 作為 NTP 伺服器。
* 將 **NTP 服務啟動原則**設為`使用主機啟動和停止`。

***建立容量主機的分散式虛擬交換器***

1. 使用「vSphere Web 用戶端」來移至**網路**，並用滑鼠右鍵按一下資料中心名稱。
2. 選取**新建分散式交換器**。
3. 命名分散式交換器，然後按**下一步**。
4. 選取適當的分散式交換器版本，然後按**下一步**。
5. 在**編輯設定**畫面上，輸入 `2` 作為上行鏈路的數目，並清除**建立預設埠群組**選項。
6. 按一下**下一步 > 完成**，並建立分散式虛擬交換器。

***建立分散式虛擬交換器的埠群組***

既然分散式虛擬交換器存在，您就必須針對 vMotion、容錯、VM 及儲存空間建立埠群組。

***建立 dvpg-Private-VM Management 埠群組***

1. 使用 vSphere Web 用戶端移至「網路」區段。
2. 用滑鼠右鍵按一下分散式虛擬交換器 (DVS)。
3. 按一下**新建分散式埠群組...**，然後針對第一個埠群組輸入表 14 中的資訊。
4. 對表格中未指定的任何選項保留預設值。

| 新建分散式埠群組功能表 | 欄位 | 值 |
| --- | --- | --- |
| 名稱及位置 | 名稱 | dvpg-Private-VM Management |
| 配置設定 | 進階 | 勾選「自訂預設原則配置」|
| 配置原則（團隊及失效接手）| 負載平衡| 根據實體 NIC 負載遞送 |
| 配置原則（團隊及失效接手）| 失效接手順序| 作用中的上行鏈路：上行鏈路 1 及上行鏈路 2 |
<caption>表 12. DVS VM 管理埠群組</caption>

在建立第一個埠群組之後，請使用下列配置選項來建立剩餘的埠群組（表 15 至表 19）。

***建立 dvpg-Private-vMotion 埠群組***

|新建分散式埠群組功能表|欄位|值|
|---|---|---|
|名稱及位置|名稱|dvpg-Private-vMotion|
|配置設定|VLAN 類型|VLAN|
|配置設定|VLAN ID|&lt;儲存空間 VLAN&gt;|
|配置設定|進階|勾選「自訂預設原則配置」|
|配置原則（團隊及失效接手）|負載平衡|根據實體 NIC 負載遞送|
|配置原則（團隊及失效接手）|失效接手順序|作用中的上行鏈路：上行鏈路 1 及上行鏈路 2 |
<caption>表 13. DVS vMotion 埠群組</caption>

***建立 dvpg-Private-Fault Tolerance 埠群組***

|新建分散式埠群組功能表|欄位|值|
|---|---|---|
|名稱及位置|名稱|dvpg-Private-Fault Tolerance|
|配置設定|VLAN 類型|VLAN|
|配置設定|VLAN ID|&lt;儲存空間 VLAN&gt;|
|配置設定|進階|勾選「自訂預設原則配置」|
|配置原則（團隊及失效接手）|負載平衡|根據實體 NIC 負載遞送|
|配置原則（團隊及失效接手）|失效接手順序|作用中的上行鏈路：上行鏈路 1 及上行鏈路 2 |
<caption>表 14. DVS FT 埠群組</caption>

***建立 dvpg-Private-VM Access 埠群組***

|新建分散式埠群組功能表|欄位|值|
|---|---|---|
|名稱及位置|名稱|dvpg-Private-VM Access|
|配置設定|VLAN 類型|VLAN|
|配置設定|VLAN ID|&lt;虛擬機器 VLAN&gt;|
|配置設定|進階|勾選「自訂預設原則配置」|
|配置原則（團隊及失效接手）|負載平衡|根據實體 NIC 負載遞送|
|配置原則（團隊及失效接手）|失效接手順序|作用中的上行鏈路：上行鏈路 1 及上行鏈路 2 |
<caption>表 15. DVS VM 存取埠群組</caption>

***建立 dvpg-Private-Storage***

|新建分散式埠群組功能表|欄位|值|
|---|---|---|
|名稱及位置|名稱|dvpg-Private-Storage Path A|
|配置設定|VLAN 類型|VLAN|
|配置設定|VLAN ID|&lt;儲存空間 VLAN&gt;|
|配置設定|進階|勾選「自訂預設原則配置」|
|配置原則（團隊及失效接手）|負載平衡|根據實體 NIC 負載遞送|
|配置原則（團隊及失效接手）|失效接手順序|作用中的上行鏈路：上行鏈路 1 及上行鏈路 2 |
<caption>表 16. DVS 儲存空間埠群組</caption>

***建立 dvpg-Primary-Public***

|新建分散式埠群組功能表|欄位|值|
|名稱及位置|名稱|dvpg-Private-Storage|
|配置設定|VLAN 類型|VLAN
|配置設定|VLAN ID|&lt;主要公用 VLAN&gt;|
|配置設定|進階|勾選「自訂預設原則配置」|
|配置原則（團隊及失效接手）|負載平衡|根據實體 NIC 負載遞送|
|配置原則（團隊及失效接手）|失效接手順序|作用中的上行鏈結：上行鏈路 1 及上行鏈路 2|
<caption>表 17. DVS 儲存空間路徑 B 埠群組</caption>

## 步驟 11 將容量叢集中的 ESXi 主機移轉至分散式虛擬交換器
{: #step-11-migrating-esxi-hosts-in-capacity-cluster-to-distributed-virtual-switch}

既然容量主機新增至容量叢集，您就可以將虛擬標準交換器配置移轉至您在*步驟 10：建立 vCenter 叢集及分散式虛擬交換器*中建立的分散式虛擬交換器。完成某個主機的移轉後，接著您可以套用主機設定檔，來配置叢集的其餘主機。

在開始新增 VMkernel 配接卡之前，請將 vmnic 指派給分散式虛擬交換器上的上行鏈路。

1. 按一下 **vCenter 庫存清單、分散式交換器**。
2. 選取適用於容量主機的分散式交換器。
3. 按一下**開始使用**頁面上的**新增及管理主機**。
4. 使用下列設定來新增上行鏈路，並移轉與主機管理相關聯的現有 VMkernel。
  - <table border="1" cellpadding="0" cellspacing="0"><caption>表 18. DVS 新增主機</caption><tbody><tr><th>功能表</th><th>欄位</th><th>值</th></tr><tr><td>選取作業</td><td>選取作業</td><td>新增主機</td></tr><tr><td>選取主機</td><td>按一下**新建主機**</td><td>按一下容量主機</td></tr><tr><td>選取網路配接卡作業</td><td>選取網路配接卡作業</td><td>選取「管理實體配接卡」及「管理 VMkernel 配接卡」</td></tr></tbody></table>
5. 選取其中一個專用 vmnic，然後按一下**管理實體網路配接卡 > 指派上行鏈路**。
6. 選取蹦現畫面上的 `uplink1`，然後按一下**確定**。
7. 針對其他專用 vmnic 重複這些步驟，並將它指派給 `uplink2`。
8. 按**下一步**、強調顯示 vmk0 VMkernel 配接卡，然後按一下**指派埠群組**。
9. 選取蹦現視窗上的 **dvpg-Private-VM Management**，然後按一下**確定**。
10. 按兩次**下一步**，然後按一下**完成**，以完成移轉至分散式虛擬交換器。**附註：**您可能會短暫失去與主機的網路連線功能。連線會快速重新建立。

在將 vmk0 配接卡移轉至分散式虛擬交換器之後，您可以將 VM 核心新增至 DVS 中的每一個埠群組。

11. 在 vCenter 內的主機上，按一下**管理 > 網路**。
12. 移至 **VM 核心配接卡 > 新增主機網路**，並新增表 19 及 21 中的 VM 核心配接卡。若要新增這些配接卡，請移轉至「VM 核心配接卡」功能表，此功能表位於 vCenter 內主機上的**管理 > 網路**標籤中。然後，按一下「新增主機網路」圖示，並新增下列「VM 核心」配接卡。

***新增 vMotion 的 vmk1 ***

|功能表|欄位|值|
|---|---|---|
|選取連線類型|選取連線類型|VMKernel 網路配接卡|
|選取目標裝置|選取現有的分散式埠群組|dvpg-Private-vMotion|
|選取網路配接卡作業|選取網路配接卡作業|選取**管理實體配接卡**及**管理 VM 核心配接卡**|
|埠內容|啟用服務|檢查 vMotion 資料流量|
|IPv4 設定|IPv4 位址|*172.16.10.X/24*<br/><br/>*這是使用者定義的位址，且可以是不同的子網路（必要的話）。請確定每一個主機上的其他 vMotion 位址都位於相同的子網路上。*|
|IPv4 設定|子網路遮罩|255.255.255.0|
<caption>表 19. 主機網路 vMotion</caption>

***新增容錯的 vmk2***

|功能表|欄位|值|
|---|---|---|
|選取連線類型|選取連線類型|VMKernel 網路配接卡|
|選取目標裝置|選取現有的分散式埠群組|dvpg-Private-Fault Tolerance|
|選取網路配接卡作業|選取網路配接卡作業|選取**管理實體配接卡**及**管理 VMKernel 配接卡**|
|埠內容|啟用服務|檢查容錯記載|
|IPv4 設定|IPv4 位址|*172.16.20.X/24*<br/><br/>*這是使用者定義的位址，且可以是不同的子網路（必要的話）。請確定每一個主機上的其他 FT 位址都位於相同的子網路上。*|
|IPv4 設定|子網路遮罩|255.255.255.0|
<caption>表 20. 主機網路容錯</caption>

***新增儲存空間的 vmk3***

|功能表|欄位|值|
|---|---|---|
|選取連線類型|選取連線類型|VMkernel 網路配接卡|
|選取目標裝置|選取現有的分散式埠群組|dvpg-Private-Storage|
|選取網路配接卡作業|選取網路配接卡作業|選取「管理實體配接卡」及「管理 VMkernel 配接卡」|
|IPv4 設定|IPv4 位址|*可攜式專用位址*<br/><br/>*此 IP 位址是從可攜式專用位址中選取的，而這些可攜式專用位址會連結至儲存空間 VLAN。此位址需要位在與「儲存空間路徑 B」不同的子網路上。*|
|IPv4 設定|子網路遮罩|與 IP 範圍相關聯的子網路遮罩|
<caption>表 21. 主機網路儲存空間</caption>

***建立主機設定檔***

若要建立主機設定檔，您必須從先前配置的單一容量主機中擷取主機設定檔。

1. 移至「vSphere Web 用戶端」首頁畫面，然後按一下**主機設定檔**圖示。
2. 按一下綠色加號 (+)、**從主機擷取設定檔**，然後在蹦現視窗上選取先前配置的容量主機。
3. 按**下一步**。
4. 為主機設定檔（Capacity01 主機設定檔）提供適當的名稱、輸入說明，然後按**下一步**。
5. 檢閱設定，然後按一下**完成**。

在建立主機設定檔之後，您需要對其進行修改，以便在將設定檔套用至容量叢集中的其餘主機時，不會提示輸入 MAC 位址。

1. 用滑鼠右鍵按一下您已建立的主機設定檔，然後選取**編輯設定 > 編輯主機設定檔、主機虛擬 NIC**。
2. 在右窗格中，將**判斷如何決定 vmknic 的 MAC 位址**變更為**使用者必須明確選擇原則選項**。
3. 按**下一步**，然後按一下**完成**。

***將主機設定檔連接至容量叢集***

既然您已建立主機設定檔，就需要將主機設定檔連接至叢集。連接叢集旨在讓它們可以套用至容量主機。

1. 移至「vSphere Web 用戶端」中的**主機及叢集**視圖。
2. 進入叢集中每一個主機的維護模式。**附註：**設定檔只能套用至維護模式下的主機。
3. 用滑鼠右鍵按一下容量叢集，並從蹦現功能表中選取**連接主機設定檔**。
4. 選取您已建立的主機設定檔，然後按**下一步**。
5. 在**自訂主機**畫面上輸入適當的資訊，然後按一下**完成**。
6. 從維護模式（非維護模式）中移除主機。

## 步驟 12 訂購、配置及連接共用儲存空間
{: #step-12-order-configure-and-attach-shared-storage}

在繼續之前，請務必訂購、配置及連接共用儲存空間，以與環境內的管理及容量叢集及主機搭配使用。如果您想要使用 {{site.data.keyword.cloud_notm}}多租戶的共用儲存空間解決方案（檔案儲存空間），請參閱 [Architecture Guide for IBM File Storage for {{site.data.keyword.cloud_notm}} with VMware](/docs/infrastructure/FileStorage?topic=FileStorage-architectureguide)。

## 步驟 13 啟用 HA/DRS 及 svMotion vCVA
{: #step-13-enabling-ha-drs-and-svmotion-vcva}

***在管理及容量叢集上啟用 HA/DRS***

既然已設定共用儲存空間，您就需要啟用 HA 及 DRS，以將額外的保護及負載平衡功能提供給管理叢集上的 VM。

1. 移至 vSphere Web 用戶端。
* 選取「管理」、「管理叢集的設定」。
* 選取 vSphere DRS，然後按一下「編輯」。請確定已選取下列設定：**開啟 vSphere DRS**、自動化層次 - **完全自動化**、移轉臨界值調節器設定 - 中途、虛擬機器自動化 - **啟用個別虛擬機器自動化層次。**、電源管理 - **關閉**。  
* 在「vSphere HA 設定」畫面上，確定已選取下列設定：**開啟 vSphere HA**、**主機監視**、VM 重新啟動優先順序 - **中**、主機隔離回應 - **保持電源開啟**。  
***儲存空間 vMotion 及 vCenter 虛擬應用裝置***

既然已在管理叢集上設定儲存空間，並已啟用 HA 及 DRS，您就需要將「vCenter 虛擬應用裝置」設為共用儲存空間。

1. 用滑鼠右鍵按一下適當的應用裝置，然後從蹦現功能表中選取**移轉**。
2. 選取**變更資料儲存庫**，然後按**下一步**。
3. 選取您先前裝載至管理叢集的 iSCSI 磁區，然後按**下一步**。
4. 檢閱選項，然後按**下一步**。

即會完成進階單一站台 VMware 環境。

## 摘要
{: #summary}

您現在具有正在 IBM Cloud 資料中心執行的 VMware 環境。您的 VMware 環境可以執行正式作業工作負載，並補充內部部署 IBM Cloud 部署。此環境會實施 VMware 最佳作法，並啟用 VMware DRS、HA、Storage DRS 及網路備援等功能。您可以使用更大的容量或管理主機，以及更多的儲存空間，來擴充此參照架構實作。

如需 VMware 的相關資訊，請參閱[部署 VMware](/docs/infrastructure/vmware?topic=VMware-using-cookbooks-for-vmware-deployments) 及 [VMware 常見問題](/docs/infrastructure/vmware?topic=VMware-faqs-vmware)

## 附錄 A：VLAN 工作表
{: #appendix-a-vlan-worksheet}

|VLAN 類型|VLAN 號碼|IP 範圍|閘道|用途|
|---|---|---|---|---|
|主要專用||||管理|
|主要專用||||儲存空間|
|主要專用||||虛擬機器|
|主要公用||||公用存取|
|可攜式專用||||管理 VM|
|可攜式專用||||儲存空間|
|可攜式專用||||虛擬機器|
|vMotion|||N/A||
|容錯|||N/A|||
<caption>VLAN 工作表</caption>
