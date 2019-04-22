---
copyright:
  years: 2014, 2018
lastupdated: "2018-06-22"

subcollection: virtualization

keywords: disaster recovery
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# VMware vSphere 環境のリカバリー
{: #recovering-your-vmware-vsphere-environment}

データ・バックアップは現在、データの安全性と保全性を維持するための主要な方法となっています。 理想的で完全な世界では、バックアップの使用が必要になることはありません。  残念ながら、現実の世界では事故や災害が発生します。  そのような状況が発生したら、システムのダウン時間を最小限に抑えて、可能な限り早急に稼働状態に戻すことが最も重要です。

かつては、高性能ストレージまたはテープにバックアップを保管するというのが合理的なソリューションでした。 今日、企業はオンプレミスの物理ストレージに関連する資本コストと運用コストを軽減しようと、オブジェクト・ストレージでストレージを補完したり、時にはストレージをオブジェクト・ストレージに置き換えたりしています。 しかし、オブジェクト・ストレージにすると、バックアップをオンプレミス・ストレージ上に置かなくなるので、バックアップからリストアするという作業が、単に必要なバックアップを見つけてそこからリストアするという作業ではなくなります。 幸い、オブジェクト・ストレージに保管されているバックアップをリストアするという単調でつらい作業の大部分を実行してくれるハイブリッド・ソリューションが存在します。 このソリューションは以下で構成されています。

* {{site.data.keyword.cos_full}}
* NetApp の NetApp AltaVault クラウド・ストレージ・ゲートウェイ (旧称 Riverbed SteelStore)
* Veeam Backup & Replication ソフトウェア

壊滅的な状況が発生して 1 次環境がオフラインになった場合、{{site.data.keyword.cloud}} 内に 2 次リカバリー・サイトを作成してリカバリーすることができます。 Veeam Backup & Replication ソフトウェアと AltaVault クラウド統合ストレージ・アプライアンスをデプロイし、{{site.data.keyword.cos_full_notm}} と対話して 1 次環境の過去のバックアップにアクセスすることができます。 ソフトウェアがストレージ・アプライアンスに接続してバックアップを新しいロケーションにリストアし、1 次環境をオンラインに戻してそれ以上のアップタイムの損失を防ぎます。

{{site.data.keyword.cloud_notm}} のリカバリー・サイトで仮想マシン (VM) をリストアしても、オンプレミスの IP アドレスは保持されます。 そのため、正常にリカバリーしたときに IP アドレスを変更するか、BYOIP インフラストラクチャーを設計することが重要です。 どちらの場合も、支援が必要であれば IBM 担当員にお問い合わせください。

以下の手順では、障害が起きた VMware vSphere 環境を完全にリストアするために、NetApp AltaVault クラウド・ストレージ・ゲートウェイ・アプライアンス、{{site.data.keyword.cloud_notm}} インフラストラクチャー、Veeam Backup & Replication を連携的に使用する方法に焦点を当てます。 このソフトウェアの使用条件として、リカバリー時には、前述の 3 つのテクノロジーを使用して作成された環境のバックアップが {{site.data.keyword.cos_full_notm}} 上に少なくとも 1 つ存在していることが必要です。

「AltaVault アプライアンス」は 2 つに区別されます。 1 つは「オンプレミスの AltaVault アプライアンス」です。障害が発生し、バックアップをリカバリーまたはリストアしなければならなくなった元のオンプレミスの AltaVault アプライアンスを指します。 もう 1 つは「{{site.data.keyword.cloud_notm}} アプライアンス」です。障害が起きたオンプレミスの AltaVault アプライアンスからバックアップをリカバリーするために使用する AltaVault アプライアンスを指します。 {{site.data.keyword.cloud_notm}} AltaVault アプライアンスは、ユーティリティー・サーバーを使用して vSphere 環境にデプロイします。

## ハイブリッド・ソリューションの導入
{: #introducing-a-hybrid-solution}

Veeam Backup & Replication は、NetApp AltaVault クラウド統合ストレージ・アプライアンスと {{site.data.keyword.cos_full_notm}} を含むハイブリッド・ソリューションの実現を可能にします。 第一の機能は、仮想環境のバックアップの作成、維持、およびリストアです。 NetApp AltaVault クラウド統合ストレージ・アプライアンスは、オンプレミス環境をプライベート・クラウドまたはパブリック・クラウドとシームレスに統合するソフトウェア・ソリューションです。 これらを一緒に使用することで、Veeam Backup & Replication でバックアップを作成し、AltaVault クラウド統合ストレージ・アプライアンスでローカルのオンプレミス・ストレージにバックアップを保管し、同時に {{site.data.keyword.cos_full_notm}} に複製することができます。 さらに、{{site.data.keyword.cloud}} の従量制課金制の価格モデルとコンテンツ配信ネットワーク (CDN) との完全な統合により、地理的に分散した 24 のノードにデータを分散させて保管することができます。

### IBM Cloud Object Storage
{: #ibm-cloud-object-storage}

{{site.data.keyword.cos_full_notm}} にバックアップされたデータ・ファイルにアクセスするには、データ・ファイルを以下を通して要求します。

* 既にオンプレミスにある AltaVault アプライアンス
* {{site.data.keyword.cloud_notm}} 内にある 2 次 AltaVault アプライアンス

障害が発生したオンプレミスの 1 次 AltaVault アプライアンスをリカバリーする 2 次 AltaVault アプライアンスを {{site.data.keyword.cloud_notm}} 内にデプロイするには、次の手順に従ってください。

この {{site.data.keyword.cloud_notm}} 環境は、2 次 AltaVault アプライアンスを収容して実行するためのローカル・ストレージを備えた単一の ESXi ホストで構成されます。 このインフラストラクチャーは、{{site.data.keyword.cloud_notm}} 仮想サーバー・インスタンス (VSI) 内で単一の ESXi ホストを vCenter サーバーで管理するという基本的な単一サイト・アーキテクチャーの代表例です。

共有ストレージや以下のフィーチャーのサポートを必要とする、構成要素の多いインフラストラクチャーを使用することもできます。

* vSphere High Availability (HA)
* vSphere Distributed Resource Scheduler (DRS)
* vSphere vMotion

#### 前提条件
{: #prerequisites-ibm-cloud-object-storage}

先に進む前に、以下の前提条件が満たされていることを確認してください。**注:** この例では、AltaVault Cloud Integrated Storage 4.1と評価版の AltaVault AVA-v8 アプライアンスを使用しました。

* 既存の環境が、{{site.data.keyword.cloud_notm}} VSI 内で vCenter サーバーによって管理される単一の ESXi ホストで構成されている。
* VMware Sphere 用語と vSphere ESXi 環境の管理についての知識。 この知識には、vSphere Web クライアント、vSphere クライアントの使用方法、ネットワークやストレージなどのハードウェア・リソースの割り当て方法についての知識が含まれますが、これに限られません。

#### ポータブル・プライベート・ネットワークを 2 つ注文する
{: #order-two-portable-private-networks}

AltaVault では、環境内の別々のネットワークにネットワーク・インターフェースを置く必要があります。 以下の手順を使用して、{{site.data.keyword.slportal_full}}からポータブル・プライベート・ネットワークを 2 つ注文します。

1. ユーザー固有の資格情報を使用して、[{{site.data.keyword.slportal_full}} ![「外部リンク」アイコン](../../icons/launch-glyph.svg "「外部リンク」アイコン")](https://control.softlayer.com/){: new_window} にアクセスします。
2. **「アカウント」>「注文」**をクリックします。
3. **「ネットワーク」**セクションを選択し、**「サブネット/IP」>「オーダー」**をクリックします。
4. ドロップダウン・メニューから**「ポータブル・プライベート」**を選択します。 「ポータブル・プライベート」を選択すると、ポータブル・プライベート IP アドレスの数を選択するオプションが表示されます。 **注:** {{site.data.keyword.cloud_notm}} は、ポータブル・プライベート・ネットワークごとにアドレス・ブロック内の IP アドレスを 3 つ以上、自動的に予約します。 予約されたアドレスは、ネットワーク・アドレス、ゲートウェイ・アドレス、およびブロードキャスト・アドレスとして使用されます。 これらのアドレスは、仮想 LAN (VLAN) に直接バインドされている必要があります。<!--If your IBM Cloud pod supports Hot Standby Ready Protocol (HSRP), then the number of usable IP addresses is reduced. For instance, if you select **4 Portable Private IP Addresses** will yield only one usable IP address or zero if the pod supports HSRP.--> ポータブル IP アドレスについて詳しくは、[サブネットおよび IP の概説](/docs/infrastructure/subnets?topic=subnets-getting-started-subnets-ips#getting-started-subnets-ips)を参照してください。
5. ポータブル・プライベート IP アドレスの数を選択したら、**「続行」**をクリックします。
6. ポータブル・プライベート IP アドレスをルーティングする VLAN を選択し、**「続行」**をクリックします。
7. 必要な連絡先情報を入力し、**「注文」**をクリックします。
8. 必要な 2 つ目のポータブル・プライベート・ネットワークを取得するために注文プロセスを繰り返します。
9. プライベート・ネットワークと IP アドレスが割り振られたら、{{site.data.keyword.slportal}} ページで**「ネットワーク」>「IP 管理」>「サブネット」**をクリックしてそれらを表示します。 IP アドレスの割り当ては記録することをお勧めします。 IP アドレスごとに**「サブネット」**ページの**「メモ」**セクションを使用して、割り当てたマシンを記入してください。

#### vSphere を構成する
{: #configuring-vsphere}

追加したポータブル・プライベート・ネットワークを反映するために vSphere を構成し、ネットワークを論理的に分離します。

1. vSphere 環境に移動し、追加したポータブル IP ブロックの 1 つを反映する仮想マシン・ポート・グループを作成します (合計 2 回)。

仮想マシン・ポート・グループには、**「Share Network」**というラベルを付けます。 ポータブル IP ブロックの 1 つは、デフォルト (プライマリー) の仮想マシン・ポート・グループに配置される VM に使用します。 もう 1 つのブロックは、AltaVault アプライアンスとエクスポートされた Common Internet File System (CIFS)/Server Message Block (SMB) マウント・ポイントの間のデータ転送に使用します。

## AltaVault クラウド・ストレージ・ゲートウェイ
{: #altavault-cloud-storage-gateway}

AltaVault を使用すると、{{site.data.keyword.cos_full_notm}} の REST API を使用してスクリプトやアプリケーションを作成することなく、オンプレミス環境をクラウドと統合できます。 AltaVault は、フロントエンドで CIFS/SMB またはネットワーク・ファイル・システム (NFS) のマウント・ポイントを公開し、バックエンドで {{site.data.keyword.cos_full_notm}} インターフェースにセキュアに接続します。 これにより、マウント・ポイントをマウントまたは指定し、クラウドに安全にデータをコピーしたり、クラウドから環境をリカバリー/リストアしたりできます。<!-- , as shown in Figure 1.-->

<!-- ![Figure 1](images/veeam_restore_fig2.png)
<br/>`Figure 1: AltaVault end-to-end flow with Veeam and IBM Cloud`-->

### 災害復旧構成で AltaVault をデプロイする
{: #deploying-altavault-in-a-disaster-recovery-configuration}

{{site.data.keyword.cos_full_notm}} を使用した災害復旧ソリューションとして AltaVault をデプロイするには、以下の手順に従ってください。

#### 始める前に
{: #before-you-get-started-deploying-altavault-in-a-disaster-recovery-configuration}

先に進む前に、以下の前提条件を満たしてください。

* AltaVault 仮想アプライアンスのコピーを取得し、ユーティリティー・サーバー上に配置する。 これは、OVA ファイル拡張子の単一ファイルです。 アプライアンスについて NetApp 担当員に問い合わせていただくか、[NetApp AltaVault Web サイト ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.netapp.com/us/products/protection-software/altavault/){: new_window} から 90 日間の評価版をダウンロードしてください。
* 既存のオンプレミスの vSphere ESXi 5.5 環境が AltaVault アプライアンス用の CPU、メモリー、ディスク・スペースの最小要件を満たしている。 評価版を使用する場合のリソース要件は、仮想 CPU (vCPU) 4 つ、メモリー 24 GB、ディスク・スペース最大 8 TB です。
* vSphere 環境内に 10 Gbps ネットワーク・インターフェース・コントローラー (NIC) が 2 つある。 NIC の 1 つはデータ・アクセス用に使用し、もう 1 つは {{site.data.keyword.cos_full_notm}} へのデータ・レプリケーション用に使用します。
* vSphere 環境内に定義した 2 つの NIC に対応する 2 つのポータブル・プライベート・ネットワークがある。 レプリケーション・ネットワークをデータ・アクセス・ネットワークと同じネットワークに割り当てることはできません。ルーティング・ループが生じる可能性があるためです。
* {{site.data.keyword.cos_full_notm}} の資格情報がある。 資格情報には、{{site.data.keyword.cloud_notm}} ユーザー名、{{site.data.keyword.cos_full_notm}} ユーザー名、および {{site.data.keyword.cloud_notm}} ユーザー名に関連付けられている API キーが含まれます。
* オンプレミス AltaVault アプライアンスのデータ・レプリケーションを一時停止する。あるいは、オンプレミス環境がまだ初期状態の場合は、{{site.data.keyword.cloud_notm}} のバックアップ・アーカイブにアクセスするために使用しているコンテナー/バケットからアプライアンスを切り離す。
  * 以下の 2 つの方法のいずれかを使用して、レプリケーションを一時停止または停止します。
    * オンプレミス AltaVault アプライアンスをオフにします。
    * AltaVault アプライアンスの Web 管理コンソールに移動し、**「Storage」>「Cloud Settings」>「Replication」**をクリックします。 **「Suspend Replication」**を選択します。
    * オンプレミス AltaVault アプライアンスの構成ファイルのコピーを取得しているか、URL でアクセスできる。 構成ファイルは .tar ファイルです。
  * AltaVault アプライアンスの Web 管理コンソールに移動し、**「Configure」>「Setup Wizard」>「Export Configuration」>「Export Configuration」**をクリックします。 構成ファイル (.tar ファイル) を保存して、ユーティリティー・サーバーに転送するか、URL でアクセスできるようにします。
    * VMware Sphere 用語と vSphere ESXi 環境の管理についての知識。 この知識には、vSphere Web クライアント、vSphere クライアントの使用方法、ネットワークやストレージなどのハードウェア・リソースの割り当て方法についての知識が含まれますが、これに限られません。

### AltaVault OVA をデプロイする
{: #deploying-altavault-ova-disaster}

すべての前提条件を満たしたら、ユーティリティー・サーバーを使用して AltaVault の OVA を vSphere 環境にデプロイします。 OVA デプロイメントの手順について詳しくは、[NetApp AltaVault Installation and Service Guide ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window} を参照してください。 AltaVault の評価版は、仮想 CPU 4 つ、メモリー 6 GB、1 次ディスク 150 GB で構成されています。 「Installation and Service Guide」を使用して、以下の手順を実行します。

1. メモリーの容量を 24 GB に増やします。
2. 重複排除されたバックアップ・データを保管するために、サイズが 8 TB 以下の 2 次ディスク・ストレージ・デバイスを追加します。
3. メモリーとディスクの構成を変更したら、別のポータブル・プライベート・ネットワークを AltaVault アプライアンスに割り当てます。

NIC には、以下のインターフェース機能が割り当てられます。

* **プライマリー**: AltaVault アプライアンスの管理とクラウドへのデータ・レプリケーションに使用されます。 この環境例では、**「Primary Network」**ポート・グループに割り当てられます。
* **e0a**: AltaVault アプライアンスからクラウドにデータを複製するために使用されるオプションのインターフェース。
* **e0b**: SMB/CIFS 共有または NFS 共有のマウント・ポイントをエクスポートするために使用されるインターフェース。 この環境例では、**「Share Network」**ポート・グループに割り当てられます。
* **e0c**: SMB/CIFS 共有または NFS 共有のマウント・ポイントをエクスポートするために使用されるオプションのインターフェース。

この手順の構成例では、AltaVault アプライアンスは、クラウドへのレプリケーション用のインターフェースとして**プライマリー**・インターフェースを使用し、CIFS/SMB マウント・ポイントのエクスポート用に **e0b** インターフェースを使用します。 **注:** 同じデータへのアクセスに CIFS/SMB 共有と NFS 共有の両方を使用することはできません。 使用するプロトコルは、オンプレミス AltaVault アプライアンスでクラウドにデータを複製するのに使用しているのと同じプロトコルでなければなりません。

<!-- ![Figure 2](images/veeam_restore_fig3.png)
<br/>`Figure 2: IBM Cloud setup with VMware`-->

AltaVault アプライアンスのデプロイメントと VM 設定の構成について詳しくは、[NetApp AltaVault Installation and Service Guide ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window} を参照してください。

### AltaVault アプライアンスを構成する
{: #configuring-the-altavault-appliance}

1. AltaVault VM を構成したら、電源を入れることができます。 AltaVault アプライアンスが補助ストレージ・ディスクをフォーマットするため、VM が起動するまでに時間がかかることがあります。

2. アプライアンスがブート・プロセスを完了したら、**「Username」**に **admin**、**「password」**に **password** を使用して、AltaVault コンソールにログインします。 これらの認証情報は、初期構成が完了した後に変更できます。 『表 1: AltaVault 初期構成値』を参照してください。

3. アプライアンスにログインした後、構成ウィザードを使用するかどうかを尋ねられます。 *y* と入力します。
4. ウィザードが開始したら、表 1 の情報を使用します。
5. **Enter** キーを押して変更を保存します。

|質問|応答|
|---|---|
|Step 1: Admin Password?|新しい管理者パスワードを入力します (**「password」**は使用できません)。|
|Step 2: Hostname?|適切なホスト名を入力します。|
|Step 3: Use DHCP on primary interface?|**n** または **no** と入力します。|
|Step 4: Primary IP Address?|プライマリー・ネットワーク IP アドレスを入力します。 この構成例では、これは管理インターフェースに使用されるネットワーク (例えば、10.120.108.132) です。|
|Step 5: Netmask?|ネットマスクを入力します (例: 255.255.255.192)。|
|Step 6: Default gateway?|デフォルト・ゲートウェイを入力します (例: 10.120.108.129)。|
|Step 7: Primary DNS server?|自分の環境のプライマリー・ドメイン・ネーム・システム (DNS) サーバーを入力します。|
|Step 8: Domain name?|使用環境のドメイン名を入力します (例: testenv.org)。|
{: caption="表 1. AltaVault 初期構成値" caption-side="top"}

### AltaVault に IBM Cloud Object Storage を設定する
{: #configuring-altavault-for-ibm-cloud-object-storage}

AltaVault アプライアンスは、セットアップ後に {{site.data.keyword.cos_full_notm}} サービスに接続する必要があります。 オンプレミス構成では、パブリック DNS を使用して Object Storage サービスに接続します。 この構成では、代わりに内部 DNS 名を使用します。

1. Web ブラウザーを開き、AltaVault アプライアンスの IP アドレスを入力します。
2. 管理者の資格情報を使用して、コンソールにログインします。 初回ログイン時に、**「Wizard Dashboard」**が表示されます。
3. **「System Settings」**を選択し、すべてのネットワーク設定が正確であることを確認して、使用環境のタイム・ゾーンを表すようにタイム・ゾーンを調整します。
4. **「Next」>「Save and Apply」>「Exit」**を選択します。 **「Wizard Dashboard」**に戻り、現在のブラウザー・セッションからログアウトします。
5. AltaVault アプライアンスに再度ログインし、**「Settings」>「Setup Wizard」>「Import Configuration」**をクリックし、オンプレミスの AltaVault アプライアンスの構成ファイルの場所を指定します。
6. **「Import Shared Data Only」**を選択します。クラウドや E メールの設定など、共有する共通設定のみがインポートされます。
7. オンプレミス AltaVault アプライアンスの作成時に暗号キーのパスフレーズを指定した場合は、そのパスフレーズを**「Key Passphrase」**テキスト・ボックスに入力します。
8. **「Import Configuration」>「Exit」**をクリックします。

<!--The AltaVault appliance web console displays a message indicating a successful import. If the import was not successful, verify that the configuration file is in the correct format and that the passphrase is correct before trying again.-->

これで、オンプレミス AltaVault アプライアンスの構成を {{site.data.keyword.cloud_notm}} AltaVault アプライアンスにインポートしました。

以下の手順を使用して、プライベート・ネットワーク経由で {{site.data.keyword.cos_full_notm}} サービスにアクセスできるように {{site.data.keywrod.cloud_notm}} AltaVault アプライアンスのクラウド設定を変更します。

1. AltaVault で**「Storage」>「Cloud Settings」>「Cloud」**に移動し、{{site.data.keyword.cos_full_notm}} サービスのプライベート・アドレスを表すホスト名を変更します。
  * プライベート・ネットワークの名前構文は <location>.objectstorage.service.networklayer.com です。ここで、*<location>* は、データ・センターの短縮名を表します (例えば、Melbourne 01 データ・センターの場合は mel01)。
2. **「Apply」**をクリックします。 AltaVault アプライアンスが、{{site.data.keyword.cos_full_notm}} サービスへの接続を試行します。 この接続の試行が失敗する場合は、クラウド・プロバイダー設定が正しいことを確認してから、接続を再試行してください。

{{site.data.keyword.cos_full_notm}} に接続したら、アプライアンスをリカバリー・モードにして、{{site.data.keyword.cos_full_notm}} から元のバックアップ・データのメタデータ・コンテンツを同期する必要があります。

AltaVault アプライアンスをリカバリー・モードにするには、以下の手順を使用します。

AltaVault アプライアンスは、{{site.data.keyword.cos_full_notm}} からデータを同期します。 **注:** この同期にかかる時間は、リストアされるバックアップのサイズと数に応じて異なります。

1. AltaVault アプライアンスにアクセスしてログインし、図 3 に示すコマンドを入力します。<br/>![図 3](images/veeam_restore_fig5.png)<br/>`図 5: リカバリー・モード・コマンド`
2. 同期プロセスが完了したら、AltaVault アプライアンスの Web コンソールに戻ります。
3. **「Storage Optimization Service」**が**「running」**で、**「Status」**が**「ready」**であることを確認します。 **「Status」**が**「ready」**に変わるまで数分かかることがあります。

### AltaVault に CIFS/SMB マウント・ポイントを設定する
{: #configure-the-cifs-smb-mount-point-in-altavault}

{{site.data.keyword.cos_full_notm}} への内部接続を確立したら、CIFS/SMB マウント・ポイントにアクセスするように **e0b** インターフェースを構成します。

1. AltaVault Web コンソールで**「Settings」>「Data Interfaces」**に移動します。
2. 必要に応じて、**e0b** インターフェースを展開して**「Enable Data Interface」**を選択します。
3. CIFS/SMB 共有のマウントに使用する IP アドレス、サブネット・マスク、およびゲートウェイを入力します。 必ず、プライマリー・インターフェースとは別のサブネットを使用してください。
4. **「MTU」**のデフォルト値の **1500 バイト**をそのまま残します。
  * デフォルトの最大伝送単位 (MTU) は 1,500 ですが、ジャンボ・フレームを使用する場合は 9,000 に変更できます。 ESXi ホストと物理インフラストラクチャーがジャンボ・フレームをサポートしている必要があります。 デフォルトで {{site.data.keyword.cloud_notm}} は 9,000 バイトの MTU サイズをサポートするので、構成変更は必要ありません。
5. **「Apply」**をクリックします。

これで、AltaVault アプライアンス、{{site.data.keyword.cos_full_notm}}、Veeam Backup & Replication の間で通信するための最小限の設定が AltaVault アプライアンスに構成されました。

## Veeam Backup & Replication
{: #veeam-backup-replication-recover}

Veeam Backup & Replication ソフトウェアは、VM とそのデータのバックアップ、複製、リカバリーの包括的な機能を提供します。 Veeam Backup & Replication は、AltaVault クラウド・ゲートウェイ・アプライアンスと完全に統合できるので、シームレスなバックアップとリカバリーを実現できます。

### Veeam Backup & Replication をデプロイする
{: #deploy-veeam-backup-replication}

例では、Veeam Backup & Replication バージョン 8 の評価版を使用しています。

#### *前提条件*
{: #prerequisites-deploy-veeam-backup-replication}

デプロイメントに進む前に、以下の前提条件が満たされていることを確認してください。

* IBM Cloud Object Storage および Veeam Backup & Replication を使用するように構成した既存の AltaVault アプライアンスがある。
* VMware 環境用の Veeam Backup & Replication (単一の実行可能ファイル) を取得します。 Veeam 担当員に問い合わせていただくか、[30 日間の評価版 ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window} をダウンロードしてください。
* Veeam Backup & Replication に使用するライセンス・ファイルを入手する。 ほとんどの場合、このファイルは Veeam Backup & Replication のダウンロードに使用した E メール・アドレス宛てにメールで送信されます。 このファイルを受信していない場合は、Veeam 担当員にお問い合わせください。<br/><br/>ライセンス・ファイルを使用することで、Veeam Backup & Replication のすべての機能がアクティブ化されます。 プログラムのインストール時にこのファイルを指定しない場合は、すべてのフィーチャーおよび機能が 30 日間の評価版のものになります。
* 表 2 の仕様の仮想サーバー・インスタンス (VSI) が IBM Cloud にプロビジョンされている。**注:** インストールされているオペレーティング・システムは 64 ビット・バージョンでなければなりません。

||最小|推奨|
|---|---|---|
|**OS**|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|<ul><li>Windows Servers 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>WIndows 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|
|**コアまたは vCPU の数**|2|4|
|**メモリー**|基本の RAM 4 GB に加えて、同時に実行するバックアップ/リストアのジョブごとに 500 MB。|基本の RAM 16 GB に加えて、同時に実行するバックアップ/リストアのジョブごとに 4 GB。|
|**ディスク・スペース**|製品インストール用に 2 GB、ゲスト・システム・カタログ・フォルダー (永続データ) 用に VM 100 台あたり 10 GB。|製品インストール用に 2 GB、ゲスト・システム・カタログ・フォルダー (永続データ) 用に VM 100 台あたり 10 GB。|
|**ネットワーク**|オンサイトのバックアップとレプリケーション用に 1 Gbps LAN、オフサイトのバックアップとレプリケーション用に 1 Mbps WAN。|オンサイトのバックアップとレプリケーション用に 1 Gbps LAN、オフサイトのバックアップとレプリケーション用に 1 Mbps WAN。|
{: caption="表 2. {{site.data.keyword.cloud_notm}} VSI のシステム要件" caption-side="top"}

AltaVault アプライアンスおよび vSphere 環境と同じデータ・センター内に VSI をプロビジョンします。 この VSI は AltaVault アプライアンスと vCenter にアクセスできる必要があります。

### Veeam Backup & Replication をインストールする
{: #installing-veeam-backup-replication-disaster}

すべての前提条件を満たしたら、以下の手順を使用して、Veeam Backup & Replication を {{site.data.keyword.cloud_notm}} VSI にインストールします。

1. プログラム実行可能ファイルをダブルクリックし、**「Veeam Backup & Replication – Install」**をクリックします。 セットアップ・ウィザードが開きます。
* **「Next」**をクリックし、**「I accept the terms in the license agreement」**を選択します。
* **「Next」**をクリックし、「Deploy Veeam Backup & Replication」の下に、取得したライセンス・ファイルの場所を指定します。
* **「Next」**をクリックして、**「Program Features」**画面で、インストールする Veeam Backup & Replication コンポーネントとそれらのインストール場所を選択します。 **注:** **「Veeam Backup & Replication」**と**「Veeam Backup Catalog」**が、必要なコンポーネントです。
* **「次へ」**をクリックします。<br/>セットアップ・ウィザードにより、すべての必須プログラム・フレームワークとサポート・コンポーネントが確実にインストールされるように一連の検査が実行されます。 不足しているコンポーネントがある場合は、セットアップ・ウィザードからそれらのインストールを勧められます。<!--Click **Install** if this is the case.-->
2. すべてのコンポーネントがシステム検査で**「Passed」**になっていることを確認して、**「Next」**をクリックします。
3. **「Service (user) Account」**で Veeam バックアップ・サービスを実行するサービス・アカウントを選択します。 デフォルトのサービス・アカウントは **LOCAL SYSTEM account** です。 **「Next」**をクリックします。
4. **「SQL Server Instance」**で Veeam Backup & Replication データベースを作成して保管するために使用する SQL サーバー・インスタンスを選択します。 詳しくは、データベース管理者にお問い合わせください。 **「Next」**をクリックします。
5. **カタログ・サービス・ポート** (9393) および **Veeam バックアップ・サービス・ポート** (9392) を入力します。 **「Next」**をクリックします。
6. ゲスト・ファイル・システム・カタログ (永続データ) および vPower NFS 書き込みキャッシュ (非永続データ) を保管するディレクトリーを選択します。 **「Next」**をクリックします。
7. すべての設定および値が正しいことを確認して、**「Install」**をクリックします。 インストールが完了したら、**「Finish」**をクリックします。

### Veeam Backup & Replication を構成する
{: #configuring-veeam-backup-replication}

Veeam Backup & Replication をインストールしたら、AltaVault アプライアンスに接続できます。

1. Veeam Backup & Replication を起動します。
2. 画面の右下にある**「Backup Infrastructure」**をクリックします。
3. **「Backup Infrastructure」**ウィンドウで、**「Managed Servers」>「Managed servers」**をクリックします。
4. 上部のリボン・メニューから**「Add Server」**をクリックし、**「VMware vSphere」**をダブルクリックします。
5. vSphere サーバーのプライベート IP アドレスを入力し、**「Next」**をクリックします。
6. 前に指定した vSphere サーバーに対する管理者権限を持っているローカル・アカウントのアカウント認証情報を入力します。 **注:** アカウントのユーザー名は、ドメイン・アカウントの場合は DOMAIN\USER 形式、ローカル・アカウントの場合は HOST\USER 形式にする必要があります。 アカウントを追加するには、**「Add」**をクリックして、適切なユーザー名とパスワードを入力します。 ネットワーク管理者から指示がない限り、デフォルトの VMware Web サービス・ポートを変更しないでください。<!-- otherwise during [Veeam Backup & Replication installation](#_Installing_Veeam_Backup).--> **「次へ」**をクリックします。
  * Veeam Backup & Replication が vSphere サーバーへの接続を試行します。 接続の試行が失敗する場合は、アカウントが存在していて vSphere サーバーに対する管理者特権を持っていることを確認してから、再試行してください。
7. **「完了」**をクリックします。
8. **「Managed Servers」>「VMware vSphere」**をクリックして、vSphere サーバーが正常に追加されたことを確認します。

### Veeam Backup & Replication へのバックアップ・リポジトリーの追加
{: #adding-a-backup-repository-to-veeam-backup-replication-disaster}

以下の手順に従って、リストアするバックアップを AltaVault アプライアンスに保管するための新規バックアップ・リポジトリーを作成します。 **注:** この例では、Veeam Backup & Replication バックアップ・サーバーを、Windows を実行するコンピューターでホストしているので、CIFS/SMB 共有を使用します。 {{site.data.keyword.cloud_notm}} AltaVault アプライアンスでバックアップのリストアに使用する共有プロトコルは、オンプレミスの AltaVault アプライアンスで同じバックアップに使用している共有プロトコルと同じものでなければなりません (CIFS/SMB 共有が使用されます)。

1. 左下隅にある**「Backup Infrastructure」>「Backup Repositories」**をクリックします。
2. 上部のリボン・メニューから、**「Add Repository」**をクリックします。
3. **「Name」**でリポジトリーに固有の名前を付け、**「Description」**に適切な説明を入力します。 **「Next」**をクリックします。
4. オンプレミス AltaVault アプライアンスで使用している共有のタイプに対応する共有のタイプを選択します。 例: オンプレミスの AltaVault アプライアンスで使用している共有タイプが CIFS/SMB である場合は、**「Shared folder」**を選択します。
5. リストアする CIFS/SMB 共有のバックアップが置かれている AltaVault アプライアンス上の場所を指定します。 共有には、オンプレミス AltaVault アプライアンスでバックアップの処理に使用された共有と同じ名前が付けられます。 例えば、オンプレミス AltaVault アプライアンスでバックアップに `cifs_test1` という共有を使用した場合は、{{site.data.keyword.cloud_notm}} AltaVault アプライアンス上の `cifs_test1` 共有の場所を指定してください。
  * 場所を調べるために、Web ブラウザーを開き、AltaVault アプライアンスの IP アドレスを入力します。
  * **「Storage」>「CIFS」**に移動し、共有の**「Share Path」**を書き留めます。 **注:** これは共有のローカル・パスとは同じではありません。 共有パスの形式は `\\<AltaVault appliance hostname>\<share name>` です。 「Share Path」の AltaVault アプライアンスのホスト名を、AltaVault アプライアンスの e0b ネットワーク・インターフェースの IP アドレス (共有のマウント・ポイント) に置き換えます。<br/>
   * AltaVault アプライアンス管理ウィンドウで**「Settings」>「Data Interfaces」**をクリックし、**e0b** インターフェースの IP アドレスを見つけます。 IFS/SMB マウントの構成について詳しくは、『AltaVault に CIFS/SMB マウント・ポイントを設定する』を参照してください。
   * Veeam Backup & Replication で指定する共有パスは、`\\restore-appliance\cifs_test1` ではなく、`\\10.120.108.133\cifs_test1` のようになります。
6. Veeam Backup & Replication に戻り、**「Shared folder」**フィールドにマウント・ポイントの共有パスを入力し、**「Next」**をクリックします。 Veeam Backup & Replication では、マウント・ポイントとの接続確立が試行されます。 接続が失敗する場合は、前に戻って AltaVault アプライアンスのネットワーク設定が正しいことを確認してから、再試行してください。
7. **「New Backup Repository」**画面で、**「Limit maximum concurrent」**に使用可能なリソース数の値を入力します。 この値は、選択された共有にバックアップ・プロキシーが送信できるタスクの最大数です。 同時タスクのデフォルトの数は 4 です。 AltaVault は、5 個の同時タスクから開始して、リソースの状況に応じてこの値を増減することをお勧めしています。
8. **「Next」**をクリックします。
9. **「vPower NFS」**画面でオプションの vPower NFS 設定を指定します。 **「Enable vPower NFS server」**チェック・ボックスのチェック・マークを外したままにすると、Veeam Backup & Replication は、リカバリーとリカバリー検証のために vPower を使用します。 **「Next」**をクリックします。
10. VM バックアップの手動選択を使用する場合を除き、**「Import existing backups automatically」**を選択します。
11. すべての設定が正しいことを確認し、**「Next」**をクリックします。
* **「Finish」**をクリックして、ウィザードを終了します。
12. **「Yes」**をクリックして先に進みます。

Veeam Backup & Replication が構成されたので、バックアップのリストアを開始できます。

### 環境のリストア
{: #restoring-the-environment}

以下の手順に従って、VM を完全にリストアします。

1. 画面の左下にある**「Backup & Replication」**をクリックします。
2. 上部のリボン・メニューから、**「Restore」**をクリックします。
3. **「Entire VM (including registration)」**を選択します。 **注:** **「Restore from replica」**では何も選択しないでください。 **「Next」**をクリックします。
4. **「Add VM」>「From Backup」**をクリックします。 オンプレミスの AltaVault アプライアンスおよび選択した CIFS/SMB 共有を介してこれまで {{site.data.keyword.cos_full_notm}} に複製された VM が表示されます。 **「Add」>「Next」**をクリックします。
5. **「Restore to a new location, or with different settings」**を選択し、VM を新しい場所にリストアします。<!--[because the original location of the VM(s) failed]--> **「次へ」**をクリックします。
6. **「Host」**画面で、リストアする各 VM のターゲット・ホストを選択します。 VM を選択してから**「Host」**をクリックします。
7. **「Select Host」**でターゲット・ホストを選択し、**「OK」**をクリックします。<br/>Veeam Backup & Replication がターゲット・ホストと通信し、ターゲット・ホストがオンラインで、データを受信する準備ができているかどうかを検査します。 この試行が失敗する場合は、すべてのネットワーク設定が正しいことを確認してから、再試行してください。 検査が完了したら、**「Next」**をクリックします。
8. VM ごとにオプションの**「Resource Pool」**を選択します。 **「Next」**をクリックします。
9. リストアする各 VM のターゲット・データ・ストアとディスク・タイプを選択します。 **「Next」**をクリックします。<br/>Veeam Backup & Replication がターゲット・データ・ストアの検証を試行します。 検証に失敗した場合は、リストアする VM 用の十分な容量がデータ・ストアにあることを確認し、すべてのネットワーク設定を確認してから接続を再試行してください。
10. リストアする VM ごとに、ターゲット・データ・ストアの宛先フォルダーを選択します。 **「Next」**をクリックします。
11. VM ごとにネットワーク接続および設定を指定します。 **「Next」**をクリックします。
12. オプションの**「Restore reason」**に、リストア操作を実行する理由を入力します。 **「Next」**をクリックします。<br/>Veeam Backup & Replication が、リストアする VM の検証を試行します。 この検証が正常に終了するのを待ってから先に進みます。
13. **「Backup & Replication」**ウィンドウでリストア操作と設定を確認し、**「Finish」**をクリックします。 Veeam Backup & Replication によって、選択した VM のリストアが自動的に開始されます。

## 次のステップ
{: #next-steps-recovering-your-vmware-vsphere-environment}

<!--The recent rise in Object Storage can be heavily attributed to enterprises' desire to reduce or even eliminate the operational and capital expenditures associated with comparatively expensive on-premises storage. While Object Storage is a highly cost-effective solution, it introduces a new problem of how to access stored files. In the case of stored data backups, the issue is how to use them to recover an environment that has failed for whatever reason.-->

<!--This problem is handily resolved by Veeam Backup & Replication, along with a NetApp AltaVault Cloud Gateway appliance, and {{site.data.keyword.cos_full_notm}}. When used together, they create a seamless backup and recovery experience that can be tailored to fit a wide variety of environmental requirements and constraints. By following the steps outlined in this procedure, enterprises can quickly realize the benefits of cloud-based storage, while reducing the complexities involved in shifting from on-premises storage to Object Storage using the three aforementioned technologies.-->

このハイブリッド・ソリューションのコンポーネントについて詳しくは、以下を参照してください。

* [NetApp AltaVault の Web サイト ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.netapp.com/us/products/protection-software/altavault/){: new_window}
* [Veeam Backup & Replication の Web サイト ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}
* [IBM Cloud Object Storage の Web サイト ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.ibm.com/cloud/storage){: new_window}

*ユーティリティー・サーバー* とは、プライベート環境へのアクセスに使用する VMware vSphere Client が含まれているサーバーを指します。これを使用して、データ・リカバリー操作用の AltaVault アプライアンスをデプロイします。
