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

# 在 Active Directory 環境中執行 Hyper-V
{: #running-hyper-v-in-an-active-directory-environment}

<!--Running Hyper-V in an Active Directory environment is the best implementation of Hyper-V. Microsoft has truly shined with the ability to remotely manage a server.You can remotely manage the Hyper-V server allows for it to be installed on a Core installation of Windows freeing up those valuable resources from the system for use within the VM’s. When this is combined with an Active Directory Domain Controller
You manage all your Hyper-V servers from a single Hyper-V Manager that runs on any 2008 or Vista computer that is connected to the Domain.--> <!--Vista?? MS doesn't support Vista.-->

## 開始之前
{: #before-you-begin-running-hyper-v-in-an-active-directory-environment}

在 Active Directory 環境中開始執行 Hyper-V 之前，您需要下列項目：

* 執行 Hyper-V 的 64 位元 Windows 2008 Datacenter 伺服器（作業系統的「完整」或「核心」安裝）。
* 網域上用來管理 Hyper-V 的廣域群組。
* 「網域管理」存取權，用來從您工作所在的電腦啟用 Hyper-V 伺服器上的變更。

## 配置 Hyper-V 伺服器
{: #configuring-the-hyper-v-server}

1. 開啟 Hyper-V 伺服器的管理連接。

2. 將 **Hyper-V** 群組新增至**分散式 COM 使用者**群組。

3. 將 **Hyper-V** 群組新增至 **CIMV2 及虛擬化**名稱空間。

4. 將 **Hyper-V** 群組新增至 Hyper-V 伺服器上 Hyper-V 的「授權」儲存庫。

5. 提供 Hyper-V 伺服器上「Hyper-V 目錄」的 **Hyper-V** 群組許可權。

## 設定遠端管理連線
{: #setting-up-remote-management-connection}

確定您已登入網域上具有「網域管理」專用權的電腦。

1. 移至**控制台 > 系統管理工具 > 電腦管理**。

2. 在「動作」功能表中，選取**連線到另一台電腦**。

3. 輸入伺服器名稱或 IP ，然後按一下**確定**。

## 將分散式 COM 使用者群組新增至 Hyper-V 伺服器
{: #adding-distributed-com-users-group-to-the-hyper-v-server}

首先，您需要將 Hyper-V 群組新增至 Hyper-V 伺服器上的「分散式 COM 使用者」群組。

1. 移至**系統工具 > 本端使用者和群組 > 群組 > 分散式 COM 使用者 > 新增至群組**。

2. 按一下**新增**，然後輸入 Hyper-V 群組的群組名稱，然後按一下**確定**。

## 將伺服器的遠端存取權授與 CIMV2 及虛擬化名稱空間
{: #granting-remote-access-to-the-server-for-cimv2-and-virtualization-namespaces}

您可以更新「虛擬化」和 CIMV2 的「遠端」伺服器存取權。

1. 從「電腦管理」視窗中，選取**服務及應用程式 > WMI 控制**。

2. 按一下滑鼠右鍵並移至**內容 > 安全 > Root > CIMV2 > 安全**。

3. 新增 Hyper-V 群組，然後選取它，並按一下**進階**。

4. 確定已選取新群組，然後按一下**編輯**。

5. 將**套用至：**變更為**名稱空間及所有子名稱空間**。

6. 對於**啟用帳戶及遠端啟用**，確定選取**容許**。

7. 選取**僅將這些許可權套用至此容器內的物件及/或容器**，然後按一下**確定**。

8. 針對「虛擬化」重複這些步驟。

## 更新授權儲存庫
{: #updating-authorization-store}

Hyper-V 的授權儲存庫是將網域群組提供給 Hyper-V 的最終元件。

1. 從執行功能表或命令提示字元執行指令 `azman.msc`，以開啟「授權管理程式」。

2. 從「動作」功能表中，選取**開啟授權儲存庫**。

3. 確定已選取 **XML**。現在，您需要遠端存取 Hyper-V 伺服器上的 `InitalStore.xml`。請使用下列路徑：

`\HOSTNAME\c$\ProgramData\Microsoft\Windows\Hyper-V\InitialStore.xml`

4. 移至 **Hyper-V 服務 > 角色指派 > 管理者**。

5. 從**動作**功能表中，選取**指派使用者及群組 > 從 Windows 及 Active Directory**。

6. 新增 Hyper-V 群組。

## 授與資料夾許可權
{: #granting-folder-permissions}

既然 Hyper-V 群組具有完整許可權，可在遠端管理 Hyper-V，則您需要提供寫入至 `C:\Users\Public\Documents\Hyper-V` 資料夾的許可權。

1. 開啟「我的電腦」並移至下列位址：

`\HOSTNAME\c$\Users\Public\Documents`

2. 移至 **Hyper-V > 內容 > 安全**

3. 新增 Hyper-V 群組，並確定可以從該目錄中「讀取」、「寫入」及「執行」檔案。一般而言，指派**完整控制**更容易。

## 完成配置
{: #finalizing-configuration}

所有配置變更完成。若要完成配置，您需要重新啟動 Hyper-V 伺服器。在伺服器回到線上之後，請從您的「本端 Hyper-V 管理程式」連接至該伺服器。您現在具有完整存取權，來管理所有 VM 及 Hyper-V 服務。
