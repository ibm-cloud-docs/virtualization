---
copyright:
  years: 1994, 2017
lastupdated: "2017-08-30"

subcollection: virtualization

---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Getting started with Virtuozzo

1.  Navigate to your console's device menu. For more information, see [Navigating to devices](/docs/virtualization?topic=virtual-servers-navigating-devices).

2. Go to the Public IP of the server in your preferred browser, by using HTTPS. It is advised that you use Firefox, Opera, or Internet Explorer to manage Virtuozzo through the PIM.  **Note:** JavaScript is REQUIRED. Without JavaScript you can be redirected to a non-existent page and the PIM will not work.

3. Enter your user name and password to log in to the PIM.  Remember, the username is ‘root’ and the password is the root password for the server, as listed in the Portal.

*PIM Login Screen*

4. Now you need to install the templates and cache them.

   a. Management

   b. Updates

   c. Go to the server you want to update.

   d. Go to **Template Updates**.

   e. Select any additional templates thhat you want to install or update.

5.  Go to the server that you want to update.

   a. Go to **Templates**

   b. Go to **OS Templates**

   c. Select the OS Templates that you want to use on the server and cache. You typically do not need to cache all of the Templates to get started. Only cache Templates that you want to use. These templates take up a significant amount of space in the `/vz` partition.

   d. If you have only one node, then click the next cache button.

   e.  You now need to wait for the full template RPMs to download and install.

   f. You can click **Details** link to view the caching status.

6.   While the server downloads and caches the templates, you can set up the network range for the Containers. Go to **Setup > Network**.

   a. Click **New IP Range**.

   b. Add additional IP addresses for your Containers. If you have not purchased additional IP addresses for your virtual servers, then you need to do this now.

7. Go to the hardware node and click **Containers > New Container**.

   a. Complete the information as to how it best suites your needs. For this example you are use slm.512MB as the default.

   b. Enter the host name as a Fully Qualified Domain Name, add your DNS Server and add a search domain if needed (this is not typical).

   c. Review the resource parameters. **Note:** You can drastically hinder the Containers ability to process data, disk space, memory or even give too much that starves your server of resources. Starving your server causes it to slow or unexpectedly stop processes. If you do not understand these settings, then contact your server administrator. You need to click **Validate** before you click **Next**. Validating ensures that your settings do not conflict with each other.

   d. Select the applications that you want to install and click **Next**.

   e. Review your configuration and click **Create**.

   f. A Container is created.

   g. You can now go back to Containers to view the new Container and obtain the automatically assigned IP address (from the pool of IPs that you added earlier).

   h.  You can now log in to your server and use it.

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
