---
copyright:
  years: 2014, 2018
lastupdated: "2018-06-22"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# 移轉 Hyper-V 虛擬機器
{: #migrating-a-hyper-v-virtual-machine}

## 開始之前
{: #before-you-begin-migrating-a-hyper-v-virtual-machine}

開始之前，請檢閱下列必要條件。

* 相容伺服器（Windows 2008 Server DC Edition，含 Hyper-V）。
* 從「來源伺服器」連接至「目的地伺服器」的方法。
* 來源與目的地伺服器必須位於相同的 VLAN 上。如果它們未位於相同的 VLAN 上，則需要重新發出虛擬機器的 IP（需要開立支援問題單）。
* 具有可運作網路上行鏈路的虛擬機器，以及已安裝的可運作/受支援軟體。

## 為何您可能需要重新定位 Hyper-V 虛擬機器
有兩個常見的原因，您可能需要重新定位「Hyper-V 虛擬機器」：
* VM 在未適當運作的硬體上。
* 現行主伺服器的資源不足。
在任一事件中，可以快速完成 Hyper-V 移轉。如果您具有先前提及的需求，請完成下列步驟。

1. 登入來源伺服器，並開啟「Hyper-V 管理員」。選取您要移轉至目的地伺服器的虛擬機器。
2. 關閉您要移轉的虛擬機器。然後，在「伺服器」動作清單下選取**匯出**，接著輸入匯出檔的位置。
3. 現在，使用來源伺服器上的 RDP，您可以登入裝載了 C：磁碟機的目的地伺服器，以帶回檔案。

您正在使用 RDP 透過資源裝載來傳送檔案。您可以選擇對此處理程序而言最輕鬆的傳送方法（Windows 共用、透過 RDP 裝載資源、FTP，以及其他傳送方法）。
{:tip}

4. 將檔案匯出至目的地伺服器時，請確定它位於 Hyper-V 虛擬硬碟 (VHD) 的預設位置中，並匯入它。預設位置為 `C:\Users\Public\Documents\Hyper-V\Virtual hard disks`。如果您已變更此預設位置，且不確定它是什麼，則可以選取 **Hyper-V 設定 > 匯入虛擬機器**來檢視位置。
5. 在找到匯出的檔案，並按一下**匯入**之後，虛擬機器會將先前具有的所有配置和檔案移入至「Hyper-V 管理員」。現在伺服器在線上並運作中。如果您的伺服器不符合需求，且目的地伺服器位於不同的 VLAN，則需要重建虛擬機器與可攜式子網路（以「VLAN 上的次要」遞送）的 IP。
