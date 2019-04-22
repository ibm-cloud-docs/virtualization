---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 設定虛擬機器網路
{: #setting-up-a-virtual-machine-network}

只需幾個步驟，即可在新的虛擬機器上完成網路配置。公用及專用網路都需要個別的可攜式 IP 區塊。假設您正在使用的虛擬化供應項目需要「VLAN 上的次要」播送網域，且未遞送至 VLAN（沒有網路 ID/閘道/播送）子網路。如果您不打算在虛擬機器上使用「專用」網路，則只需要「公用」子網路。您可以直接透過入口網站的[銷售->新增 IP 位址 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://manage.softlayer.com/Sales/orderAdditionalServices/subnet){: new_window}，或從[公用網路 IP 管理程式 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://manage.softlayer.com/PublicNetwork/ipManager){: new_window} 訂購可攜式 IP 區塊。

在取得 IP 區塊之後，您可以在虛擬機器上配置網路。如果未安裝虛擬機器，您可以透過「作業系統」安裝處理程序來配置公用網路區塊。此處理程序是配置公用網路的最快速方法。所有網路配置步驟都假設第一個網路介面是專用網路，第二個網路介面是公用網路。

對於每一個作業系統 ，配置網路的方式有所不同。您可以尋找針對下列「作業系統」配置網路的詳細資訊。**附註：**同組的「作業系統」具有相同的網路配置。

* Windows 2008 Server Core
* Windows 2003 Standard 及 Enterprise
* Windows 2008 Web、Standard、Enterprise 及 Datacenter
* RedHat、Fedora 及 CentOS
* Ubuntu 及 Debian

網路配置需要「公用」及「專用」 IP 區塊的下列資訊。可在入口網站的[公用網路 -> IP 管理程式 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://manage.softlayer.com/PublicNetwork/ipManager){: new_window} 下找到「公用」IP 區塊的資訊，以及在 [專用網路 -> IP 管理程式 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://manage.softlayer.com/PrivateNetwork/ipManager){: new_window} 下找到「專用」IP 區塊的資訊。

    -IP Address
    -Gateway
    -Subnet Mask
    --Subnet conversion
    ---/29 - 255.255.255.248
    ---/28 - 255.255.255.240
    ---/27 - 255.255.255.224
    ---/26 - 255.255.255.192
    ---/25 - 255.255.255.128
    ---/24 - 255.255.255.0

## Windows 2008 Server Core
{: #windows-2008-server-core}

Windows 2008 Server Core 版本未提供圖形介面來配置系統網路。需要使用命令提示字元來手動執行配置。您需要執行下列指令。此範例中使用的 IP 位址需要取代為您 IP 區塊中的 IP 位址。這些指令使用舊版的 Windows Server，其中包括 [netsh ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://support.microsoft.com/kb/242468){: new_window} 指令。

**範例公用 IP 區塊 - 192.0.2.0/29**

* IP 位址 - 192.0.2.2
* 閘道 - 192.0.2.1
* 子網路遮罩 - 255.255.255.248

**範例專用 IP 區塊 - 10.0.0.0/29**

* IP 位址 - 10.0.0.2
* 閘道 - 10.0.0.1
* 子網路遮罩 - 255.255.255.248

**公用網路**

此指令會建立公用網路，以將伺服器連接至網際網路。

* *Netsh interface ip set address name="Local Area Connection 2" static 192.0.2.2 255.255.255.248 192.0.2.1*

這些指令會建立 DNS 項目。如果您不是使用專用網路，則需要將這些 IP 位址取代為工作中 DNS 伺服器

* *Netsh interface ip add dns "Local Area Connection 2" 10.0.80.11*
* *Netsh interface ip add dns "Local Area Connection 2" 10.0.80.12*

**專用網路**

下列兩個指令可配置專用網路，並建立專用網路的持續路徑。持續路徑可讓您的專用網路存取整個專用網路。存取包括公用網路配置中的 DNS 伺服器。**附註：**不會將任何閘道指派給專用網路。

* `Netsh interface IP set address name="Local Area Connection" static 10.0.0.2 255.255.255.248`
* `Route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 -p`

## Windows 2003 Standard 及 Enterprise
{: #windows-2003-standard-and-enterprise}

您可以使用 Windows 2003，利用指令行或圖形使用者介面來配置網路。如果想要使用指令行，請參閱 Windows 2008 Server Core 的配置指示。這些指示也適用於 Windows 2003。這裡提供使用圖形介面的指示。此範例中使用的 IP 位址需要取代為您 IP 區塊中的 IP 位址。

**範例專用 IP 區塊 - 10.0.0.0/29**

* IP 位址 - 10.0.0.2
* 閘道 - 10.0.0.1
* 子網路遮罩 - 255.255.255.248

### 開啟網路配置
{: #opening-your-network-configurations}

1. 移至**開始功能表 > 設定 > 控制台**
2. 開啟**網路連線**
   **附註：**如果您已安裝兩個網路配接卡，則會看到「本端區域連線」及「本端區域連線 2」

**公用網路**

請使用下列指示，透過 Windows 圖形介面配置公用網路。

1. 用滑鼠右鍵按一下**本端區域連線 > 內容**，然後選取**網際網路通訊協定 (TCP/IP) > 內容**。
2. 移至**一般 > 使用下列 IP 位址：**。
3. 輸入您的公用 IP 位址、子網路遮罩及閘道。
4. 選取**使用下列 DNS 伺服器位址：**。如果您是配置專用網路，請使用下列「DNS 伺服器」。如果您不是配置專用網路，則需要提供 DNS 伺服器。
    * 偏好的 DNS 伺服器：10.0.80.11
    * 替代 DNS 伺服器：10.0.80.12
5. 按一下**確定**

**專用網路**

1. 用滑鼠右鍵按一下**本端區域連線 2 > 內容**，然後選取**網際網路通訊協定 (TCP/IP) > 內容**。
2. 移至**一般 > 使用下列 IP 位址：**，然後輸入您的專用 IP 位址和子網路遮罩。將閘道欄位保留空白，然後按一下**確定**。
3. 再次按一下**確定**以關閉網路配置視窗。

若要提供對包括 DNS 伺服器之整個專用網路的存取權，您需要將自訂路徑新增至伺服器。請遵循下列步驟來提供專用網路存取權：

1. 移至**開始 > 執行**，然後輸入 *cmd*，並按下**確定**。
2. 執行下列指令，請執行下列動作：**附註：**確定將閘道位址 (10.0.0.1) 取代為您的專用 IP 區塊閘道。<br/>
*"route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 -p"*

## Windows 2008 Web、Standard、Enterprise 及 Datacenter
{: #windows-2008-web-standard-enterprise-and-datacenter}

如果您有 Windows 2008，則可以使用命令提示字元或圖形使用者介面來配置網路。如果想要使用命令提示字元，請參閱 Windows 2008 Server Core 的配置指示。這些指示適用於所有 2008 伺服器。這裡提供使用圖形介面的指示。此範例中使用的 IP 位址需要取代為您 IP 區塊中的 IP 位址。

**範例專用 IP 區塊 - 10.0.0.0/29**

* IP 位址 - 10.0.0.2
* 閘道 - 10.0.0.1
* 子網路遮罩 - 255.255.255.248

**開啟網路配置**

1. 移至**開始功能表 > 設定 > 控制台**
2. 開啟**網路和共用中心**，然後按一下**管理網路連線**
* 如果您已安裝兩個網路配接卡，則會看到「本端區域連線」及「本端區域連線 2」。

**公用網路**

下列指示提供透過 Windows 圖形介面來配置公用網路的詳細步驟。

1. 用滑鼠右鍵按一下**本端區域連線 > 內容**。
2. 選取 **網際網路通訊協定第 4 版 (TCP/IPv4) 內容**。
3. 移至**一般 > 使用下列 IP 位址：**，然後輸入您的公用 IP 位址、子網路遮罩及閘道。
4. 選取**使用下列 DNS 伺服器位址：**。如果您是配置專用網路，請使用下列「DNS 伺服器」。如果您不是配置專用網路，則需要提供 DNS 伺服器。
    * 偏好的 DNS 伺服器：10.0.80.11
    * 替代 DNS 伺服器：10.0.80.12
5. 按一下**確定**

**專用網路**

1. 用滑鼠右鍵按一下**本端區域連線 2 > 內容**，然後選取**網際網路通訊協定第 4 版 (TCP/IPv4) > 內容**。
2. 移至**一般 > 使用下列 IP 位址：**、輸入您的專用 IP 位址及子網路遮罩，然後按一下**確定**。
3. 再次按一下**確定**以關閉網路配置視窗。

若要提供對包括 DNS 伺服器之整個專用網路的存取權，您需要將自訂路徑新增至伺服器。

1. 移至**開始 > 執行**，然後輸入 "cmd"，並按一下**確定**。
2. 執行下列指令：**附註：**將閘道位址 (_10.0.0.1_) 取代為您的專用 IP 區塊閘道。
  * `route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 -p`

## RedHat、Fedora 及 CentOS
{: #redhat-fedora-and-centos}

在 RedHat、Fedora 和 CentOS 上配置網路，是透過手動編輯配置檔來完成。若要手動編輯這些檔案，您需要登入虛擬機器。

**範例公用 IP 區塊 - 192.0.2.0/29**

* IP 位址 - 192.0.2.2
* 閘道 - 192.0.2.1
* 子網路遮罩 - 255.255.255.248

**範例專用 IP 區塊 - 10.0.0.0/29**

* IP 位址 - 10.0.0.2
* 閘道 - 10.0.0.1
* 子網路遮罩 - 255.255.255.248

**公用網路**

公用網路設定包含在下列檔案中。您需要使用提供的資訊來編輯此檔案。將範例 IP 位址取代為您公用 IP 區塊中的 IP 位址。如果該檔案不存在，請建立它。如果存在，請將檔案中的所有資料取代為下列資訊：
* /etc/sysconfig/network-scripts/ ifcfg-eth1
      DEVICE=eth1
      BOOTPROTO=static
      BROADCAST=192.0.2.7
      IPADDR=192.0.2.2
      NETMASK=255.255.255.248
      NETWORK=192.0.2.0

      GATEWAY=192.0.2.1
      ONBOOT=yes

**專用網路**

專用網路設定位於下列檔案中。您需要使用提供的資訊來編輯此檔案。將範例 IP 位址取代為「專用」IP 區塊中的正確 IP 位址。如果該檔案不存在，請建立它。如果存在，請將檔案中的所有資料取代為下列資訊：**附註：**專用網路*將不會* 具有預設路徑，因此不會定義*閘道*。

* /etc/sysconfig/network-scripts/ ifcfg-eth0
      DEVICE=eth0
      BOOTPROTO=static
      IPADDR=10.0.0.2
      NETMASK=255.255.255.248
      ONBOOT=yes

最後，需要新路徑才能提供對整個專用網路的專用存取權，以包括 DNS 伺服器，作法為編輯下列檔案。此檔案不存在，因此需要建立它。**附註：**將範例中的 "10.0.0.1" 取代為您的專用子網路 IP 閘道。

* /etc/sysconfig/network-scripts/route-eth0
      10.0.0.0/8 via 10.0.0.1

**DNS 配置**

主要及次要 DNS 配置包含在個別檔案中。您需要使用下列資訊來編輯此檔案。如果此虛擬機器無法存取專用網路，則您需要將伺服器 IP 取代為要使用之 DNS 伺服器的 IP 位址。

* /etc/resolv.conf
      nameserver 10.0.80.11
      nameserver 10.0.80.12

若要讓這些變更生效，您需要重新啟動伺服器上的網路。您可以執行下列指令來重新啟動網路：

* `service network restart`

## Ubuntu 及 Debian
{: #ubuntu-and-debian}

您可以透過單一配置檔來配置 Ubuntu 及 Debian 網路。您需要使用下列資訊來編輯此配置檔。若要編輯此檔案，您需要具有伺服器的 root 存取權。Ubuntu 的基本安裝不提供 root 登入。不過，在安裝過程中建立的使用者可以存取 sudo。如果您是執行 Ubuntu，則需要使用 sudo 指令來編輯此檔案。

**範例公用 IP 區塊 - 192.0.2.0/29**

* IP 位址 - 192.0.2.2
* 閘道 - 192.0.2.1
* 子網路遮罩 - 255.255.255.248

**範例專用 IP 區塊 - 10.0.0.0/29**

·IP 位址 - 10.0.0.2
·閘道 - 10.0.0.1
·子網路遮罩 - 255.255.255.248

**公用和專用網路**

使用任何文字編輯器來編輯下列檔案，並將範例 IP 位址取代為「公用」及「專用」IP 區塊中的 IP。

* /etc/network/interfaces
      auto lo
      iface lo inet loopback

      auto eth1
      iface eth1 inet static
      address 192.0.2.2
      netmask 255.255.255.248
      gateway 192.0.2.1

      auto eth0
      iface eth0 inet static
      address 10.0.0.2
      netmask 255.255.255.248

      #Static route for backend service network
      up route add -net 10.0.0.0/8 gw 10.0.0.1

**DNS 配置**

主要及次要 DNS 配置包含在個別檔案中。您需要使用下列資訊來編輯此配置檔。如果此虛擬機器無法存取專用網路，則您需要將伺服器 IP 取代為要使用之 DNS 伺服器的 IP 位址。

* /etc/resolv.conf
      nameserver 10.0.80.11
      nameserver 10.0.80.12

若要讓這些變更生效，您需要重新啟動伺服器上的網路。您可以執行下列指令來重新啟動網路：

* */etc/init.d/network restart*

**附註：**根據 RFC1166 及 RFC5737，192.0.2.0/24 用於「公用 IP 文件」。請不要在您的伺服器上使用這些 IP 位址。
