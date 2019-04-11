---



copyright:
  years: 2014, 2018
lastupdated: "2018-01-09"

subcollection: virtualization


---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# 開始使用 Virtuozzo
{: #getting-started-with-virtuozzo}

## 存取 Parallels Infrastructure Manager (PIM)
{: #accessing-the-parallels-infrastructure-manager-pim-}

1. 從 [{{site.data.keyword.slportal_full}} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://control.softlayer.com/){: new_window} 取得裸機伺服器的相關資訊。
    1. 從**裝置**功能表中，選取**裝置清單**。
    2. 按一下您的伺服器。
    3. 尋找公用 IP 位址及密碼。
5. 在偏好瀏覽器中使用 HTTPS 移至伺服器的公用 IP 位址。請使用 Firefox、Opera 或 Internet Explorer，透過 PIM 來管理 Virtuozzo。

需要 JavaScript。若沒有 JavaScript，可能會將您重新導向至不存在的頁面，而且 PIM 將無法運作。
{:tip}

6. 輸入您的使用者名稱和密碼來登入 PIM。

使用者名稱為 "root"，密碼為伺服器的 root 密碼，如 {{site.data.keyword.slportal}}中所列。
{:tip}

## 安裝和快取範本
{: #installing-and-caching-templates}

1. 在 PIM 畫面中，選取**管理** > **更新**。
2. 移至您要更新的伺服器。
3. 按一下**範本更新項目**。
4. 選取任何您要安裝或更新的其他範本。
5. 若要選取您要更新的伺服器，請按一下**範本** > **作業系統範本**。
6. 選取您要在伺服器及快取上使用的作業系統範本。一般而言，您不需要快取所有範本，即可開始。只快取您要使用的範本。這些範本會佔用 `/vz` 分割區中的大量空間。
7. 如果只有一個節點，請按**下一個快取**。
8. 等待完整範本 RPM 下載及安裝。請按一下**詳細資料**來檢視快取狀態。

## 設定容器的網路範圍
{: #setting-up-network-range-for-containers}

當伺服器下載和快取範本時，您可以設定容器的網路範圍。

1. 按一下**選取** > **網路**。
2. 選取**新建 IP 範圍**。
3. 為您的容器新增其他 IP 位址。如果您尚未購買虛擬伺服器的其他 IP 位址，請移至 [{{site.data.keyword.slportal}} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://control.softlayer.com/){: new_window}。選取**網路** > **IP 管理** > **VLAN** > **訂購 IP**。
4. 移至硬體節點，然後按一下**容器** > **新建容器**。
5. 視需要完成資訊。對於此範例，請使用 slm.512MB 作為預設值。
6. 將主機名稱輸入為完整網域名稱、新增 DNS 伺服器，以及新增搜尋網域（必要的話）。
7. 檢閱資源參數。

請注意，不要新增太多參數，導致伺服器缺乏資源，因而禁止容器處理資料、其磁碟空間或記憶體的能力。使伺服器缺乏資源會導致它減慢或非預期地停止處理程序。如果您不瞭解這些設定，請聯絡您的伺服器管理者或[協力廠商管理公司 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://developer.ibm.com/bluemix/){: new_window}。
{:tip}

8. 按一下**驗證**，然後再按**下一步**。驗證可確保您的設定不會彼此衝突。
9. 選取您要安裝的應用程式，然後按**下一步**。
10. 檢閱您的配置，然後按一下**建立**。即會建立容器。
11. 您現在可以回到**容器**，以檢視新容器並取得自動指派的 IP 位址（來自您之前新增的 IP）。
12. 您現在可以登入伺服器並使用它。

        myname/myserver[0]~$ ssh 1.234.123.4
        Warning: Permanently added '1.234.123.4' (DSA) to the list of known hosts.
        root@1.234.123.4's password:
        [root@test01 ~]# ip a s
        1: lo: mtu 16436 qdisc noqueue
            link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
            inet 127.0.0.1/8 scope host lo
            inet6 ::1/128 scope host
              valid_lft forever preferred_lft forever
        3: venet0: mtu 1500 qdisc noqueue
            link/void
            inet 127.0.0.1/32 scope host venet0
            inet 1.234.123.4/32 brd 1.234.123.4 scope global venet0:0
        [root@test01 ~]# ps auxww
        USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
        root         1  0.0  0.1  10328   744 ?        Ss   13:23   0:00 init [3]
        root     20419  0.0  0.1  12580   632 ?        Sroot     21726  0.0  0.2  60532  1220 ?        Ss   13:23   0:00 /usr/sbin/sshd
        root     21737  0.0  0.1  22512   892 ?        Ss   13:23   0:00 xinetd -stayalive -pidfile /var/run/xinetd.pid
        root     21756  0.0  0.3  62732  2052 ?        Ss   13:23   0:00 sendmail: accepting connections
        smmsp    21764  0.0  0.3  57608  1752 ?        Ss   13:23   0:00 sendmail: Queue runner@01:00:00 for /var/spool/clientmqueue
        root     21776  0.0  2.0 283796 10728 ?        Ss   13:23   0:00 /usr/sbin/httpd
        root     21786  0.0  0.2  20832  1144 ?        Ss   13:23   0:00 crond
        root     21796  0.0  0.1  46648   800 ?        Ss   13:23   0:00 /usr/sbin/saslauthd -m /var/run/saslauthd -a pam -n 2
        apache   21797  0.0  1.1 283932  5788 ?        S    13:23   0:00 /usr/sbin/httpd
        root     21798  0.0  0.1  46648   544 ?        S    13:23   0:00 /usr/sbin/saslauthd -m /var/run/saslauthd -a pam -n 2
        root     23573  0.0  0.6  85876  3240 ?        Rs   13:45   0:00 sshd: root@pts/0
        root     23594  0.0  0.3  12044  1604 pts/0    Ss   13:45   0:00 -bash
        root     23626  0.0  0.1  10700   952 pts/0    R+   13:45   0:00 ps auxww

        [root@test01 ~]# df -h
        Filesystem            Size  Used Avail Use% Mounted on
        vzfs                  1.0 G   64 M  961 M   7% /
        none                 1004 M  4.0 K 1004 M   1% /dev
        [root@test01 ~]#
