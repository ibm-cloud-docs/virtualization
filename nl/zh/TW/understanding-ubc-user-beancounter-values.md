---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 瞭解 UBC/使用者 Bean 計數器值
{: #understanding-ubc-user-beancounter-values}

如果不瞭解 UBC，請使用「SLM 模式」。SLM 比較容易，並提供相同類型的控制項。

|名稱|類型|說明|
|---|---|---|
|*主要參數*|||
|`numproc`|*系統*|處理程序及執行緒的數目|
|`numtcpsock`|*系統*|TCP Socket 的數目|
|`numothersock`|*系統*|TCP 以外的 Socket 數目|
|`vmguardpages`|*系統*|記憶體配置保證|
|`cpuunits`|*cpu*|CPU 電源|
|`diskspace`|*磁碟*|磁碟空間配額|
|`rate`|*網路*|網路頻寬|
|`ratebound`|*網路*|是否限制網路頻寬|
|*其他參數*|||
|`kmemsize`|*系統*|無法交換之核心記憶體的大小，而此記憶體是配置給這個「容器」中的處理程序|
|`tcpsndbuf`|*系統*|TCP 傳送緩衝區的大小總計|
|`tcprcvbuf`|*系統*|TCP 接收緩衝區的大小總計|
|`othersockbuf`|*系統*|UNIX 網域 Socket 緩衝區、UDP 及其他資料封包通訊協定傳送緩衝區的大小總計|
|`dgramrcvbuf`|*系統*|UDP 及其他資料封包通訊協定的接收緩衝區|
|`oomguardpages`|*系統*|保證的記憶體數量，以防記憶體超出預訂數量（保證不會發生記憶體不足的情況）|
|`privvmpages`|*系統*|記憶體配置限制|
|`lockedpages`|*系統*|不容許交換處理程序頁面（透過 [mlock(2) ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](http://linux.die.net/man/2/mlock) 鎖定的頁面）|
|`shmpages`|*系統*|頁面中共用記憶體（交互處理程序通訊或 IPC、共用的匿名對映及 tmpfs 物件）的大小總計|
|`physpages`|*系統*|處理程序使用的 RAM 頁面總數|
|`numfile`|*系統*|開啟的檔案數目|
|`numflock`|*系統*|檔案鎖定的數目|
|`numpty`|*系統*|虛擬終端機的數目|
|`numsiginfo`|*系統*|siginfo 結構數|
|`dcachesize`|*系統*|記憶體中鎖定之 dentry 及 inode 結構大的小總計|
|`numiptent`|*系統*|NETFILTER（IP 封包過濾）項目的數目|
|`cpulimit`|*cpu*|CPU 耗用量的限制|
|`diskinodes`|*磁碟*|磁碟 inode（檔案系統物件）配額|
|`quotatime`|*磁碟*|磁碟限額寬限期|
|`quotaugidlimit`|*磁碟*|uid 及 gid 帳戶登錄次數的限制|
<caption>表 1. SLM 參數說明</caption>

您可以在伺服器上的 PIM 內存取 VzLinuxUBCMgmt.pdf，或以 HTML 檢視它，方法為移至：**管理 > 支援 > 下載 > Management of UBC Resources Administrator's Guide**

如需 UBC 的相關資訊，請參閱 [OpenVZ Virtuozzo Containers, Category: UBC ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](http://wiki.openvz.org/Category:UBC)。
**附註：**並非 OpenVZ 中的一切都與 Virtuozzo 相同。
