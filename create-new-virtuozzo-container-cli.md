---
copyright:
  years: 2014, 2023
lastupdated: "2023-04-04"

subcollection: virtualization

keywords: Virtuozzo, create Virtuozzo container, Virtuozzo container, create container
---

{{site.data.keyword.attribute-definition-list}}

# Creating a Virtuozzo container
{: #creating-a-new-virtuozzo-container-cli}

Use the following steps to create a Viruozzo container by using the CLI.

1. Check the list of containers on your host.

   ```text
   [root@vztrain01 ~]# vzlist -a

   CTID      NPROC STATUS    IP_ADDR         HOSTNAME

   1         93 running   192.168.135.58  ServiceCT

   100          9 running   67.228.128.93   vztest01.softlayer.local
   ```
   {: codeblock}

2. Create a container with an open ID#. The number must be higher than 100 and not already in use. In this example, use 122.

   ```text
   [root@vztrain01 ~]# vzctl create 122 --pkgset redhat-as3-minimal-x86_64  --config basic

   Creating Container private area (redhat-as3-minimal-x86_64/20080630)

   Container is mounted

   Postcreate action done

   Container is unmounted

   Container private area created

   Container registered succesfully

   [root@vztrain01 ~]# vzlist -a

      CTID      NPROC STATUS    IP_ADDR         HOSTNAME

         1         93 running   192.168.135.58  ServiceCT

       100          9 running   67.228.128.93   vztest01.softlayer.local

       122          - stopped   -               -
   ```
   {: codeblock}

   After you create a container, the VZ is in the stopped state. If you are not sure what packages are installed, you can use the `vzpkgls` command. This command shows you a list of installed Virtuozzo OS and application packages that are on the server. For the example, a Red Hat template is used, but Virtuozzo supports several different operating systems: Red Hat, CentOS, Debian, Fedora Core, and SUSE.
3. Review the default configuration for the container and make necessary changes.

```text
[root@vztrain01 ~]# less /vz/private/122/ve.conf
```
{: codeblock}

4. Start the container.

   ```
   [root@vztrain01 ~]# vzctl start 122

   Starting Container ...

   Container is mounted

   Setup slm memory limit

   Setup slm subgroup (default)

   Setting devperms 20002 dev 0x7d00

   Adding port redirection to Container(1): 4643 8443

        Adding IP address(es):

        Configure meminfo: 65536

        Container start in progress...

   Moments later the container runs. You can check the status again with the `vzctl` status 122 command.
5. Add parameters that are necessary to manage the container.

        [root@vztrain01 ~]# vzctl set 122 --ipadd 12.34.234.142 --hostname vz122.domain.com --userpasswd root:password123 --save

        Hostname for Container set: vz122.domain.com

        Adding IP address(es) to pool: 12.34.234.142

        Adding IP address(es): 12.34.234.142

        Shutting down loopback interface:  [  OK  ]

        Setting network parameters:  [  OK  ]

        Bringing up loopback interface:  [  OK  ]

        Bringing up interface venet0:  [  OK  ]

        Changing password for user root.

        passwd: all authentication tokens updated successfully.

        Saved parameters for Container 122

The basic setup of a new Virtuozzo container by using the CLI is complete.
