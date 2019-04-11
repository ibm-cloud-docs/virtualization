---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Parallels Server 4 裸機伺服器的概觀
{: #overview-of-parallels-server-4-bare-metal}

Parallels Server Bare Metal 是一種虛擬化解決方案，可提供 Virtuozzo 的硬體虛擬化及軟體虛擬化，同時提供適用於虛擬機器及容器的技術*。
{:shortdesc}

**指令行介面**

除了一般 Virtuozzo 指令之外，還可以使用其他指令：`pctl`、`pmigrate`、`pstat` 及 `prl_disk_tool`。

**移轉**

您現在可以將 Virtuozzo Containers 移轉至虛擬機器、將實體伺服器連接至 Virtuozzo Containers，以及將實體伺服器移轉至虛擬機器，以容許合併服務。這項功能也容許在裸機伺服器之間移轉容器或 VM、轉換在不同虛擬化環境 (V2V) 上建立的 VM，以及從範本複製或部署 Windows VM 時變更 Windows SID。

**IP 位址使用情形及 VLAN**

您可以使用 VM 中的 Parallels Tools，將 VM 指派給來自實體伺服器的 IP。此外，PSBM 容許在 VM 內建立虛擬交換器及 VLAN，以更安全地保護 VM 內的網路資料流量。

**執行指令**

您可以直接在 Virtuozzo Containers 內執行實體伺服器中的原始指令，且現在可在 VM 內這樣做（如果 Parallels Tools 安裝在 VM 內的話），包括使用者密碼重設。

**處理程序統計作業**

您可以使用 Parallels Server Bare Metal，快速增加及減少配置給 VM 之資源的優先順序（CPU、磁碟 I/O 優先順序）。

**備份**

Parallels Server Bare Metal 提供備份及還原功能，用來備份及還原本端裸機伺服器或遠端裸機伺服器上的 VM 及容器，以包括完整及增量備份。

**網路統計作業**

您可以使用 Parallels Server Bare Metal，根據現行及歷程網路傳輸量輕鬆地檢視及尋找 VM 或「容器」。

**網路**

Parallels Server Bare Metal 使用「可攜式」IP 位址，而 Virtuozzo 則會使用「可攜式」或「靜態」IP 位址（視配置而定）。

\* {{site.data.keyword.BluSoftlayer_full}} 僅授權 Parallels Server 4 Bare Metal 上的硬體虛擬機器，除非訂單表格上另有指示。
_VM_ = 虛擬機器。_V_ = VPS 或容器。
