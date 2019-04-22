---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 设置系统管理程序
{: #setting-up-a-hypervisor}

使用以下步骤来设置系统管理程序。

1. 使用您的唯一凭证登录到 [{{site.data.keyword.slportal_full}} ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://control.softlayer.com/){: new_window}。
2. 从**设备**菜单中，选择**设备列表**，然后找到系统管理程序。
3. 通过[安全 VPN ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://www.softlayer.com/vpn-access){: new_window} 连接到专用网络，以访问系统管理程序。

**注：**{{site.data.keyword.cloud}} 系统管理程序提供程序包含 XenServer、VMware 和 Hyper-V。每个提供程序都具有独特的管理控制台，可通过不同方式进行访问。有关在管理控制台中访问和工作的更多信息，请参阅以下链接：

   * [XenServer ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://support.citrix.com/en/products/xenserver){: new_window}
   * [VMware ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://www.vmware.com/support/vsphere-hypervisor.html){: new_window}
   * [Hyper-V ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://technet.microsoft.com/en-us/windowsserver/dd448604){: new_window}

4. 获取虚拟机的可移植 IP。
    * VM 需要可移植 IP 地址。公共和专用可移植 IP 块可以通过 {{site.data.keyword.slportal}} 进行订购。
    * 有关分配 IP 地址的更多信息，请参阅[子网和 IP 入门](/docs/infrastructure/subnets?topic=subnets-getting-started-subnets-ips#getting-started-subnets-ips)。

5. 在专用网络上为 VM 建立路由。VM 需要符合以下规范才能通过专用网络路由到其他 VM：
    * 可移植专用 IP
    * 与 10.0.0.0/8 网络范围相关的静态路由

有关 VM 路由过程的更多信息，请参阅[设置虚拟机网络](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-a-virtual-machine-network)。设置 VM 网络后，同一 VLAN 上的机器可以进行通信。如果位于不同 VLAN 上的机器必须进行通信，请[启用 VLAN 生成](/docs/infrastructure/vlans?topic=vlans-vlan-spanning)。

## 访问并安全地存储 ISO
{: #access-and-securely-store-isos}

{{site.data.keyword.cloud_notm}} 网络上的 VM 可以运行预配置的 ISO 或定制 ISO。{{site.data.keyword.cloud_notm}} 网络上的 VM 可以访问内部镜像站点，该站点专供 {{site.data.keyword.cloud_notm}} 用户使用，并提供了最常用操作系统的常用配置。如果需要特定操作系统的特殊版本或配置，请参阅操作系统供应商的 Web 站点。

如果在 VM 上运行定制 ISO，请将 ISO 上传到安全位置，以便在设备发生故障时可以检索到该 ISO。许多用户选择使用 SSH 或 RDP 在设备的本地系统上存储定制 ISO。或者，通过具有各种功能的存储服务来提供空间。
