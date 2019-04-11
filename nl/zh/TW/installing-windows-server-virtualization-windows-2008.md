---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-23"

subcollection: virtualization

keywords: Windows 2008
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 在 Windows 2008 上安裝 Windows Server 虛擬化
{: #installing-windows-server-virtualization-on-windows-2008}

<!--Windows 2008 64-bit edition comes with the option to install Windows next generation virtualization application codenamed Veridian. By default the application will not be a selectable option when Adding and Removing Roles from Windows 2008.

Note: At this time, this is only available in full installation of 64-bit editions of Windows 2008. 32-bit versions and Server-core installations do not support this.-->

## 開始之前
{: #before-you-begin-win-2008}

開始之前，請檢閱下列必要條件。

### 硬體
{: #hardware}

* 已在 BIOS 中啟用硬體輔助虛擬化技術
* Intel VT
* AMD-V
* 已在 BIOS 中啟用資料執行防止
* Intel Execute Disable (XD)
* AMD No Execute (NX)

### 軟體
{: #software}

若要對 Windows Server 虛擬化啟用**角色**選項，必須安裝一些修補程式。
1. 開啟瀏覽器視窗，並瀏覽至 %sysdir%\Windows\wsv，通常為 C:\Windows\wsv。下列兩個檔案將位於該資料夾中：
    * Windows6.0-KB939853-x64.msu
    * Windows6.0-KB939854-x64.msu
2. 可依任何順序安裝這些修補程式。請安裝這兩個修補程式，然後重新啟動系統。

## 安裝
{: #installation}

1. 在系統重新啟動之後，您需要將**角色**新增至系統。
2. 在新增角色之後，會出現**建立虛擬網路**對話框。
**附註：**會短暫失去網路連線功能。
3. 選取**本端區域連線**、您的專用網路配接卡。按一下**繼續**。安裝會繼續，且需要您重新啟動。
4. 在系統重新啟動之後，請透過「公用」連線登入系統。**附註：**您必須使用您用來安裝此連線的相同登入認證來登入。您將失去與介面的網路連線功能。如果未失去，您可能會收到下列錯誤：
    * *嘗試配置「Windows Server 虛擬化」失敗，錯誤碼為 0x80078000。*
若要解決此錯誤，請移至管理主控台中的**開始 > 程式集 > 系統管理工具 > Windows 虛擬化管理**，以進行 Windows 虛擬化。
5. 在右窗格中，按一下適當的伺服器。
6. 然後，在動作窗格中，按一下**虛擬網路管理員**。您現在可以看到**虛擬網路交換器管理**。
7. 在左窗格中，按一下**新增**網路交換器下的網路交換器。
8. 將網路交換器重新命名為專用。
9. 選取「實體」網路配接卡以進行**連線**，然後選取第一個網路配接卡。現在會從專用網路介面取消連結所有網路通訊協定。
10. **重要事項** 若要重新建立與專用網路介面的網路連線功能，您需要配置新的交換器裝置，**而非**專用介面。移至**開始 > 設定 > 網路連線**。您會看到一個稱為**本端區域連線 2** 的新裝置。
11. 用滑鼠右鍵按一下此連線，然後移至**內容**。
12. 選取 **ipv4** 及其內容。您需要使用專用網路介面 IP 位址、網路遮罩及 DNS 伺服器來配置此裝置。
13. 按一下**確定 > 關閉**。此配置會在專用端重新啟用網路。您可以連線測試專用 IP，以驗證網路是否為專用網路。

RDP 至專用 IP 以設定公用網路。

### 新增公用交換器
{: #adding-a-public-switch}

新增公用交換器會遵循與新增專用交換器相同的處理程序。
1. 回到**虛擬網路交換器管理**，然後選取**新增網路交換器**。
2. 選取**外部**作為網路交換器類型，然後按一下**新增**。
3. 將交換器重新命名為**公用**，並選取**實體網路配接卡**。
4. 選取第二個網路配接卡，然後按一下**確定**。此配置會導致公用埠不回應網路。請配置新的公用交換器介面，就像您使用適當設定來配置專用交換器介面一樣。
