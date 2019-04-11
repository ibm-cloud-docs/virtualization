---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-16"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 在 XenServer 共用儲存空間中設定及裝載 iSCSI 節點
{: #setting-up-and-mounting-an-iscsi-node-in-xenserver-shared-storage}

「共用儲存空間」是一個儲存空間儲存庫 (SR) ，可以是由 XenServer 使用的任何形式的儲存空間。您可以從下列兩個 SR 種類中選擇：共用和非共用。共用 SR 容許多個節點從相同的儲存區中共用儲存空間位置。{{site.data.keyword.cloud}} 支援 SR 的兩個選項、NFS 及 LVMoiSCSI (LVM over iSCSI)。

您需要安裝 NFS 伺服器，並從另一個客戶系統或使用 {{site.data.keyword.cloud_notm}} QuantaStor 伺服器來管理它。請遵循下列步驟來裝載 XenCenter 中的 NFS 儲存庫：

1. 開啟 XenCenter 主控台。
2. 按一下**新建儲存空間**。
* 在新建對話框中，選取**虛擬磁碟儲存空間 > NFS**。
* 提供新 SR 的適當名稱。
* 在**共用名稱**之下，輸入 server:/path
* 按一下**掃描**

此處理程序會掃描 NFS 共用，以取得任何先前的 SR。

您可以將 {{site.data.keyword.blockstoragefull}} 用於 LVMoiSCSI。iSCSI 可以來自客戶管理儲存空間伺服器或 {{site.data.keyword.blockstoragefull}} 供應項目。若為「效能」及「備援」區塊儲存空間，請移至**儲存空間 > 區塊儲存空間 > 選取 Lun 名稱**，從 {{site.data.keyword.slportal}} 擷取 IQN。若要透過 XenCenter 裝載 iSCSI 節點，請遵循下列步驟：

1. 開啟 XenCenter 主控台。
2. 移至**一般 > 內容**，並設定 IQN。
3. 按一下**新建儲存空間**。
4. 在新建對話框中，移至**虛擬磁碟儲存空間 > 軟體 iSCSI**。
5. 為新 SR 輸入適當的名稱。
6. 輸入「iSCSI 目標」的主機名稱或 IP，並保留預設的 3260 埠。
7. 選取**使用 CHAP**。
8. 輸入 LUN 的使用者名稱和密碼。
9. 按一下**掃描目標主機**。
* 選取**目標 IQN** 選項。
* 選取**目標 Lun** 選項。
10. 在 IQN 和 LUN 欄位移入之後，請按一下**完成**。即會掃描目標，以檢查是否有先前的 SR。如果 SR 存在，則安裝程式會詢問您要建立新的 SR，還是連接至前一個 SR。

如果伺服器同時位於儲存區中，則會自動共用 iSCSI 儲存庫。

如需針對多路徑配置 iSCSI 的相關資訊，請參閱[配置 iSCSI 多路徑 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus1000/sw/5_x/sys_mgmt_config/b_Cisco_N1KV_VMware_Sys_Mgmt_Config_5x/b_Cisco_N1KV_VMware_Sys_Mgmt_Config_5x_chapter_01110.html?dtid=osscdc000283){: new_window}
