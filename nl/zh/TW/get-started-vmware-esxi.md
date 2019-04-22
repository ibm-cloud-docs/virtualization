---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-14"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 開始使用 VMware ESXi
{: #getting-started-with-vmware-esxi}

在佈建 ESX 伺服器之後，存取伺服器的第一個步驟就是連接至「{{site.data.keyword.cloud}} 專用網路 VPN」。依預設，所有 {{site.data.keyword.cloud_notm}} 部署的 VMware 伺服器只有使用 IP 位址配置的專用網路介面或結合。此部署是一種額外的安全措施，可用來限制將新的 ESX 伺服器暴露於公用網際網路中。由於新增了這種安全保護，伺服器只會在 {{site.data.keyword.cloud_notm}} 專用 10.x.x.x IP 位址上進行接聽，這只能透過 {{site.data.keyword.cloud_notm}} VPN 或另一個現有 {{site.data.keyword.cloud_notm}} 主機存取，而這些主機可以存取新 VMware 伺服器所在的相同專用 VLAN。

如需 {{site.data.keyword.cloud_notm}} VPN 的相關資訊，請參閱[開始使用虛擬專用網路 (VPN)](/docs/infrastructure/iaas-vpn?topic=VPN-gettingstarted-with-virtual-private-networking#gettingstarted-with-virtual-private-networking)。

## 存取 ESX 主機
{: #accessing-your-esx-host}

在連接至 {{site.data.keyword.cloud_notm}} VPN 之後，您可以與新的 VMware 伺服器進行通訊。ESX 是由 vSphere 用戶端管理。您可以從新 VMware 伺服器中擷取，方法為在 Web 瀏覽器中輸入專用網路 IP 位址，然後在產生的頁面上按一下「下載 vSphere 用戶端」鏈結。

在下載 vSphere 用戶端，並將它安裝在本端工作站上之後，您可以啟動應用程式，並輸入伺服器專用位址及登入認證。在按一下「密碼」標籤之後，可在 {{site.data.keyword.slportal_full}} 的「裝置」頁面上檢視您的登入認證。

1. 在 vSphere 用戶端的適當欄位中輸入伺服器的專用 IP 位址、root 使用者及密碼，然後按一下**登入**來連接。
2. 既然已連接至伺服器，請選取**庫存**以移至新的 ESX 主機。
3. 您的 ESX 主機會在產生的頁面上顯示為配置的可用節點。從這裡，您有多種方法來部署虛擬機器。其中一種方法是將偏好作業系統的安裝 ISO 上傳至伺服器本端資料儲存庫。在上傳 ISO 之後，您可以在建立 VM 時，選取 ISO 作為安裝媒體。  

## 上傳 ISO
{: #uploading-an-iso}

請完成下列步驟，將 ISO 上傳至伺服器的資料儲存庫。

1. 在左窗格中強調顯示伺服器（以伺服器圖示及其專用 IP 位址來表示），然後在右窗格中選取**配置**標籤。
2. 選取**硬體 > 儲存空間**。請確定**視圖**設為**資料儲存庫**。
3. 用滑鼠右鍵按一下適當的資料儲存庫，然後按一下**瀏覽資料儲存庫**。
4. 產生的頁面具有檔案管理程式，可瀏覽資料儲存庫、上傳檔案至資料儲存庫，或從中下載檔案。  
  * 若要上傳 ISO，請按一下磁區圖示（具有向上箭頭），然後選取**上傳檔案**。
5. 在本端檔案系統上選取您要上傳至資料儲存庫的 ISO 檔，然後按一下**開啟**。
6. 您選擇的 ISO 現在位於資料儲存庫中。
7. 既然您的安裝媒體在 VMWare 伺服器上，您就可以繼續[建立虛擬機器](/docs/infrastructure/vmware?topic=VMware-create-esx-vm#create-esx-vm)。
