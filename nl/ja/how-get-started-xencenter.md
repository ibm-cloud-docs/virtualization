---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-24"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# XenCenter の準備
{: #getting-started-with-xencenter}

## 始める前に
{: #before-you-begin-xencenter}

始める前に、以下の前提条件を確認してください。

- 「Secondary on VLAN」としてルーティングされる新規のポータブル IP アドレス範囲 (パブリックの範囲とプライベートの範囲)。 これは新規 VM をセットアップするためのソリューションの例です。この例では、プライベート・ネットワーク上に存在する使用可能な IP が必要です。 サブネットは、[{{site.data.keyword.slportal_full}} ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://control.softlayer.com/network/subnets/order) から注文できます。
- VPN 経由で {{site.data.keyword.cloud}} プライベート・ネットワークに接続できること。 VPN アクセスについて詳しくは、[{{site.data.keyword.cloud_notm}} インフラストラクチャー・プライベート・ネットワークへのアクセスの有効化](/docs/customer-portal?topic=customer-portal-getting-started#enable-private-network)を参照してください。
- Citrix XenCenter がローカル・システムにインストールされていること。<!-- . https://downloads.service.softlayer.com/citrix/xen/-->

**注:** サーバーには、すぐに XenServer を実行できるように、さまざまなテンプレートが事前構成されています。

## XenCenter を使用して仮想マシンを作成する
{: #creating-a-virtual-machine-with-xencenter}

XenCenter で仮想マシンを作成するには、以下の手順を使用します。

1. VPN 経由で {{site.data.keyword.cloud_notm}} プライベート・ネットワークに接続していることを確認してください。 SSL VPN または PPTP を使用して接続を確立します。
2. XenCenter を開き、**「XenServer の追加 (Add your XenServer)」**をクリックします。
3. サーバーのホスト名、ユーザー名、およびパスワードを入力します。 サーバーのプライベート IP アドレス (例えば 10.x.x.x)、ユーザー名 `root`、サーバーの root のパスワードを使用する必要があります。 この情報を入手するには、{{site.data.keyword.slportal}}で、[デバイス ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://control.softlayer.com/devices){: new_window} に移動し、XenServer の名前をクリックします。 **「接続」**をクリックします。
4. 無料ライセンスのアクティベーション画面が表示されたら、ここで、この情報を入力してください。
5. {{site.data.keyword.cloud_notm}} 名を右クリックし、**「新規 VM...」**を選択します。<!--You can now create your first Virtual Machine. Create a CentOS virtual machine with a disk of 10 GB and have both Public and Private Networks functioning-->
6. 新規仮想マシンに使用するオペレーティング・システムを選択して、**「次へ」**をクリックします。 **注:** 一部のテンプレートは、ユーザーが独自にメディアを用意する必要があります。<!--Because you are using CentOS, you can use {{site.data.keyword.BluSoftlayer_notm}} private mirrors for CentOS to get our installation going.Select a version of CentOS and then click **Next**.-->
7. 新規仮想マシンにとって適切な名前および説明を入力します。
8. ゲスト・オペレーティング・システムのインストール・メディアのロケーションを入力して、**「次へ」**をクリックします。 <!-- In the example, {{site.data.keyword.BluSoftlayer_notm}} a CentOS mirror is used as installation media. Provide the Install URL of: https://mirrors.service.softlayer.com/centos/5/os/x86_64 and click **Next**.
  *A trailing ‘/’ at the end of the URL can sometimes break the installation.*
  *This mirror is available only on the {{site.data.keyword.BluSoftlayer_notm}} Private Network. The full mirror's contents are  available here: https://mirrors.service.softlayer.com/.-->
9. CPU の数とメモリーの割り当て量を選択します (ここでは簡単なシステムを構築するので、あまり多くの RAM は必要ありません。 したがって、512 MB あれば十分すぎるほどです)。 **「次へ」**をクリックします。
10. VM にディスク・スペースを割り振るための仮想ディスクを選択します。<!--Remember that this is like adding hard disks, it is not like partitioning your system. Partitioning is done during the installation of the OS.--> このテンプレートのデフォルト・サイズは 8 GB です。 この量は、ここで構築するサーバーには十分すぎるほどです。 *オプション: 要件に合わせてディスクを大きくするには、ディスクを強調表示し、**「編集...」**をクリックします。* **「次へ」**をクリックします。
11. 仮想マシンの仮想ネットワーク・インターフェースを追加または削除します。 プライベート・ネットワークでの通信が不要なシステムの場合は、デフォルトを受け入れることができます。 この例では、システム内の両方のインターフェースをそのままにする必要がありますが、経験豊富な Xen 管理者であれば、独自の目的のためにそのいずれかを削除できます。 その場合は、インターフェースを強調表示して削除できます。 **「次へ」**をクリックします。
12. 構成が完了しました。 「VM を自動的に起動する」を選択したままにして、**「完了」**をクリックします。 インストールが開始されます。 主画面に戻ります。 新規 VM が左側にリストされます。
13. 新規 VM を選択して、**「コンソール」**タブをクリックします。<!--You can now see that your system is booted into the CentOS installer awaiting your input.-->
14. <!--All of the parameters of a CentOS installation are outside of the scope of this article and will need to be customized by your System Administrator, but this article will provide some specific pieces of information that you need to complete the installation. Select your language to get started. The CentOS installer will then ask you for assistance in configuring the Networking Devices in the system.-->**「eth0 - xen 仮想イーサネット (eth0 - xen Virtual Ethernet)」**を選択して、**「OK」**をクリックします。
  <!--![14](images/14.png)-->
15. <!--In the pre-requisite notes, we made sure that we already had a set of Portable IP Addresses routed as "Secondary on VLAN" ready for this installation.-->TCP/IP の構成情報を用意します。 インターフェースに IPV4 サポートを手動で構成し、IPV6 サポートを無効にする必要があります。
  <!--[15](images/15.png)-->
16. プライベート IP サブネット (10.17.37.240/29) の使用方法を理解していることを確認し、**「OK」**をクリックします<!-- to go to the CentOS installer-->。 ユーザーとシステム管理者は、独自のガイドラインに従って、ゲスト・オペレーティング・システムをインストールできます。 静的 IP ブロックとポータブル IP ブロックについて詳しくは、[サブネットおよび IP の概説](/docs/infrastructure/subnets?topic=subnets-getting-started-subnets-ips#getting-started-subnets-ips)を参照してください。

新規 VM を作成しました。
