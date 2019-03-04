---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Working with ISOs on Xenserver

XenServer expects you to do little with your base operating system as possible, which includes the storage of ISO media for your systems. For more information about setting up a XenServer's ISO Library, see {{site.data.keyword.BluSoftlayer}} LockBox.

**Prerequisites:**

* Configure and connect XenCenter to your XenServer.
* Establish a working CIFS or NFS server with logins for usage. In this example, use {{site.data.keyword.cloud_notm}} LockBox, which is accessed by using CIFS. You can also use a purchased NAS account for this storage space.

1. Open XenCenter on your local computer and connect to your XenServer.

2. In the menu bar, click **Storage > New SR...**

3. For the LockBox example, select **CIFS** and click **Next**.

4. Enter an appropriate name for the new CIFS storage and click **Next**.

5. Enter your LockBox or NAS information and click **Finish**.

You can now add your files to the storage space. The storage is available to you if you SSH to your server. Use the following example to help get you started.

```
[root@xenbox ~]# mount
/dev/sda1 on / type ext3 (rw)
none on /proc type proc (rw)
none on /sys type sysfs (rw)
none on /dev/pts type devpts (rw)
none on /dev/shm type tmpfs (rw)
none on /proc/sys/fs/binfmt_misc type binfmt_misc (rw)
sunrpc on /var/lib/nfs/rpc_pipefs type rpc_pipefs (rw)
//lockbox01.service.softlayer.com/SLLB#####-## on /var/run/sr-mount/6e691cc8-5a17-1fbc-36a9-00042e
eb9ad3 type cifs (rw,mand)
```

```
[root@xenbox ~]# cd /var/run/sr-mount/6e691cc8-5a17-1fbc-36a9-00042eeb9ad3
[root@xenbox 6e691cc8-5a17-1fbc-36a9-00042eeb9ad3]# ls
7.0-RELEASE-i386-disc1.isofreebsd-INSTALL_XENU
```

```
[root@xenbox 6e691cc8-5a17-1fbc-36a9-00042eeb9ad3]# wget http://cdimage.debian.org/debian-cd/4.0_r
4a/i386/iso-cd/debian-40r4a-i386-netinst.iso
--20:21:34--http://cdimage.debian.org/debian-cd/4.0_r4a/i386/iso-cd/debian-40r4a-i386-netinst.iso
Resolving cdimage.debian.org... 130.239.18.173, 2001:6b0:e:2018::173
Connecting to cdimage.debian.org|130.239.18.173|:80... connected.
HTTP request sent, awaiting response... 302 Found
Location: http://laotzu.acc.umu.se/debian-cd/4.0_r4a/i386/iso-cd/debian-40r4a-i386... [fo
llowing]
--20:21:34--http://laotzu.acc.umu.se/debian-cd/4.0_r4a/i386/iso-cd/debian-40r4a-i386-netinst.iso
Resolving laotzu.acc.umu.se... 130.239.18.158, 2001:6b0:e:2018::158
Connecting to laotzu.acc.umu.se|130.239.18.158|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 169388032 (162M) [application/octet-stream]
Saving to: `debian-40r4a-i386-netinst.iso'

100%[=====================================================================>] 169,388,032 2.05M/sin 35s

20:22:10 (4.61 MB/s) - `debian-40r4a-i386-netinst.iso' saved [169388032/169388032]

[root@xenbox 6e691cc8-5a17-1fbc-36a9-00042eeb9ad3]# ls
7.0-RELEASE-i386-disc1.isodebian-40r4a-i386-netinst.isofreebsd-INSTALL_XENUi386cd-4.0.iso
```

Now that you downloaded the ISO that you want to use to create a new VM, use that ISO to get start the installation.

1. From the menu bar, click **New VM...**

2. Select **Other installaltion media** and click **Next**.

3. Enter an appropriate name and description for the virtual machine and click **Next**. 

4. Select **ISO Image**, select your ISO from the list, and click **Next**.

5. If you are running a cluster, you can choose the XenServer that you want the VM created on. If you are running a single XenServer, click **Next**.

6. Assign resources to your system by using **"Setting the number of CPUs and the initial memory allocation for the new VM"**. Set it up the way that fits your needs and click **Next**.

7. Click **Add...** (no disks are assigned). By default the virtual disk size is 1 GB. You can make the device larger or smaller to fit your needs. Click **OK > Next**.

8. Unless your VM is not going to communicate on the Private Network, accept the default virtual network interfaces and click **Next**.

9. You are now ready to create the new VM. Keep **Start VM automatically** selected and click **Finish**. The ISO installation begins and you can see your new VM listed.

10. Click your VM in the menu and click the **Console** tab to start your installation.


