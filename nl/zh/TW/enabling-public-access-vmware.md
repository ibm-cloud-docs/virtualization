---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-11"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 啟用對 VMware 的公用存取
{: #enabling-public-access-to-vmware}

依預設，「ESX 主機」會隨著服務主控台僅安裝在專用網路上，這表示會在「ESX 主機」上阻止公用資料流量進出公用網路。

若要開始，您需要遵循下列步驟，在伺服器的專用介面上使用「VMware vSphere 用戶端」來連接至伺服器。

1. 按一下 **ESX 主機 > 配置標籤 > 網路 > 新增網路**
2. 選取「服務主控台」。
3. 選取要使用的 vSwitch。在此情況下，它是外部/公用介面，因此請選取 **vSwitch1**（vmnic1 及 vmnic3）。
4. 將「服務主控台」重新命名為可輕鬆辨識的名稱（例如，"Public Service Console"）。
5. 輸入使用正確的子網路遮罩指派給伺服器的主要外部 IP 位址。
6. 按一下**編輯**，然後新增指派給伺服器的預設閘道。
