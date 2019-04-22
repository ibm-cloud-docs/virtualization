---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-14"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# VMware ESXi の準備
{: #getting-started-with-vmware-esxi}

ESX サーバーをプロビジョンしたら、そのサーバーにアクセスするための最初の手順は、{{site.data.keyword.cloud}} のプライベート・ネットワーク VPN に接続することです。 デフォルトでは、{{site.data.keyword.cloud_notm}} でデプロイされたすべての VMware サーバーは、IP アドレスが 1 つ構成されたプライベートのネットワーク・インターフェースまたはネットワーク・ボンドしか装備していません。 このようにデプロイされるのは、これが新規 ESX サーバーをパブリック・インターネットにさらさないようにするための追加のセキュリティー手段であるからです。 この追加のセキュリティーが適用された結果、サーバーは、{{site.data.keyword.cloud_notm}} プライベート 10.x.x.x IP アドレスでのみ listen します。このアドレスには、{{site.data.keyword.cloud_notm}} VPN 経由でアクセスするか、または、新規 VMware サーバーが存在するのと同じプライベート VLAN にアクセスできる既存の {{site.data.keyword.cloud_notm}} ホストからアクセスするしかありません。

{{site.data.keyword.cloud_notm}} VPN について詳しくは、[仮想プライベート・ネットワーキング (VPN) の概説](/docs/infrastructure/iaas-vpn?topic=VPN-gettingstarted-with-virtual-private-networking#gettingstarted-with-virtual-private-networking)を参照してください。

## ESX ホストにアクセスする
{: #accessing-your-esx-host}

{{site.data.keyword.cloud_notm}} VPN に接続したら、新規 VMware サーバーと通信できます。 ESX は vSphere Client を使用して管理します。 プライベート・ネットワーク IP アドレスを Web ブラウザーに入力し、表示されたページで「vSphere Client のダウンロード」リンクをクリックすると、新規 VMware サーバーから取得できます。

vSphere Client をダウンロードしてローカル・ワークステーションにインストールしたら、アプリケーションを開始して、サーバーのプライベート・アドレスとログイン資格情報を入力できます。 ログイン資格情報は、{{site.data.keyword.slportal_full}}の「デバイス」ページで「パスワード」タブをクリックすると表示されます。

1. サーバーのプライベート IP アドレス、root ユーザー、およびパスワードを、vSphere Client の該当するフィールドに入力し、**「ログイン」**をクリックして接続します。
2. サーバーに接続されたので、**「インベントリ」**を選択して新規 ESX ホストに移動します。
3. 表示されたページに、構成可能なノードとして、ESX ホストが表示されます。 ここから、いくつかの方法で仮想マシンをデプロイできます。 その 1 つは、選択したオペレーティング・システムのインストール ISO をサーバーのローカル・データ・ストアにアップロードする方法です。 ISO をアップロードすると、VM の作成時にその ISO をインストール・メディアとして選択できます。  

## ISO をアップロードする
{: #uploading-an-iso}

以下の手順を実行して、ISO をサーバーのデータ・ストアにアップロードします。

1. 左側のペインにあるサーバー (サーバー・アイコンとそのプライベート IP アドレスで表されます) を強調表示し、右側のペインにある**「構成」**タブを選択します。
2. **「ハードウェア」>「ストレージ」**を選択します。 **「表示」**が**「データストア」**に設定されていることを確認します。
3. 該当するデータ・ストアを右クリックして、**「データストアの参照」**をクリックします。
4. 表示されたページには、データ・ストアのファイルを参照、アップロード/ダウンロードするためのファイル・マネージャーがあります。  
  * ISO をアップロードするには、ボリューム・アイコン (上向きの矢印付き) をクリックして、**「ファイルのアップロード」**を選択します。
5. データ・ストアにアップロードするローカル・ファイル・システム上の ISO ファイルを選択して、**「開く」**をクリックします。
6. 選択した ISO がデータ・ストア内に配置されました。
7. これで、VMWare サーバー上にインストール・メディアが置かれたので、[仮想マシンの作成](/docs/infrastructure/vmware?topic=VMware-create-esx-vm#create-esx-vm)に進むことができます。
