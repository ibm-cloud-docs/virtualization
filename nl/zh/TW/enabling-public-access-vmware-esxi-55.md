---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization

keywords: ESXi, VMware
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# 啟用對 VMware ESXi 的公用存取
{: #enabling-public-access-to-vmware-esxi}

依預設，「ESXi 主機」會隨著服務主控台僅安裝在專用網路上。因此，會阻止公用資料流量進出「ESXi 主機」上的公用網路。

若要開始，您需要在伺服器的專用介面上使用「VMware vSphere 用戶端」來連接至伺服器。

您需要具有伺服器的「主要公用 IP」資訊，才能完成設定。可在 [{{site.data.keyword.slportal_full}} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://control.softlayer.com/){: new_window} 中找到伺服器「公用 IP」的相關資訊。
{:tip}

登入「ESXi 主機」之後，請遵循下列步驟來啟用「公用介面」：

1. 您可能需要使用「專用 IP 」來更正遞送，以透過 SSH 登入伺服器。
* 執行 `esxcfg-route -l` 來判定現行路徑
* 執行 `esxcfg-route -a 10.0.0.0/8 [您的伺服器專用閘道 IP]`
* 執行 `esxcfg-route [您的伺服器公用閘道]` 來確定公用閘道是預設閘道

登入 vSphere 並遵循下列步驟：

1. 按一下「配置」標籤，然後按一下**網路**。
2. 對於 vSwitch1，按一下**內容 > 新增**
3. 選擇 **VM 核心**，然後按**下一步**
* 設定可以保留原狀。為了便於識別，您可以將網路重新標示為 "VMKernelPublic"。
4. 輸入伺服器「公用 IP」資訊。
5. 對於「VM 核心」預設閘道，按一下**編輯**、輸入公用閘道 IP，然後按一下**確定**。
* 按一下**確定**。**附註：**斷線是正常的，且是預期的情況。*

您現在可以同時使用「公用」及「專用」 IP 位址來存取伺服器。

啟用對 ESXi 的「公用 IP」存取包含固有的安全風險。請確定您採取適當的步驟，限制只有需要 ESXi 的使用者才能存取它。
{:tip}
