---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-23"

subcollection: virtualization

keywords: Windows 2008
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Windows 2008 への Windows Server 仮想化のインストール
{: #installing-windows-server-virtualization-on-windows-2008}

<!--Windows 2008 64-bit edition comes with the option to install Windows next generation virtualization application codenamed Veridian. By default the application will not be a selectable option when Adding and Removing Roles from Windows 2008.

Note: At this time, this is only available in full installation of 64-bit editions of Windows 2008. 32-bit versions and Server-core installations do not support this.-->

## 始める前に
{: #before-you-begin-win-2008}

始める前に、以下の前提条件を確認してください。

### ハードウェア
{: #hardware}

* BIOS でハードウェア支援型仮想化テクノロジーが有効化されていること
* Intel VT
* AMD-V
* BIOS でデータ実行防止が有効化されていること
* Intel Execute Disable (XD)
* AMD No Execute (NX)

### ソフトウェア
{: #software}

Windows Server 仮想化の**役割**オプションを有効にするには、いくつかのパッチをインストールする必要があります。
1. ブラウザー・ウィンドウを開いて %sysdir%&#xa5;Windows&#xa5;wsv を参照します。通常は C:&#xa5;Windows&#xa5;wsv です。 このフォルダーに以下の 2 つのファイルを配置します。
    * Windows6.0-KB939853-x64.msu
    * Windows6.0-KB939854-x64.msu
2. これらのパッチは任意の順序でインストールできます。 両方のパッチをインストールし、システムを再始動します。

## インストール
{: #installation}

1. システムを再始動したら、システムに**役割**を追加する必要があります。
2. 役割を追加すると、**「仮想ネットワークの作成」**ダイアログ・ボックスが表示されます。
**注:** 短時間ですがネットワーク接続が失われます。
3. プライベート・ネットワーク・アダプターの**「ローカル エリア接続」**を選択します。 **「続行」**をクリックします。 インストールが開始され、再始動が必要になります。
4. システムが再始動したら、パブリック接続を使用してシステムにログインします。 **注:** この接続のインストールに使用したログイン資格情報を使用してログインしなければなりません。 インターフェースに対するネットワーク接続は失われます。 そうでない場合は、以下のエラーを受け取ります。
    * *Windows Server Virtualization を構成しようとしましたが、エラー・コード 0x80078000 で失敗しました (Attempt to configure Windows Server Virtualization failed with error code 0x80078000.)*
このエラーを解決するには、Windows の仮想化の管理コンソールで**「スタート」>「すべてのプログラム」>「管理ツール」>「Windows Virtualization の管理 (Windows Virtualization Management)」**に進みます。
5. 右側のペインで、該当するサーバーをクリックします。
6. 操作ペインで、**「仮想ネットワーク マネージャー」**をクリックします。 **「仮想ネットワーク・スイッチの管理 (Virtual Network Switch Management)」**が表示されます。
7. 左側のペインで、**「新規追加」**ネットワーク・スイッチの下のネットワーク・スイッチをクリックします。
8. ネットワーク・スイッチをプライベートに名前変更します。
9. **「接続」**で物理ネットワーク・アダプターを選択し、最初のネットワーク・アダプターを選択します。 この時点で、すべてのネットワーク・プロトコルがプライベート・ネットワーク・ インターフェースからアンバインドされます。
10. **重要** プライベートに対するネットワーク接続を再確立するには、プライベート・インターフェースでは**なく**、新しいスイッチ・デバイスを構成する必要があります。 **「スタート」>「設定」>「ネットワーク接続」**に進みます。 **「ローカル エリア接続 2」**という新しいデバイスが表示されます。
11. この接続を右クリックし、**「プロパティ」**に進みます。
12. **「IPv4」**とそのプロパティーを選択します。 このデバイスにプライベート・ネットワーク・ インターフェースの IP アドレス、ネットマスク、DNS サーバーを構成する必要があります。
13. **「OK」>「閉じる」**をクリックします。 この構成によって、プライベート側のネットワーキングが再び有効になります。 プライベート IP を ping して、ネットワークがプライベートであることを検証できます。

プライベート IP に RDP 接続して、パブリック・ネットワークをセットアップします。

### パブリック・スイッチの追加
{: #adding-a-public-switch}

パブリック・スイッチを追加するには、プライベート・スイッチの追加と同じプロセスに従います。
1. **「仮想ネットワーク・スイッチの管理 (Virtual Network Switch Management)」**に戻って、**「新規ネットワーク・スイッチの追加 (Add new network switch)」**を選択します。
2. ネットワーク・スイッチのタイプとして**「外部」**を選択し、**「追加」**をクリックします。
3. スイッチを**「パブリック」**に名前変更し、**「物理ネットワーク アダプター」**を選択します。
4. 2 番目のネットワーク・アダプターを選択して、**「OK」**をクリックします。 この構成によって、パブリック・ポートがネットワークに応答しなくなります。 プライベートのものを構成したときと同じ方法で、新しいパブリック・スイッチ・インターフェースに適切な設定を構成します。
