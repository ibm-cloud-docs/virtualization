---

copyright:
years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: vsphere, Veeam
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# Veeam を使用した VMware vSphere 環境のバックアップ
{: #backing-up-your-vmware-vsphere-environment-by-using-veeam}

以下のサービスで構成されるハイブリッド・ソリューションを使用して、VMware vSphere 環境をバックアップできます。

* {{site.data.keyword.cloud}} Object Storage サービス
* NetApp AltaVault クラウド・ストレージ・ゲートウェイ
* Veeam Backup & Replication ソフトウェア

Veeam Backup & Replication を使用して、NetApp AltaVault クラウド統合ストレージ・アプライアンスと {{site.data.keyword.cos_full}} を含むハイブリッド・ソリューションを実現できます。 このソフトウェアによって、バックアップを作成して保守し、バックアップから仮想環境をリストアします。 NetApp AltaVault クラウド統合ストレージ・アプライアンスと一緒に使用して、ローカル (オンプレミス) に保管されるバックアップを作成します。 バックアップは、同時に {{site.data.keyword.cos_full_notm}} にも複製されます。 このハイブリッド・ソリューションでは、バックアップが 2 つ作成されますが、そのうち 1 つのみがローカルに存在します。

## AltaVault クラウド統合ストレージ・ゲートウェイ
{: #altavault-cloud-integrated-storage-gateway}

AltaVault クラウド・ストレージ・ゲートウェイを使用すると、{{site.data.keyword.cos_full_notm}} に対してスクリプトや REST API を使用したアプリケーションを作成することなく、オンプレミス環境とクラウドを統合できます。 マウントするか、マウント・ポイントをポイントして、クラウドへのデータ・コピーを安全に開始できます。

### AltaVault のオンプレミス・デプロイ
{: #deploying-altavault-on-premises}

AltaVault を {{site.data.keyword.cos_full_notm}} のオンプレミス・バックアップ・ソリューションとしてデプロイするには、以下の手順に従います。

物理アプライアンスまたは仮想アプライアンスとして AltaVault を購入できます。 この手順では、評価版 VMware vSphere ESXi ベース AltaVault 仮想アプライアンスのデプロイメントについて説明しています。
{:tip}

<a name="prerequisites"></a>
#### 前提条件

以下の前提条件が満たされていることを確認します。

* AltaVault 仮想アプライアンス。 これは、OVA ファイル拡張子の単一ファイルです。 アプライアンスについて NetApp 担当員に問い合わせていただくか、[NetApp AltaVault Web サイト ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](http://www.netapp.com/us/products/protection-software/altavault/){: new_window} から 90 日間の評価版をダウンロードしてください。
* AltaVault アプライアンス用の CPU、メモリー、ディスク・スペースの最小要件を満たしている既存のオンプレミス vSphere ESXi 5.5 環境。 評価版を使用する場合、これらの要件は、4 つの仮想 CPU (vCPU)、24 GB のメモリー、最大 8 TB のディスク・スペースです。
* vSphere 環境内で使用可能な 2 つの 10 Gbps ネットワーク・インターフェース・コントローラー (NIC)。 一方の NIC はデータ入力に使用され、もう一方は {{site.data.keyword.cos_full_notm}} へのデータ複製に使用されます。
* vSphere 環境内で定義される 2 つの NIC に対応する 2 つのネットワーク (VLAN)。 複製用のネットワークをデータ入力用のネットワークと同じネットワークに割り当てることはできません。これを行うと、ルーティング・ループが生じる可能性があります。
* {{site.data.keyword.cos_full_notm}} 資格情報一式。 これらの資格情報には、{{site.data.keyword.cloud_notm}} ユーザー名、{{site.data.keyword.cos_full_notm}} ユーザー名、{{site.data.keyword.cloud_notm}} ユーザー名に関連付けられている API キーが含まれます。
* VMware Sphere 用語と vSphere ESXi 環境の管理についての知識。 この知識には、vSphere Web クライアントおよび vSphere クライアントの使用法、ネットワークとストレージを含むハードウェア・リソースの割り当てが含まれ、これら以外の知識も含まれます。

### AltaVault OVA のデプロイ
{: #deploying-altavault-ova}

すべての前提条件を満たした後、vSphere 環境に AltaVault OVA をデプロイできます。 OVA デプロイメントの手順について詳しくは、[NetApp AltaVault Installation and Service Guide ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window} を参照してください。

1. OVA のデプロイメントが完了したら、AltaVault 仮想マシン (VM) を編集します。
2. 編集ウィンドウに表示されている AltaVault と一致するように割り振り済みメモリーを変更します。 評価版を使用している場合は、24 GB のメモリーを割り当て、8 TB 以下のディスクを 1 つ追加します。 **注:** この 2 次ディスク・ストレージ・デバイスは、重複排除されるバックアップ・データの保管に使用されます。
* メモリーとディスクの構成が変更されたら、AltaVault アプライアンスに別のネットワーク (VLAN) が割り当てられていることを確認します。

NIC には、以下のインターフェース機能が割り当てられます。

* プライマリー。 管理インターフェースとして使用されます。
* e0a。 AltaVault アプライアンスからクラウドにデータを複製するために使用されるインターフェースです。
* e0b。 SMB/CIFS 共有または NFS 共有のマウント・ポイントをエクスポートするために使用されるインターフェースです。
* e0c。 SMB/CIFS 共有または NFS 共有のマウント・ポイントをエクスポートするために使用されるオプションのインターフェースです。

この構成例では、AltaVault アプライアンスは、クラウドへの複製のインターフェースとして **e0a** インターフェースを使用し、CIFS/SMB マウント・ポイントをエクスポートするために **e0b** インターフェースを使用します。 **注:** 同じデータにアクセスするために CIFS/SMB 共有と NFS 共有の両方は使用できません。 つまり、データが CIFS/SMB 共有に置かれている場合、NFS 共有を使用してそのデータにアクセスすることはできず、その逆の場合も同様です。

AltaVault アプライアンスのデプロイ、およびアプライアンスの VM 設定の構成について詳しくは、[NetApp AltaVault Installation and Service Guide ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window} を参照してください。

### AltaVault アプライアンスの初期構成 <!--initial configuration?-->
{: #initial-configuration-of-the-altavault-appliance-initial-configuration-}

適切なハードウェアを使用して AltaVault VM が構成された後、電源を入れることができます。 **注:** 初めて AltaVault VM が始動する場合はしばらく時間がかかります。これは、AltaVault アプライアンスが 2 次メタデータ・キャッシュ・ディスクをフォーマットしているためです。

1. アプライアンスの始動プロセスが完了したら、AltaVault コンソールにログインします。 **ユーザー名**に ``*admin`` を使用し、**パスワード**に ``password`` を使用します。 初期構成が完了した後、これらの資格情報を変更できます。
2. ログインした後、初期構成のウィザードを使用するかどうかを尋ねられます。 **y** を入力して **Enter** キーを押し、変更を保存します。

ウィザードが開いたら、表 1 の情報を使用します。

|質問|応答|
|---|---|
|ステップ 1: 管理者パスワードは?|新規の管理者パスワードを入力します (「password」は使用できません)|
|ステップ 2: ホスト名は?|使用するホスト名を入力します|
|ステップ 3: 基本インターフェースで DHCP を使用しますか?|**n** を入力します|
|ステップ 4: プライマリー IP アドレスは?|プライマリー・ネットワーク IP アドレスを入力します。 構成例では、プライマリー IP アドレスは、クラウド複製とアプライアンス管理に使用されるネットワークです (192.168.50.15)|
|ステップ 5: ネットマスクは?|ネットマスクを入力します (255.255.255.0)|
|ステップ 6: デフォルト・ゲートウェイは?|デフォルト・ゲートウェイを入力します (192.168.50.1)|
|ステップ 7: プライマリー DNS サーバーは?|自分の環境のプライマリー・ドメイン・ネーム・システム (DNS) サーバーを入力します|
|ステップ 8: ドメイン名は?|自分の環境のドメイン名を入力します (testenv.org)|
{: caption="表 1. AltaVault 初期構成値" caption-side="top"}

### Object Storage に対する AltaVault の構成
{: #configuring-altavault-for-object-storage}

以下の手順を使用して、アプライアンスを {{site.data.keyword.cos_full_notm}} サービスに接続するように構成します。

1. Web ブラウザーを開き、AltaVault アプライアンスのプライマリー・インターフェースの IP アドレスを入力します (前のステップを参照)。
2. 管理者の資格情報を使用して、コンソールにログインします。 最初のログインでは、「Wizard Dashboard」が表示されます。
3. **「System Settings」**を選択し、次の画面で情報が正しいことを確認して、自分の環境のタイム・ゾーンを反映するようにタイム・ゾーンを調整します。
4. **「Next」>「Save and Apply」>「Exit」**をクリックします。 「Wizard Dashboard」に戻ります。
5. **「Cloud Settings」**を選択して、**「Provider」**をクリックします。 **「IBM Cloud Object Storage」**を選択します。
6. 該当する**「Object Storage Region」**を選択します。 **注:** すべての地域が表示されるわけではありません (メルボルンなど)。 ただし、{{site.data.keyword.cos_short}} サービスのホスト名は、**「Host name」**フィールドを使用して変更できます。 例えば、地域としてメルボルンを使用する場合は、**「Region」**ドロップダウン・メニューから**「San Jose 1」**を選択して、**「Host name」**フィールドを **mel01.objectstorage.softlayer.net** に変更することができます。
7. **「User name」**フィールドに {{site.data.keyword.cos_full_notm}} 資格情報を入力します。 ユーザー名の形式は、`object_storage_username:IBM_Cloud_username` にする必要があります。 例えば、**ABC-DE123456-7:user** のようにします。 Object Storage のユーザー名は、[{{site.data.keyword.slportal_full}} ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://control.softlayer.com/){: new_window} の**「Storage」>「Object Storage」**で見つけることができます。
8. データを保管するための**「Bucket Name」**を入力します。 バケット名は、{{site.data.keyword.cos_full_notm}} でデータを保管するコンテナーの名前です。
* ネットワーク管理者に指示された場合を除き、デフォルト・ポートを変更しないでください。 **「Enable Archiving」**フィールドは、デフォルトで**「No」**になっているので、**「Next」**をクリックします。
9. 必要に応じて、**「License Request Token」**を入力し、**「Next」**をクリックします。
10. **「Encryption Key」**を入力します。 AltaVault での新規暗号鍵の生成を許可することも、データの暗号化と復号に使用する既存の鍵を入力することもできます。 **「次へ」**をクリックします。
11. すべての設定が正しいことを確認してから、**「Finish and Apply」**をクリックします。 AltaVault では、「Cloud Settings Wizard」での入力と設定を使用して {{site.data.keyword.cos_full_notm}} サービスへの接続が試行されます。 接続が失敗する場合は、設定を確認し、サービスへの適切なアクセス権限があることを確認します。
12. 接続が確立したら、**「Exit」**をクリックして「Wizard Dashboard」に戻り、**「Exit Wizard」**をクリックして AltaVault アプライアンスの状況ページに戻ります。
13. *Storage Optimization Service* が稼働中であり、状況が「ready」になっていることを確認します。 **注:** 状況が *Ready* に変わるまで数分間かかることがあります。

AltaVault アプライアンスは、{{site.data.keyword.cos_full_notm}} サービスと通信するように構成されました。

### AltaVault での CIFS/SMB マウント・ポイントの構成
{: #configuring-the-cifs-smb-mount-point-in-altavault}

**e0b** インターフェースで CIFS/SMB マウント・ポイントが作成されるように構成する必要があります。 以下の手順を使用して、**e0b** を構成します。

1. **「Settings」>「Data Interfaces」**に移動して、**「e0b」**インターフェースを展開します。 **「Enable Data Interface」**にチェック・マークを付け、CIFS/SMB 共有のマウントに使用する **IP アドレス、サブネット・マスク**、および**ゲートウェイ**を入力します。
2. **「MTU」**のデフォルト値の **1500 バイト**をそのまま残します。<br/><br/>デフォルトの最大伝送単位 (MTU) は 1,500 に設定されていますが、ジャンボ・フレームを使用する場合は 9,000 に変更できます。 **注:** ESXi ホストおよび物理インフラストラクチャーでジャンボ・フレームがサポートされている必要があります。 デフォルトで、{{site.data.keyword.cloud_notm}} は、既に 9,000 バイトの MTU サイズをサポートしており、構成を変更する必要はありません。
3. **「Apply」**をクリックします。 マウント・ポイントの構成が準備できました。
4. **「Storage」>「CIFS」>「Add CIFS Share」**を選択して、固有の名前を入力します。
5. **「Pin Share」**ドロップダウン・メニューをクリックして、**「Yes」**を選択します。 **注:** Veeam Backup & Replication バックアップは、ピン留めを解除された共有に対しては失敗する場合があります。
6. **「Path」**フィールドに、共有のための固有パスを入力します。 パスとして共有名を使用するとよいでしょう。例えば、共有名が cifs_share0 の場合、パスとして `/cifs_share0` を入力します。
7. セキュリティーが問題でない場合は、**「Allow Everyone Access」**をクリアします。 CIFS/SMB 共有を使用するクライアントをホワイトリストに登録するとよいでしょう。 それ以外の場合で、セキュリティーが問題になる場合は、**「Allow Everyone Access」**を選択したままにし、**「Add Share」**をクリックします。
8. **「Add CIFS User」**をクリックして許可ユーザーのアカウントを作成し、**「User name」**フィールドと**「Password」**フィールドを入力します。
9. 新規 CIFS 共有を展開して**「Add a user or group」**をクリックし、許可ユーザー・アカウントを追加します。
10. **「Global CIFS Settings」**に移動して**「Listening Interface」**ドロップダウン・メニューをクリックし、**「e0b」**を選択して**「Apply」**をクリックします。

AltaVault アプライアンスは、アプライアンス自体と、{{site.data.keyword.cos_full_notm}} と、Veeam Backup & Replication を実行するコンピューターとの間で通信が許可されるように構成されました。 今後のデプロイメントを促進するため、必要に応じて、AltaVault アプライアンスの構成をエクスポートすることをお勧めします。

AltaVault アプライアンスの構成をエクスポートするには、以下の手順に従います。

1. オンプレミス AltaVault アプライアンスの Web 管理コンソールで、**「Settings」>「Setup Wizard」**をクリックして「Wizard Dashboard」にアクセスします。
2. **「Export Configuration」**をクリックして、**「Export Configuration」**をクリックします。
3. 安全な場所に構成ファイル (tarball/.tar) を保存します。

## Veeam Backup & Replication
{: #veeam-backup-replication-backup}

Veeam Backup & Replication ソフトウェアは、VM とそのデータのバックアップ、複製、リカバリーの包括的な機能を提供します。 バックアップは、AltaVault クラウド・ゲートウェイ・アプライアンスと完全に統合できます。

### 新規サーバーでの Veeam のプロビジョニング
{: #provisioning-veeam-on-a-new-server}

新規 VSI サーバーまたはベアメタル・サーバーをプロビジョンするときに、Veeam を注文できます。 プロビジョニングには、以下の情報を使用します。
  * Veeam は月単位の請求でのみ使用可能です。
  * Veeam は Windows オペレーティング・システムでのみ使用可能です。

Veeam を追加するには、以下のようにします。
  * 「システム・アドオン」セクションの「OS 固有のアドオン」で、「Veeam」タブをクリックして Veeam オプションの 1 つを選択します。
  * 「システム・アドオン」セクションの「CDP アドオン」で、追加の Veeam オプションを選択して追加します。 <br><br>**注**: *Veeam Backup and Replication 9.5 Update 3* を選択すると、CDP アドオンのリストから少なくとも 1 つのオプションを選択する必要があります。

### 既存のサーバーでの Veeam の注文
{: #ordering-veeam-on-an-existing-server}

[OS の再ロード](/docs/infrastructure/software?topic=software-reloading-the-os)の手順を使用して、既存のサーバーに Veeam を追加できます。 サーバーは、Windows OS で実行されていて、月単位の請求でセットアップされている必要があります。

Veeam を追加するには、以下のようにします。
1. 「システム・アドオン」セクションの「OS 固有のアドオン」で、「Veeam」タブをクリックして Veeam オプションの 1 つを選択します。
2. 「システム・アドオン」セクションの「CDP アドオン」で、追加の Veeam オプションを選択して追加します。 <br><br>**注**: *Veeam Backup and Replication 9.5 Update 3* を選択すると、CDP アドオンのリストから少なくとも 1 つのオプションを選択する必要があります。

### Veeam Backup & Replication のデプロイ
{: #deploying-veeam-backup-replication}

例では、Veeam Backup & Replication バージョン 8 の評価版を使用しています。

#### 前提条件
{: #prerequisites-deploying-veeam-backup-replication}

デプロイメントを進める前に、以下の前提条件が満たされていることを確認します。

* {{site.data.keyword.cos_full_notm}} と Veeam Backup & Replication と共に使用するように構成されている既存の AltaVault アプライアンスがあります。
* VMware 環境用の Veeam Backup & Replication (単一の実行可能ファイル) を取得します。 Veeam 担当員に問い合わせていただくか、[30 日間の評価版 ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](http://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window} をダウンロードしてください。
* Veeam Backup & Replication と共に使用するライセンス・ファイルを取得します。 多くの場合、このファイルは、Veeam Backup & Replication のダウンロードに使用した E メール・アドレスに送信されます。 このファイルを受信していない場合は、Veeam 担当員にお問い合わせください。<br/><br/>ライセンス・ファイルは、Veeam Backup & Replication のすべての機能をアクティブにするために使用されます。 プログラムのインストール時にこのファイルが指定されない場合、すべての機能は 30 日間の評価版に戻されます。
* オンサイトの場合でもオフサイトの場合でも、既存のバックアップ・サーバーに表 2 の仕様を使用します。インストールされているオペレーティング・システムは 64 ビット・バージョンでなければなりません。

||最小|推奨|
|---|---|---|
|**OS**|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>WIndows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|
|**コアまたは vCPU の数**|2|4|
|**メモリー**|4 GB 基本 RAM および同時バックアップ・ジョブごとに 500 MB。|16 GB 基本 RAM および同時バックアップ・ジョブごとに 4 GB。|
|**ディスク・スペース**|製品インストールに 2 GB; ゲスト・ファイル・システム・カタログ・データ (永続データ) に対し 100 VM ごとに 10 GB。|製品インストールに 2 GB; ゲスト・ファイル・システム・カタログ (永続データ) に対し 100 VM ごとに 10 GB。|
|**ネットワーク**|オンサイトのバックアップと複製用に 1 Gbps LAN; オフサイトのバックアップと複製用に 1 Mbps WAN。|オンサイトのバックアップと複製用に 1 Gbps LAN; オフサイトのバックアップと複製用に 1 Mbps WAN。|
{: caption="表 2. Veeam Backup & Replication バックアップ・サーバーのシステム要件" caption-side="top"}

#### Veeam Backup & Replication のインストール
{: #installing-veeam-backup-replication}

すべての前提条件を満たした後、以下の手順を使用して、Veeam Backup & Replication をバックアップ・サーバーにインストールします。

1. セットアップ・ウィザードを開始するには、Veeam の実行可能ファイルをダブルクリックして、**「Veeam Backup & Replication - Install」**をクリックします。
2. **「Next」**をクリックして、使用条件の条項に同意します。
3. **「Next」**をクリックして、**「Veeam Backup & Replication」**の**「Install」**をクリックします。
4. 前に取得したライセンス・ファイルの場所を入力して、**「Next」**をクリックします。
5. **「Veeam Backup & Replication Setup」**画面で、インストールする Veeam Backup & Replication コンポーネントを選択して、インストール場所を指定します。 **「Veeam Backup & Replication」と「Veeam Backup catalog」**は必須コンポーネントです。 **「次へ」**をクリックします。 セットアップ・ウィザードにより、すべての必須プログラム・フレームワークとサポート・コンポーネントが確実にインストールされるように一連の検査が実行されます。 欠落しているものがある場合、セットアップ・ウィザードによってそれらのインストールは自動的に提供されます。 欠落しているフレームワークまたはコンポーネントをインストールする必要がある場合、**「Install」**をクリックします。
6. すべてのコンポーネントがシステム検査で**「Passed」**になっていることを確認して、**「Next」**をクリックします。
7. Veeam Backup Service を実行する**サービス (ユーザー) アカウント**を選択します。 **注:** デフォルトのサービス・アカウントは*「LOCAL SYSTEM account」*です。 **「次へ」**をクリックします。
8. Veeam Backup & Replication データベースを作成して保管するために使用する**「SQL Server Instance」**を選択します。<!--Contact your database administrator for more information, if necessary. --> **「次へ」**をクリックします。
9. **「Catalog service port」**と**「Veeam Backup service port」**を入力します (デフォルトのポートは **9393** と **9392** です)。<!--Contact your network administrator for more information, if necessary.--> **「次へ」**をクリックします。
10. ゲスト・ファイル・システム・カタログ (永続データ) および vPower NFS 書き込みキャッシュ (非永続データ) を保管するディレクトリーを選択します。 **「次へ」**をクリックします。
11. すべての設定および値が正しいことを確認して、**「Install」**をクリックし、インストールを開始します。 インストールが完了したら、**「Finish」**をクリックします。

### バックアップのための Veeam Backup & Replication の構成
{: #configuring-veeam-backup-replication-for-backups}

Veeam Backup & Replication をインストールした後、AltaVault 仮想アプライアンスを含む vSphere ESXi ホストと接続できます。

1. Veeam Backup & Replication を始動します。
2. 画面左下側の**「Backup Infrastructure」>「Managed Servers」**をクリックします。
3. **「Add Server」**をクリックして、**「VMware vSphere」**をダブルクリックします。
4. vCenter サーバーの**「DNS name or IP address」**および**「Description」**を入力して、**「Next」**をクリックします。
5. vSphere サーバーの管理者特権が付与されているローカル・アカウントの**「Credentials」**を入力します。 **注:** アカウント・ユーザー名は、ドメイン・アカウントの **DOMAIN\USER** 形式、またはローカル・アカウントの **HOST\USER** 形式にする必要があります。 アカウントを追加するには、**「Add」**をクリックして、アカウントのユーザー名とパスワードを入力します。<br/>ネットワーク管理者に指示された場合を除き、Veeam Backup & Replication インストール中の**「Default VMware web services port」**を変更しないでください。
6. **「次へ」**をクリックします。 Veeam Backup & Replication が VMware vSphere サーバーに接続されます。 接続の試行が失敗する場合、アカウントが存在しており VMware vSphere サーバーの管理者特権が付与されていることを確認してから、接続を再試行してください。
7. **「Summary」**ウィンドウで**「Finish」**をクリックし、**「Managed Servers」>「VMware vSphere」**をクリックして、vSphere サーバーが正常に追加されたことを確認します。

### Veeam Backup & Replication へのバックアップ・リポジトリーの追加
{: #adding-a-backup-repository-to-veeam-backup-replication}

デフォルトでは、Veeam Backup & Replication は、プログラムのインストール中に、Veeam Backup & Replication バックアップ・サーバーの C:\ ドライブにローカル・バックアップ・リポジトリーを作成します。

以下の手順を使用して、AltaVault アプライアンスのすべてのバックアップが保管されるバックアップ・リポジトリーを作成します。 デフォルトのバックアップ・リポジトリーを使用する場合は、以下の手順はスキップしてください。

1. 左下の**「Backup Infrastructure」**画面で、**「Backup Infrastructure」>「Backup Repositories」>「Add Repository」**をクリックします。
2. **「Name」**フィールドに、固有のリポジトリーの名前を入力します。 オプションとして、**「Description」**を入力できます。 **「次へ」**をクリックします。
3. **「Shared folder」**を選択します。 作成した CIFS/SMB 共有が、このバックアップ・リポジトリーで使用する共有です。 **「次へ」**をクリックします。
4. AltaVault アプライアンスの CIFS/SMB 共有の場所を指定します。 場所を判別するには、Web ブラウザーを開き、AltaVault アプライアンスの IP アドレスを入力します。 **「Storage」>「CIFS」**に移動して、共有の**「Share Path」**をメモします。 **注:** これは共有のローカル・パスとは同じではありません。<br/><br/>共有パスの形式は、`\\<AltaVault appliance hostname>\<share name>` です。 「Share Path」の AltaVault アプライアンスのホスト名は、AltaVault アプライアンスの **e0b** ネットワーク・インターフェース (共有のマウント・ポイント) の IP アドレスに置き換えます。<br/><br/>**e0b** インターフェースの IP アドレスを見つけるには、AltaVault アプライアンスの管理ウィンドウで、**「Settings」>「Data Interfaces」**をクリックします。 Veeam Backup & Replication で指定されている共有パスは、`\\192.168.50.16\cifs_test2` です。
5. Veeam Backup & Replication に戻り、**「Shared folder」**フィールドにマウント・ポイントの共有パスを入力して、**「Next」**をクリックします。 Veeam Backup & Replication では、マウント・ポイントとの接続確立が試行されます。 接続の試行が失敗する場合、前に戻って AltaVault アプライアンスのネットワーク設定が正しいことを確認してから、再試行してください。
6. 最大同時タスク数が、使用可能なリソース数に制限されるように、値を入力します。 この値は、選択された共有にバックアップ・プロキシーが送信できるタスクの最大数です。 同時タスクのデフォルトの数は 4 です。AltaVault では、5 個の同時タスクから開始して、リソースの状況に応じてこの値を増減することが推奨されています。 この値は、バックアップ・リポジトリーが作成された後に調整できます。
7. **「次へ」**をクリックします。
8. 必要に応じて、**「vPower NFS settings」**を指定します。 **「Enable vPower NFS server」**の選択がクリアされたままである場合、Veeam Backup & Replication では、リカバリーとリカバリー検証のために vPower が使用されます。 **「次へ」**をクリックします。
9. **「Review」**画面ですべての設定が正しいことを確認して、**「Next」**をクリックします。
10. **「Finish」**をクリックして、ウィザードを終了します。 これで、データのバックアップを開始できます。

### 環境のバックアップ
{: #backing-up-the-environment}

以下の手順に従って、完全な仮想環境のバックアップを作成します。

1. **「Backup Infrastructure」**画面で、**「Backup & Replication」**をクリックします。
2. **「Backup & Replication」**ウィンドウで、**「Jobs」>「Backup Job」**をクリックします。
3. **「Name」**フィールドに、固有の名前を入力します。 オプションとして、**「Description」**を入力できます。 **「次へ」**をクリックします。
4. バックアップする VM を選択するため、**「Add Objects」**をクリックし、ツリー構造をクリックして VM を選択します。 該当する VM を選択した後、**「Add」**をクリックします。<br/>VM の特定の部分のみをバックアップする場合 (始動ディスク) は、**「Exclusions」**をクリックしてその部分を指定します。 それ以外の場合は、**「Next」**をクリックします。
5. **「Backup repository」**ドロップダウン・メニューを使用して、作成したバックアップ・リポジトリーを選択します。

**注:** 最適なパフォーマンスのためには、データ重複排除の設定と圧縮の設定を変更してください。 以下の手順を使用して、パフォーマンスを最適化します。

1. **「Advanced」**をクリックして、**「Storage」**タブを選択し、**「Enable inline data duplication」**をクリアします。 AltaVault アプライアンスで、それを通過する Veeam Backup & Replication バックアップのブロック・レベル重複排除が実行されるため、パフォーマンスが向上します。
2. **「Compression level」**ドロップダウン・メニューで**「None」**を選択し、**「Storage optimization」**ドロップダウン・メニューで**「LAN target」**を選択します。<br/>**注:** CIFS/SMB 共有のネットワークの場所が非常に混雑している場合は、**「Enable inline data deduplication」**をチェックしたままにすると、ネットワーク・パフォーマンスの問題が緩和する場合がありますが、AltaVault アプライアンスでのデータ重複排除率は低下します。
3. **「次へ」**をクリックします。
4. アプリケーションを認識した処理やゲスト・ファイル・システムのインデックス作成を行う場合は、該当するチェック・ボックスを選択してください。 必要に応じて、**「Guest OS credentials」**(バックアップされる VM のゲスト OS のユーザー名およびパスワード) を設定します。 **「次へ」**をクリックします。
5. バックアップを定期的に実行する場合は、**「Run the job automatically」**チェック・ボックスを選択して、実行する間隔を設定します。 それ以外の場合は、**「Create」**および**「Finish」**をクリックします。

#### 手動バックアップの開始
{: starting-a-manual-backup}
バックアップを手動で開始するには、バックアップ・ジョブを右クリックして、**「Start」**を選択します。 または、新規バックアップを作成する場合は、**「Active Full」**を選択します。

**注:** Veeam Backup & Replication は、バックアップから仮想環境をリストアできます。 仮想環境のリストアについて詳しくは、[Veeam Backup & Replication ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window} Web サイトを参照してください。
