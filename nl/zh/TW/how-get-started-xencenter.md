---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-24"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 開始使用 XenCenter
{: #getting-started-with-xencenter}

## 開始之前
{: #before-you-begin-xencenter}

開始之前，請檢閱下列必要條件。

- 以「VLAN 上的次要」遞送之可攜式 IP 位址（公用及專用）的新範圍。用來設定新 VM 的這個範例解決方案需要您在「專用網路」上具有可用的 IP。您可以從 [{{site.data.keyword.slportal_full}} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://control.softlayer.com/network/subnets/order) 訂購子網路。
- 能夠透過 VPN 連接至「{{site.data.keyword.cloud}} 專用網路」。如需 VPN 存取的相關資訊，請參閱[啟用對 {{site.data.keyword.cloud_notm}} 基礎架構專用網路的存取](/docs/customer-portal?topic=customer-portal-getting-started#enable-private-network)。
- Citrix XenCenter 已安裝至本端系統。<!-- . http://downloads.service.softlayer.com/citrix/xen/-->

**附註：**您的伺服器已預先配置各種範本，來協助您利用 XenServer 快速開始及執行。

## 使用 XenCenter 建立虛擬機器
{: #creating-a-virtual-machine-with-xencenter}

請使用下列步驟，協助您使用 XenCenter 建立虛擬機器。

1. 確定您透過 VPN 連接至「{{site.data.keyword.cloud_notm}} 專用網路」。透過 SSL VPN 或 PPTP 建立連線。
2. 開啟 XenCenter，然後按一下**新增您的 XenServer**。
3. 輸入您的伺服器主機名稱、使用者名稱及密碼。您需要使用伺服器的「專用」IP 位址（類似於 10.x.x.x）、使用者名稱 `root`，然後使用伺服器的 root 密碼。可在 {{site.data.keyword.slportal}} 取得此資訊，方法為移至[裝置 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://control.softlayer.com/devices){: new_window}，然後按一下 XenServer 的名稱。按一下**連接**。
4. 如果您收到「免費授權」啟動畫面，請立即完成此資訊。
5. 用滑鼠右鍵按一下 {{site.data.keyword.cloud_notm}} 名稱，然後選取**新建 VM...**。<!--You can now create your first Virtual Machine. Create a CentOS virtual machine with a disk of 10 GB and have both Public and Private Networks functioning-->
6. 選取要用於新虛擬機器的作業系統，然後按**下一步**。**附註：**部分範本需要您提供自己的媒體。<!--Because you are using CentOS, you can use {{site.data.keyword.BluSoftlayer_notm}} private mirrors for CentOS to get our installation going.Select a version of CentOS and then click **Next**.-->
7. 輸入新虛擬機器的適當名稱和說明。
8. 輸入來賓作業系統安裝媒體的位置，然後按**下一步**。<!-- In the example, {{site.data.keyword.BluSoftlayer_notm}} a CentOS mirror is used as installation media. Provide the Install URL of: http://mirrors.service.softlayer.com/centos/5/os/x86_64 and click **Next**.
  *A trailing ‘/’ at the end of the URL can sometimes break the installation.*
  *This mirror is available only on the {{site.data.keyword.BluSoftlayer_notm}} Private Network. The full mirror's contents are  available here: http://mirrors.service.softlayer.com/.-->
9. 選取 CPU 數目及記憶體配置數量。（您正在建置簡化的系統，而且不需要太多 RAM。因此，512 MB 綽綽有餘。）。按**下一步**。
10. 選取您的虛擬磁碟，以將磁碟空間配置給您的 VM。<!--Remember that this is like adding hard disks, it is not like partitioning your system. Partitioning is done during the installation of the OS.-->此範本的預設大小為 8 GB。此數量對於您正在建置的伺服器綽綽有餘。*選用項目：您可以強調顯示磁碟，並按一下**編輯**，讓磁碟變得更大以符合您的需求。*按**下一步**。
11. 新增或移除虛擬機器的虛擬網路介面。除非您不想要系統能夠在「專用網路」上進行通訊，否則接受預設值。此範例需要您將這兩個介面留在系統中，但經驗豐富的「Xen 管理者」可以基於其自己的用途移除其中一個介面。在此情況下，您可以強調顯示並刪除介面（如果喜歡的話）。按**下一步**。
12. 配置完成。將「自動啟動 VM」保持選取狀態，然後按一下**完成**。安裝即會開始。您會回到主畫面。您可以看到左邊列出新 VM。
13. 選取新的 VM，然後按一下**主控台**標籤。<!--You can now see that your system is booted into the CentOS installer awaiting your input.-->
14. <!--All of the parameters of a CentOS installation are outside of the scope of this article and will need to be customized by your System Administrator, but this article will provide some specific pieces of information that you need to complete the installation. Select your language to get started. The CentOS installer will then ask you for assistance in configuring the Networking Devices in the system.-->選取 **eth0 - xen 虛擬乙太網路**然後按一下**確定**。
  <!--![14](images/14.png)-->
15. <!--In the pre-requisite notes, we made sure that we already had a set of Portable IP Addresses routed as "Secondary on VLAN" ready for this installation.-->確定您已備妥配置 TCP/IP 的資訊。您需要手動配置支援 IPV4 的介面，並停用 IPV6 支援。
  <!--[15](images/15.png)-->
16. 確認您知道如何使用「專用 IP 子網路」(10.17.37.240/29)，然後按一下**確定**<!-- to go to the CentOS installer-->。您和系統管理者可以根據您自己的準則來安裝來賓作業系統。如需靜態及可攜式 IP 區塊的相關資訊，請參閱[開始使用子網路及 IP](/docs/infrastructure/subnets?topic=subnets-getting-started-with-subnets-and-ips)。

您已建立新的 VM。
