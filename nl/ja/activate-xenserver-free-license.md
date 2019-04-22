---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-13"

subcollection: virtualization

keywords: Citrix XenServer 
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# XenServer のアクティブ化
{: #activating-xenserver}

Citrix XenServer のフリー・ライセンスは、初回インストールまたは再ロードから 30 日間にわたって供与されます。 この期間中に、Citrix に登録して、フル機能付きの 1 年間のライセンスを要請する必要があります。 このライセンスを作成するために、Citrix は、{{site.data.keyword.cloud}} がアクセスできない個人情報を必要とします。 このライセンスは、XenServer を使用するにあたってのユーザーと Citrix の間の契約です。
{:shortdesc}

ご使用のシステムを 30 日以内に登録しない場合は、どの仮想マシンの電源もオンにできなくなります。
{:tip}

ご使用のシステムを登録するには、以下の手順を実行します。

1. XenCenter クライアントをダウンロードしてインストールします。 このクライアントには、[Citrix XenCenter ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://community.citrix.com/display/xs/XenCenter){: new_window} のページからアクセスできます。

2. VPN を介してプライベート・ネットワークに接続します。

3. XenCenter を起動して、**「Add New Server」**をクリックするか、ドロップダウン・メニューから**「Server」>「Add...」**をクリックします。

4. **「Host name」**にはプライベート IP アドレスを指定し、**「User name」**には「 root」を指定し、**「Password」**にはルート・パスワードを指定します。

5. システムの接続後に、ダイアログ・ボックスが表示されます。 このダイアログ・ボックスでは、当該ライセンスが 30 日後に失効することが警告されます。 ダイアログ・ボックスが表示されない場合は、ドロップダウン・メニューから、**「Tools」>「License Manager...」**を選択します。

6. サーバーを確認して、**「Activate...」**をクリックします。 ブラウザーが開いて、Citrix の登録サイトに自動的にアクセスします。 フォームに入力して、フォームを送信します。 受信ボックスを開いて、Citrix から電子メールが届いていないか確認します。 この電子メールにはライセンス・キーが添付されています。

7. 上記の電子メールを受信したら、添付されているライセンス・キー・ファイルをご使用のコンピューターに保存します。

8. XenCenter の左側ペインで、ご使用のサーバーを強調表示します。 メニュー・バーで、**「Server」>「Install license key...」**に移動します。 ダイアログ・ボックスが表示されて、ライセンス・キーの場所を尋ねられます。 ステップ 7 で保存した場所を指定します。
