---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-24"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# XenCenter 入门
{: #getting-started-with-xencenter}

## 开始之前
{: #before-you-begin-xencenter}

开始之前，请复查以下先决条件：

- 作为“VLAN 上的辅助子网”路由的可移植 IP 地址（公共和专用）的新范围。用于设置新 VM 的此示例解决方案需要您在专用网络上提供可用的 IP。可以通过 [{{site.data.keyword.slportal_full}} ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://control.softlayer.com/network/subnets/order) 来订购子网。
- 能够通过 VPN 连接到 {{site.data.keyword.cloud}} 专用网络。有关 VPN 访问的更多信息，请参阅[启用对 {{site.data.keyword.cloud_notm}} 基础架构专用网络的访问](/docs/customer-portal?topic=customer-portal-getting-started#enable-private-network)。
- Citrix XenCenter 已安装到本地系统。<!-- . http://downloads.service.softlayer.com/citrix/xen/-->

**注：**服务器已使用各种模板进行预配置，以帮助您快速开始使用并运行 XenServer。

## 使用 XenCenter 创建虚拟机
{: #creating-a-virtual-machine-with-xencenter}

使用以下步骤来帮助您使用 XenCenter 创建虚拟机。

1. 确保已通过 VPN 连接到 {{site.data.keyword.cloud_notm}} 专用网络。请通过 SSL VPN 或 PPTP 建立连接。
2. 打开 XenCenter，然后单击**“添加 XenServer”**。
3. 输入服务器主机名、用户名和密码。您需要使用服务器的专用 IP 地址（类似于 10.x.x.x）、用户名 `root` 以及服务器的 root 用户密码。这些信息可通过在 {{site.data.keyword.slportal}} 中转至[设备 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://control.softlayer.com/devices){: new_window}，然后单击 XenServer 的名称来获取。单击**连接**。
4. 如果收到“免费许可证”激活屏幕，请立即填写这些信息。
5. 右键单击您的 {{site.data.keyword.cloud_notm}} 名称，并选择**新建 VM...**。<!--You can now create your first Virtual Machine. Create a CentOS virtual machine with a disk of 10 GB and have both Public and Private Networks functioning-->
6. 选择要用于新虚拟机的操作系统，然后单击**下一步**。**注：**某些模板需要提供您自己的介质。<!--Because you are using CentOS, you can use {{site.data.keyword.BluSoftlayer_notm}} private mirrors for CentOS to get our installation going.Select a version of CentOS and then click **Next**.-->
7. 为新虚拟机输入适用的名称和描述。
8. 输入访客操作系统安装介质的位置，然后单击**下一步**。<!-- In the example, {{site.data.keyword.BluSoftlayer_notm}} a CentOS mirror is used as installation media. Provide the Install URL of: http://mirrors.service.softlayer.com/centos/5/os/x86_64 and click **Next**.
  *A trailing ‘/’ at the end of the URL can sometimes break the installation.*
  *This mirror is available only on the {{site.data.keyword.BluSoftlayer_notm}} Private Network. The full mirror's contents are  available here: http://mirrors.service.softlayer.com/.-->
9. 选择 CPU 数和内存分配量。（您要构建的是简化的系统，无需太多 RAM。因此，512 MB 绰绰有余。）单击**下一步**。
10. 选择虚拟盘，以将磁盘空间分配给 VM。<!--Remember that this is like adding hard disks, it is not like partitioning your system. Partitioning is done during the installation of the OS.--> 对于此模板，缺省大小为 8 GB。对于要构建的服务器，此空间量绰绰有余。*可选：可以通过突出显示磁盘并单击**编辑...** 来增大磁盘以适应您的需求。*单击**下一步**。
11. 针对虚拟机，添加或除去虚拟网络接口。缺省值是可接受的，除非您不希望系统能够在专用网络上进行通信。示例需要在系统中保留两个接口，但经验丰富的 Xen 管理员可以除去其中一个接口以用于自己的用途。对于这种情况，您可以根据需要突出显示并删除接口。单击**下一步**。
12. 配置完成。使“自动启动 VM”保留选中，然后单击**完成**。这将开始进行安装。您会返回到主屏幕。在左侧可以看到新的 VM 列出。
13. 选择新的 VM，然后单击**控制台**选项卡。 <!--You can now see that your system is booted into the CentOS installer awaiting your input.-->
14. <!--All of the parameters of a CentOS installation are outside of the scope of this article and will need to be customized by your System Administrator, but this article will provide some specific pieces of information that you need to complete the installation. Select your language to get started. The CentOS installer will then ask you for assistance in configuring the Networking Devices in the system.-->选择 **eth0 - Xen 虚拟以太网**，然后单击**确定**。
  <!--![14](images/14.png)-->
15. <!--In the pre-requisite notes, we made sure that we already had a set of Portable IP Addresses routed as "Secondary on VLAN" ready for this installation.--> 确保您已有用于配置 TCP/IP 的信息。您需要手动配置具有 IPV4 支持的接口，并禁用 IPV6 支持。
  <!--[15](images/15.png)-->
16. 确认您了解如何使用专用 IP 子网 (10.17.37.240/29)，然后单击**确定**<!-- to go to the CentOS installer-->。您和系统管理员可以根据自己的准则来安装访客操作系统。有关静态 IP 块和可移植 IP 块的更多信息，请参阅[子网和 IP 入门](/docs/infrastructure/subnets?topic=subnets-getting-started-with-subnets-and-ips)。

您已创建新的 VM。
