---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Linux 사용 시 XenServer 도구 설치 
{: #installing-xenserver-tools-when-using-linux}

CentOS, Debian 및 RedHat 운영 체제를 사용하는 가상 머신에 XenServer Tools를 설치하려면 다음 단계를 따르십시오. 

이러한 단계는 {{site.data.keyword.cloud}} 제공 VSI가 아니라 특별히 고객 관리 XenServer에 필요합니다.
{:tip}

1. XenCenter를 열고 XenServer에 대한 연결을 설정하십시오. XenServer 도구가 설정되어 있지 않은 새 가상 머신의 예를 참조하십시오. 소프트웨어에 **"가상화 상태:"**가 "도구가 설치되지 않음"으로 표시됩니다.

2. XenServer 도구를 설치할 가상 머신의 이름을 마우스 오른쪽 단추로 클릭하고 **XenServer 도구 설치**를 클릭하십시오. 다음과 같은 경고가 표시됩니다. "xenServer 도구가 설치되면 현재 가상 머신의 CD 드라이브에 있는 CD 또는 DVD가 꺼내집니다."

3. 계속하려면 **XenServer 도구 설치**를 클릭하십시오.

가상 머신에 대한 XenCenter의 콘솔 탭에서 DVD 드라이브에 로드되는 "xs-tools.iso"의 CD 이미지를 볼 수 있습니다. 또한 시스템 내의 디바이스 이름도 제공합니다.

4. 콘솔을 통해 또는 시스템에 대한 SSH 세션을 통해 다음 단계를 수행할 수 있습니다. 이 예에 따라 설치는 SSH를 사용합니다. **참고:** 드라이브 디바이스 이름은 XenCenter에서 제공됩니다.

        $ ssh 10.17.37.243
        root@10.17.37.243's password:
        Last login: Thu Sep 11 12:57:31 2008 from 10.0.80.84
        [root@ns0 ~]# cd /mnt
        [root@ns0 mnt]# ls
        [root@ns0 mnt]# mkdir xs-tools
        [root@ns0 mnt]# mount /dev/xvdd /mnt/xs-tools/
        mount: block device /dev/xvdd is write-protected, mounting read-only
        [root@ns0 mnt]# cd /mnt/xs-tools/Linux/
        [root@ns0 Linux]# bash install.sh
        Detected `CentOS release 5.2 (Final)' (centos version 5).

        The following changes are made to this virtual machine:
        * packages to be installed or upgraded:
        - kernel-xen-2.6.18-53.1.13.el5.xs4.1.0.24.x86_64.rpm
        - xe-guest-utilities-4.1.0-257.x86_64.rpm

        Continue? [y/n] y

        Preparing...########################################### [100%]
        1:xe-guest-utilities##########################################[100%]
        Preparing...########################################### [100%]
        package kernel-xen-2.6.18-92.el5 (which is newer than kernel-xen-2.6.18-53.1.13.el5.xs4.1.0.24) is already installed

        Reboot this virtual machine.
        [root@ns0 Linux]#

쉘에 복사하여 붙여넣으려면 /dev/xvdd의 동일한 디바이스 이름을 지정한 경우 다음 라인이 모든 단계를 수행합니다.

        mkdir /mnt/xs-tools
        mount /dev/xvdd /mnt/xs-tools
        cd /mnt/xs-tools/Linux/
        bash install.sh

어떤 경우에는 Xen Tools ISO가 /dev/cdrom에 마운트됩니다.

        mkdir /mnt/xs-tools
        mount /dev/cdrom /mnt/xs-tools
        cd /mnt/xs-tools/Linux
        ./install.sh

5. 명령행에서 시스템을 다시 시작하면 XenServer 도구가 설치된 것을 볼 수 있습니다. 

6. 이제 XenCenter는 가상 머신 내에서 운영 체제를 더 많이 제어하고 있으며, XenCenter의 **성능** 탭에서 이전에 사용할 수 없는 카운터를 볼 수 있습니다.
