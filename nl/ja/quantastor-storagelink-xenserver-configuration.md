---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: QuantaStor
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# QuantaStor StorageLink Xenserver のインストール
{: #installing-quantastor-storagelink-xenserver}

## QuantaStor Adapter for Citrix StorageLink のインストール
{: #installing-the-quantastor-adapter-for-citrix-storagelink}

1. XenServer のインストール済み環境に、StorageLink の適切なライセンスが適用されていることを確認します。

StorageLink 機能を使用できるのは、Citrix から「Enterprise」または「Platinum」ライセンスを購入したお客様のみです。 「Advanced」の Citrix XenServer ライセンスを所有しているお客様は、StorageLink 機能を利用できません。
{:tip}

2. 管理者プロンプトで次のコマンドを入力して、StorageLink サービスを停止します。 また、XenServer のサービス・パネルから StorageLink サービスを停止することもできます。

        C:&#xa5;> net stop storagelink

3. [OSNexus ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.osnexus.com/trynow/){: new_window} で提供されている StorageLink アダプターをダウンロードして実行します。
4. サービスが停止したら、次のコマンドを実行して QuantaStor StorageLink アダプターをインストールします。

        osn_quantastor_csladapter.exe

5. この XML ブロックを StorageLink 構成の最後に追加します。
  * `C:&#xa5;Program Files&#xa5;Citrix&#xa5;StorageLink&#xa5;Server&#xa5;scsi_device_id_config.xml`
  * `OSNEXUS`
  * `QUANTASTOR1`
  * このファイルの先頭には、_2.0.0.4._ のようなバージョン番号があります。 このバージョン番号を大きくします (例: 2.0.0.5)。 バージョン番号は StorageLink に対し、これらの新しいデバイス識別ルールを使用して XenServer システムをアップグレードする必要があることを示します。
6. Citrix StorageLink サービスを再始動します。

        C:&#xa5;> net start storagelink

## StorageLink が正しくロードされたかどうかを検証する
{: #verifying-that-storagelink-loaded-properly}

1. Citrix StorageLink Manager にログインし、左側の「管理 (Administration)」ツリーを選択して、[QuantaStor ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://svn.osnexus.com/mediawiki/images/thumb/c/c8/Storagelink_admin.png/640px-Storagelink_admin.png){: new_window} リストから OS NEXUS QuantaStor を見つけ出します。
2. ストレージ・システムの認証情報を追加します。これにより、StorageLink で QuantaStor ストレージ・システムのストレージ・ボリュームを動的に構成し、割り当てることができます。

## XenServer マルチパス構成設定のインストール
{: #installing-the-xenserver-multipath-configuration-settings}

XenServer は、Linux のデバイス・マッパー・マルチパス・ドライバー (`dmmp`) を使用して、ハードウェア・マルチパスのサポートを有効にします。 `dmmp` ドライバーは、`multipath.conf` という構成ファイルを使用します。この構成ファイルには、各ベンダーのマルチパス・モードとフェイルオーバー・ルールが含まれています。

QuantaStor には、`multipath.conf` ファイルに追加する必要がある特別なルールがいくつかあります。 各 XenServer `dom0` ノードにある `/etc/multipath-enabled.conf` ファイルを編集してください。 そのファイル内で、最後の device { } セクションの後に次のスタンザを追加します。

    device {
          vendor                  "OSNEXUS"
          product                 "QUANTASTOR"
          getuid_callout          "/sbin/scsi_id -g -u -s /block/%n"
          path_selector           "round-robin 0"
          path_grouping_policy    multibus
          failback                immediate
          rr_weight               uniform
          rr_min_io               100
          path_checker            tur
    }
