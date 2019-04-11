---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-16"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# XenServer 共有ストレージの iSCSI ノードのセットアップとマウント
{: #setting-up-and-mounting-an-iscsi-node-in-xenserver-shared-storage}

共有ストレージとは、XenServer で使用できる任意の方式のストレージであるストレージ・リポジトリー (SR) です。 共有と非共有という 2 つのカテゴリーから SR を選択できます。 共有 SR では、同じプールに属する 1 つのストレージ・ロケーションを複数のノードが共有できます。 {{site.data.keyword.cloud}} は、SR のオプションとして、NFS と LVMoiSCSI (LVM over iSCSI) の 2 つをサポートします。

NFS サーバーをインストールし、お客様の別のシステムから、または {{site.data.keyword.cloud_notm}} QuantaStor サーバーを使用して NFS サーバーを管理する必要があります。 XenCenter で NFS リポジトリーをマウントするには、以下の手順に従います。

1. XenCenter コンソールを開きます。
2. **「新規ストレージ」**をクリックします。
* 新しいダイアログ・ボックスで、**「仮想ディスク ストレージ (Virtual disk storage)」>「NFS」**を選択します。
* 新規 SR に適切な名前を付けます。
* **「共有名」**の下に server:/path を入力します。
* **「スキャン」**をクリックします。

このプロセスにより、既存の SR がないか NFS 共有がスキャンされます。

LVMoiSCSI で {{site.data.keyword.blockstoragefull}} を使用できます。 iSCSI は、お客様が管理するストレージ・サーバーのものでも、{{site.data.keyword.blockstoragefull}} オファリングのものでもかまいません。 Performance and Redundant Block Storage の場合は、{{site.data.keyword.slportal}}で**「ストレージ」>「ブロック・ストレージ」>「Lun 名の選択 (Select Lun Name)」**に移動して IQN を取得します。 XenCenter で iSCSI ノードをマウントするには、以下の手順に従います。

1. XenCenter コンソールを開きます。
2. **「全般」>「プロパティ」**に移動し、IQN を設定します。
3. **「新規ストレージ」**をクリックします。
4. 新しいダイアログ・ボックスで、**「仮想ディスク ストレージ」>「ソフトウェア iSCSI」**に移動します。
5. 新規 SR の適切な名前を入力します。
6. iSCSI ターゲットのホスト名または IP を入力します。ポートはデフォルトの 3260 のままにしておきます。
7. **「CHAP を使用する」**を選択します。
8. Lun のユーザー名とパスワードを入力します。
9. **「ターゲットホストのスキャン」**をクリックします。
* **「ターゲット IQN」**オプションを選択します。
* **「ターゲット Lun」**オプションを選択します。
10. IQN フィールドと Lun フィールドに値が設定されたら、**「完了」**をクリックします。 ターゲットがスキャンされて既存の SR がないか検査されます。 SR が存在していれば、新規 SR を作成するか既存の SR に付加するか、インストーラーから尋ねられます。

すべてのサーバーが 1 つのプール内に存在していれば、iSCSI リポジトリーは自動的に共有されます。

iSCSI のマルチパスを構成する方法については、[Configuring iSCSI Multipath ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus1000/sw/5_x/sys_mgmt_config/b_Cisco_N1KV_VMware_Sys_Mgmt_Config_5x/b_Cisco_N1KV_VMware_Sys_Mgmt_Config_5x_chapter_01110.html?dtid=osscdc000283){: new_window} を参照してください。
