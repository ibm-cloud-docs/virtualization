---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-14"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# VMware ESXi 入门
{: #getting-started-with-vmware-esxi}

供应 ESX 服务器后，访问服务器的第一步是连接到 {{site.data.keyword.cloud}} 专用网络 VPN。缺省情况下，所有 {{site.data.keyword.cloud_notm}} 部署的 VMware 服务器都仅具有配置了 IP 地址的专用网络接口或绑定。此部署是一种额外的安全措施，用于限制新 ESX 服务器向公用因特网公开。由于添加了此安全性，因此服务器仅侦听 {{site.data.keyword.cloud_notm}} 专用 10.x.x.x IP 地址，该 IP 地址只能通过 {{site.data.keyword.cloud_notm}} VPN 或其他某个现有 {{site.data.keyword.cloud_notm}} 主机（有权访问新 VMware 服务器所在的同一专用 VLAN）进行访问。

有关 {{site.data.keyword.cloud_notm}} VPN 的更多信息，请参阅[虚拟专用联网 (VPN) 入门](/docs/infrastructure/iaas-vpn?topic=VPN-gettingstarted-with-virtual-private-networking#gettingstarted-with-virtual-private-networking)。

## 访问 ESX 主机
{: #accessing-your-esx-host}

连接到 {{site.data.keyword.cloud_notm}} VPN 后，可以与新的 VMware 服务器进行通信。ESX 由 vSphere Client 进行管理。可以通过在 Web 浏览器中输入专用网络 IP 地址，以在新的 VMware 服务器中进行检索，然后单击所生成页面上的“下载 vSphere Client”链接。

下载 vSphere Client 并将其安装在本地工作站上后，可以启动该应用程序，并输入服务器专用地址和登录凭证。在 {{site.data.keyword.slportal_full}} 的“设备”页面上，单击“密码”选项卡后可查看您的登录凭证。

1. 在 vSphere Client 的相应字段中，输入服务器的专用 IP 地址、root 用户和密码，然后单击**登录**以进行连接。
2. 既然已连接到服务器，现在请通过选择**清单**转至新的 ESX 主机。
3. 在生成的页面上，您的 ESX 主机会显示为可供配置的节点。在此，您有多种方法可部署虚拟机。一种方法是将首选操作系统的安装 ISO 上传到服务器本地数据存储。ISO 上传后，可以在创建 VM 时选择该 ISO 作为安装介质。  

## 上传 ISO
{: #uploading-an-iso}

完成以下步骤将 ISO 上传到服务器的数据存储。

1. 在左侧窗格中突出显示服务器（由“服务器”图标及其专用 IP 地址表示），然后选择右侧窗格中的**配置**选项卡。
2. 选择**硬件 > 存储器**。确保**视图**设置为**数据存储**。
3. 右键单击相应的数据存储，并单击**浏览数据存储**。
4. 生成的页面具有文件管理器，可用于浏览数据存储中的文件，以及将文件上传到数据存储/从数据存储下载文件。  
  * 要上传 ISO，请单击“卷”图标（带有向上箭头），然后选择**上传文件**。
5. 选择本地文件系统上要上传到数据存储的 ISO 文件，然后单击**打开**。
6. 您选择的 ISO 现在已位于数据存储中。
7. 既然安装介质已位于 VMware 服务器上，现在可以继续[创建虚拟机](/docs/infrastructure/vmware?topic=VMware-create-esx-vm#create-esx-vm)。
