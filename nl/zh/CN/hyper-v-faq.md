---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 常见问题：Hyper-V
{: #faqs-hyper-v}

## 什么是 Hyper-V？
{: #what-is-hyper-v-}

Hyper-V 是用于 Windows 2008 Server Datacenter Edition 的虚拟化系统，允许单个物理服务器托管多个运行其自己操作系统的虚拟机。

## 运行 Hyper-V 的需求是什么？
{: #what-are-the-requirements-to-run-hyper-v-}

Hyper-V 必须安装在 Windows 2008 Server Datacenter Edition（64 位）上。所有 {{site.data.keyword.cloud}} 硬件均满足 Hyper-V 的系统需求。Microsoft 建议至少有 2 GB RAM 用于服务器。

## 其他任何版本的 2008 都可以使用 Hyper-V 吗？
{: #is-hyper-v-available-on-any-other-versions-of-2008-}

{{site.data.keyword.cloud_notm}} 仅在 Datacenter Edition 上提供 Hyper-V。

## Hyper-V 可以在 Windows 2003 上运行吗？
{: #can-hyper-v-run-on-windows-2003}

Hyper-V 是针对 Windows 2008 开发的，不能在 Windows 2003 上运行。

## 可以在虚拟机上安装哪些操作系统？
{: #what-operating-systems-can-be-installed-on-a-virtual-machine}

{{site.data.keyword.cloud_notm}} 在 Hyper-V 上支持以下操作系统：

* Windows 2003 Server 和 Windows 2008 Server 的所有版本
* CentOS、Fedora 和 Ubuntu Linux 分发版

## 一台服务器可以运行多少个虚拟机？
{: #how-many-virtual-machines-can-a-server-run-}

一台服务器可以运行的虚拟机数根据该服务器的处理器数、RAM 和硬盘空间而变化。

## 每个虚拟机上的 RAM 都可定制吗？
{: #is-the-ram-customizable-on-each-virtual-machine-}

是的。可以使用 Hyper-V 来定制每个虚拟机的系统资源，包括内存。

## 一个虚拟机需要多少 RAM？
{: #how-much-ram-does-a-virtual-machine-need-}

RAM 需求根据虚拟机的需求而变化。请查看访客操作系统的系统需求。为虚拟机提供的内存量可以随时更改。

## 虚拟机可以访问多个处理器吗？
{: #can-a-virtual-machine-access-to-more-than-one-processor-}

可以向任何 Windows 虚拟机授予对多个处理器的虚拟机访问权。但是，Linux 虚拟机限制为只能访问单个处理器。

## 创建虚拟机后可以更改硬盘大小吗？
{: #can-hard-disk-sizes-change-after-a-virtual-machine-is-created-}

可以。

## 每个虚拟操作系统都需要拥有许可证吗？
{: #does-each-virtual-operating-system-need-to-have-a-license-}

Windows 2003 和 Windows 2008 虚拟机通过 {{site.data.keyword.cloud_notm}} 获得许可。Linux 虚拟机可免费获得许可，无需进行任何操作。

## 虚拟机有权访问专用网络吗？
{: #will-the-virtual-machines-have-access-to-the-private-network-}

是的。虚拟机可以连接到公用和专用网络。

## 为虚拟机提供专用网络访问权有哪些优势？
{: #what-advantages-are-there-to-providing-private-network-access-to-virtual-machines-}

为虚拟机提供专用网络访问权将允许虚拟机相互通信。通过专用网络访问权，虚拟机还可以与其他内部系统（例如，NAS 和 iSCSI）以及您针对 {{site.data.keyword.cloud_notm}} 拥有的其他任何服务器进行通信。

## 虚拟机可以使用服务器随附的辅助 IP 块吗？
{: #can-virtual-machines-use-the-secondary-ip-block-that-came-with-the-server-}

不能。随服务器一起提供的辅助 IP 将专门进行路由，以用于物理服务器，而不是虚拟机。您需要可移植 IP 块来将虚拟机连接到网络。

## 什么是可移植 IP 地址？
{: #what-are-portable-ip-addresses-}

有关可移植 IP 地址的信息，请参阅[子网和 IP 入门](/docs/infrastructure/subnets?topic=subnets-getting-started-subnets-ips#getting-started-subnets-ips)。

## 如何订购 Hyper-V？
{: #how-do-i-order-hyper-v-}

您可以通过发出操作系统重装，并将操作系统更改为“Windows 2008 Server Datacenter Edition（带 Hyper-V）”，从而在现有服务器上安装 Hyper-V。要使用 Hyper-V 来供应新服务器，请订购新服务器，然后选择“Windows 2008 Server Datacenter Edition（带 Hyper-V）”作为操作系统。

## Hyper-V 随 Windows 2008 Server Datacenter Edition 一起预安装吗？
{: #is-hyper-v-preinstalled-with-windows-2008-server-datacenter-edition-}

Hyper-V 不随 Windows 2008 Server 一起预安装。如果要在 Windows 2008 Server 上安装 Hyper-V，您需要发出操作系统重装，并选择“Windows 2008 Server Datacenter Edition（带 Hyper-V）”作为操作系统。

## Hyper-V 的操作系统重装已完成。接下来要做什么？
{: #the-os-reload-for-hyper-v-is-done-what-s-next-}

需要设置 Hyper-V。有关设置 Hyper-V 的更多信息，请参阅[设置 Hyper-V](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-hyper-v)。

## 虚拟机未启动。收到“无法初始化机器远程系统：错误：‘不明错误’”。该如何解决此问题？
{: #a-virtual-machine-does-not-start-cannot-initialize-machine-remoting-system-error-unspecified-error-how-do-i-resolve-this-issue-}

要解决此问题，请停止并重新启动 Hyper-V 服务。此问题通常是因 Hyper-V 尝试在服务器激活期间启动而引起的。重新启动服务可使虚拟机在激活完成后恢复运行。
