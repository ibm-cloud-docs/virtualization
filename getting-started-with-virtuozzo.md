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

# Getting started with Virtuozzo
{: #getting-started-with-virtuozzo}

## Accessing the Parallels Infrastructure Manager (PIM)
{: #accessing-the-parallels-infrastructure-manager-pim-}

1. Obtain information from the [{{site.data.keyword.slportal_full}} ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://control.softlayer.com/){: new_window} about your bare metal server.
    1. From the **Devices** menu select **Device list**.
    2. Click on your server.
    3. Locate the public IP address and password.
5. Go to the public IP address of the server in your preferred browser by using HTTPS. Use Firefox, Opera, or Internet Explorer to manage Virtuozzo through the PIM.

JavaScript is required. Without JavaScript, you can be redirected to a non-existent page and the PIM will not work.
{:tip}

6. Enter your username and password to log in to the PIM.

The username is "root" and the password is the root password for the server, as listed in the {{site.data.keyword.slportal}}.
{:tip}

## Installing and caching templates
{: #installing-and-caching-templates}

1. In the PIM screen, select **Management** > **Updates**.
2. Go to the server that you want to update.
3. Click **Template Updates**.
4. Select any additional templates that you want to install or update.
5. To select the server that you want to update, click **Templates** > **OS Templates**.
6. Select the OS templates that you want to use on the server and cache. Typically, you do not need to cache all of the templates to get started. Only cache templates that you want to use. These templates take up a significant amount of space in the `/vz` partition.
7. If you only have one node, then click **Next Cache**.
8. Wait for the full template RPMs to download and install. Click **Details** to view the caching status.

## Setting up network range for containers
{: #setting-up-network-range-for-containers}

While the server downloads and caches templates, you can set up the network range for the containers.

1. Click **Select** > **Network**.
2. Select **New IP Range**.
3. Add additional IP addresses for your containers. If you have not purchased additional IP addresses for your virtual servers, go the the [{{site.data.keyword.slportal}} ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://control.softlayer.com/){: new_window}. Select **Network** > **IP Management** > **VLANs** > **Order IP**.
4. Go to the hardware node and click **Containers** > **New Container**.
5. Complete the information as it fits your needs. For this example, use slm.512MB as the default.
6. Enter the hostname as a fully qualified domain name, add your DNS server, and add a search domain if needed.
7. Review the resource parameters.

Be careful not to prohibit the container's ability to process data, its disk space, or memory by adding too many parameters, which could starve your server of resources. Starving your server causes it to slow or unexpectedly stop processes. If you do not understand these settings, then contact your server administrator or a [third-party management company ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://cloud.ibm.com/docs){: new_window}.
{:tip}

8. Click **Validate** before you click **Next**. Validating ensures that your settings do not conflict with each other.
9. Select the applications that you want to install and click **Next**.
10. Review your configuration and click **Create**. A container is then created.
11. You can now go back to **Containers** to view the new containers and obtain the automatically assigned IP address (from the IPs that you added earlier).
12. You can now log in to your server and use it.

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
