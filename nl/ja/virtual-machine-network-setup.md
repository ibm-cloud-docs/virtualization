---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 仮想マシン・ネットワークのセットアップ
{: #setting-up-a-virtual-machine-network}

新規仮想マシンのネットワーク構成は数手順で済みます。 パブリック・ネットワーク用とプライベート・ネットワーク用に別々のポータブル IP ブロックが必要です。 (ネットワーク ID/ゲートウェイ/ブロードキャストが存在しない) Routed to VLAN サブネットではなく、Secondary on VLAN ブロードキャスト・ドメインを必要とする仮想化オファリングを使用するものとします。 仮想マシン上でプライベート・ネットワークを使用する予定がなければ、必要なのはパブリック・サブネットだけです。 ポータブル IP ブロックは、ポータルの[「営業」->「IP アドレスの追加」 ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://manage.softlayer.com/Sales/orderAdditionalServices/subnet){: new_window} で直接注文することも、[「パブリック・ネットワーク IP マネージャー (Public Network IP Manager)」 ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://manage.softlayer.com/PublicNetwork/ipManager){: new_window} から注文することもできます。

IP ブロックを取得したら、仮想マシン上でネットワーキングを構成できます。 仮想マシンがインストールされていない場合は、オペレーティング・システムのインストール・プロセスでパブリック・ネットワーク・ブロックを構成できます。 このプロセスが、最も簡単にパブリック・ネットワークを構成できる方法です。 すべてのネットワーク構成手順は、最初のネットワーク・インターフェースがプライベート・ネットワークであり、2 番目のネットワーク・インターフェースがパブリック・ネットワークであることを前提としています。

ネットワークの構成方法は OS によって異なります。 ここでは、以下のオペレーティング・システムでネットワークを構成する方法について詳しく記載します。 **注:** グループとしてまとめた各オペレーティング・システムのネットワーク構成は同じです。

* Windows 2008 Server Core
* Windows 2003 Standard および Enterprise
* Windows 2008 Web、Standard、Enterprise、および Datacenter
* RedHat、Fedora、および CentOS
* Ubuntu および Debian

ネットワーク構成には、パブリック IP ブロックとプライベート IP ブロックの両方について、以下の情報が必要です。 この情報は、ポータルの[「パブリック・ネットワーク」->「IP マネージャー (IP Manager)」 ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://manage.softlayer.com/PublicNetwork/ipManager){: new_window} (パブリック IP ブロックの場合)、および[「プライベート・ネットワーク」->「IP マネージャー (IP Manager)」 ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://manage.softlayer.com/PrivateNetwork/ipManager){: new_window} にあります。

    -IP Address
    -Gateway
    -Subnet Mask
    --Subnet conversion
    ---/29 - 255.255.255.248
    ---/28 - 255.255.255.240
    ---/27 - 255.255.255.224
    ---/26 - 255.255.255.192
    ---/25 - 255.255.255.128
    ---/24 - 255.255.255.0

## Windows 2008 Server Core
{: #windows-2008-server-core}

Windows 2008 Server Core エディションは、システム・ネットワークを構成するためのグラフィカル・インターフェースを備えていません。 コマンド・プロンプトを使用して手動で構成を行う必要があります。 以下のコマンドを実行する必要があります。 この例で使用している IP アドレスを、実際の IP ブロックに含まれる IP アドレスに置き換える必要があります。 [netsh ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://docs.microsoft.com/en-us/previous-versions/tn-archive/bb490939(v=technet.10)){: new_window} コマンドを含め、これらのコマンドは、旧バージョンの Windows Server で使用できます。

**パブリック IP ブロックの例 – 192.0.2.0/29**

* IP アドレス – 192.0.2.2
* ゲートウェイ – 192.0.2.1
* サブネット・マスク – 255.255.255.248

**プライベート IP ブロックの例 – 10.0.0.0/29**

* IP アドレス – 10.0.0.2
* ゲートウェイ – 10.0.0.1
* サブネット・マスク – 255.255.255.248

**パブリック・ネットワーク**

このコマンドは、サーバーをインターネットに接続するためのパブリック・ネットワークを作成します。

* *Netsh interface ip set address name=”Local Area Connection 2” static 192.0.2.2 255.255.255.248 192.0.2.1*

これらのコマンドは DNS エントリーを作成します。 プライベート・ネットワークを使用しない場合は、これらの IP アドレスを有効な DNS サーバーに置き換える必要があります。

* *Netsh interface ip add dns “Local Area Connection 2” 10.0.80.11*
* *Netsh interface ip add dns “Local Area Connection 2” 10.0.80.12*

**プライベート・ネットワーク**

以下の 2 つのコマンドは、プライベート・ネットワークを構成し、プライベート・ネットワーク用の永続ルートを作成します。 永続ルートにより、プライベート・ネットワーク全体へのプライベート・ネットワーク・アクセスを行えるようになります。 アクセスには、パブリック・ネットワーク構成の DNS サーバーが含まれます。 **注:** プライベート・ネットワークにはゲートウェイは割り当てられません。

* `Netsh interface IP set address name=”Local Area Connection” static 10.0.0.2 255.255.255.248`
* `Route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 -p`

## Windows 2003 Standard および Enterprise
{: #windows-2003-standard-and-enterprise}

Windows 2003 では、ネットワーキングの構成にはコマンド・ラインとグラフィカル・ユーザー・インターフェースのどちらでも使用できます。 コマンド・ラインを使用する場合は、Windows 2008 Server Core の構成手順を参照してください。 Windows 2003 にも同じ手順を使用できます。 ここでは、グラフィカル・インターフェースを使用する場合の手順を記載します。 この例で使用している IP アドレスを、実際の IP ブロックに含まれる IP アドレスに置き換える必要があります。

**プライベート IP ブロックの例 – 10.0.0.0/29**

* IP アドレス – 10.0.0.2
* ゲートウェイ – 10.0.0.1
* サブネット・マスク – 255.255.255.248

### ネットワーク構成を開く
{: #opening-your-network-configurations}

1. **「スタート」メニュー>「設定」>「コントロール パネル」**に移動します。
2. **「ネットワーク接続」**を開きます。
   **注:** ネットワーク・アダプターを 2 つインストールした場合は、「ローカル エリア接続」と「ローカル エリア接続 2」が表示されます。

**パブリック・ネットワーク**

Windows グラフィカル・インターフェースでパブリック・ネットワークを構成するには、以下の手順を使用します。

1. 右クリックを使用して**「ローカル エリア接続」>「プロパティ」**に移動し、**「インターネットプロトコル (TCP/IP)」>「プロパティ」**を選択します。
2. **「全般」>「次の IP アドレスを使う」**に移動します。
3. パブリック IP アドレス、サブネット・マスク、ゲートウェイを入力します。
4. **「次の DNS サーバーのアドレスを使う」**を選択します。 プライベート・ネットワークを構成する場合は、以下の DNS サーバーを使用してください。 プライベート・ネットワークを構成しない場合は、ユーザーが DNS サーバーを用意する必要があります。
    * 優先 DNS サーバー: 10.0.80.11
    * 代替 DNS サーバー: 10.0.80.12
5. **「OK」**をクリックします。

**プライベート・ネットワーク**

1. 右クリックを使用して**「ローカル エリア接続 2」>「プロパティ」**に移動し、**「インターネット プロトコル (TCP/IP)」>「プロパティ」**を選択します。
2. **「全般」>「次の IP アドレスを使う」**に移動し、プライベート IP アドレスとサブネット・マスクを入力します。 ゲートウェイ・フィールドは空のままにしておき、**「OK」**をクリックします。
3. もう一度**「OK」**をクリックしてネットワーク構成ウィンドウを閉じます。

DNS サーバーが含まれるプライベート・ネットワーク全体にアクセスできるように、サーバーへのカスタム・ルートを追加する必要があります。 プライベート・ネットワーク・アクセスを行えるようにするには、以下の手順を実行します。

1. **「スタート」>「ファイル名を指定して実行」**に移動し、*cmd* と入力して**「OK」**を押します。
2. 次のコマンドを実行します。**注:** ゲートウェイ・アドレス (10.0.0.1) は、実際のプライベート IP ブロックのゲートウェイに置き換えてください。<br/>
*“route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 –p”*

## Windows 2008 Web、Standard、Enterprise、および Datacenter
{: #windows-2008-web-standard-enterprise-and-datacenter}

Windows 2008 では、ネットワーキングの構成にはコマンド・プロンプトとグラフィカル・ユーザー・インターフェースのどちらでも使用できます。 コマンド・プロンプトを使用する場合は、Windows 2008 Server Core の構成手順を参照してください。 同じ手順をすべての 2008 サーバーに使用できます。 ここでは、グラフィカル・インターフェースを使用する場合の手順を記載します。 この例で使用している IP アドレスを、実際の IP ブロックに含まれる IP アドレスに置き換える必要があります。

**プライベート IP ブロックの例 – 10.0.0.0/29**

* IP アドレス – 10.0.0.2
* ゲートウェイ – 10.0.0.1
* サブネット・マスク – 255.255.255.248

**ネットワーク構成を開く**

1. **「スタート」メニュー>「設定」>「コントロール パネル」**に移動します。
2. **「ネットワークと共有センター」**を開き、**「ネットワーク接続の管理」**をクリックします。
* ネットワーク・アダプターを 2 つインストールした場合は、「ローカル エリア接続」と「ローカル エリア接続 2」が表示されます。

**パブリック・ネットワーク**

以下は、Windows グラフィカル・インターフェースでパブリック・ネットワークを構成する詳しい手順です。

1. 右クリックを使用して**「ローカル エリア接続」>「プロパティ」**に移動します。
2. **「インターネット プロトコル バージョン 4 (TCP/IPv4)」>「プロパティ」**を選択します。
3. **「全般」>「次の IP アドレスを使う」**に移動して、パブリック IP アドレス、サブネット・マスク、ゲートウェイを入力します。
4. **「次の DNS サーバーのアドレスを使う」**を選択します。 プライベート・ネットワークを構成する場合は、以下の DNS サーバーを使用してください。 プライベート・ネットワークを構成しない場合は、ユーザーが DNS サーバーを用意する必要があります。
    * 優先 DNS サーバー: 10.0.80.11
    * 代替 DNS サーバー: 10.0.80.12
5. **「OK」**をクリックします。

**プライベート・ネットワーク**

1. 右クリックを使用して**「ローカル エリア接続 2」>「プロパティ」**に移動し、**「インターネットプロトコル バージョン 4 (TCP/IPv4)」>「プロパティ」**を選択します。
2. **「全般」>「次の IP アドレスを使う」**に移動して、プライベート IP アドレスとサブネット・マスクを入力し、**「OK」**をクリックします。
3. もう一度**「OK」**をクリックしてネットワーク構成ウィンドウを閉じます。

DNS サーバーが含まれるプライベート・ネットワーク全体にアクセスできるように、サーバーへのカスタム・ルートを追加する必要があります。

1. **「スタート」>「ファイル名を指定して実行」**に移動し、「cmd」と入力して**「OK」**を押します。
2. 次のコマンドを実行します。**注:** ゲートウェイ・アドレス (_10.0.0.1_) は、実際のプライベート IP ブロックのゲートウェイに置き換えてください。
  * `route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 –p`

## RedHat、Fedora、および CentOS
{: #redhat-fedora-and-centos}

RedHat、Fedora、および CentOS でのネットワークの構成は、構成ファイルを手動で編集して行います。 手動でファイルを編集するには、仮想マシンにログインする必要があります。

**パブリック IP ブロックの例 – 192.0.2.0/29**

* IP アドレス – 192.0.2.2
* ゲートウェイ – 192.0.2.1
* サブネット・マスク – 255.255.255.248

**プライベート IP ブロックの例 – 10.0.0.0/29**

* IP アドレス – 10.0.0.2
* ゲートウェイ – 10.0.0.1
* サブネット・マスク – 255.255.255.248

**パブリック・ネットワーク**

パブリック・ネットワーク設定は次のファイルに含まれています。 このファイルを編集し、用意しておいた情報を入力する必要があります。 例の IP アドレスを、実際のパブリック IP ブロックに含まれる IP アドレスに置き換えてください。 ファイルが存在しない場合は作成してください。 存在する場合は、ファイルの中のすべてのデータを以下の情報に置き換えてください。
* /etc/sysconfig/network-scripts/ ifcfg-eth1
      DEVICE=eth1
      BOOTPROTO=static
      BROADCAST=192.0.2.7
      IPADDR=192.0.2.2
      NETMASK=255.255.255.248
      NETWORK=192.0.2.0

      GATEWAY=192.0.2.1
      ONBOOT=yes

**プライベート・ネットワーク**

プライベート・ネットワーク設定は次のファイルに含まれています。 このファイルを編集し、用意しておいた情報を入力する必要があります。 例の IP アドレスを、実際のプライベート IP ブロックの正しい IP アドレスに置き換えてください。 ファイルが存在しない場合は作成してください。 ファイルが存在する場合は、ファイルの中のすべてのデータを以下の情報に置き換えてください。**注:** プライベート・ネットワークにはデフォルト・ルートが存在*しない* ので、*ゲートウェイ* は定義しません。

* /etc/sysconfig/network-scripts/ ifcfg-eth0
      DEVICE=eth0
      BOOTPROTO=static
      IPADDR=10.0.0.2
      NETMASK=255.255.255.248
      ONBOOT=yes

最後に、DNS サーバーを含むプライベート・ネットワーク全体にプライベート・アクセスを行えるように、新しいルートを設定する必要があります。そのためには次のファイルを編集します。 このファイルが存在しない場合は作成する必要があります。 **注:** 例の中の「10.0.0.1」は、実際のプライベート・サブネットの IP ゲートウェイに置き換えてください。

* /etc/sysconfig/network-scripts/route-eth0
      10.0.0.0/8 via 10.0.0.1

**DNS 構成**

別個のファイルにプライマリー DNS とセカンダリー DNS の両方の構成が入っています。 このファイルを編集し、以下の情報を入力する必要があります。 プライベート・ネットワークにアクセスできない仮想マシンの場合は、サーバー IP を有効な DNS サーバーの IP アドレスに置き換える必要があります。

* /etc/resolv.conf
      nameserver 10.0.80.11
      nameserver 10.0.80.12

これらの変更を有効にするには、サーバー上でネットワーキングを再始動する必要があります。 ネットワーキングを再始動するために、次のコマンドを実行します。

* `service network restart`

## Ubuntu および Debian
{: #ubuntu-and-debian}

Ubuntu ネットワークと Debian ネットワークは、単一の構成ファイルで構成します。 この構成ファイルを編集し、以下の情報を入力する必要があります。 このファイルを編集するためには、サーバーに対する root 権限が必要です。 Ubuntu の基本インストールを行った場合は root ログインができません。 ただし、インストール・プロセスで作成されたユーザーは sudo を利用できます。 Ubuntu を実行している場合は、sudo コマンドを使用してファイルを編集する必要があります。

**パブリック IP ブロックの例 – 192.0.2.0/29**

* IP アドレス – 192.0.2.2
* ゲートウェイ – 192.0.2.1
* サブネット・マスク – 255.255.255.248

**プライベート IP ブロックの例 – 10.0.0.0/29**

·IP アドレス – 10.0.0.2
·ゲートウェイ – 10.0.0.1
·サブネット・マスク – 255.255.255.248

**パブリック・ネットワークおよびプライベート・ネットワーク**

次のファイルを任意のテキスト・エディターで編集し、例の IP アドレスを、実際のパブリック IP ブロックおよびプライベート IP ブロックに含まれる IP に置き換えます。

* /etc/network/interfaces
      auto lo
      iface lo inet loopback

      auto eth1
      iface eth1 inet static
      address 192.0.2.2
      netmask 255.255.255.248
      gateway 192.0.2.1

      auto eth0
      iface eth0 inet static
      address 10.0.0.2
      netmask 255.255.255.248

      #Static route for backend service network
      up route add -net 10.0.0.0/8 gw 10.0.0.1

**DNS 構成**

別個のファイルにプライマリー DNS とセカンダリー DNS の両方の構成が入っています。 この構成ファイルを編集し、以下の情報を入力する必要があります。 プライベート・ネットワークにアクセスできない仮想マシンの場合は、サーバー IP を有効な DNS サーバーの IP アドレスに置き換える必要があります。

* /etc/resolv.conf
      nameserver 10.0.80.11
      nameserver 10.0.80.12

これらの変更を有効にするには、サーバー上でネットワーキングを再始動する必要があります。 ネットワークを再始動するために、次のコマンドを実行します。

* */etc/init.d/network restart*

**注:** 192.0.2.0/24 は、RFC1166 および RFC5737 に従って、パブリック IP ドキュメンテーションとして使用されます。 これらの IP アドレスをサーバー上で使用しないでください。