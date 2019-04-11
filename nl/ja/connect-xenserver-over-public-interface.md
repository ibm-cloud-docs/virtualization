---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Xenserver
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# パブリック・インターフェースを介した XenServer への接続
{: #connecting-to-xenserver-over-a-public-interface}

デフォルトでは、{{site.data.keyword.BluSoftlayer}} にインストールされた XenServer は、すべての管理接続用にプライベート・ネットワーク・インターフェースを使用するように構成されています。 プライベート・ネットワーク上で接続の問題が発生している場合は、サポート・チケットをオープンしてください。

この構成の変更は、セキュリティー・リスクとなるため推奨されません。
{:tip}

パブリック・インターフェースを使用するように XenServer を再構成する場合は、ホストから root として以下のコマンドを実行します (ホストをプールすることはできません)。.

1. `xe pif-list`

2. 有効にするインターフェースの uuid を書き留めます (通常は eth1 または bond1)。

3. 次のコマンドを実行します。

        `xe host-management-reconfigure pif-uuid=`

これらの変更は即時に有効になるため、再始動は必要ありません。
