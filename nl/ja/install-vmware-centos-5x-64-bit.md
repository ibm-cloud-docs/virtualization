---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# CentOS 5.x (64 ビット) への VMware のインストール
{: #installing-vmware-on-centos-5-x-64-bit-}

始めに、root ユーザーでサーバーにログインします。

## VMware のインストールの準備
{: #preparing-to-install-vmware}

1. [VMware インストーラー ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://vmware.com/download/server/){: new_window} をダウンロードします。 ダウンロードのリンクをクリックし、EULA に同意し、以下のように LinuxTarball (この例では VMware-server-1.0.3-44356.tar.gz) をダウンロードします。

* `# wget –O vmware-server.tar.gz https://download3.vmware.com/software/vmserver/VMware-server-1.0.3-44356.tar.gz`

2. ソフトウェアをダウンロードしたら、ライセンス・キーを取得する必要があります (無償版の VMWare Server ではライセンス・キーは無料です)。 登録するには、[VMware ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://register.vmware.com/content/registration.html){: new_window} を参照してください。

3. 以下のようにして、.tar ファイルを解凍します。

* `# tar -xzvf vmware-server.tar.gz`

## インストールを開始する前に
{: #before-you-begin-installation}

インストールを始める前に、以下の前提条件を確認してください。

```
# yum update
# yum install libXtst.i386
# yum install libXrender.i386
# yum install xinetd
```

## VMWare Server のインストール
{: #installing-vmware-server}

1. 以下のディレクトリーに移動します。

* `# cd vmware-server-distrib/`

2. 以下のようにして、VMware インストール・スクリプトを実行します。

* `# ./vmware-install.pl`

3. インストールの際に、基本的な質問が表示され、どのディレクトリーに VMware のパーツを作成してインストールするかを尋ねられます。 ここでは、デフォルトを受け入れます。 使用条件を受諾すると、インストールを進めることができます。

4. 次の質問が表示されることがあります。「None of the pre-built vmmon modules for VMware Server is suitable for your running kernel. Do you want this program to try to build the vmmon module for your system (you need a C compiler installed on your system)?」(VMware Server のビルド済みの vmmon モジュールが実行中のカーネルに適していません。このプログラムで、システムの vmmon モジュールをビルドしますか? (システムに C コンパイラーがインストールされている必要があります))。
* この質問には**「yes」**と答えてください (これがデフォルトです)。

## VMware ネットワーキングのセットアップ
{: #setting-up-vmware-networking}

以下の質問のプロンプトが出されます。

1. 「Do you want to network your virtual machines?」(仮想マシンのネットワーキングを設定しますか)
* **「yes」**と答えます。 パブリック・ネットワーク・デバイス用のネットワーク・セットアップを作成して、仮想マシン上でインターネットにアクセスできるようにする必要があります。

2. 「Your computer has multiple ethernet interfaces available: eth0, eth1. Which one do you want to bridge to vmnet0?」(コンピューターには複数のイーサネット・インターフェースがあります。どちらを vmnet0 にブリッジしますか?)
* すべての {{site.data.keyword.cloud}} サーバーは、パブリック・ネットワークを eth1 上で実行し、プライベート・ネットワークを eth0 上で実行するようにセットアップされます。 VMware では、vmnet0 のデフォルトのブリッジ・デバイスは eth0 です。 Enter キーを押さずに、eth1 と入力してください。

## プライベート・ネットワークのブリッジング
{: #bridging-the-private-network}

「Do you want to configure another bridged network?」(別のブリッジ接続ネットワークを構成しますか? ) という質問が表示されるので、**yes** または **no** で答えてください。

プライベート・ネットワーク上でサービスやその他のアプリケーションを実行する予定がある場合は、この質問に「yes」と答えて先に進みます (プライベート・ネットワークを使用しないことが明確な場合は別です)。プライベート・ネットワークに対するネットワーク・ブリッジが作成されます。 Enter キーを押すと、自動的に eth0 がインターフェースとして使用されます。残っているネットワーク・カードは eth0 しかないからです (サーバーにあるネットワーク・カードは 2 つのみ)。

## その他のネットワーキング設定
{: #other-networking-settings}

VMware サーバーのネットワーク・セットアップに関する質問がさらにいくつか表示されます。 以下の推奨事項に沿って進みます。

* *「Do you want to be able to use NAT networking in your virtual machines?」(仮想マシンで NAT ネットワーキングを使用可能にしますか?)*

- 「yes」と答えて進みます。

* *「Do you want this program to probe for an unused private subnet?」(このプログラムで未使用のプライベート・サブネットがないか調べますか?)*

- 「yes」と答えて進みます。

- このプロセスが完了したら、別の NAT ネットワークを構成していないことを確認してください。

* *「Do you want to be able to use host-only networking in your virtual machines?」(仮想マシンでホスト専用ネットワーキングを使用可能にしますか?)*

- 「yes」と答えて進みます。

* *「Do you want this program to probe for an unused private subnet?」(このプログラムで未使用のプライベート・サブネットがないか調べますか?)*

- 「yes」と答えて進みます。

- このプロセスが完了したら、別のホスト専用ネットワークを構成していないことを確認してください。

## listen ポートの指定
{: #specifying-listening-port}

次の質問で、VMware Server が listen するポートを尋ねられます。 デフォルトのポート 904 のままでかまいません。

## 仮想マシンの保管
{: #storing-the-virtual-machines}

次に、インストーラーから*「In which directory do you want to keep your virtual machine files?」*(どのディレクトリーに仮想マシン・ファイルを保持しますか?) と尋ねられます。 デフォルトの場所は /var/lib/vmware/virtual machines です。 冗長 RAID アレイや大規模な 2 次ハード・ディスクなど、ディスク・スペースが十分ある場所に仮想マシンを配置してください。 必ず、仮想マシン用に十分な空き容量があることを確認してください。 ここでは、大規模なディスクにマウントされているマウント・ポイント /data/vm を使用します。

## VMware のシリアル番号の提供
{: #providing-the-serial-number-for-vmware}

インストールの最後の部分では、VMware のライセンス・キーとシリアル番号を入力する必要があります。 まだライセンス・キーがない場合は、[VMware サイト ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://register.vmware.com/content/registration.html){: new_window} を参照してください。 この VMware Server のシリアル番号がある場合は、その番号をプロンプトに入力して Enter キーを押します。

この時点で、以下のような文が表示されます。

    「The configuration of VMware Server 1.0.3 build-44356 for Linux for this running kernel completed successfully.」(この実行中のカーネル用の Linux 版 VMware Server 1.0.3 ビルド 44356 の構成が正常に完了しました。)

これで、サーバーに VMware がセットアップされました。 次は、VMware Server Console (VMware Server の GUI クライアント) をダウンロードし、仮想マシンを構成してインストールする必要があります。

## VMware Server Console のダウンロード
{: #downloading-vmware-server-console}

VMware Server Console は、VMware Server のクライアント・アプリケーションです。 このコンソールを使用して、仮想マシンの作成、構成、インストールを実行できる VMware Server を管理します。 このアプリケーションをインストールするには、VMware Server の Windows クライアント・パッケージを [VMware ダウンロード ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://vmware.com/download/server/){: new_window} からダウンロードします。 このパッケージは .zip ファイルです。 ダウンロードしたら、パッケージを解凍して VMware-console-1.0.3-x ファイルをインストールします。 このインストールが完了したら、VMware Server Console のインストールは終了です。VMware Server の構成を開始できます。

## VMware Console へのログイン
{: #logging-in-to-the-vmware-console}

VMware Server Console を、インストールしたコンピューターから開きます。 ロードされると、「ホストの切り替え (Switch Host)」(ログイン) 画面が表示されます。 VMware Server では Linux システムのユーザー名とパスワードを使用してユーザーを認証するので、VMware にログインするには、ユーザー名 (特に root) を使用する必要があります。 次の資格情報を使用します。

* **ホスト名:** IP アドレスとポート (67.228.160.201:904 など)<br />
* **ユーザー名:** root<br />
* **パスワード:** password (システムの実際のルート・パスワードを使用してください)

## ファイアウォール・ルール (IPTable) の構成
{: #configuring-the-firewall-rules-iptables-}

認証の問題ではないのに VMware サーバーに接続できない場合は (ユーザー名とパスワードのエラーを受け取った場合はユーザーまたはパスワードが間違っています)、ファイアウォールが VMware Server への接続をブロックしている可能性があります。 解決策として、以下の IPTable ルールを /etc/sysconfig/iptablesfile に追加してみてください。 **注:** 命名規則がサーバー構成に従っていることを確認してください。

- `# -A FWALL-INPUT -p tcp -m tcp -s 0/0 --dport 904 -j ACCEPT`
