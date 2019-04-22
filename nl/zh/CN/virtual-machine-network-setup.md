---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 设置虚拟机网络
{: #setting-up-a-virtual-machine-network}

新虚拟机上的网络配置需通过若干步骤来完成。对于公用和专用网络，需要单独的可移植 IP 块。假定您使用的虚拟化产品需要在 VLAN 广播域上有辅助子网，而不路由到 VLAN（无网络标识/网关/广播）子网。如果未计划在虚拟机上使用专用网络，那么只需要公用子网。可移植 IP 块可以直接通过门户网站的[销售 -> 添加 IP 地址 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://manage.softlayer.com/Sales/orderAdditionalServices/subnet){: new_window} 进行订购，或者通过[公用网络 IP 管理器 ![外部链接图标](../../icons/launch-glyph.svg " 外部链接图标")](https://manage.softlayer.com/PublicNetwork/ipManager){: new_window} 进行订购。

获取 IP 块后，可以在虚拟机上配置联网。如果未安装虚拟机，那么可以通过操作系统安装过程来配置公用网络块。此过程是配置公用网络的最快方法。所有网络配置步骤都假定第一个网络接口是专用网络，第二个网络接口是公用网络。

配置网络对于每种操作系统各不相同。您可以找到有关为以下操作系统配置网络的详细信息。**注：**分组在一起的操作系统具有相同的网络配置。

* Windows 2008 Server Core
* Windows 2003 Standard 和 Windows 2003 Enterprise
* Windows 2008 Web、Windows 2008 Standard、Windows 2008 Enterprise 和 Windows 2008 Datacenter
* RedHat、Fedora 和 CentOS
* Ubuntu 和 Debian

网络配置需要公共和专用 IP 块的以下信息。这些信息可在门户网站的[公用网络 -> IP 管理器 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://manage.softlayer.com/PublicNetwork/ipManager){: new_window} 下的“公共 IP 块”以及[专用网络 -> IP 管理器 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://manage.softlayer.com/PrivateNetwork/ipManager){: new_window} 下的“专用 IP 块”中找到。

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

Windows 2008 Server Core 版本未提供图形界面来配置系统网络。配置需要使用命令提示符来手动完成。您需要运行以下命令。此示例中使用的 IP 地址需要替换为您的 IP 块中的 IP 地址。这些命令适用于包含 [netsh ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://docs.microsoft.com/en-us/previous-versions/tn-archive/bb490939(v=technet.10)){: new_window} 命令的较低版本 Windows 服务器。

**示例公共 IP 块 - 192.0.2.0/29**

* IP 地址 - 192.0.2.2
* 网关 - 192.0.2.1
* 子网掩码 - 255.255.255.248

**示例专用 IP 块 - 10.0.0.0/29**

* IP 地址 - 10.0.0.2
* 网关 - 10.0.0.1
* 子网掩码 - 255.255.255.248

**公用网络**

以下命令创建公用网络，用于将服务器连接到因特网。

* *Netsh interface ip set address name=”Local Area Connection 2” static 192.0.2.2 255.255.255.248 192.0.2.1*

以下命令创建 DNS 条目。如果不打算使用专用网络，那么需要将这些 IP 地址替换为有效 DNS 服务器。

* *Netsh interface ip add dns “Local Area Connection 2” 10.0.80.11*
* *Netsh interface ip add dns “Local Area Connection 2” 10.0.80.12*

**专用网络**

以下两个命令配置专用网络，并为专用网络创建持久路径。持久路径提供对整个专用网络的专用网络访问权。该访问权包含公用网络配置中的 DNS 服务器。**注：**不会为专用网络分配网关。

* `Netsh interface IP set address name=”Local Area Connection” static 10.0.0.2 255.255.255.248`
* `Route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 -p`

## Windows 2003 Standard 和 Windows 2003 Enterprise
{: #windows-2003-standard-and-enterprise}

可以使用 Windows 2003 通过命令行或图形用户界面来配置联网。如果要使用命令行，请参阅 Windows 2008 Server Core 的配置指示信息。这些指示信息也适用于 Windows 2003。此处提供的是使用图形界面的指示信息。此示例中使用的 IP 地址需要替换为您的 IP 块中的 IP 地址。

**示例专用 IP 块 - 10.0.0.0/29**

* IP 地址 - 10.0.0.2
* 网关 - 10.0.0.1
* 子网掩码 - 255.255.255.248

### 打开网络配置
{: #opening-your-network-configurations}

1. 转至**“开始”菜单 > 设置 > 控制面板**
2. 打开**网络连接**
   **注：**如果已安装两个网络适配器，那么会看到“本地连接”和“本地连接 2”

**公用网络**

使用以下指示信息通过 Windows 图形界面来配置公用网络。

1. 右键单击**本地连接 > 属性**，然后选择 **Internet 协议 (TCP/IP) > 属性**。
2. 转至**常规 > 使用下面的 IP 地址：**
3. 输入您的公共 IP 地址、子网掩码和网关。
4. 选中**使用下面的 DNS 服务器地址：**。如果要配置专用网络，请使用以下 DNS 服务器。如果不打算配置专用网络，那么需要提供 DNS 服务器。
    * 首选 DNS 服务器：10.0.80.11
    * 备用 DNS 服务器：10.0.80.12
5. 单击**确定**。

**专用网络**

1. 右键单击**本地连接 2 > 属性**，然后选择 **Internet 协议 (TCP/IP) > 属性**。
2. 转至**常规 > 使用下面的 IP 地址：**，然后输入您的专用 IP 地址和子网掩码。使网关字段保留为空，然后单击**确定**。
3. 再次单击**确定**以关闭网络配置窗口。

要提供对包含 DNS 服务器的整个专用网络的访问权，您需要向服务器添加定制路径。执行以下步骤来提供专用网络访问权：

1. 转至**开始 > 运行**，输入 *cmd*，然后按**确定**。
2. 运行以下命令：**注：**确保将网关地址 (10.0.0.1) 替换为您的专用 IP 块网关。<br/>
*“route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 –p”*

## Windows 2008 Web、Windows 2008 Standard、Windows 2008 Enterprise 和 Windows 2008 Datacenter
{: #windows-2008-web-standard-enterprise-and-datacenter}

如果您有 Windows 2008，请使用命令提示符或图形用户界面来配置联网。如果打算使用命令提示符，请参阅 Windows 2008 Server Core 的配置指示信息。这些指示信息适用于所有 2008 服务器。此处提供的是使用图形界面的指示信息。此示例中使用的 IP 地址需要替换为您的 IP 块中的 IP 地址。

**示例专用 IP 块 - 10.0.0.0/29**

* IP 地址 - 10.0.0.2
* 网关 - 10.0.0.1
* 子网掩码 - 255.255.255.248

**打开网络配置**

1. 转至**“开始”菜单 > 设置 > 控制面板**
2. 打开**网络和共享中心**，然后单击**管理网络连接**
* 如果已安装两个网络适配器，那么会看到“本地连接”和“本地连接 2”。

**公用网络**

以下指示信息提供了通过 Windows 图形界面配置公用网络的详细步骤。

1. 右键单击**本地连接 > 属性**。
2. 选择 **Internet 协议版本 4 (TCP/IPv4) > 属性**。
3. 转至**常规 > 使用下面的 IP 地址：**，然后输入您的公共 IP 地址、子网掩码和网关。
4. 选中**使用下面的 DNS 服务器地址：**。如果要配置专用网络，请使用以下 DNS 服务器。如果不打算配置专用网络，那么需要提供 DNS 服务器。
    * 首选 DNS 服务器：10.0.80.11
    * 备用 DNS 服务器：10.0.80.12
5. 单击**确定**。

**专用网络**

1. 右键单击**本地连接 2 > 属性**，然后选择 **Internet 协议版本 4 (TCP/IPv4) > 属性**。
2. 转至**常规 > 使用下面的 IP 地址：**，输入您的专用 IP 地址和子网掩码，然后单击**确定**。
3. 再次单击**确定**以关闭网络配置窗口。

要提供对包含 DNS 服务器的整个专用网络的访问权，您需要向服务器添加定制路径。

1. 转至**开始 > 运行**，输入“cmd”，然后单击**确定**。
2. 运行以下命令：**注：**请将网关地址 (_10.0.0.1_) 替换为您的专用 IP 块网关。
  * `route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 –p`

## RedHat、Fedora 和 CentOS
{: #redhat-fedora-and-centos}

在 RedHat、Fedora 和 CentOS 上配置网络是通过手动编辑配置文件来完成的。要手动编辑这些文件，您需要登录到虚拟机。

**示例公共 IP 块 - 192.0.2.0/29**

* IP 地址 - 192.0.2.2
* 网关 - 192.0.2.1
* 子网掩码 - 255.255.255.248

**示例专用 IP 块 - 10.0.0.0/29**

* IP 地址 - 10.0.0.2
* 网关 - 10.0.0.1
* 子网掩码 - 255.255.255.248

**公用网络**

公用网络设置包含在以下文件中。您需要使用提供的信息来编辑此文件。请将示例 IP 地址替换为您的公共 IP 块中的 IP 地址。如果此文件不存在，请进行创建。如果存在，请将文件中的所有数据替换为以下信息：
* /etc/sysconfig/network-scripts/ ifcfg-eth1
      DEVICE=eth1
      BOOTPROTO=static
      BROADCAST=192.0.2.7
      IPADDR=192.0.2.2
      NETMASK=255.255.255.248
      NETWORK=192.0.2.0

      GATEWAY=192.0.2.1
      ONBOOT=yes

**专用网络**

专用网络设置包含在以下文件中。您需要使用提供的信息来编辑此文件。请将示例 IP 地址替换为您的专用 IP 块中的正确 IP 地址。如果此文件不存在，请进行创建。如果文件存在，请将文件中的所有数据替换为以下信息：**注：**专用网络*没有*缺省路径，因此未定义 *GATEWAY*。

* /etc/sysconfig/network-scripts/ ifcfg-eth0
      DEVICE=eth0
      BOOTPROTO=static
      IPADDR=10.0.0.2
      NETMASK=255.255.255.248
      ONBOOT=yes

最后，需要新路径来提供对整个专用网络的专用访问权以包含 DNS 服务器，这可通过编辑以下文件来完成。此文件不存在，因此需要进行创建。**注：**请将示例中的“10.0.0.1”替换为您的专用子网 IP 网关。

* /etc/sysconfig/network-scripts/route-eth0
      10.0.0.0/8 via 10.0.0.1

**DNS 配置**

主 DNS 配置和辅助 DNS 配置包含在单独的文件中。您需要使用以下信息来编辑该文件。如果此虚拟机无权访问专用网络，那么需要将服务器 IP 替换为要使用的 DNS 服务器的 IP 地址。

* /etc/resolv.conf
      nameserver 10.0.80.11
      nameserver 10.0.80.12

为了使这些更改生效，需要在服务器上重新启动联网。可以通过运行以下命令来重新启动联网：

* `service network restart`

## Ubuntu 和 Debian
{: #ubuntu-and-debian}

可以通过单个配置文件来配置 Ubuntu 和 Debian 网络。您需要使用以下信息来编辑此配置文件。要编辑此文件，您需要具有对服务器的 root 用户访问权。Ubuntu 的基本安装未提供 root 用户登录。但是，在安装过程中创建的用户可以访问 sudo。如果运行的是 Ubuntu，那么需要使用 sudo 命令来编辑此文件。

**示例公共 IP 块 - 192.0.2.0/29**

* IP 地址 - 192.0.2.2
* 网关 - 192.0.2.1
* 子网掩码 - 255.255.255.248

**示例专用 IP 块 - 10.0.0.0/29**

·IP 地址 - 10.0.0.2
·网关 - 10.0.0.1
·子网掩码 - 255.255.255.248

**公用和专用网络**

使用任何文本编辑器来编辑以下文件，并将示例 IP 地址替换为您的公共和专用 IP 块中的 IP。

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

主 DNS 配置和辅助 DNS 配置包含在单独的文件中。您需要使用以下信息来编辑此配置文件。如果此虚拟机无权访问专用网络，那么需要将服务器 IP 替换为要使用的 DNS 服务器的 IP 地址。

* /etc/resolv.conf
      nameserver 10.0.80.11
      nameserver 10.0.80.12

为了使这些更改生效，需要在服务器上重新启动联网。可以通过运行以下命令来重新启动网络：

* */etc/init.d/network restart*

**注：**根据 RFC1166 和 RFC5737，192.0.2.0/24 将用作公共 IP 文档。因此，不要在服务器上使用这些 IP 地址。
