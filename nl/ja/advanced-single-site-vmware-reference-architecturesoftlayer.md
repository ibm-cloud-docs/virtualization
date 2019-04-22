---
copyright:
  years: 2014, 2019
lastupdated: "2019-01-15"

subcollection: virtualization

keywords: vmware, ESXi
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# 高度な単一サイト型 VMware リファレンス・アーキテクチャー
{: #advanced-single-site-vmware-reference-architecture}

以下の手順に従って、高度な単一サイト型 vSphere 環境を作成します。 以下の手順では、VMware で推奨されているベスト・プラクティスを使用して {{site.data.keyword.cloud}} リファレンス・アーキテクチャーのデプロイメントを定義します。

このリファレンス・アーキテクチャーの目的は、共有ストレージ、VMware High Availability (HA) と vSphere Distributed Resource Scheduler (DRS)、および {{site.data.keyword.cloud_notm}} のゲートウェイまたはファイアウォールを使用する環境をプロビジョンすることです。 高度な単一サイト型 VMware リファレンス・アーキテクチャーはほとんどの実稼働デプロイメント環境に適しており、これらのデプロイメント環境は、ワークロードに応じて規模を拡大/縮小できるとともに、オンプレミスの実装環境の代わりに使用したり、ハイブリッド IT シナリオに拡張したりできます。

## 環境の概要
{: #environment-overview}

ここで紹介している高度な VMware 環境は、管理クラスターと容量クラスターという 2 つの別々のクラスターを管理する 1 つのデータ・センターからなります。 この構成は、要件に応じて単一の 4 ノード・クラスターを使用してセットアップできます。 管理クラスターには、インフラストラクチャーを管理するために使用される次の仮想マシン (VM) が含まれています。

* VMware vCenter Server 5.5/6.0 Appliance
* Microsoft Windows 2012 R2 Standard (Active Directory と Domain Name System (DNS) のロール付き)

容量クラスターには、VM を作成して実行するために必要なリソースとインフラストラクチャーが含まれています。 ネットワーキングについては、この環境は 3 つのプライベート内部 VLAN と、外部通信用に使用される単一のパブリック VLAN で構成されています。 表 1 では、この環境全体にわたって使用される VLAN タイプと VLAN 名を示しています。

|VLAN タイプ|VLAN 名|説明|                                                                             |
|---|---|---|
|プライマリー・プライベート| 管理| 物理 ESXi ホストと仮想サーバーを管理してこれらにアクセスするために割り当てられます。|
|プライマリー・プライベート| ストレージ| 各 ESXi ホストに接続された共有ストレージを管理してこの共有ストレージにアクセスするために割り当てられます。|
|プライマリー・プライベート| VM アクセス | 各 ESXi ホスト上で実行される仮想マシンに割り当てられます。|
|プライマリー・パブリック | パブリック | パブリック・ネットワークからのアクセスを必要とする仮想マシンなどのデバイスに割り当てられます。|
<caption>表 1. プライマリー VLAN</caption>

共有ストレージについては、シングル・テナント型の共有ストレージ・サーバーである OS Nexus QuantaStor、または {{site.data.keyword.cloud_notm}} の Endurance または Performance ストレージ・サービスを使用できます。 どちらの場合でも、共有ストレージ・デバイスを使用して管理クラスターと容量クラスターの両方で VM を格納します。 ストレージ・オプションについて詳しくは、[{{site.data.keyword.cloud_notm}} Storage solutions ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.ibm.com/cloud/storage){: new_window} を参照してください。

このストレージ環境は、NFS ボリュームをサポートするように構成されています。

## ステップ 1: プライマリー・パブリック VLAN とプライマリー・プライベート VLAN の注文
{: #step-1-ordering-primary-public-and-private-vlans}

**注:** VLAN の注文は確認と承認を受ける必要があります。 VLAN の注文が自動承認されるための特定の条件や要件は設けられていません。 VLAN の注文はさまざまな理由により却下される可能性があります。

このステップでは、4 つの VLAN を作成します。管理用、ストレージ用、VM 用、およびパブリック・アクセス用に 1 つずつです。 これらの 4 つの VLAN を作成してから、サーバーを注文することをお勧めします。 VLAN を最初に注文すると、サーバーが正しい VLAN と正しいデータ・センターに確実に配置されます。

この環境は、5 つの ESXi ホスト (管理クラスター用に 2 つ、容量クラスター用に 3 つ) と 1 つの仮想サーバーで構成されています。 プライベート管理 VLAN は、その VLAN をサポートするための 16 個の IP アドレスを持つ 1 つのサブネットからなります。 この環境には単一のストレージ・サーバーが含まれており、VM ではポータブル・サブネットが使用されるため、ストレージ用と VM トラフィック用のプライマリー・プライベート VLAN は 8 つのアドレスからなります。 管理ネットワーク用にさらに広いサブネット範囲が必要な場合は、ESXi ホストの数を計算して 2 を掛けることで範囲を調整してください。また、これらの VLAN を作成する対象となるデータ・センターも指定してください。

{{site.data.keyword.cloud_notm}} にログインした後に、**「サポート」>「チケットの追加」**を選択して、管理 VLAN と仮想マシン VLAN のサポート・チケットをオープンします。 **表 2 **に示している内容を各フィールドに入力します。

|フィールド|値|
|---|---|
|件名|プライベート・ネットワークに関する質問|
|タイトル|VLAN の注文|
|詳細|3 つのプライマリー・プライベート VLAN と 1 つのプライマリー・パブリック VLAN をプロビジョンします。 次のアドレッシング方式を各 VLAN について関連付けます。 <br/><ul><li>プライマリー・プライベート VLAN について 1x18 (16 個のアドレス) を関連付けます</li><li>プライマリー・プライベート VLAN について 2x29 (8 つのアドレス) を関連付けます</li><li>プライマリー・パブリック VLAN について 1x29 (8 つのアドレス) を関連付けます</li></ul>|
|これらの追加の VLAN が必要な理由は何ですか? (Why do I need these additional VLANs?)|ホスト、ストレージ、および VM を VMware 環境用の異なるネットワークに配置するためです。
|パブリック VLAN やプライベート VLAN が必要ですか? (Do I need public and or private VLANs?)|プライベートおよびパブリック|
|VLAN はいくつ必要ですか? (How many VLANs do I need?)|プライベート = 3、パブリック = 1|
|IP アドレスはいくつ必要ですか? (How many IP addresses do I need?)|各 VLAN について 8 から 16|
|VLAN の前にどのルーターを配置する必要がありますか? (What router do I the VLANs need to be behind?)|VLAN がすべて同じポッド内にあっても問題ありません。|
|VLAN はどのポッド内に配置される必要がありますか? (Which pod do the VLANs need to be in?)|すべての VLAN は <データ・センター名> 内の同じポッドに配置されます。|
<caption>表 2. サポート・チケットの情報</caption>

VLAN がプロビジョンされた後に、VLAN 番号、サブネット範囲、およびゲートウェイを確認して、それらを論理 vSphere ネットワークに割り当てます。 『付録 A: VLAN ワークシート』のワークシートを使用して、VLAN と関連情報を記録できます。 例えば次のようにします。

|VLAN タイプ|VLAN 番号|IP 範囲|ゲートウェイ|目的|
|---|---|---|---|---|
|プライマリー・プライベート|1101|10.X.Y.Z/28|10.X.Y.1|管理|
|プライマリー・プライベート|1102|10.A.B.C/29|10.A.B.1|ストレージ|
|プライマリー・プライベート|1103|10.Q.R.S/29|10.Q.R.1|仮想マシン|
|プライマリー・パブリック|2101|75.S.T.U/29|75.S.T.1|パブリック・アクセス|
<caption>表 3. プライマリー VLAN の例</caption>

**注:** VLAN がプロビジョンされるまで、次のステップに進まないでください。

## ステップ 2: ポータブル・プライベート IP アドレスの注文
{: #step-2-ordering-portable-private-ip-addresses}

ステップ 2 では、容量クラスター内の VM 用、VM カーネル・ストレージへのアクセス用、および管理 VM 用のポータブル・プライベート・サブネットの作成を要求しました。 各 VLAN サブネットに必要なアドレスの数を決定する必要があります。 この環境では、次のアドレスを注文します。

* 管理 VLAN - 1x8 アドレス /29 - vCenter Appliance 用に 1 つのアドレス、DNS と Active Directory 用に 1 つのアドレス
* ストレージ VLAN - 1x16 アドレス /28 - 異なるサブネットを使用して共有ストレージ・デバイスにアクセスすることで、ストレージ用の同じ VLAN 上に 2 つのサブネットを作成して、各 ESXi ホスト上に 2 つの VM カーネル・ポートを作成します。
* VM VLAN - 1x32 アドレス /27 - これらのアドレスは、容量クラスター内の VM に IP アドレスを割り当てるために使用されます。

任意の数量を注文する際は、今後の 30 日間および 6 カ月間に必要な IP アドレスの数を考慮してください。 もう 1 つの重要な留意事項として、{{site.data.keyword.cloud_notm}} では 1 つのポータブル・サブネット・ブロックにつき 3 つまたは 4 つの IP アドレスが予約されます。 このため、4 つの IP アドレスを注文した場合は、1 つの IP アドレスのみが使用可能になります。または、ポッドが Hot Standby Router Protocol をサポートしている場合は、使用できる IP アドレスはゼロ個になります。

以下の手順に従って、作成する各サブネットの各 VLAN について、1 ブロックのポータブル IP アドレスを注文します。

1. ブラウザー・ウィンドウを開いて、{{site.data.keyword.cloud_notm}} にログインします。
2. **「アカウント」>「注文」**を選択します。
3. ポップアップ・ウィンドウで、**「ネットワーク」>「サブネット/ IP」>「注文」**に移動します。
4. ドロップダウン・メニューから、**「ポータブル・プライベート」**を選択します。
5. **「XX 個のポータブル・プライベート IP アドレス (XX Portable Private IP address)」**を選択して、**「続行」**をクリックします。 **注:** _XX_ は IP アドレスの数を示します。
6. IP アドレス・ブロックに関連付ける VLAN を選択して、**「続行」**をクリックします。
7. 画面上で情報を入力して、**「続行」**をクリックします。

IP アドレスの作成は短時間で完了し、**「ネットワーク」>「IP 管理」**から**「サブネット」**を選択することでその結果を表示できます。 『付録 A: VLAN ワークシート』にあるワークシートにこれらの IP アドレスを記録できます。

## ステップ 3: 仮想サーバーの注文
{: #step-3-ordering-a-virtual-server}

Windows 2012 R2 Standard の仮想サーバーは、ISO 用のユーティリティー・サーバーとして使用するためにプロビジョンされているとともに、このステップで環境にアクセス可能にするためにプロビジョンされています。

1. ブラウザー・ウィンドウを開いて、{{site.data.keyword.cloud_notm}} にログインします。
2. **「アカウント」>「注文」**を選択します。
3. **「仮想サーバー」>「時間単位」または「月単位」**に移動します。
4. 仮想サーバーをプロビジョンするための適切なデータ・センター (VLAN が作成された場所) を選択して、各オプションを次のように指定します。
  * コンピューティング・インスタンス - 1x2.0 GHz コア
  * RAM: 4 GB
  * オペレーティング・システム: Windows Server 2012 R2 Standard Edition (64 ビット)
  * 1 番目のディスク: 100 GB (SAN)
  * アップリンク・ポート速度 - 1 Gbps のパブリック/プライベート・ネットワーク・アップリンク
5. **「注文を続行する (Continue Your Order)」**をクリックして、**「ご注文の要約と請求金額」**画面でバックエンドとフロントエンドの VLAN を選択します。 **注:** データ・センター内の正しいポッドにユーティリティーを配置できるようにするために、VLAN の選択は重要です。 このサンプル環境では、バックエンド VLAN は管理 VLAN (1101) であり、フロントエンド VLAN はパブリック VLAN (2101) です。
6. サーバーのホスト名とドメイン名を入力して、**「注文」**をクリックします。

## ステップ 4: ESXi ホストとゲートウェイ/ファイアウォールの注文
{: #step-4-ordering-esxi-hosts-and-gateway-firewall}

仮想サーバーがプロビジョンされる間に、ESXi ホストと Brocade vRouter (Vyatta) のゲートウェイ/ファイアウォール・アプライアンスを注文する必要があります。 これらすべてのサーバーを同時に注文することで、これらが同じポッドに同時に配置されるようにしてください。 ハードウェアを別の機会に注文すると、ホストとファイアウォールが {{site.data.keyword.cloud_notm}} データ・センター内の別のポッドに配置される可能性があります。

### ESXi ホスト
{: #esxi-hosts}

この環境用に注文される各 ESXi ホストでは、VMware ESXi 5.5 がオペレーティング・システムです。 {{site.data.keyword.cloud_notm}} vSphere ライセンスの使用を希望する場合は、使用量に基づいて月額課金が発生します。

もう 1 つの選択肢は、独自の ISO を使用して ESXi をインストールすることです。 このプロセスの手順については、[リモート・コンソールと仮想メディアを使用した VMware vSphere ESXi のインストール](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi)を参照してください。 独自の ISO を使用して ESXi をインストールすることを希望する場合は、注文プロセス時に管理ホストと容量ホストのオペレーティング・システムとして**「オペレーティング・システムなし」**を選択してください。

**注:** この実装では、vSphere 分散仮想スイッチを使用するために Enterprise Plus のライセンスが必要です。 お持ちのライセンスが Enterprise Plus に対して有効でない場合は、{{site.data.keyword.cloud_notm}} によって提供される VMware Service Provider Program (VSPP) ライセンスを使用する必要があります。

### 管理ホストの注文
{: #ordering-management-hosts}

以下の手順に従って、管理ホスト・サーバーを注文します。

1. ブラウザー・ウィンドウを開いて、{{site.data.keyword.cloud_notm}} にログインします。
2. **「アカウント」>「注文」**を選択します。
3. **「ベア・メタル・サーバー」>「月単位」**を選択します。
4. 管理クラスターの要件を満たす適切なサーバーをサーバー・リスト画面で選択します。 **注:** ESXi 5.5 の最小要件は単一のデュアル・コア・プロセッサー、4 GB の RAM、および 1 Gb のイーサネット・コントローラーです。
5. ESXi サーバーをプロビジョンするための適切なデータ・センター (VLAN が作成された場所) を選択して、各オプションを次のように指定します。
  * 数量: 2
  * RAM: 32 GB
  * オペレーティング・システム: VMware ESXi 5.5 ([リモート・コンソールと仮想メディアを使用した VMware vSphere ESXi のインストール](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi)を使用している場合は「オペレーティング・システムなし」)
  * ハード・ディスク: ディスク 1 & 2: RAID 1 の 500 GB SATA
  * パブリック帯域幅: 「プライベート・ネットワークのみ」->「0 GB 帯域幅」
  * アップリンク・ポート速度: 10 Gbps の冗長プライベート・ネットワーク・アップリンク
6. **「注文を続行する (Continue Your Order)」**をクリックします。
7. **「サーバーの追加」**をクリックして、容量クラスターの ESXi ホストを注文に追加開始します。

### 容量ホストの注文
{: #ordering-capacity-hosts}

1. 容量クラスター・ホストの要件を満たす適切なサーバーをサーバー・リスト画面で選択します。 **注:** ESXi 5.5 の最小要件は単一のデュアル・コア・プロセッサー、4 GB の RAM、および 1 GB のイーサネット・コントローラーです。
2. ESXi サーバーをプロビジョンするための適切なデータ・センター (VLAN が作成された場所) を選択して、各オプションを次のように指定します。
  * 数量: 3
  * RAM: 128 GB
  * オペレーティング・システム: VMware ESXi 5.5 ([リモート・コンソールと仮想メディアを使用した VMware vSphere ESXi のインストール](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi)を使用している場合は「オペレーティング・システムなし」)
  * ハード・ディスク: ディスク 1 & 2: RAID 1 の 500 GB SATA
  * パブリック帯域幅: 「プライベート・ネットワークのみ」>「0 GB の帯域幅」
  * アップリンク・ポート速度: 10 Gbps の冗長プライベート・ネットワーク・アップリンク
3. **「注文を続行する (Continue Your Order)」**をクリックします。

### 構成の完了
{: #completing-configuration}

これで、ESXi ホストがショッピング・カートに入りました。 デバイスが正しくプロビジョンされるためには、パブリック VLAN (該当する場合)、プライベート VLAN、ホスト名、およびドメインをこれらのデバイスに割り当てる必要があります。

1. 以下の VLAN を割り当てて、デバイスのホスト名を作成します。

* ESXi ホスト - バックエンド VLAN: (1101)
* バックエンド VLAN: (1101)
* フロントエンド VLAN: (2101)

2. 支払方法を選択して、ご使用条件に同意して、**「注文を確定する」**をクリックします。 **重要:** サーバーがプロビジョンされて、前のステップで注文した仮想サーバーまたは VPN を介してアクセス可能になるまで、ステップ 5 に進まないでください。

## ステップ 5: BCS スイッチ上の VLAN のトランキング
{: #step-5-trunking-vlans-on-bcs-switches}

デフォルトでは、ポートはアクセス・モードでバックエンド・カスタマー・スイッチ (つまり、ポッド内のプライベート・ネットワーク・スイッチ。バックエンド・カスタマー・スイッチ (BCS) など) に配置されます。 このため、ESXi ホストに接続されたポートをトランキングする必要があります。そうすることで、これらのホストはストレージにアクセス可能になり、VM はプライベート・ネットワーク上で通信可能になります。

VLAN をトランキングするためのチケットをオープンする前に、プライベート・ネットワーク上にあるネットワーク・インターフェースを確認する必要があります。 インターフェースを確認するには、各 ESXi サーバーの**「Device Details」**に移動して、**「Network」>「Private」**に移動します。 この環境では、`eth0` と `eth2` がプライベート・ネットワーク・アダプターです。

ESXi ホストの VLAN をトランキングすることに加えて、管理ホストと容量ホストの NIC をアンバインドする必要もあります。 これらの NIC をアンバインドする理由は、Link Aggregation Control Protocol (LACP) はソフトウェア iSCSI マルチパスに対応していないからです。

VLAN をトランキングして NIC をアンバインドするには、次の手順に従ってチケットをオープンする必要があります。

1. ブラウザー・ウィンドウを開いて、{{site.data.keyword.cloud_notm}} にログインします。
2. **「サポート」、「チケットの追加」**を選択します。
3. 次の情報を入力します。
  * 件名: プライベート・ネットワークの質問
  * タイトル: VLAN のトランキングと NIC のアンバインド
  * 詳細: [各 ESXi ホストのリスト] というホストについて eth0 と eth2 の NIC ペア上の VLAN である `<Management VLAN>`、`<Storage VLAN>`、および `<VM VLAN>` をトランキングします。 また、[各 ESXi ホストのリスト] というサーバー上のプライベート NIC (eth0 と eth2) をアンバインド (LACP を削除) します。
4. **「チケットの追加」**をクリックします。

<> で囲まれた VLAN を必ずご使用の実際の VLAN に置き換えてください。

## ステップ 6: 管理ホストのネットワーキングの構成
{: #step-6-configuring-management-host-networking}

サーバーがプロビジョンされて、VLAN がトランキングされたら、管理クラスター内のホスト上でネットワーキングをセットアップする必要があります。 この構成のために、管理クラスター用の vSphere 標準スイッチを使用します。 vMotion とフォールト・トレランスのポート・グループを除いて、ポータブル IP アドレスを使用して VM カーネル・ポート・グループを構成します。 **注:** トラフィックをルーティングする必要はないため、vMotion とフォールト・トレランスについては独自の IP 方式を使用します。 ただし、vMotion とフォールト・トレランスの機能を使用するには、すべてのホストは、クラスター内の他のホストと同じサブネット上に存在している必要があります。 このサブネットをルーティングする必要がある場合は、{{site.data.keyword.cloud_notm}} のポータブル IP アドレスを使用することをお勧めします。

ポート・グループを構成するには、{{site.data.keyword.cloud_notm}} の管理 VPN を介してホストにアクセスするワークステーションまたはユーティリティー仮想サーバーに、vSphere クライアントがインストールされている必要があります。

1. ブラウザー・ウィンドウを開いて、{{site.data.keyword.cloud_notm}} にログインします。
2. ユーティリティー・サーバーまたは {{site.data.keyword.cloud_notm}} の VPN に接続した後に、vSphere クライアントを起動します。
3. いずれかの管理 ESXi ホストの IP アドレス、ユーザー名、およびパスワードを入力します。 (ESXi ホストのルート・パスワードを確認するには、**「Device Details」>「Passwords」**を選択します。)
4. **「Configurations」>「Networking」**に移動して、vSphere 標準スイッチ (通常は vSwitch0) 上の以下のポート・グループを作成または変更します。

管理クラスター内の各ホストについて、上記の手順を実行します。

### vSwitch0 のプロパティー
{: #vswitch0-properties}

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 4. vSwitch0 のプロパティー</caption>
<tbody>
<tr><th>ネットワーク・アダプター</th><th>vmnicX および vmnicY</th></tr>
<tr><td>ロード・バランシング</td><td>起点仮想ポート ID に基づくルーター</td></tr>
<tr><td>アクティブなアダプター</td><td>vmnicX および vmnicY</td></tr>
</tbody>
</table>

### 既存の仮想マシン・ポート・グループの編集
{: #edit-existing-virtual-machine-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 5. vmPG-Management ポート・グループ</caption>
<tbody>
<tr><th>ネットワーク・ラベル</th><th>*vmPG-Management*</th></tr>
</tbody>
</table>

### 既存の VM カーネル・ポート・グループの編集
{: #edit-existing-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 6. vmkPG-Management ポート・グループ</caption>
<tbody>
<tr><th>ネットワーク・ラベル</th><th>*vmkPG-Management*</th></tr>
</tbody>
</table>

### vMotion VM カーネル・ポート・グループの追加
{: #add-vmotion-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 7. vMotion ポート・グループ</caption>
<tbody>
<tr><th>接続タイプ</th><th>VMKernel</th></tr>
<tr><td>ネットワーク・ラベル</td><td>*vmkPG-vMotion*</td></tr>
<tr><td>VLAN ID</td><td>*&lt;ストレージ VLAN&gt; (1102)*</td></tr>
<tr><td>vMotion トラフィック</td><td>有効</td></tr>
<tr><td>IP アドレス</td><td>*172.16.10.X/24*<br/><br/>*異なるサブネットであってもかまわないユーザー定義アドレス。 各ホスト上の他の vMotion アドレスが同じサブネット上にあることを確認してください。*</td></tr>
<tr><td>サブネット・マスク</td><td>255.255.255.0</td></tr>
</tbody>
</table>

### フォールト・トレランス VM カーネル・ポート・グループの追加
{: #add-fault-tolerance-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 8. FT ポート・グループ</caption>
<tbody>
<tr><th>接続タイプ</th><th>VM カーネル</th></tr>
<tr><td>ネットワーク・ラベル</td><td>*vmkPG-FT*</td></tr>
<tr><td>VLAN ID</td><td>*&lt;ストレージ VLAN&gt; (1102)*</td></tr>
<tr><td>フォールト・トレランスのロギング</td><td>有効</td></tr>
<tr><td>IP アドレス</td><td>*172.16.20.X/24*<br/><br/>*必要に応じて異なるサブネットであってもかまわないユーザー定義アドレス。 各ホスト上の他の FT アドレスが同じサブネット上にあることを確認してください。*</td></tr>
<tr><td>ネットマスク</td><td>255.255.255.0</td></tr>
</tbody>
</table>

### ストレージ VM カーネル・ポート・グループの追加
{: #add-storage-vm-kernel-port-group}

各ポータブル IP アドレスの**「メモ」**セクションを、割り当てられた VM カーネル・ポートおよびホストの名前に基づいて更新します。 **「メモ」**セクションにアクセスするには、{{site.data.keyword.slportal_full}} に移動して、**「ネットワーク」>「IP 管理」>「サブネット」>[サブネット]** を選択します。

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 9. ストレージ・ポート・グループ</caption>
<tbody>
<tr><th>接続タイプ</th><th>VM カーネル</th></tr>
<tr><td>ネットワーク・ラベル</td><td>*vmkPG-Storage*</td></tr>
<tr><td>VLAN ID</td><td>*&lt;ストレージ VLAN&gt; (1102)*</td></tr>
<tr><td>IP アドレス</td><td>*ポータブル・プライベート・アドレス*<br/><br/>*ストレージ VLAN にバインドされたポータブル・プライベート・アドレスから選択された IP アドレス。*</td></tr>
<tr><td>ネットマスク</td><td>*IP 範囲に関連付けられたサブネット・マスク*</td></tr>
</tbody>
</table>

### パブリック・アドレス・ポート・グループの追加
{: #add-public-address-port-group}

各ポータブル IP アドレスの**「メモ」**セクションを、割り当てられた VM カーネル・ポートおよびホストの名前に基づいて更新します。 **「メモ」**セクションにアクセスするには、{{site.data.keyword.slportal}} に移動して、**「ネットワーク」>「IP 管理」>「サブネット」>[サブネット]** を選択します。

<table border="1" cellpadding="0" cellspacing="0">
<caption>表 10. パブリック・ポート・グループ</caption>
<tbody>
<tr><th>接続タイプ</th><th>仮想マシン</th></tr>
<tr><td>ネットワーク・ラベル</td><td>vmPG-Public</td></tr>
<tr><td>VLAN ID</td><td>&lt;プライマリー・パブリック VLAN&gt; (2101 など)</td></tr>
</tbody>
</table>

## ステップ 7: ISO イメージと vCenter 仮想アプライアンスのダウンロードまたはアップロード
{: #step-7-download-or-upload-iso-images-and-vcenter-virtual-appliance}

これで、この環境に VMware vCenter 仮想アプライアンスをデプロイして、DNS 用、Active Directory 用、または BIND 用の仮想マシンをインストールする準備ができました。 ただし、デプロイ前にイメージをダウンロードする必要があります。 イメージをダウンロードするには、既にプロビジョンされた仮想サーバーにリモート・デスクトップ接続して、この環境用にその仮想サーバー上の適切なイメージをダウンロードします。

* Active Directory/Windows DNS: Windows Server 2008R2/Windows Server 2012 の ISO イメージ (ライセンス交付済みメディア)
* Linux BIND: [CentOS Install Image ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://isoredirect.centos.org/centos/6/isos/x86_64/){: new_window}
* [vCenter Virtual Appliance ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://my.vmware.com/web/vmware/info/slug/datacenter_cloud_infrastructure/vmware_vsphere/5_5){: new_window} (有効な VMware サブスクリプションが必要)

ISO をダウンロードした後に、そのイメージを管理ホストのデータ・ストアにアップロードする必要があります。これにより、そのイメージを仮想マシンに添付できるようになります。 vSphere クライアントを使用して管理ホストに接続して、ローカル・データ・ストア上に ISO ディレクトリーを作成します。

## ステップ 8: DNS のデプロイ
{: #step-8-deploying-dns}

VM を (管理ホスト上に) デプロイして、DNS サービスをインストールする必要があります。 従来の vSphere クライアントを使用して、Windows または Linux の ISO が配置された管理 ESXi ホスト上に VM を作成します。 適切な ISO (Windows または CentOS) を接続して、その VM 上に DNS サーバーをデプロイします。 その VM を前のステップで作成した VM ポート・グループである (vmpg)-management ポート・グループに必ず関連付けてください。

VM のインストール後に、ポータブル・プライベート・サブネット・グループから IP アドレスとデフォルト・ゲートウェイを割り当てます。 付録 A の VLAN ワークシートを使用している場合は、管理 VM サブネットです。 各ポータブル IP アドレスの**「メモ」**セクションを、割り当てられた VM の名前に基づいて更新します。 **「メモ」**セクションにアクセスするには、{{site.data.keyword.slportal}} に移動して、**「ネットワーク」>「IP 管理」>「サブネット」>[サブネット]** を選択します。

DNS を有効にするために必要な手順は本資料の範囲外ですが、以下の手順を参考にしてください。

1. **「DNS 転送 (DNS Forwarding)」**を以下に示す service.softlayer.com のローカル DNS ホストに設定します。
  * `rs1.service.softlayer.com 10.0.80.11`
  * `rs2.service.softlayer.com 10.0.80.12`
2. DNS をセットアップした後に、プロビジョンされたすべてのポータブル・サブネットとプライマリー・サブネット用にローカル DNS ゾーン (dal06.mycompany.local) とリバース・ルックアップ・ゾーンを作成します。
3. 各ホストの管理 IP アドレスの A HOST レコードを追加します (vmkPG-management 上の vmk0)。
4. ご使用の vCenter 仮想アプライアンスの管理 VLAN にバインドされたポータブル・プライベート・サブネットから A HOST レコードを追加します。
5. vCenter に割り当てたポータブル IP サブネットの**「メモ」**セクションを更新します。

詳しくは、以下のリンクを参照してください。
* [Windows DNS and Active Directory ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://social.technet.microsoft.com/wiki/contents/articles/12370.step-by-step-guide-for-setting-up-a-windows-server-2012-domain-controller.aspx){: new_window}.
<!--* [CentOS BIND ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.centos.org/docs/2/rhl-rg-en-7.2/s1-bind-configuration.html){: new_window}.-->

## ステップ 9: vCenter 仮想アプライアンスのデプロイと構成
{: #step-9-deploying-and-configuring-vcenter-virtual-appliance}

DNS が構成されたので、vCenter Server Appliance をデプロイして構成できます。 このアプライアンスをデプロイするには、以下の手順を実行します。

1. 仮想サーバーにリモート・デスクトップ接続して、vSphere クライアントを開きます。
2. 管理ホストに接続して、**「File」、「Deploy OVF Template」**を選択します。
3. ウィザードに従ってデプロイメントを完了します。

<!--For more information about OVF Template deployment, see [VMware documentation ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://pubs.vmware.com/vsphere-55/index.jsp?topic=%2Fcom.vmware.vsphere.hostclient.doc%2FGUID-6C847F77-8CB2-4187-BD7F-E7D3D5BD897B.html&resultof=%22thick%22%20%22thin%22%20){: new_window}.-->

起動時に vCenter 仮想アプライアンスに IP アドレスを割り当てるための動的ホスト構成プロトコル (DHCP) サーバーは用意されていないため、ルート・コンソールを使用してこのアプライアンスを構成する必要があります。 **注:** `NO NETWORKING DETECTED` (ネットワーキングが検出されません) というメッセージが vCenter 仮想アプライアンスのコンソールに表示されます。

以下の手順に従って、アプライアンスを構成します。

1. 「root」という**ユーザー名**と「vmware」という**パスワード**を使用してコンソールにログインします。
2. `/opt/vmware/share/vami/vami_config_net` を実行して、テキスト・ウィザードに従って IP、サブネット、および DNS のプロパティーを構成します。 **『ステップ 8: DNS のデプロイ』**で作成した DNS サーバーまたは BIND サーバーの IP アドレスを必ず使用してください。
3. ネットワーク設定を保存して、コンソールを終了して、仮想サーバー上でブラウザーを開きます。
4. vCenter 仮想アプライアンス (VCVA) に割り当てた IP アドレスの後ろにポート 5480 を付加したアドレスにアクセスします。<!-- (https://:5480)-->
5. ウィザードの使用許諾契約書に同意して、**「Customer Improvement Experience Program」**の質問に回答して、**「Configure Options」、「Set custom configuration」**を選択します。
6. **「Next」**をクリックして、以下の値を入力します。
<table border="1" cellpadding="0" cellspacing="0"><caption>表 11. VCVA セットアップ・ウィザード</caption><tbody><tr><th>ウィザード・メニュー</th><th>オプション</th><th>値</th></tr><tr><td>Database Settings</td><td>Database Type</td><td>embedded</td></tr><tr><td>SSO Settings</td><td>SSO Deployment Type</td><td>embedded</td></tr><tr><td>SSO Settings</td><td>New administrator password *(for administrator@vsphere.local)*</td><td>&lt;パスワードを入力します&gt;</td></tr><tr><td>SSO Settings</td><td>Retype the new password</td><td>&lt;先ほど入力したのと同じパスワードを入力します&gt;</td></tr><tr><td>時刻同期</td><td>NTP synchronization</td><td>servertime.service.softlayer.com</td></tr></tbody></table>
7. **「Start」**をクリックします。 VCVA が構成されます。
8. **「Admin」**にあるオプションを使用してルート・パスワードを変更します。
9. VCVA 構成の Web ページからログアウトします。
10. VCVA の IP アドレスの後ろに `9443/vsphere-client`<!-- (https://:9443/vsphere-client)--> を付加したアドレスを入力して、vSphere Web クライアントにアクセスします。
11. root にログインして、**「Administration」、「Licenses」**を選択します。
12. お持ちの VMware vCenter ライセンスを入力します。

## ステップ 10: vCenter クラスターと分散仮想スイッチの作成
{ #step-10-create-vcenter-clusters-and-distributed-virtual-switch}

VCVA が構成されてそのライセンスが交付されたので、データ・センターとクラスターの構成体、および容量クラスター用の分散仮想スイッチを作成できます。

### データ・センターとクラスターの作成
{: #creating-data-center-and-clusters}

1. vSphere クライアントで、**「Home」**画面に移動します。
2. **「Getting Started」**を選択して、**「click here」**をクリックします。
3. **「Create Datacenter」**をクリックします。
4. データ・センター名を入力します (このサンプル環境は Toronto 01 データ・センターです。 `Toronto 01` をデータ・センター名として使用します)。
5. データ・センターが作成されたら、**「Getting Started」**ページで**「Create a cluster」**をクリックします。
6. 1 つ目のクラスターに `Management` (管理) という名前を付けます。 他のすべてのオプションを未指定のままにして、**「OK」**をクリックします。
7. 完了したら、管理クラスターと同じプロセスを繰り返して、もう 1 つのクラスターを作成します。

これで、**「Add a host」**を使用して、管理ホストと容量ホストを管理クラスターと容量クラスターに追加できるようになりました。 IP アドレスではなく、サーバーの完全修飾ドメイン名 (FQDN) を必ず使用してください。そうすることで、各ホストの追加時に DNS が使用されるようになります。

ESXi ホストを vCenter に追加した後に、各 ESXi ホスト上のシェルと SSH の有効化に関するいくつかの警告メッセージが表示されることがあります。 これらの警告が表示されないようにするには、ポップアップ・ウィンドウで**「Suppress Warning」>「Yes」**をクリックします。 「Suppress Warnings」リンクがない場合は、以下の手順を実行します。

1. ESXi ホストの**「Manage」**タブに移動します。
2. **「Settings」**を選択して、**「Advanced System Settings」**をクリックします。
3. **UserVars.SupressShellWarning** キーを探して、値を **1** に変更します。
4. **「OK」**をクリックします。

管理ホストと容量ホストがそれぞれのクラスターに追加された後に、各ホストにアクセスして DNS と NTP をセットアップします。 DNS をセットアップするには、以下の手順を実行します。

1. ホストをクリックして、**「Manage」>「Networking」**を選択します。
2. デフォルトのシステム・スタック (**「TCP/IP configuration」**) を選択して、鉛筆アイコンをクリックします。
3. 以前に作成した DNS サーバーの IP アドレスおよびホスト名とドメイン名を入力します。

NTP 設定については、以下の手順を実行します。

1. **「Manage」、「Settings」、「Time Configuration」**に移動します。
* `servertime.service.softlayer.com` を NTP サーバーとして入力します。
* **「NTP Service Startup Policy」**を `Start and stop with host` に設定します。

***容量ホスト用の分散仮想スイッチの作成***

1. vSphere Web クライアントを使用して、**「Networking」**に移動して、データ・センター名を右クリックします。
2. **「New Distributed Switch」**を選択します。
3. 分散スイッチに名前を付けて、**「Next」**をクリックします。
4. 適切な分散スイッチ・バージョンを選択して、**「Next」**をクリックします。
5. **「Edit Settings」**画面で、アップリンクの数として `2` を入力して、**「Create a default port group」**オプションをオフにします。
6. **「Next」>「Finish」**をクリックして、分散仮想スイッチを作成します。

***分散仮想スイッチのポート・グループの作成***

分散仮想スイッチを作成したので、vMotion、フォールト・トレランス、VM、およびストレージの各ポート・グループを作成する必要があります。

***dvpg-Private-VM 管理ポート・グループの作成***

1. vSphere Web クライアントを使用して「Networking」セクションに移動します。
2. 分散仮想スイッチ (DVS) を右クリックします。
3. **「New Distributed Port Group…」**をクリックして、表 14 に示している内容を 1 つ目のポート・グループについて入力します。
4. この表で指定されていないオプションは、デフォルト値のままにします。

| 「New Distributed Port Group」のメニュー | フィールド | 値 |
| --- | --- | --- |
| Name and Location | 名前 | dvpg-Private-VM Management |
| Configure Settings | Advanced | Check Customize default policies configuration |
| Configure Policies (Teaming and Failover)| ロード・バランシング| Route that is based on physical NIC load |
| Configure Policies (Teaming and Failover)| Failover Order| Active Uplinks: Uplink 1 & Uplink 2 |
<caption>表 12. DVS VM 管理ポート・グループ</caption>

1 つ目のポート・グループを作成した後に、以下の構成オプション (表 15 から表 19) を使用して残りのポート・グループを作成します。

***dvpg-Private-vMotion ポート・グループの作成***

|「New Distributed Port Group」のメニュー|フィールド|値|
|---|---|---|
|Name and Location|名前|dvpg-Private-vMotion|
|Configure Settings|VLAN Type|VLAN|
|Configure Settings|VLAN ID|&lt;ストレージ VLAN&gt;|
|Configure Settings|拡張|Check Customize default policies configuration|
|Configure Policies (Teaming and Failover)|ロード・バランシング|Route that is based on physical NIC load|
|Configure Policies (Teaming and Failover)|Failover Order|Active Uplinks: Uplink 1 & Uplink 2|
<caption>表 13. DVS vMotion ポート・グループ</caption>

***dvpg-Private-Fault Tolerance ポート・グループの作成***

|「New Distributed Port Group」のメニュー|フィールド|値|
|---|---|---|
|Name and Location|名前|dvpg-Private-Fault Tolerance|
|Configure Settings|VLAN Type|VLAN|
|Configure Settings|VLAN ID|&lt;ストレージ VLAN&gt;|
|Configure Settings|拡張|Check Customize default policies configuration|
|Configure Policies (Teaming and Failover)|ロード・バランシング|Route that is based on physical NIC load|
|Configure Policies (Teaming and Failover)|Failover Order|Active Uplinks: Uplink 1 & Uplink 2|
<caption>表 14. DVS FT ポート・グループ</caption>

***dvpg-Private-VM Access ポート・グループの作成***

|「New Distributed Port Group」のメニュー|フィールド|値|
|---|---|---|
|Name and Location|名前|dvpg-Private-VM Access|
|Configure Settings|VLAN Type|VLAN|
|Configure Settings|VLAN ID|&lt;仮想マシン VLAN&gt;|
|Configure Settings|拡張|Check Customize default policies configuration|
|Configure Policies (Teaming and Failover)|ロード・バランシング|Route that is based on physical NIC load|
|Configure Policies (Teaming and Failover)|Failover Order|Active Uplinks: Uplink 1 & Uplink 2|
<caption>表 15. DVS VM アクセス・ポート・グループ</caption>

***dvpg-Private-Storage の作成***

|「New Distributed Port Group」のメニュー|フィールド|値|
|---|---|---|
|Name and Location|名前|dvpg-Private-Storage Path A|
|Configure Settings|VLAN Type|VLAN|
|Configure Settings|VLAN ID|&lt;ストレージ VLAN&gt;|
|Configure Settings|拡張|Check Customize default policies configuration|
|Configure Policies (Teaming and Failover)|ロード・バランシング|Route that is based on physical NIC load|
|Configure Policies (Teaming and Failover)|Failover Order|Active Uplinks: Uplink 1 & Uplink 2|
<caption>表 16. DVS ストレージ・ポート・グループ</caption>

***dvpg-Primary-Public の作成***

|「New Distributed Port Group」のメニュー|フィールド|値|
|Name and Location|Name|dvpg-Private-Storage|
|Configure Settings|VLAN Type|VLAN
|Configure Settings|VLAN ID|&lt;プライマリー・パブリック VLAN&gt;|
|Configure Settings|Advanced|Check Customize default policies configuration|
|Configure Policies (Teaming and Failover)|Load Balancing|Route that is based on physical NIC load|
|Configure Policies (Teaming and Failover)|Failover Order|Active Uplinks: Uplink 1 & Uplink 2|
<caption>表 17. DVS ストレージ・パス B ポート・グループ</caption>

## ステップ 11: 容量クラスター内の ESXi ホストの分散仮想スイッチへのマイグレーション
{: #step-11-migrating-esxi-hosts-in-capacity-cluster-to-distributed-virtual-switch}

容量ホストが容量クラスターに追加されたので、*『ステップ 10: vCenter クラスターと分散仮想スイッチの作成』* で作成した分散仮想スイッチに、仮想標準スイッチの構成をマイグレーションできます。 単一のホストについてマイグレーションを実行して、後でホスト・プロファイルを適用することで、クラスター内の残りのホストを構成します。

VMkernel アダプターの追加を開始する前に、分散仮想スイッチ上のアップリンクに vmnic を割り当てます。

1. **「vCenter Inventory Lists」、「Distributed Switches」**をクリックします。
2. 容量ホスト用の適切な分散スイッチを選択します。
3. **「Getting Started」**ページで**「Add and manage hosts」**をクリックします。
4. 次の設定を使用して、アップリンクを追加して、ホストの管理に関連付けられている既存の VMkernel をマイグレーションします。
  - <table border="1" cellpadding="0" cellspacing="0"><caption>表 18. DVS のホストの追加</caption><tbody><tr><th>メニュー</th><th>フィールド</th><th>値</th></tr><tr><td>Select Task</td><td>Select Tasks</td><td>Add Hosts</td></tr><tr><td>Select Hosts</td><td>**「New Hosts」**をクリック</td><td>容量ホストをクリック</td></tr><tr><td>Select network adapter tasks</td><td>Select network adapter tasks</td><td>「Manage physical adapters」と「Manage VMkernel adapters」を選択</td></tr></tbody></table>
5. いずれかのプライベート vmnic を選択して、**「Manage physical network adapters」>「Assign uplink」**をクリックします。
6. ポップアップ画面で `uplink1` を選択して、**「OK」**をクリックします。
7. 上記の手順を他方のプライベート vmnic について繰り返して、この vmnic を `uplink2` に割り当てます。
8. **「Next」**をクリックして、vmk0 という VMkernel アダプターを強調表示して、**「Assign port group」**をクリックします。
9. ポップアップ・ウィンドウで**「dvpg-Private-VM Management」**を選択して、**「OK」**をクリックします。
10. **「Next」**を 2 回クリックしてから、**「Finish」**をクリックして分散仮想スイッチへのマイグレーションを完了します。 **注:** ホストへのネットワーク接続が一時的に途切れることがあります。 この接続はすぐに回復します。

vmk0 アダプターを分散仮想スイッチにマイグレーションしたら、DVS 内の各ポート・グループに VM カーネルを追加できます。

11. vCenter 内のホスト上で**「Manage」>「Networking」**をクリックします。
12. **「VM Kernel adapters」>「Add host networking」**に移動して、表 19 と 21 の VM カーネル・アダプターを追加します。 これらのアダプターを追加するには、vCenter 内のホスト上の**「Manage」>「Networking」**タブにある「VM Kernel adapters」メニューに移動します。 次に、「Add host networking」アイコンをクリックして、以下の VM カーネル・アダプターを追加します。

***vMotion 用の vmk1 の追加***

|メニュー|フィールド|値|
|---|---|---|
|Select connection type|Select connection type|VMKernel Network Adapter|
|Select target device|Select an existing distributed port group|dvpg-Private-vMotion|
|Select network adapter tasks|Select network adapter tasks|**「Manage physical adapters」**と**「Manage VM kernel adapters」**を選択|
|Port Properties|Enable Services|Check vMotion traffic|
|IPv4 Settings|IPv4 Address|*172.16.10.X/24*<br/><br/>*これはユーザー定義アドレスであり、必要に応じて異なるサブネットであってもかまいません。 各ホスト上の他の vMotion アドレスが同じサブネット上にあることを確認してください。*|
|IPv4 Settings|Subnet Mask|255.255.255.0|
<caption>表 19. ホスト・ネットワーキングの vMotion</caption>

***フォールト・トレランス用の vmk2 の追加***

|メニュー|フィールド|値|
|---|---|---|
|Select connection type|Select connection type|VMKernel Network Adapter|
|Select target device|Select an existing distributed port group|dvpg-Private-Fault Tolerance|
|Select network adapter tasks|Select network adapter tasks|**「Manage physical adapters」**と**「Manage VMKernel adapters」**を選択|
|Port Properties|Enable Services|Check Fault Tolerance Logging|
|IPv4 Settings|IPv4 Address|*172.16.20.X/24*<br/><br/>*これはユーザー定義アドレスであり、必要に応じて異なるサブネットであってもかまいません。 各ホスト上の他の FT アドレスが同じサブネット上にあることを確認してください。*|
|IPv4 Settings|Subnet Mask|255.255.255.0|
<caption>表 20. ホスト・ネットワーキングのフォールト・トレランス</caption>

***ストレージ用の vmk3 の追加***

|メニュー|フィールド|値|
|---|---|---|
|Select connection type|Select connection type|VMkernel Network Adapter|
|Select target device|Select an existing distributed port group|dvpg-Private-Storage|
|Select network adapter tasks|Select network adapter tasks|「Manage physical adapters」と「Manage VMkernel adapters」を選択|
|IPv4 Settings|IPv4 Address|*Portable Private Address*<br/><br/>*この IP アドレスはストレージ VLAN にバインドされたポータブル・プライベート・アドレスから選択されます。 このアドレスはストレージ・パス B とは異なるサブネット上にある必要があります。*|
|IPv4 Settings|Subnet Mask|IP 範囲に関連付けられたサブネット・マスク|
<caption>表 21. ホスト・ネットワーキングのストレージ</caption>

***ホスト・プロファイルの作成***

ホスト・プロファイルを作成するには、以前に構成した単一容量ホストからそのプロファイルを取り込む必要があります。

1. vSphere Web クライアントのホーム画面に移動して、**「Host Profiles」**アイコンをクリックします。
2. 緑のプラス (+) 記号をクリックして、**「Extract profile from a host」**を実行して、以前に構成した容量ホストをポップアップ・ウィンドウで選択します。
3. **「次へ」**をクリックします。
4. ホスト・プロファイル (Capacity01 ホスト・プロファイル) に適切な名前を付けて、説明を入力して、**「Next」**をクリックします。
5. 設定を確認して、**「Finish」**をクリックします。

ホスト・プロファイルを作成した後に、そのプロファイルが容量クラスター内の残りのホストに適用される際に、MAC アドレスを求めるプロンプトが表示されないようにそのプロファイルを変更する必要があります。

1. 作成したホスト・プロファイルを右クリックして、**「Edit Settings」>「Edit Host Profile」、「Host virtual NIC」**を選択します。
2. 右側のペインで、**「Determine how MAC address for vmknic is decided」**を**「User must explicitly choose the policy option」**に変更します。
3. **「Next」**をクリックしてから、**「Finish」**をクリックします。

***容量クラスターへのホスト・プロファイルの添付***

ホスト・プロファイルを作成したので、このホスト・プロファイルをクラスターに添付する必要があります。 プロファイルが添付されることで、それらのプロファイルを容量ホストに適用できるようになります。

1. vSphere Web クライアントで**「Host and Clusters」**ビューに移動します。
2. クラスター内の各ホストについて保守モードに切り替えます。 **注:** プロファイルは保守モードのホストのみに適用できます。
3. 容量クラスターを右クリックして、ポップアップ・メニューから**「Attach Host Profile」**を選択します。
4. 作成したホスト・プロファイルを選択して、**「Next」**をクリックします。
5. **「Customize host」**画面で適切な情報を入力して、**「Finish」**をクリックします。
6. ホストの保守モードを終了します (非保守モード)。

## ステップ 12: 共有ストレージの注文、構成、および接続
{: #step-12-order-configure-and-attach-shared-storage}

作業を進める前に、当環境内の管理クラスターと容量クラスターおよび管理ホストと容量ホストで使用する共有ストレージを注文、構成、および接続する必要があります。 {{site.data.keyword.cloud_notm}} のマルチテナント共有ストレージ・ソリューション (ファイル・ストレージ) を使用する場合は、[VMware を使用した {{site.data.keyword.cloud_notm}} 用の IBM ファイル・ストレージのアーキテクチャー・ガイド](/docs/infrastructure/FileStorage?topic=FileStorage-architectureguide)を参照してください。

## ステップ 13: HA/DRS および svMotion vCVA の有効化
{: #step-13-enabling-ha-drs-and-svmotion-vcva}

***管理クラスターと容量クラスターでの HA/DRS の有効化***

共有ストレージがセットアップされたので、HA と DRS を有効にして、管理クラスター上の VM に保護機能とロード・バランシング機能を追加する必要があります。

1. vSphere Web クライアントにアクセスします。
* 管理クラスターの「Manage」、「Settings」を選択します。
* 「vSphere DRS」を選択して「Edit」をクリックします。 次の設定が選択されていることを確認します。**「Turn on vSphere DRS」**、「Automation Level」-**「Fully Automated」**、「Migration threshold」スライダーの設定 - 真ん中、「virtual machine automation」-**「Enable individual virtual machine automation levels」**、「Power Management」- **オフ**。  
* 「vSphere HA Settings」画面で、次の設定が選択されていることを確認します。**「Turn on vSphere HA」**、**「Host Monitoring」**、「VM restart priority」-**「Medium」**、「Host isolation response」-**「Leave powered on」**。  
***vCenter 仮想アプライアンスのストレージ vMotion***

管理クラスター上でストレージがセットアップされて、HA と DRS が有効化されたので、vCenter 仮想アプライアンスを共有ストレージに設定する必要があります。

1. 適切なアプライアンスを右クリックして、ポップアップ・メニューから**「Migrate」**を選択します。
2. **「Change data store」**を選択して、**「Next」**をクリックします。
3. 管理クラスターに以前にマウントした iSCSI ボリュームを選択して、**「Next」**をクリックします。
4. 選択内容を確認して、**「Next」**をクリックします。

高度な単一サイト型 VMware 環境が完成しました。

## サマリー
{: #summary}

これで、IBM Cloud データ・センターで稼働している VMware 環境が得られました。 この VMware 環境は実稼働ワークロードを実行可能であり、オンプレミスの IBM Cloud デプロイメント環境を補っています。 この環境では、VMware のベスト・プラクティスが実践されており、VMware DRS、HA、ストレージ DRS、ネットワーキング冗長性などの機能を使用できます。 より大規模な容量ホストや管理ホスト、および追加のストレージを使用して、このリファレンス・アーキテクチャー実装を拡張できます。

VMware について詳しくは、[VMware のデプロイ](/docs/infrastructure/vmware?topic=VMware-using-cookbooks-for-vmware-deployments)および [VMware の FAQ](/docs/infrastructure/vmware?topic=VMware-faqs-vmware) を参照してください。

## 付録 A: VLAN ワークシート
{: #appendix-a-vlan-worksheet}

|VLAN タイプ|VLAN 番号|IP 範囲|ゲートウェイ|目的|
|---|---|---|---|---|
|プライマリー・プライベート||||管理|
|プライマリー・プライベート||||ストレージ|
|プライマリー・プライベート||||仮想マシン|
|プライマリー・パブリック||||パブリック・アクセス|
|ポータブル・プライベート||||管理 VM|
|ポータブル・プライベート||||ストレージ|
|ポータブル・プライベート||||仮想マシン|
|vMotion|||該当なし||
|フォールト・トレランス|||該当なし|||
<caption>VLAN ワークシート</caption>
