---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# FAQ: Hyper-V
{: #faqs-hyper-v}

## Hyper-V とは何ですか?
{: #what-is-hyper-v-}

Hyper-V は、Windows 2008 Server Datacenter Edition の仮想化システムであり、単一の物理サーバーで複数の仮想マシンをホストし、仮想マシンごとに独自のオペレーティング・システムを実行することができます。

## Hyper-V を実行するための要件は何ですか?
{: #what-are-the-requirements-to-run-hyper-v-}

Hyper-V が Windows 2008 Server 64 ビット Datacenter Edition にインストールされている必要があります。 すべての {{site.data.keyword.BluSoftlayer}} ハードウェアが Hyper-V のシステム要件を満たしていなければなりません。 Microsoft は、サーバーの RAM として 2 GB 以上を推奨しています。

## Hyper-V は、他のバージョンの 2008 で使用できますか?
{: #is-hyper-v-available-on-any-other-versions-of-2008-}

{{site.data.keyword.BluSoftlayer_notm}} では、Hyper-V は Datacenter Edition でのみ利用できます。

## Hyper-V は Windows 2003 で実行できますか?
{: #can-hyper-v-run-on-windows-2003}

Hyper-V は Windows 2008 用に開発されたものであり、Windows 2003 では実行できません。

## 仮想マシンにインストールできるオペレーティング・システムは何ですか?
{: #what-operating-systems-can-be-installed-on-a-virtual-machine}

{{site.data.keyword.BluSoftlayer_notm}} では、Hyper-V で以下のオペレーティング・システムを利用できます。

* すべてのバージョンの Windows 2003 サーバーと 2008 サーバー
* CentOS、Fedora、および Ubuntu Linux ディストリビューション

## 1 つのサーバーで実行できる仮想マシンはいくつですか?
{: #how-many-virtual-machines-can-a-server-run-}

1 つのサーバーで実行できる仮想マシンの数は、サーバーのプロセッサー、RAM、およびハード・ディスク・スペースによって異なります。

## 各仮想マシンの RAM をカスタマイズできますか?
{: #is-the-ram-customizable-on-each-virtual-machine-}

はい。 メモリーを含め、Hyper-V では各仮想マシンのシステム・リソースをカスタマイズできます。

## 仮想マシンに必要な RAM の量はどのくらいですか?
{: #how-much-ram-does-a-virtual-machine-need-}

RAM の必要量は、仮想マシンの要件によって異なります。 ゲスト・オペレーティング・システムのシステム要件を確認してください。 仮想マシンに提供されるメモリーの量は、いつでも変更できます。

## 1 台の仮想マシンで複数のプロセッサーを利用できますか?
{: #can-a-virtual-machine-access-to-more-than-one-processor-}

Windows 仮想マシンの場合は、1 台の仮想マシンに複数のプロセッサーを割り当てることができます。 一方、Linux 仮想マシンは、シングル・プロセッサーに制限されます。

## 仮想マシンの作成後にハード・ディスクのサイズを変更することはできますか?
{: #can-hard-disk-sizes-change-after-a-virtual-machine-is-created-}

はい。

## ライセンスは仮想オペレーティング・システムごとに必要ですか?
{: #does-each-virtual-operating-system-need-to-have-a-license-}

Windows 2003 と 2008 の仮想マシンのライセンスは、{{site.data.keyword.BluSoftlayer_notm}} から提供されます。 Linux 仮想マシンのライセンスは無料で提供されるので、アクションは不要です。

## 仮想マシンは、プライベート・ネットワークにアクセスできますか?
{: #will-the-virtual-machines-have-access-to-the-private-network-}

はい。 仮想マシンは、パブリック・ネットワークとプライベート・ネットワークの両方に接続できます。

## 仮想マシンからプライベート・ネットワークにアクセスできると、どのような利点がありますか?
{: #what-advantages-are-there-to-providing-private-network-access-to-virtual-machines-}

仮想マシンからプライベート・ネットワークにアクセスできると、仮想マシンどうしの相互通信が可能になります。 また、プライベート・ネットワークにアクセスできれば、仮想マシンは、他の内部システム (NAS および iSCSIなど) や、{{site.data.keyword.BluSoftlayer_notm}} で使用している他のサーバーとも通信できるようになります。

## サーバーに設定されていたセカンダリー IP ブロックは、仮想マシンに使用できますか?
{: #can-virtual-machines-use-the-secondary-ip-block-that-came-with-the-server-}

いいえ。サーバーと一緒に提供されたセカンダリー IP は、仮想マシンではなく、特定の物理サーバーで使用できるようにルーティングされます。 仮想マシンをネットワークに接続するには、ポータブル IP ブロックが必要です。

## ポータブル IP アドレスとは何ですか?
{: #what-are-portable-ip-addresses-}

ポータブル IP アドレスについては、[サブネットおよび IP の概説](/docs/infrastructure/subnets?topic=subnets-getting-started-with-subnets-and-ips)を参照してください。

## Hyper-V を注文するにはどうすればよいですか?
{: #how-do-i-order-hyper-v-}

Hyper-V を既存のサーバーにインストールするには、OS 再ロードを要求し、オペレーティング・システムを「Windows 2008 Server Datacenter Edition with Hyper-V」に変更します。 Hyper-V を搭載した新規サーバーをプロビジョンする場合は、新規サーバーを注文し、「Windows 2008 Server Datacenter Edition with Hyper-V」をオペレーティング・システムとして選択します。

## Hyper-V は Windows 2008 Server Datacenter Edition にプリインストールされていますか?
{: #is-hyper-v-preinstalled-with-windows-2008-server-datacenter-edition-}

Hyper-V は Windows 2008 サーバーにプリインストールされていません。 Hyper-V を Windows 2008 サーバーにインストールする場合は、OS 再ロードを要求して、「Windows 2008 Server Datacenter Edition with Hyper-V」をオペレーティング・システムとして選択する必要があります。

## Hyper-V の OS 再ロードは終わりました。 次は何をすればよいですか?
{: #the-os-reload-for-hyper-v-is-done-what-s-next-}

Hyper-V をセットアップする必要があります。 Hyper-V のセットアップについて詳しくは、[Hyper-V のセットアップ](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-hyper-v)を参照してください。

## 仮想マシンが始動しません。 「マシンのリモート・システムを初期化できません: エラー:「不明なエラー」(Cannot initialize machine remoting system: Error:'Unspecified error')」になります。どうすればこの問題を解決できますか?
{: #a-virtual-machine-does-not-start-cannot-initialize-machine-remoting-system-error-unspecified-error-how-do-i-resolve-this-issue-}

この問題を解決するには、Hyper-V サービスを停止して、再始動してください。 この問題は、通常、サーバーの起動中に Hyper-V が開始しようとすることで発生します。 起動の完了後にサービスを再始動すると仮想マシンが再稼働します。
