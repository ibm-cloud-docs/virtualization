---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 在 CentOS 5.x（64 位）上安装 VMware
{: #installing-vmware-on-centos-5-x-64-bit-}

开始之前，请使用 root 用户登录到服务器。

## 准备安装 VMware
{: #preparing-to-install-vmware}

1. 下载 [VMware 安装程序 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://vmware.com/download/server/){: new_window}。单击下载链接，接受 EULA，然后下载 LinuxTarball（在此示例中为 VMware-server-1.0.3-44356.tar.gz）：

* `# wget –O vmware-server.tar.gz https://download3.vmware.com/software/vmserver/VMware-server-1.0.3-44356.tar.gz`

2. 下载软件后，需要获取许可证密钥（这在 VMWare 服务器免费版本中是免费的）。要进行注册，请参阅 [VMware ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://register.vmware.com/content/registration.html){: new_window}。

3. 解压缩该 .tar 文件：

* `# tar -xzvf vmware-server.tar.gz`

## 开始安装之前
{: #before-you-begin-installation}

开始安装之前，请复查以下先决条件：

```
# yum update
# yum install libXtst.i386
# yum install libXrender.i386
# yum install xinetd
```

## 安装 VMWare 服务器
{: #installing-vmware-server}

1. 转至以下目录：

* `# cd vmware-server-distrib/`

2. 运行 VMware 安装脚本：

* `# ./vmware-install.pl`

3. 安装会显示基本问题，而且希望了解要创建哪些目录并将 VMware 的特定部件安装到其中。在此，请接受缺省值。接受许可协议以继续安装。

4. 系统可能会询问您以下问题：“VMware 服务器的预构建 vmmon 模块都不适合运行中的内核。需要此程序尝试为系统构建 vmmon 模块吗（您需要在系统上安装 C 编译器)？”
* 对于此问题，请回答**是**（缺省值）。

## 设置 VMware 联网
{: #setting-up-vmware-networking}

系统将提示您回答以下问题：

1. “要将虚拟机联网吗？”
* 请回答**是**。您需要为公用网络设备创建网络设置，以便可以在虚拟机上访问因特网。

2. “计算机有多个可用的以太网接口：eth0 和 eth1。您希望哪个接口桥接到 vmnet0？”
* 请记住，所有 {{site.data.keyword.BluSoftlayer}} 服务器的设置和运行方式是，公用网络在 eth1 上运行，专用网络在 eth0 上运行。在 VMware 中，vmnet0 的缺省网桥设备为 eth0。不要按 Enter 键，请改为输入 eth1。

## 桥接专用网络
{: #bridging-the-private-network}

系统会提示您回答以下问题，对于该问题，可以回答**是**或**否**：
*“要配置其他桥接网络吗？”

如果计划在专用网络上运行服务或其他应用程序，请对此问题回答“是”以继续（除非您知道自己使用的不是专用网络），系统将创建网桥来连接到专用网络。按 Enter 键后，会自动将 eth0 用作接口，因为这是唯一剩下可用的接口（原因是服务器中只有两块网卡）。

## 其他联网设置
{: #other-networking-settings}

系统将向您显示有关 VMware 服务器网络设置的其他若干问题。请使用以下建议以继续：

* *“您希望能够在虚拟机中使用 NAT 联网吗？”*

- 请回答“是”以继续

* *“您希望此程序探测未使用的专用子网吗？”*

- 请回答“是”以继续

- 此过程完成后，请确保未配置另一个 NAT 网络。

* *“您希望能够在虚拟机中使用仅主机联网吗？”*

- 请回答“是”以继续

* *“您希望此程序探测未使用的专用子网吗？”*

- 请回答“是”以继续

- 此过程完成后，请确保未配置另一个仅主机网络。

## 指定侦听端口
{: #specifying-listening-port}

下一个询问您的问题是您希望 VMware 服务器侦听哪个端口。可以保留使用缺省端口 904。

## 存储虚拟机
{: #storing-the-virtual-machines}

安装程序询问的下一个问题是*“要将虚拟机文件保存在哪个目录中？”*。缺省位置为 /var/lib/vmware/virtual machines。确保将虚拟机放置在您有大量磁盘空间的位置，例如冗余 RAID 阵列或大型辅助硬盘。务必确保有足够的空间用于虚拟机。在本例中，可以使用安装到大型磁盘的安装点 /data/vm。

## 提供 VMware 的序列号
{: #providing-the-serial-number-for-vmware}

安装的最后一部分需要插入 VMware 许可证密钥和序列号。如果您还没有许可证密钥，请参阅 [VMware 站点 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://register.vmware.com/content/registration.html){: new_window}。如果您具有此 VMware 服务器的序列号，请将其插入提示符处，然后按 Enter 键。

现在，您会看到类似于以下语句的内容：

    “为此运行的内核配置 VMware Server 1.0.3 build-44356 for Linux 成功完成。”

VMware 现在已在服务器上设置。现在，您需要下载 VMware Server Console（这是 VMware 服务器的 GUI 客户机），然后配置和安装虚拟机。

## 下载 VMware Server Console
{: #downloading-vmware-server-console}

VMware Server Console 是 VMware 服务器的客户机应用程序。您可使用该控制台来管理可以在其中创建、配置和安装虚拟机的 VMware 服务器。要安装此应用程序，请从 [VMware 下载 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://vmware.com/download/server/){: new_window} 中下载 VMware Server Windows 客户机包。此包是 .zip 文件。下载后，解压缩该包并安装 VMware-console-1.0.3-x 文件。安装完成后，即已完成安装 VMware Server Console，并准备好配置 VMware 服务器。

## 登录到 VMware 控制台
{: #logging-in-to-the-vmware-console}

在安装 VMware Server Console 的计算机上，打开 VMware Server Console。装入 VMware Server Console 时，系统会向您提示“交换机主机”（登录）屏幕。VMware 服务器使用 Linux 系统用户名和密码来对用户进行认证，因此需要使用用户名（尤其是 root 用户）登录到 VMware。请使用以下凭证：

* **主机名：**IP 地址以及端口（例如，67.228.160.201:904）<br />
* **用户名：**root<br />
* **密码：**password（请使用系统的实际 root 用户密码）

## 配置防火墙规则 (IPTables)
{: #configuring-the-firewall-rules-iptables-}

如果连接到 VMware 服务器时遇到问题，并且此问题不属于认证问题（如果您收到用户名和密码错误，说明您的用户名或密码有误），那么可能是因为防火墙阻止您连接到 VMware 服务器。解决方法是尝试将以下 IPTable 规则添加到 /etc/sysconfig/iptablesfile 中。**注：**确保命名约定符合服务器配置：

- `# -A FWALL-INPUT -p tcp -m tcp -s 0/0 --dport 904 -j ACCEPT`
