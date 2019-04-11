---



copyright:
  years: 2014, 2018
lastupdated: "2018-01-04"

subcollection: virtualization


---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# 常见问题：常规
{: #faqs-general}

## 为什么我的服务器没有在客户门户网站的“虚拟服务器”列表中列出？
{: #why-isn-t-my-server-listed-in-the-virtual-servers-list-in-the-customer-portal-}

如果服务器上有 **XenServer**、**Hyper-V** 或 **Virtuozzo**，但并未在[门户网站 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://manage.softlayer.com/){: new_window} 的[硬件 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://manage.softlayer.com/Hardware/configuration){: new_window} 部分中的 [{{site.data.keyword.virtualmachinesshort}} ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://manage.softlayer.com/Virtual/live){: new_window} 列表中列出，那么您需要开具[标准支持凭单 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://manage.softlayer.com/Support/addTicket){: new_window} 并提交给支持部门，以便可以将其添加到列表中。

此请求会在美国 CST 正常工作时间内受理。
{:tip}

## 如果客户也是服务提供者，他们可以将自己的 VSPP 许可用于在 IBM Cloud 中租用的主机吗？
{: #if-clients-are-a-service-provider-can-they-use-their-own-vspp-licensing-for-hosts-that-they-rent-in-ibm-cloud-}

不能。他们可以使用 {{site.data.keyword.cloud}} VSPP 或 BYOL 实现基于套接字的许可。但不能使用他们自己的 VSPP 协议。

## 可以将服务器随附的辅助 IP 块用于虚拟机吗？
{: #can-i-use-the-secondary-ip-block-that-came-with-the-server-for-virtual-machines-}

不能。随服务器一起提供的辅助 IP 将路由用于物理服务器，而不是虚拟机。您需要具有可移植 IP 块来将虚拟机连接到网络。
