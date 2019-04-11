---



copyright:
  years: 2014, 2018
lastupdated: "2018-01-04"

subcollection: virtualization


---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# FAQ: 一般
{: #faqs-general}

## カスタマー・ポータルの仮想サーバー・リストに自分のサーバーがリストされていないのは何故ですか?
{: #why-isn-t-my-server-listed-in-the-virtual-servers-list-in-the-customer-portal-}

サーバーに **XenServer**、**Hyper-V** または **Virtuozzo** があるのに、[ポータル ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://manage.softlayer.com/){: new_window} の[「ハードウェア」 ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://manage.softlayer.com/Hardware/configuration){: new_window}セクション内の[{{site.data.keyword.virtualmachinesshort}} ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://manage.softlayer.com/Virtual/live){: new_window} のリストに表示されない場合は、サポート部門の[標準サポート・チケット ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://manage.softlayer.com/Support/addTicket){: new_window} を開いて、サーバーをリストに追加してもらう必要があります。

この要求への対応は、通常の米国 CST 営業時間中に行われます。
{:tip}

## サービス・プロバイダーであるクライアントが、IBM Cloud で借りているホストに、VSPP ライセンスを持ち込んで使用することはできますか?
{: #if-clients-are-a-service-provider-can-they-use-their-own-vspp-licensing-for-hosts-that-they-rent-in-ibm-cloud-}

いいえ。ソケット・ベースのライセンスとして使用できるのは、{{site.data.keyword.cloud}} の VSPP または BYOL のいずれかです。 クライアントの VSPP 契約は使用できません。

## サーバーに設定されていたセカンダリー IP ブロックは仮想マシンに使用できますか?
{: #can-i-use-the-secondary-ip-block-that-came-with-the-server-for-virtual-machines-}

いいえ。サーバーと一緒に提供されたセカンダリー IP は、仮想マシンではなく、物理サーバーで使用するためにルーティングされます。 仮想マシンをネットワークに接続する場合は、ポータブル IP ブロックが必要です。
