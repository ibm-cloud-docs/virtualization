---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-05"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 更新 Veeam Backup and Replication 应用程序
{: #updating-veeam-backup-and-replication-application}

## 概述
{: #overview}

Veeam Backup and Replication 应用程序有新的更新可用时，请使用以下步骤来更新在服务器上运行的该产品。

### 先决条件
{: #prerequisites-updating-veeam-backup-and-replication-application}

* 免费的 Veeam 帐户。您可以在 [Veeam Registration ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://www.veeam.com/signin.html) 上进行注册。
* Internet Explorer、Chrome、Firefox 或 Safari。

### 检查更新
{: #checking-for-updates}

Veeam Backup and Replication 应用程序包含更新检查程序。要确保收到更新通知，请转至左上角的菜单图标，并单击**常规选项** > **通知**。确保选择**定期检查产品和系统管理程序更新**。

### 更新 Veeam
{: #updating-veeam}

要启动更新，请执行以下步骤。
1. 打开**备份基础架构**视图。
2. 在清单窗格中，转至**受管服务器节点** > **未执行的更新**。
3. 单击更新链接。

启动更新之前，有几点需要注意：

* 确保所有现有作业的当前运行均已完成。如果某些作业失败，请重新运行失败的作业。
* 确保没有作业正在运行，包括复原会话、即时 VM 恢复会话和 SureBackup 作业。建议不要停止正在运行的作业。
* 临时禁用任何定期和备份复制作业，以阻止它们在升级期间启动。

### 后续步骤
{: #next-steps-updating-veeam-backup-and-replication-application}

现在，可以解压缩下载的更新，然后双击 Veeam 更新安装程序。更新将同时推送到本地 Veeam 服务器以及 Veeam 代理程序所在的系统管理程序。此过程完成后，启动 Veeam Backup and Replication 并重新启用备份作业。
