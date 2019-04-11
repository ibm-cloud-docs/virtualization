---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 設定 Hypervisor
{: #setting-up-a-hypervisor}

請使用下列步驟來設定 Hypervisor。

1. 使用您的唯一認證登入 [{{site.data.keyword.slportal_full}} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](http://control.softlayer.com/){: new_window}。
2. 從**裝置**功能表中，選取**裝置清單**，並尋找您的 Hypervisor。
3. 透過[安全 VPN ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](http://www.softlayer.com/vpn-access){: new_window} 連接至「專用網路」，以存取您的 Hypervisor。

**附註：**{{site.data.keyword.cloud}}Hypervisor 提供者包括 XenServer、VMware 和 Hyper-V。每一個提供者都有以不同方式存取的專用管理主控台。如需在管理主控台中存取及運作的相關資訊，請參閱下列鏈結：

   * [XenServer ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://support.citrix.com/en/products/xenserver){: new_window}
   * [VMware ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://www.vmware.com/support/vsphere-hypervisor.html){: new_window}
   * [Hyper-V ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](http://technet.microsoft.com/en-us/windowsserver/dd448604){: new_window}

4. 取得虛擬機器的可攜式 IP。
    * VM 需要可攜式 IP 位址。公用及專用可攜式 IP 的區塊可以透過 {{site.data.keyword.slportal}} 訂購。
    * 如需配置 IP 位址的相關資訊，請參閱[開始使用子網路及 IP](/docs/infrastructure/subnets?topic=subnets-getting-started-with-subnets-and-ips)。

5. 為 VM 建立「專用網路」上的遞送。VM 需要下列規格，才能透過「專用網路」遞送至其他 VM：
    * 可攜式專用 IP
    * 與 10.0.0.0/8 網路範圍相關的靜態路徑

如需 VM 遞送處理程序的相關資訊，請參閱[設定虛擬機器網路](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-a-virtual-machine-network)。在設定 VM 網路之後，位於相同 VLAN 上的機器可以進行通訊。如果位於不同 VLAN 的機器必須進行通訊，請[啟用 VLAN Spanning](/docs/infrastructure/vlans?topic=vlans-vlan-spanning)。

## 存取及安全地儲存 ISO
{: #access-and-securely-store-isos}

{{site.data.keyword.cloud_notm}} 網路上的 VM 可以執行預先配置或自訂的 ISO。{{site.data.keyword.cloud_notm}} 網路上的 VM 可以存取內部鏡映網站，而此網台專供 {{site.data.keyword.cloud_notm}} 使用者使用，並提供最常用之作業系統的熱門配置。如果您需要特定作業系統的特殊版本或配置，請參閱作業系統供應商的網站。

如果您在 VM 上執行自訂 ISO，請將 ISO 上傳至安全位置，以便在裝置故障時可以擷取它。許多使用者選擇使用 SSH 或 RDP，將自訂 ISO 儲存在裝置的本端系統上。或者，透過具有各種功能的儲存服務提供空間。
