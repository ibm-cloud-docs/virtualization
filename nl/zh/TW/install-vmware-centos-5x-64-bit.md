---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 在 CentOS 5.x 上安裝 VMware（64 位元）
{: #installing-vmware-on-centos-5-x-64-bit-}

開始之前，請以 root 使用者身分登入伺服器。

## 準備安裝 VMware
{: #preparing-to-install-vmware}

1. 下載 [VMware 安裝程式 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](http://vmware.com/download/server/){: new_window}。按一下下載鏈結、接受 EULA，然後下載 LinuxTarball（此範例中的 VMware-server-1.0.3-44356.tar.gz）：

* `# wget -O vmware-server.tar.gz http://download3.vmware.com/software/vmserver/VMware-server-1.0.3-44356.tar.gz`

2. 在下載軟體之後，您將需要取得授權碼（在免費版的 VMWare Server 中，這是免費的）。若要登錄，請參閱 [VMware ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](http://register.vmware.com/content/registration.html){: new_window}。

3. 解壓縮 .tar 檔：

* `# tar -xzvf vmware-server.tar.gz`

## 開始安裝之前
{: #before-you-begin-installation}

開始安裝之前，請檢閱下列必要條件。

```
# yum update
# yum install libXtst.i386
# yum install libXrender.i386
# yum install xinetd
```

## 安裝 VMWare Server
{: #installing-vmware-server}

1. 移至目錄：

* `# cd vmware-server-distrib/`

2. 執行 VMware 安裝 Script：

* `# ./vmware-install.pl`

3. 安裝會呈現基本問題，並想要知道要建立哪些目錄，並在其中安裝 VMware 的特定部分。從這裡，接受預設值。接受授權合約以繼續安裝。

4. 可能會向您詢問此問題：「VMware Server 沒有預先建置的 vmmon 模組適合您執行中的核心。您是否想要此程式嘗試為系統建置 vmmon 模組（您需要在系統上安裝 C 編譯器）？」
* 對此問題回答**是**（這是預設值）。

## 設定 VMware 網路
{: #setting-up-vmware-networking}

系統會以下列問題提示您：

1. 「您是否想要透過網路連接虛擬機器？」
* 回答**是**。您需要為公用網路裝置建立網路設定，以便您可以在虛擬機器上存取網際網路。

2. 「您的電腦有多個可用的乙太網路介面：eth0、eth1。您想要哪一個橋接至 vmnet0？」
* 請記住，設定及執行所有 {{site.data.keyword.BluSoftlayer}} 伺服器的方式，公用網路執行於 eth1 上，而專用網路則執行於 eth0 上。在 VMware 中，vmnet0 的預設橋接器裝置為 eth0。請鍵入 eth1，而非按下 Enter 鍵。

## 橋接專用網路
{: #bridging-the-private-network}

系統會提示您下列問題，您可以回答**是**或**否**：
*「您是否想要配置另一個橋接網路？」

如果您計劃在專用網路上執行服務或其他應用程式，請對此問題回答「是」以繼續（除非您知道不是使用專用網路），並對您的專用網路建立網路橋接器。按下 Enter 鍵之後，它會自動使用 eth0 作為介面，因為這是唯一剩下的可用介面（原因是伺服器中只有兩個網路卡）。

## 其他網路設定
{: #other-networking-settings}

有一些其他關於 VMware 伺服器網路設定的問題。請遵循下列建議以繼續：

* *「您是否想要能夠在虛擬機器中使用 NAT 網路？」*

- 回答「是」以繼續

* *「您是否想要探測此程式，以找出未用的專用子網路？」*

- 回答「是」以繼續

- 完成此處理程序之後，請確定您沒有配置另一個 NAT 網路。

* *「您是否想要能夠在虛擬機器中使用僅主機網路？」*

- 回答「是」以繼續

* *「您是否想要探測此程式，以找出未用的專用子網路？」*

- 回答「是」以繼續

- 完成此處理程序之後，請確定您沒有配置另一個僅主機網路。

## 指定接聽埠
{: #specifying-listening-port}

下一個詢問您的問題是，您想要讓 VMware 伺服器接聽哪個埠。您可以將預設埠保留為 904。

## 儲存虛擬機器
{: #storing-the-virtual-machines}

安裝程式詢問的下一個問題是，*「您想要在哪一個目錄中保留虛擬機器檔案？」*。預設位置為 /var/lib/vmware/virtual machines。請確定將虛擬機器放置在有大量磁碟空間的位置，例如備援 RAID 陣列或大型次要硬碟。請務必確定您有足夠的空間可供虛擬機器使用。在此情況下，您可以使用裝載至大型磁碟的裝載點 /data/vm。

## 提供 VMware 的序號
{: #providing-the-serial-number-for-vmware}

安裝的最終部分需要您插入 VMware 授權碼及序號。如果您還沒有授權碼，請參閱 [VMware 網站 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](http://register.vmware.com/content/registration.html){: new_window}。如果您具有此 VMware 伺服器的序號，請將它插入至提示，然後按 Enter 鍵。

現在，您會看到如下聲明：

    "The configuration of VMware Server 1.0.3 build-44356 for Linux for this running kernel completed successfully."

VMware 現在已設定在您的伺服器上。現在，您需要下載「VMware Server 主控台」，這是 VMware Server 的 GUI 用戶端，用來配置並安裝虛擬機器。

## 下載 VMware Server Console
{: #downloading-vmware-server-console}

「VMware Server 主控台」是 VMware Server 的用戶端應用程式。您可以使用這個主控台來管理 VMware Server，在其中您可以建立、配置及安裝虛擬機器。若要安裝此應用程式，請從 [VMware 下載 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](http://vmware.com/download/server/){: new_window} 下載 VMware Server Windows 用戶端套件。此套件是 .zip 檔。下載之後，請解壓縮此套件，並安裝 VMware-console-1.0.3-x 檔。安裝完成時，表示您已安裝「VMware Server 主控台」，且已準備好配置 VMware Server。

## 登入 VMware 主控台
{: #logging-in-to-the-vmware-console}

從安裝「VMware Server 主控台」的電腦中開啟此主控台。載入時，會以「切換主機」（登入）畫面提示您。VMware Server 會使用 Linux 系統使用者名稱和密碼來鑑別使用者，因此您需要使用這些使用者名稱（尤其是 root ）來登入 VMware。請使用下列認證：

* **主機名稱：**IP 位址加上埠（例如 67.228.160.201:904）<br />
* **使用者名稱：**root<br />
* **密碼：**password（使用系統的真正 root 密碼）

## 配置防火牆規則 (IPTable)
{: #configuring-the-firewall-rules-iptables-}

如果您在連接至 VMware Server 時發生問題，但不是鑑別問題（如果您收到使用者名稱及密碼錯誤，表示您的使用者或密碼不正確），則防火牆可能會阻止您連接至 VMware Server。解決方法是嘗試將下列 IPTable 規則新增至 /etc/sysconfig/iptablesfile。**附註：**請確定命名慣例遵循您的伺服器配置：

- `# -A FWALL-INPUT -p tcp -m tcp -s 0/0 --dport 904 -j ACCEPT`
