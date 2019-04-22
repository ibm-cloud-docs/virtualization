---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Avago SafeStore, encryption
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Avago SafeStore 暗号化サービスを使用したドライブ・セキュリティーの有効化
{: #enabling-drive-security-by-using-avago-safestore-encryption-services}

ドライブ・セキュリティーをセットアップすると、取り外されたディスクに保管されているデータにセキュリティー・キーなしでアクセスされることを防止できます。 このキーがないと、ドライブのデータを回復できません。 {{site.data.keyword.cloud}} は、ベアメタル・サーバー上で購入できるドライブ用に一部のデータ・センターで自己暗号化ドライブ (SED) を提供しています。 IBM の米国データ・センターでは 10 TB SATA ドライブを使用できます。

## 前提条件
{: #prerequisites-enabling-drive-security-by-using-avago-safestore-encryption-services}

* 10 TB SATA の SED ドライブを装備したベアメタル・サーバー
* LSI/AVAGO MegaRAID SAS 9361 -8i または同様の LSI/AVAGO RAID カード
* インストール済みの Mega RAID Storage Manager ソフトウェア

## MegaRAID Storage Manager (MSM) を使用したドライブ・セキュリティーの有効化
{: #enabling-drive-security-by-using-megaraid-storage-manager-msm-}

MegaRAID Storage Manager を使用することで、セキュリティー・キーを設定して、そのセキュリティー・キー内のデータを保護できます。 WebBIOS インターフェースを使用することもできます。このインターフェースでは、サーバーの始動時に MegaRAID カードの BIOS に入って、ドライブ・セキュリティーの設定を構成する必要があります。 MegaRAID Controller Card SAS 9361-8i について詳しくは、Broadcom のサイト [MegaRAID SAS 9361-8i ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.broadcom.com/products/storage/raid-controllers/megaraid-sas-9361-8i#documentation) を参照してください。

### プリインストールされた SED ドライブの識別
{: #identifying-preinstalled-sed-drives}

MegaRAID Storage Manager は、ほとんどのサポート対象オペレーティング・システムにプリインストールされています。 MegaRAID Storage Manager がインストールされていない場合は、Broadcom サイトから手動でインストールできます。 詳しくは、[MegaRAID SAS 9361-8i downloads](https://www.broadcom.com/products/storage/raid-controllers/megaraid-sas-9361-8i#downloads) を参照してください。

MegaRAID Storage Manager は、システム資格情報を使用して開くことができます。 この例では、Windows マシンを使用しており、MSM はプリインストールされています。

MSM を開始する際は、特権ユーザー (Administrator) の**「User name」**と**「Password」**を入力する必要があります。

<!--![Figure 1](images/1_adapter_login.jpg)-->

**「Physical」**タブをクリックします。 次に、システム上で使用可能なドライブをクリックします。 **「Properties」**ペインにある
**「Drive Security Properties」**の**「Full Disk Encryption capable」**フィールドの値は、**「Yes」**となっています。 ここで使用している例では、2 つの非 SED ディスクと 4 つの SED ディスクが使用されています。

<!--![Figure 2](images/1_fde_capable_drives.jpg)-->

### コントローラーでのドライブ・セキュリティーの有効化
{: #enabling-drive-security-at-the-controller}

1. ドライブ・セキュリティーを有効にするには、**「Physical」**タブで**「Controller 0 :AVAGO MegaRAID SAS 9361-8i」**を
右クリックして、**「Enable Drive Security」**を選択します。
  * これで、**「Security key identifier」**と**「Security key」**を入力できるようになりました。 複数のセキュリティー・キーがある場合は、セキュリティー・キー ID に基づいて、使用するセキュリティー・キーを識別できます。 セキュリティー・キーは安全な場所に記録しておく必要があります。 セキュリティー・キーが必要となるのは、ドライブを再構成するときです (ドライブの取り外しや再挿入など)。 セキュリティー・キーがないと、SED を使用して作成されたボリュームに保管されているデータを取得できません。 紛失したセキュリティー・キーを再取得することはできません。 始動時のパスワードを設定することもできます。このパスワードによってシステムが一時停止状態になっている間に、ここで設定したパスワードを入力します。 始動時のパスワードは任意指定ですが、このパスワードが設定されている場合は、システムがリブートされるたびに、IPMI にログインして始動パスワードを入力する必要があります。 スクロールダウンして、**「I recorded the security settings for future reference」**というチェック・ボックスを選択して、**「Yes」**をクリックしてドライブ・セキュリティーを有効にします。
  * ドライブ・セキュリティーが有効になると、**「Controller 0 AVAGO MegaRAID SAS 9361-8i」**に黄色い鍵の画像が表示されます。
2. 次に、SED を使用してセキュア・ボリュームを作成します。 **「Logical」**タブで**「Controller0」**を右クリックして、  
**「Create Virtual Drive」**を選択できます。
3. **「Advanced」**オプションを選択します。 画面上で、**「RAID level」**を指定して、**「Drive security method」**を
**「Full Disk encryption (FDE)」**に設定する必要があります。 必要な FDE ドライブを選択して、**「Add」**>**「Create Drive Group」**>**「Next」**をクリックします。
4. 仮想ドライブの設定を確認して、必要に応じて変更を加えます。 **「Read Policy」**の推奨設定は**「Always Read Ahead」**です。 **「Write policy」**の推奨設定は**「Write Back」**です。 **「Create Virtual Drive」**をクリックします。 **「Yes」**をクリックして、BBU に起因する Write Back ポリシーの影響を受け入れます。 **「Next」**をクリックして、要約画面の内容を確認します。 **「完了」**をクリックします。
5. 仮想ディスクが保護されたことを確認するには、**「Logical」**タブをクリックして、作成された仮想ドライブをクリックします。 **「Drive Security Properties」**で、**「Secured」**フィールドの値が**「Yes」**になっていることがわかります。

<!--![Figure 3](images/2_enable_drive_security.jpg)-->
<!--![Figure 4](images/3_security_key_details_page.jpg)-->
<!--![Figure 5](images/4_security_key_set_0.jpg)-->
<!--![Figure 6](images/9_create_vd_with_fde_drives.jpg)-->
<!--![Figure 7](images/10_create_vd_advanced_select_raid_drive_encryption_0.jpg)-->
<!--![Figure 8](images/create_vd_settings.jpg)-->
<!--![Figure 9](images/6_vd_secured_confirmation_0.jpg)-->

SED ドライブを使用して既に作成された RAID ボリュームがサーバーに付随している場合は、次の手順に従ってそのボリュームをセキュア化できます。

**「Logical」**タブで、**「Drive Group」**を右クリックして**「Secure Using FDE」**を選択します。 同一ボリューム用に FDE ドライブと非 FDE ドライブを
組み合わせている場合は、このオプションは表示されません。

<!--![Figure 10](images/5_secure_existing_vd_with_fde_drives_0.jpg)-->

ドライブ・セキュリティーを解除するには、まず保護された仮想ディスクを削除して、「Controller 0」を右クリックして**「Disable Drive Security」**を選択する必要があります。 これにより、そのコントローラー内のデータが安全に消去されて、ドライブ・セキュリティーが解除されます。

ドライブ・セキュリティーをセットアップするためのもう 1 つの方法として、WebBIOS を使用して、始動時に IPMI を通じてログインして、RAID BIOS に入ってセットアップすることもできます。 詳しくは、**「12 Gb/s MegaRAID SAS Software User Guide」**の**『Avago SafeStore Encryption Services』**を参照してください。
