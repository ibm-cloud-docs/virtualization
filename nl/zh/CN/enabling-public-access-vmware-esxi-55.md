---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization

keywords: ESXi, VMware
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# 启用对 VMware ESXi 的公共访问
{: #enabling-public-access-to-vmware-esxi}

缺省情况下，通过服务控制台安装的 ESXi 主机仅位于专用网络上。因此，在 ESXi 主机上会阻止进出公用网络的公共流量。

首先，需要在服务器的专用接口上使用 VMware vSphere Client 来连接到服务器。

您需要具有服务器的“主”公共 IP 信息才能完成设置。有关服务器的公共 IP 的信息可以在 [{{site.data.keyword.slportal_full}} ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://control.softlayer.com/){: new_window} 中找到。
{:tip}

登录到 ESXi 主机后，请执行以下步骤来启用公共接口：

1. 您可能需要使用专用 IP 通过 SSH 登录到服务器，才能更正路由。
* 运行 `esxcfg-route -l` 以确定当前路径
* 运行 `esxcfg-route -a 10.0.0.0/8 [your servers private gateway ip]`
* 运行 `esxcfg-route [your servers public gateway]` 以确保公共网关是缺省值

登录到 vSphere 并执行以下步骤：

1. 单击“配置”选项卡，然后单击**联网**。
2. 对于 vSwitch1，单击**属性 > 添加**。
3. 选择 **VM 内核**，然后单击**下一步**。
* 可以保留这些设置原样不变。您可以将网络重新标记为“"VMKernelPublic”以供识别。
4. 输入服务器公共 IP 信息。
5. 对于 VM 内核缺省网关，单击**编辑**，输入公共网关 IP，然后单击**确定**。
* 单击**确定**。**注：**断开连接是正常的预期行为。*

现在，可以同时使用公共和专用 IP 地址来访问服务器。

启用对 ESXi 的公共 IP 访问包含固有的安全风险。请确保执行相应的步骤，将对 ESXi 的访问权仅限于需要此权限的用户。
{:tip}
