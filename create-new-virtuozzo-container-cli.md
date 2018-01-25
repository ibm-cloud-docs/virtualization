---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-05"
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Creating a new Virtuozzo container (CLI)

## How to create a new Virtuozzo container (CLI)

Complete the following steps to create a new container on a Virtuozzo server.

1. Check the list of containers on your host.

        [root@vztrain01 ~]# vzlist -a

          CTID      NPROC STATUS    IP_ADDR         HOSTNAME

             1         93 running   192.168.135.58  ServiceCT

           100          9 running   67.228.128.93   vztest01.softlayer.local

2. Create a container with an open ID#. The number must be above 100 and not already in use. In this example, use 122.

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

After you create a container, the VZ is in the stopped state. If you are not sure what packages are installed, you can use the `vzpkgls` command. This command shows you a list of installed Virtuozzo OS and application packages that are on the server. For the example, a RedHat template is used, but Virtuozzo supports several different operating systems: RedHat, CentOS, Debian, Fedora Core, and SUSE.

3. Review the default configuration for the container and make necessary changes. 

        [root@vztrain01 ~]# less /vz/private/122/ve.conf

4. Start the container.

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

The basic setup of a new Virtuozzo container using the command line is complete.
