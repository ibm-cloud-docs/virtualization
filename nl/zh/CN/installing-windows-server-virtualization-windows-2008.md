---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-23"

subcollection: virtualization

keywords: Windows 2008
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 在 Windows 2008 上安装 Windows 服务器虚拟化
{: #installing-windows-server-virtualization-on-windows-2008}

<!--Windows 2008 64-bit edition comes with the option to install Windows next generation virtualization application codenamed Veridian. By default the application will not be a selectable option when Adding and Removing Roles from Windows 2008.

Note: At this time, this is only available in full installation of 64-bit editions of Windows 2008. 32-bit versions and Server-core installations do not support this.-->

## 开始之前
{: #before-you-begin-win-2008}

开始之前，请复查以下先决条件。

### 硬件
{: #hardware}

* 在 BIOS 中启用了硬件辅助虚拟化技术
* Intel VT
* AMD-V
* 在 BIOS 中启用了数据执行防御
* Intel Execute Disable (XD)
* AMD No Execute (NX)

### 软件
{: #software}

要对 Windows 服务器虚拟化启用**角色**选项，必须安装若干补丁。
1. 打开浏览器窗口，并浏览至 %sysdir%\Windows\wsv，通常为 C:\Windows\wsv。该文件夹中将有两个文件：
    * Windows6.0-KB939853-x64.msu
    * Windows6.0-KB939854-x64.msu
2. 可以按任意顺序安装这两个补丁。安装这两个补丁后，重新启动系统。

## 安装
{: #installation}

1. 系统重新启动后，需要将**角色**添加到系统。
2. 添加角色后，将显示**创建虚拟网络**对话框。
**注：**网络连接会短暂中断。
3. 选择**本地连接**，即专用网络适配器。单击**继续**。安装将继续，并要求您重新启动。
4. 系统重新启动后，请通过公共连接登录到系统。**注：**您必须使用用于安装此连接的相同登录凭证进行登录。您会失去与接口的网络连接。如果使用其他凭证，可能会收到以下错误：
    * *尝试配置 Windows 服务器虚拟化失败，错误代码 0x80078000。*
要解决此错误，请转至**开始 > 程序 > 管理工具 > Windows 虚拟化管理**以访问 Windows 虚拟化管理控制台。
5. 在右侧窗格中，单击相应的服务器。
6. 然后在“操作”窗格中，单击**虚拟网络管理器**。现在，您会看到**虚拟网络交换机管理**。
7. 在左侧窗格中，单击**添加新网络交换机**下的网络交换机。
8. 将该网络交换机重命名为“专用”。
9. 为**连接**选择“物理网络适配器”，然后选择第一个网络适配器。现在，所有网络协议都已从专用网络接口取消绑定。
10. **重要信息**：要将网络连接重新建立为专用，需要配置新的交换机设备，而**不是**配置专用接口。请转至**开始 > 设置 > 网络连接**。您会看到名为**本地连接 2** 的新设备。
11. 右键单击此连接并转至**属性**。
12. 选择 **ipv4** 及其属性。您需要将此设备配置为使用专用网络接口 IP 地址、网络掩码和 DNS 服务器。
13. 单击**确定 > 关闭**。此配置会在专用端重新启用联网。可以通过对专用 IP 执行 ping 操作，验证网络是否为专用。

通过 RDP 连接到专用 IP 以设置公用网络。

### 添加公共交换机
{: #adding-a-public-switch}

添加公共交换机所执行的过程与添加专用交换机的相同。
1. 返回到**虚拟网络交换机管理**，然后选择**添加新网络交换机**。
2. 选择**外部**作为网络交换机类型，然后单击**添加**。
3. 将交换机重命名为**公共**，然后选择**物理网络适配器**。
4. 选择第二个网络适配器，然后单击**确定**。此配置会导致公共端口不响应网络。请使用正确的设置来配置新的公共交换机接口，就像配置专用交换机时一样。
