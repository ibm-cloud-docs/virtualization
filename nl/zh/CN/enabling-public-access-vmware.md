---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-11"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 启用对 VMware 的公共访问
{: #enabling-public-access-to-vmware}

缺省情况下，通过服务控制台安装的 ESX 主机仅位于专用网络上，这意味着在 ESX 主机上会阻止进出公用网络的公共流量。

首先，需要通过遵循以下步骤，在服务器的专用接口上使用 VMware vSphere Client 连接到服务器。

1. 单击 **ESX 主机 > 配置选项卡 > 联网 > 添加联网**。
2. 选择“服务控制台”。
3. 选择要使用的 vSwitch。在本例中，这是外部/公共接口，因此请选择 **vSwitch1**（vmnic1 和 vmnic3）。
4. 将“服务控制台”重命名为易于识别的名称（例如，“公共服务控制台”）。
5. 输入分配给服务器的具有正确子网掩码的主外部 IP 地址。
6. 单击**编辑**，并添加分配给服务器的缺省网关。
