---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-05"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 更新 Veeam Backup and Replication 應用程式
{: #updating-veeam-backup-and-replication-application}

## 概觀
{: #overview}

當產品有新的更新項目可用時，請使用下列步驟來更新伺服器上執行的 Veeam Backup and Replication 應用程式。

### 必要條件
{: #prerequisites-updating-veeam-backup-and-replication-application}

* 免費 Veeam 帳戶。您可以在 [Veeam 登錄 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://www.veeam.com/signin.html) 中註冊。
* Internet Explorer、Chrome、Firefox 或 Safari。

### 檢查是否有更新項目
{: #checking-for-updates}

Veeam Backup and Replication 應用程式包括更新項目檢查程式。若要確定您要收到關於更新項目的通知，請移至左上方功能表圖示，然後按一下**一般選項** > **通知**。確定已選取**定期檢查是否有產品及 Hypervisor 更新項目**。

### 更新 Veeam
{: #updating-veeam}

若要開始更新，請遵循下列步驟。
1. 開啟**備份基礎架構**視圖。
2. 在庫存窗格中，移至**受管理伺服器節點** > **遺漏更新項目**。
3. 按一下更新鏈結。

開始更新之前，需要注意幾件事：

* 確定目前已完成所有現有工作的執行。如果部分工作失敗，請重新執行失敗的工作。
* 確定沒有任何執行中的工作，包括還原階段作業、「即時 VM 回復」階段作業及 SureBackup 工作。建議您不要停止執行中的工作。
* 暫時停用任何定期及備份副本工作，以防止它們在升級期間啟動。

### 後續步驟
{: #next-steps-updating-veeam-backup-and-replication-application}

現在，您可以擷取已下載的更新項目，然後按兩下 Veeam 更新安裝程式。更新項目即會同時推送至本端 Veeam 伺服器及 Veeam 代理程式所在的 Hypervisor。在處理程序完成之後，啟動 Veeam Backup and Replication，然後重新啟用備份工作。
