---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization
---

# Parallels Virtuozzo Containers 4.0 快速參照手冊
{: #parallels-virtuozzo-containers-4-0-quick-reference-guide}

您可以使用此資訊，來尋找 Virtuozzo 容器公用程式及常用術語。

## 一般公用程式
{: #general-utilities}

一般公用程式適用於日常維護作業。

|一般公用程式||
|---|---|
|vzctl|此公用程式用來控制「容器」。|
|vzlist|此公用程式用來檢視「節點」上存在的「容器」清單，並隨附其他資訊。|
|vzquota|此公用程式用來控制 Virtuozzo Containers 磁碟限額。|
{: caption="表 1. 一般公用程式" caption-side="top"}

## 授權公用程式
{: #licensing-utilities}

授權公用程式是用來安裝新授權、檢視授權狀態，或產生新授權的授權要求。

|授權公用程式||
|---|---|
|vzlicview|此公用程式用來顯示 Virtuozzo 授權狀態和參數。|
|vzlicload|此公用程式用來管理「硬體節點」上的 Virtuozzo 授權。|
|vzlicupdate|此公用程式用來啟動 Virtuozzo Containers 安裝、更新「硬體節點」上安裝的 Virtuozzo 授權，或將 Virtuozzo 授權從「來源節點」傳送至「目的地節點」。|
{: caption="表 2. 授權公用程式" caption-side="top"}

## 容器移轉公用程式
{: #container-migration-utilities}

容器移轉工具用來在硬體節點之間或某個硬體節點內移轉容器。

|容器移轉公用程式||
|---|---|
|vzmigrate|此公用程式用於將容器從某個硬體節點移轉至另一個硬體節點。|
|vzmlocal|此公用程式用於進行容器的本端複製或移動。|
|vzp2v|此公用程式用來將實體伺服器移轉至節點上的容器。|
|vzv2p|此公用程式用來將容器移轉至實體伺服器。|
{: caption="表 3. 容器移轉公用程式" caption-side="top"}

## 容器備份公用程式
{: #container-backup-utilities}

容器備份公用程式是用來備份及還原容器專用區域、配置檔、動作 Script 及配額資訊。

|容器備份公用程式||
|---|---|
|vzbackup|此公用程式用來備份容器。|
|vzrestore|此公用程式用來還原備份的容器。|
|vzabackup|此公用程式用來備份「硬體節點」及其「容器」。此公用程式需要 Parallels Agent 軟體。|
|vzarestore|此公用程式用來還原備份的「硬體節點」及「容器」。此公用程式需要 Parallels Agent 軟體。|
{: caption="表 4. 容器備份公用程式" caption-side="top"}

## 範本管理公用程式
{: #template-management-utilities}

範本管理工具是用來建立範本，以及維護應用程式並將其安裝至容器。

|範本管理公用程式||
|---|---|
|vzpkg|此公用程式用來在容器內或硬體節點本身上管理作業系統及應用程式 EZ 範本。|
|vzmktmpl|此公用程式用來建立作業系統及應用程式 EZ 範本。|
|vzveconvert|此公用程式用來將基於 Virtuozzo 標準範本的容器轉換為 EZ 範本型容器。|
|vzpkgproxy|此公用程式用來建立快取 Proxy 伺服器，以處理作業系統和應用程式 EZ 範本。|
|vzrhnproxy|此公用程式用來建立 RHN Proxy 伺服器，以處理 RHEL 4 和 RHEL 5 作業系統 EZ 範本中所包含的套件。|
|vzpkgls|此公用程式用來取得可在硬體節點上及容器中使用的範本清單。|
|vzpkginfo|此公用程式用來取得硬體節點上所安裝之任何範本的相關資訊。|
|vzpkgcreat|此公用程式用來從二進位 RPM 或 DEB 檔案建立新的套件集。|
|vzpkgadd|此公用程式用來將範本新增至「容器」。|
|vzpkglink|此公用程式用來將容器內的實際檔案取代為節點上檔案的符號鏈結。|
|vzpkgrm|此公用程式用來從容器中移除範本。|
|vzpkgcache|此公用程式用來在安裝新範本之後更新一組預先安裝的容器保存檔。|
{: caption="表 5. 範本管理公用程式" caption-side="top"}

## 增補工具
{: #supplementary-tools}

增補工具用於硬體節點及容器環境定義中的細項作業。

|增補工具||
|---|---|
|vzup2date|此公用程式用來更新 Virtuozzo 軟體和範本。|
|vzup2date-mirror|此公用程式用來建立 Virtuozzo 正式儲存庫的本端鏡映。|
|vzfsutil|此公用程式用於 VZFS 最佳化和一致性檢查。|
|vzcache|此公用程式用來快取不同容器中相同的檔案來取得額外的磁碟空間。|
|vzsveinstall|此公用程式用來在「硬體節點」上建立「服務容器」。|
|vzsveupgrade|此公用程式用來更新「服務容器」內的套件。|
|vzps|此公用程式用來當作標準 ps 和最上層公用程式運作，並新增容器相關功能。|
|vztop|此公用程式用來當作標準 ps 和最上層公用程式運作，並新增容器相關功能。|
|vzsetxinetd|此公用程式用來在獨立式模式與 `xinetddependent` 模式之間切換部分服務。|
|vzdqcheck|此公用程式用來從配額的觀點列印檔案空間的現行用量。|
|vzdqdump|此公用程式用來從核心或配額檔傾出「容器」使用者或群組配額限制及寬限時間，或用於將它們載入至配額檔。|
|vzdqload|此公用程式用來從核心或配額檔傾出「容器」使用者或群組配額限制及寬限時間，或用於將它們載入至配額檔。|
|vznetstat|此公用程式可依容器列印網路資料流量的用量統計資料。|
|vzcpucheck|此公用程式用於依容器檢查 CPU 使用率。|
|vzmemcheck|此公用程式用於檢查硬體節點及容器現行記憶體參數。|
|vzcalc|此公用程式用來依容器計算資源用量。|
|vzcheckovr|此公用程式用來檢查總計資源控制設定的現行系統過量使用及安全。|
|vzstat|此公用程式用來即時監視硬體節點及容器資源耗用量。|
|vzpid|此公用程式用來列印處理程序所屬的容器 ID。|
|vzsplit|此公用程式用來產生容器配置檔範例，將硬體節點「分割」成相同的部分。|
|vzcfgscale|此公用程式用來調整容器配置。|
|vzcfgvalidate|此公用程式用來驗證容器配置檔的正確性。|
|vzcfgconvert|此公用程式用來將 Virtuozzo 2.0.2 容器配置檔轉換為 Virtuozzo 2.5.x 格式。|
|vzstatrep|此公用程式用來分析 vzlmond 所收集的日誌，並根據這些日誌產生統計資料報告（以文字和圖形表示）。|
|vzreport|此公用程式用來繪製問題報告，並自動將它傳送給 Parallels 支援團隊。|
|vzhwcalc|此公用程式用來掃描任何 Linux 伺服器的主要資源，並建立在其中指定此資訊的檔案。|
|vzveconvert|此公用程式用來將基於 Virtuozzo 標準作業系統範本的容器轉換為 EZ 範本型容器。|
|vznetcfg|此公用程式用來在硬體節點上管理網路裝置。|
|vzmtemplate|此公用程式用來將已安裝的作業系統及應用程式範本從某個硬體節點移轉至另一個硬體節點。|
{: caption="表 6. 增補工具" caption-side="top"}

## 常用術語
{: #common-terms}

|常用術語||
|---|---|
|硬體節點|硬體節點（或 HN）是管理容器的實體電腦。|
|容器|容器的功能與隔離的獨立式伺服器相同，但具有自己的 IP 位址、處理程序、檔案、使用者、自己的版本、自己的配置檔、自己的應用程式、系統檔案庫，以及其他伺服器功能。容器共用硬體節點及相同的作業系統核心，但彼此隔離。每一個容器都有以 1 開始的唯一數字 ID。|
|主機作業系統|主機作業系統（或「主機」）是安裝在硬體節點上的作業系統。它也稱為容器 0，對照於具有以 1 開始之 ID 的實際容器。|
|範本|範本（或套件集）是一組已重新套裝以透過 Virtuozzo 檔案系統 (VZFS) 進行裝載的原始應用程式檔案。在 Virtuozzo 中提供兩種類型的範本：可以用來在建立新容器時作為其基礎的作業系統範本，以及可在建立容器之後新增至容器的應用程式範本。|
|PIM|Parallels Infrastructure Manager（或 PIM）是一種專為「主機」管理者設計的 Web 型管理工具。|
|PMC|Parallels Management Console（或 PMC）是一種遠端管理工具，其中具有專為「主機」管理者設計的圖形使用者介面。|
|PPP|Parallels Power Panel 是一種專為「容器」擁有者設計的 Web 型管理工具。|
{: caption="表 7. 常用術語" caption-side="top"}
