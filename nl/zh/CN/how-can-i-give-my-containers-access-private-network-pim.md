---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 通过 PIM 授予容器对专用网络的访问权
{: #giving-containers-access-to-the-private-network-through-pim}

可以向容器授予对专用网络的访问权。但是，您需要了解向客户提供内部基础架构访问权方面的影响，这可能会导致安全问题。

1. 转至要向其授予专用网络访问权的容器。
2. 单击**网络**选项卡，然后单击**配置**。
3. 现在，可以设置“桥接网络”部分：
  * **桥接网络**；选中此复选框。
  * **连接至**：这不是必需的，用于提供对 {{site.data.keyword.cloud}} 专用网络的访问权。
  * **通过 DHCP 获取 IP 地址**：清除此复选框。
  * **IP 地址/子网掩码**：从专用地址池中选择内部 IP 地址。
  * 单击**提交**。

现在，您需要通过右上角的链接来刷新页面，设置即完成。
