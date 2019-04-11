---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-10"

subcollection: virtualization

keywords: hypervisor
---
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# 在 Active Directory 环境中运行 Hyper-V
{: #running-hyper-v-in-an-active-directory-environment}

<!--Running Hyper-V in an Active Directory environment is the best implementation of Hyper-V. Microsoft has truly shined with the ability to remotely manage a server.You can remotely manage the Hyper-V server allows for it to be installed on a Core installation of Windows freeing up those valuable resources from the system for use within the VM’s. When this is combined with an Active Directory Domain Controller
You manage all your Hyper-V servers from a single Hyper-V Manager that runs on any 2008 or Vista computer that is connected to the Domain.--> <!--Vista?? MS doesn't support Vista.-->

## 开始之前
{: #before-you-begin-running-hyper-v-in-an-active-directory-environment}

开始在 Active Directory 环境中运行 Hyper-V 之前，您需要满足以下条件：

* 有运行 Hyper-V 的 64 位 Windows 2008 Datacenter 服务器（操作系统为完整安装或核心安装）。
* 域上有用于管理 Hyper-V 的全局组。
* 具有域管理员访问权，支持您从正在使用的计算机对 Hyper-V 服务器进行更改。

## 配置 Hyper-V 服务器
{: #configuring-the-hyper-v-server}

1. 打开与 Hyper-V 服务器的管理连接。

2. 将 **Hyper-V** 组添加到**分布式 COM 用户**组。

3. 将 **Hyper-V** 组添加到 **CIMV2 和虚拟化**名称空间。

4. 将 **Hyper-V** 组添加到 Hyper-V 服务器上 Hyper-V 的授权存储。

5. 提供对 Hyper-V 服务器上 Hyper-V 目录的 **Hyper-V** 组许可权。

## 设置远程管理连接
{: #setting-up-remote-management-connection}

确保您已使用域管理特权登录到域上的计算机。

1. 转至**控制面板 > 管理工具 > 计算机管理**。

2. 在“操作”菜单中，选择**连接到另一台计算机**。

3. 输入服务器名称或 IP，然后单击**确定**。

## 向 Hyper-V 服务器添加分布式 COM 用户组
{: #adding-distributed-com-users-group-to-the-hyper-v-server}

首先需要将 Hyper-V 组添加到 Hyper-V 服务器上的“分布式 COM 用户”组。

1. 转至**系统工具 > 本地用户和组 > 组 > 分布式 COM 用户 > 添加到组**。

2. 单击**添加**并输入 Hyper-V 组的组名，然后单击**确定**。

## 授予对服务器的远程访问权以访问 CIMV2 和虚拟化名称空间
{: #granting-remote-access-to-the-server-for-cimv2-and-virtualization-namespaces}

可以更新对服务器的远程访问许可权以访问虚拟化和 CIMV2。

1. 在“计算机管理”窗口中，选择**服务和应用程序 > WMI 控制**。

2. 右键单击并转至**属性 > 安全 > 根 > CIMV2 > 安全**。

3. 添加 Hyper-V 组，然后选择该组并单击**高级**。

4. 确保已选择新组，然后单击**编辑**。

5. 将**应用于：**更改为**此名称空间和所有子名称空间**。

6. 对于**启用帐户和远程启用**，请确保选择了**允许**。

7. 选择**仅将这些许可权应用于此容器中的对象和/或容器**，然后单击**确定**。

8. 对虚拟化重复上述步骤。

## 更新授权存储
{: #updating-authorization-store}

Hyper-V 的授权存储是向 Hyper-V 提供域组的最后一个组件。

1. 通过从运行菜单或命令提示符运行 `azman.msc` 命令来打开授权管理器。

2. 从“操作”菜单中，选择**打开授权存储**。

3. 确保选择了 **XML**。现在，您需要远程访问 Hyper-V 服务器上的 `InitalStore.xml`。请使用以下路径：

`\HOSTNAME\c$\ProgramData\Microsoft\Windows\Hyper-V\InitialStore.xml`

4. 转至 **Hyper-V 服务 > 角色分配 > 管理员**。

5. 从**操作**菜单中，选择**分配用户和组 > 从 Windows 和 Active Directory**。

6. 添加 Hyper-V 组。

## 授予文件夹许可权
{: #granting-folder-permissions}

既然 Hyper-V 组已具有远程管理 Hyper-V 的完整许可权，现在需要授予对 `C:\Users\Public\Documents\Hyper-V` 文件夹的写许可权。

1. 打开“我的电脑”并转至以下地址：

`\HOSTNAME\c$\Users\Public\Documents`

2. 转至 **Hyper-V > 属性 > 安全**。

3. 添加 Hyper-V 组，并确保该组有能力对该目录中的文件执行读取、写入和执行操作。通常，分配**完全控制**会更容易。

## 最终完成配置
{: #finalizing-configuration}

所有配置更改都已完成。要最终完成配置，需要重新启动 Hyper-V 服务器。服务器恢复联机后，请通过本地 Hyper-V 管理器连接到该服务器。现在，您具有管理所有 VM 和 Hyper-V 服务的完全访问权。
