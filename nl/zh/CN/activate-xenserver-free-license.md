---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-13"

subcollection: virtualization

keywords: Citrix XenServer 
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 激活 XenServer
{: #activating-xenserver}

Citrix XenServer 免费许可证从初始安装或重新装入后许可使用 30 天。在此期间，您需要向 Citrix 注册并请求完整功能的 1 年许可证。要创建此许可证，Citrix 需要 {{site.data.keyword.BluSoftlayer}} 无权访问的个人信息。许可证是您与 Citrix 之间的 XenServer 使用协议。
{:shortdesc}

如果未在 30 天内注册系统，那么 30 天后将无法打开任何虚拟机的电源。
{:tip}

要注册系统，请完成以下步骤：

1. 下载并安装 XenCenter 客户机。可以从 [Citrix XenCenter ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://community.citrix.com/display/xs/XenCenter){: new_window} 页面访问客户机。

2. 通过 VPN 连接到专用网络。

3. 启动 XenCenter 并单击**添加新服务器**，或者从下拉菜单中，单击**服务器 > 添加...**。

4. 对于**主机名**，使用专用 IP 地址；对于**用户名**，使用“root”；对于**密码**，使用您的 root 用户密码。

5. 系统连接后，将显示一个对话框。此对话框会提醒您许可证将在 30 天后到期。如果未看到此对话框，请从下拉菜单中，转至**工具 > 许可证管理器...**。

6. 检查服务器，并单击**激活...**。这将打开浏览器，并转至 Citrix 注册站点。填好表单并提交。检查收件箱中是否有来自 Citrix 的电子邮件。此电子邮件的附件是许可证密钥。

7. 收到此电子邮件后，请在本地保存附加的许可证密钥文件。

8. 在 XenCenter 左侧窗格中，突出显示您的服务器。在菜单栏中，转至**服务器 > 安装许可证密钥...**。这将显示一个对话框，要求提供许可证密钥的位置。请将其指向步骤 7 中的保存位置。
