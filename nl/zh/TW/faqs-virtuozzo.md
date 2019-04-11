---



copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# 常見問題：Virtuozzo
{: #faqs-virtuozzo}

## 需要 Virtuozzo 的靜態或可攜式 IP 位址嗎？
{: #do-i-need-static-or-portable-ip-addresses-for-virtuozzo-}

* 靜態 IP 位址：如果只有一個硬體節點（每個 VLAN 或總計），請使用靜態 IP 位址
* 可攜式 IP 位址：
    * 遞送至 VLAN：如果您在同一個 VLAN 內具有多個主機節點，請使用此方法。您也可以使用此方法，將容器移轉至相同 VLAN 內的不同硬體節點，而不會變更容器的 IP。
    * VLAN 上的次要：此方法可運作，但您不需要次要閘道。由於主機 ID、閘道及播送，您失去了 3 個 IP 位址。

每個 IP 的成本皆可變更，取決於遞送類型。在您選取要為其訂購 IP 位址的伺服器之後，即會在入口網站中顯示價格。可以隨時開立支援問題單來變更遞送。
{:tip}

## 可以在無關閉時間的情況下，將 Virtuozzo 3 Container 移轉至 Virtuozzo Container 4 硬體節點嗎？
{: #can-i-migrate-a-virtuozzo-3-container-to-a-virtuozzo-container-4-hardware-node-with-no-downtime-}

**Linux：**

是。依預設，Virtuozzo Containers for Linux 4 會在相容模式下執行。如果 Virtuozzo Containers 不是在相容模式下執行，則您可以透過 SSH 在硬體節點上使用下列指令來啟動它。

`/usr/sbin/vzagent_compat_ctl start`

**Windows（不支援）：**

使用 Virtuozzo Containers 4 for Windows 時，您在轉移期間會有關閉時間（介於 20 秒至 5 分鐘之間），因為沒有線上選項來傳遞。

如果使用 Virtuozzo 3.0 for Windows，則需要將硬體節點升級為至少為 Virtuozzo 3.5.1 for Windows。

Virtuozzo Containers 4 for Windows 依預設會在相容模式下執行。

也可以將備份從 3.5.1 還原至 Virtuozzo Containers 4 硬體節點，方法為將完整備份複製至目的地節點、執行 `vzbackupsync.exe`，然後將備份還原為一般備份。

請確定新的硬體節點上已安裝相同的範本。
{:tip}

## 容器彼此隔離，也與主機隔離嗎？
{: #are-containers-isolated-from-each-other-and-the-host-}

您的容器受到限制，無法從自己的環境執行 `chroot("../../../")` 來載入 LKM，也無法共用「IPC 資源」（號誌集及共用記憶體區段）。

對於 Virtuozzo 如何管理資源及隔離處理程序，您無需顧慮安全。使用橋接網路配置，相同 VLAN 中的所有容器（在同一個硬體節點上）都可以看到彼此的資料流量。強烈建議使用遞送模式。

## 為何無法在 Virtuozzo 中快取 RedHat 及 SUSE 作業系統範本？
{: #why-can-t-i-cache-redhat-and-suse-os-templates-in-virtuozzo-}

如果使用 Virubozzo，則您可能會在快取 RedHat 及 SUSE 作業系統範本時遭遇問題。這些作業系統範本需要額外的授權，但 {{site.data.keyword.cloud_notm}} 不提供這些授權。如果您收到下列任何錯誤，則需要具有自己的帳戶搭配個別供應商，並在 /etc/vztt/url.map 內配置對應變數（Virtuozzo 3 中的 /etc/vztt/vztt.conf）。

||||
|---|---|---|
|時間戳記|按一下這裡以開啟/關閉作業詳細資料。更新作業系統範本快取|**失敗**|
|時間戳記|搭配 Env(s) "virtuozzo00.softlayer.local" 及套件的作業更新。redhat-as4-x86_64 已啟動||
|時間戳記|搭配 Env(s) "virtuozzo00.softlayer.local" 的作業更新已完成，但發生錯誤：無法更新套件：執行程式失敗：錯誤：URL $RH_SERVER/download/mirrors/redhat-as4 內容有未定義的變數。您可以在 /etc/vztt/url.map 定義此變數。|**失敗**|
|時間戳記|按一下這裡以開啟/關閉作業詳細資料。更新作業系統範本快取|**失敗**|
|時間戳記|搭配 Env(s) "virtuozzo00.softlayer.local" 及套件的作業更新。redhat-el5-x86_64 已啟動||
|時間戳記|搭配 Env(s) "virtuozzo00.softlayer.local" 的作業更新已完成，但發生錯誤：無法更新套件：執行程式失敗：錯誤：URL $RH_SERVER/download/mirrors/redhat-el5 內容有未定義的變數。您可以在 /etc/vztt/url.map 定義此變數。如需詳細資料，請參閱《Virtuozzo 安裝手冊》。|**失敗**|
|時間戳記|按一下這裡以開啟/關閉作業詳細資料。更新作業系統範本快取|**失敗**|
|時間戳記|搭配 Env(s) "virtuozzo00.softlayer.local" 及套件的作業更新。sles-10-x86_64 已啟動||
|時間戳記|搭配 Env(s) "virtuozzo00.softlayer.local" 的作業更新已完成，但發生錯誤：無法更新套件：執行程式失敗：錯誤：URL $SLES_SERVER/download/mirrors/suse-es10 內容有未定義的變數。您可以在 /etc/vztt/url.map 定義此變數。如需詳細資料，請參閱《Virtuozzo 安裝手冊》。|**失敗**|
|時間戳記|按一下這裡以開啟/關閉作業詳細資料。更新作業系統範本快取|**失敗**|
|時間戳記|搭配 Env(s) "virtuozzo01.softlayer.local" 及套件的作業更新。redhat-as4-x86_64 已啟動||
|時間戳記|搭配 Env(s) "virtuozzo01.softlayer.local" 的作業更新已完成，但發生錯誤：無法更新套件：執行程式失敗：錯誤：URL $RH_SERVER/download/mirrors/redhat-as4 內容有未定義的變數。您可以在 /etc/vztt/url.map 定義此變數。如需詳細資料，請參閱《Virtuozzo 安裝手冊》。|**失敗**|
|時間戳記|按一下這裡以開啟/關閉作業詳細資料。更新作業系統範本快取|**失敗**|
|時間戳記|搭配 Env(s) "virtuozzo01.softlayer.local" 及套件的作業更新。redhat-el5-x86_64 已啟動||
|時間戳記|搭配 Env(s) "virtuozzo01.softlayer.local" 的作業更新已完成，但發生錯誤：無法更新套件：執行程式失敗：錯誤：URL $RH_SERVER/download/mirrors/redhat-el5 內容有未定義的變數。您可以在 /etc/vztt/url.map 定義此變數。|**失敗**|
|時間戳記|按一下這裡以開啟/關閉作業詳細資料。更新作業系統範本快取          失敗<br/><br/>2008 年 10 月 29 日下午 01:16:56     搭配 Env(s) "virtuozzo01.softlayer.local" 及套件的作業更新。sles-10-x86_64 已啟動||
|時間戳記|搭配 Env(s) "virtuozzo01.softlayer.local" 的作業更新已完成，但發生錯誤：無法更新套件：執行程式失敗：錯誤：URL $SLES_SERVER/download/mirrors/suse-es10 內容有未定義的變數。|**失敗**|
|時間戳記|正在完成處理程序|**失敗**|

## 為何我可以連線測試 ServiceVE，但無法透過 SSH 登入 (PIM/PMC)？
{: #why-can-i-ping-my-serviceve-but-cannot-log-in-to-via-ssh-pim-pmc-}

請確定除了「入口網站」中列出的 IP 位址（1 個公用 IP 及 1 個專用 IP [位於 10.0.0.0/8]）以外，主機節點沒有指派其他任何的 IP 位址。

如果您有任何在主機節點上配置的範圍檔案，則需要移除這些檔案並重新啟動網路。

    [root@virtuozzo ~]# mv -vi /etc/sysconfig/network-scripts/eth[0-9]-range[0-9]* ~/.sl-orig-configs/

    [root@virtuozzo ~]# /sbin/service network restart


`vzcp` 未在「服務 VE」內啟動。請從硬體節點啟動「服務 VE」內的 `vzcp`。

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp status

    vzcp is stopped

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp start

    Starting vzcpd: [  OK  ]

    Starting vzcp: [  OK  ]

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp status

    vzcp (pid 3775 3774 3771) is running...

    [root@virtuozzo ~]#
