---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Linux を使用する場合の XenServer Tools のインストール
{: #installing-xenserver-tools-when-using-linux}

CentOS、Debian、および RedHat のオペレーティング・システムを使用する仮想マシンに XenServer Tools をインストールするには、以下の手順に従ってください。

この手順は、お客様管理の XenServers にのみ適用される手順であり、{{site.data.keyword.cloud}} 提供の VSI には適用されません。
{:tip}

1. XenCenter を開き、XenServer への接続を確立します。 XenServer Tools がセットアップされていない新規仮想マシンの例を参照してください。 **「仮想化の状態:」**に「Tools がインストールされていません (Tools not installed)」と表示されます。

2. XenServer Tools をインストールする仮想マシンの名前を右クリックして、**「XenServer Tools のインストール (Install XenServer Tools)」**をクリックします。 「XenServer Tools をインストールするときに、仮想マシンの CD ドライブに現在入っている CD または DVD はイジェクトされます。(Any CD or DVD currently in the virtual machine's CD drive ejected when xenServer Tools are installed)」という警告が表示されます。

3. **「XenServer Tools をインストールする (Install XenServer Tools)」**をクリックして先に進みます。

仮想マシンの XenCenter のコンソール・タブに、DVD ドライブにロードされた CD イメージ「xs-tools.iso」が表示されます。 システム内のデバイス名も表示されます。

4. コンソールまたはシステムへの SSH セッションのいずれかを使用して、以下の手順を実行できます。 この例では、SSH を使用してインストールします。 **注:** ドライブのデバイス名は、XenCenter によって提供されます。

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

同じデバイス名 /dev/xvdd が割り当てられている場合は、以下の行をシェルにコピーして貼り付ければ、すべての手順を実行できます。

        mkdir /mnt/xs-tools
        mount /dev/xvdd /mnt/xs-tools
        cd /mnt/xs-tools/Linux/
        bash install.sh

場合によっては、Xen Tools の ISO は /dev/cdrom にマウントされます。

        mkdir /mnt/xs-tools
        mount /dev/cdrom /mnt/xs-tools
        cd /mnt/xs-tools/Linux
        ./install.sh

5. コマンド・ラインからシステムを再始動すると、XenServer Tools がインストールされたことを確認できます。

6. XenCenter による仮想システム内のオペレーティング・システムの制御性が高まり、以前は使用できなかったカウンターが XenCenter の**「パフォーマンス」**タブに表示されるようになりました。
