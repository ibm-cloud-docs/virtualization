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

# Virtuozzo 入门
{: #getting-started-with-virtuozzo}

## 访问 Parallels Infrastructure Manager (PIM)
{: #accessing-the-parallels-infrastructure-manager-pim-}

1. 从 [{{site.data.keyword.slportal_full}} ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://control.softlayer.com/){: new_window} 获取有关裸机服务器的信息。
    1. 从**设备**菜单中，选择**设备列表**。
    2. 单击您的服务器。
    3. 找到公共 IP 地址和密码。
5. 在首选浏览器中使用 HTTPS 转至服务器的公共 IP 地址。使用 Firefox、Opera 或 Internet Explorer 通过 PIM 来管理 Virtuozzo。

JavaScript 是必需的。如果没有 JavaScript，可能会将您重定向到不存在的页面，并且 PIM 无法正常工作。
{:tip}

6. 输入用户名和密码以登录到 PIM。

用户名为“root”，密码是服务器的 root 用户密码，如 {{site.data.keyword.slportal}} 中所列。
{:tip}

## 安装和高速缓存模板
{: #installing-and-caching-templates}

1. 在 PIM 屏幕中，选择**管理** > **更新**。
2. 转至要更新的服务器。
3. 单击**模板更新**。
4. 选择要安装或更新的任何其他模板。
5. 要选择需要更新的服务器，请单击**模板** > **操作系统模板**。
6. 选择要在服务器上使用并进行高速缓存的操作系统模板。通常，一开始无需对所有模板进行高速缓存。请仅对要使用的模板进行高速缓存。这些模板会占用 `/vz` 分区中的大量空间。
7. 如果只有一个节点，请单击**下次高速缓存**。
8. 等待下载完整模板 RPM 并进行安装。单击**详细信息**以查看高速缓存状态。

## 设置容器的网络范围
{: #setting-up-network-range-for-containers}

服务器下载模板以及对模板进行高速缓存时，可以设置容器的网络范围。

1. 单击**选择** > **网络**。
2. 选择**新建 IP 范围**。
3. 为容器添加其他 IP 地址。如果您没有为虚拟服务器购买其他 IP 地址，请转至 [{{site.data.keyword.slportal}} ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://control.softlayer.com/){: new_window}。选择**网络** > **IP 管理** > **VLAN** > **订购 IP**。
4. 转至硬件节点，然后单击**容器** > **新建容器**。
5. 根据您的需要，填写相关信息.对于此示例，请使用 slm.512MB 作为缺省值。
6. 输入主机名作为标准域名，添加 DNS 服务器，并根据需要添加搜索域。
7. 复查资源参数。

请注意，不要添加太多参数而造成容器无法处理数据，或者影响到其磁盘空间或内存，这可能会导致服务器资源不足。服务器资源不足会引起进程变慢或意外停止。如果您不了解这些设置，请联系服务器管理员或[第三方管理公司 ![外部链接图标](../../icons/launch-glyph.svg "外部链接图标")](https://cloud.ibm.com/docs){: new_window}。
{:tip}

8. 单击**下一步**之前，请先单击**验证**。验证可确保设置不会相互冲突。
9. 选择要安装的应用程序，然后单击**下一步**。
10. 复查配置，然后单击**创建**。这将创建容器。
11. 现在，可以返回到**容器**来查看新容器，并获取自动分配的 IP 地址（从先前添加的 IP 中分配）。
12. 现在，您可以登录到服务器并使用该服务器。

        myname/myserver[0]~$ ssh 1.234.123.4
        警告：已向已知主机列表永久添加“1.234.123.4”(DSA)。
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
