---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-13"

subcollection: virtualization

keywords: Citrix XenServer 
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 啟動 XenServer
{: #activating-xenserver}

Citrix XenServer 免費授權可讓您從起始安裝或重新載入 Citrix XenServer 起，免費使用 30 天。在這段時間，您需要先向 Citrix 登錄，並要求完整功能的 1 年授權。若要建立此授權，Citrix 需要 {{site.data.keyword.BluSoftlayer}} 無權存取的個人資訊。此授權是您為了使用 XenServer 而與 Citrix 訂定的協議。
{:shortdesc}

如果未在 30 天內登錄您的系統，則您無法開啟任何虛擬機器的電源。
{:tip}

若要登錄您的系統，請完成下列步驟：

1. 下載並安裝 XenCenter 用戶端。您可以從 [Citrix XenCenter ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://community.citrix.com/display/xs/XenCenter){: new_window} 頁面存取用戶端。

2. 透過 VPN 連接至專用網路。

3. 啟動 XenCenter，然後按一下**新增伺服器**，或從下拉功能表中按一下**伺服器 > 新增...**。

4. 使用專用 IP 位址作為**主機名稱**、使用 'root' 作為**使用者名稱**，以及使用您的 root 密碼作為**密碼**。

5. 在系統連接之後，會出現一個對話框。此對話框會警示您授權在 30 天後到期。如果您沒有看到對話框，請移至**工具 > 授權管理程式...**。

6. 檢查您的伺服器，然後按一下**啟動...**。這時會開啟瀏覽器，並移至 Citrix 登錄網站。請填妥表單，然後提交。檢查您的收件匣，以取得來自 Citrix 的電子郵件。此電子郵件附有您的授權碼作為附件。

7. 收到電子郵件之後，請在本端儲存附加的授權碼檔案。

8. 在 XenCenter 左窗格中，強調顯示您的伺服器。在功能表列中，移至**伺服器 > 安裝授權碼...**。這時會出現一個對話框，要求授權碼的位置。將它指向步驟 7 中儲存的位置。
