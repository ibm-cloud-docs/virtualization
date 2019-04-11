---



copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# FAQ: Virtuozzo
{: #faqs-virtuozzo}

## Virtuozzo には静的 IP アドレスとポータブル IP アドレスのどちらが必要ですか?
{: #do-i-need-static-or-portable-ip-addresses-for-virtuozzo-}

* 静的 IP アドレス: (VLAN ごとに、または合計で) ハードウェア・ノードが 1 つしかない場合は静的 IP アドレスを使用してください。
* ポータブル IP アドレス:
    * Routed to VLAN (VLAN にルーティング): 同じ VLAN 内に複数のホスト・ノードがある場合は、この方式を使用してください。 コンテナーの IP を変更せずに、コンテナーを同じ VLAN 内の別のハードウェア・ノードにマイグレーションする場合も、この方式を使用してください。
    * Secondary on VLAN (VLAN 上のセカンダリー): この方式も使用できますが、セカンダリー・ゲートウェイは不要です。 ホスト ID、ゲートウェイ、およびブロードキャストのために、3 つの IP アドレスが失われます。

IP 1 つあたりのコストは、ルーティングされるタイプによって異なります。 IP アドレスを注文するサーバーを選択すると、ポータルに価格が表示されます。 ルーティングは、サポート・チケットを開いていつでも変更できます。
{:tip}

## ダウン時間なしで、Virtuozzo 3 Container を Virtuozzo Container 4 のハードウェア・ノードにマイグレーションできますか?
{: #can-i-migrate-a-virtuozzo-3-container-to-a-virtuozzo-container-4-hardware-node-with-no-downtime-}

**Linux:**

はい。 デフォルトでは、Virtuozzo Containers for Linux 4 は、互換モードで実行されます。 Virtuozzo Containers が互換モードで実行されていない場合は、SSH を使用してハードウェア・ノードで次のコマンドを実行すれば互換モードを開始できます。

`/usr/sbin/vzagent_compat_ctl start`

**Windows (非サポート):**

Virtuozzo Containers 4 Windows では、オンライン・オプションを渡せないので、移行中にダウン時間 (20 秒から最大 5 分) が生じます。

Virtuozzo 3.0 for Windows を使用している場合は、ハードウェア・ノードを少なくとも Virtuozzo 3.5.1 for Windows にアップグレードする必要があります。

Virtuozzo Containers 4 for Windows は、デフォルトでは互換モードで実行されます。

3.5.1 のバックアップを Virtuozzo Containers 4 のハードウェア・ノードにリストアすることもできます。そのためには、フルバックアップを宛先ノードにコピーし、`vzbackupsync.exe` を実行して通常どおりにバックアップをリストアします。

新規ハードウェア・ノードに同じテンプレートがインストールされていること確認してください。
{:tip}

## コンテナーは、他のコンテナーやホストから切り離されていますか?
{: #are-containers-isolated-from-each-other-and-the-host-}

コンテナーは、LKM のロードや自環境外での `chroot(“../../../”)` の実行を制限されます。また、IPC リソース (セマフォー・セットおよび共有メモリー・セグメント) は共有されません。

Virtuozzo のリソース管理やプロセス分離についてセキュリティー上の問題を失敗する必要はありません。 ブリッジされたネットワーク構成によって、(同じハードウェア・ノード上の) 同じ VLAN 内のすべてのコンテナーが互いのトラフィックを確認できます。 ルーティング・モードを使用することを強くお勧めします。

## Virtuozzo で RedHat と SUSE の OS テンプレートをキャッシュできないのはなぜですか?
{: #why-can-t-i-cache-redhat-and-suse-os-templates-in-virtuozzo-}

Vitruozzo を使用する場合、RedHat と SUSE の OS テンプレートをキャッシュすると、問題が生じることがあります。 これらの OS テンプレートには追加のライセンスが必要ですが、{{site.data.keyword.cloud_notm}} からは提供されません。 以下のいずれかのエラーを受け取った場合は、対応するベンダーで独自にアカウントを取得し、/etc/vztt/url.map (Virtuozzo 3 では /etc/vztt/vztt.conf) 内に対応する変数を構成する必要があります。

||||
|---|---|---|
|TIMESTAMP|ここをクリックして、操作の詳細をオープン/クローズしてください。OS テンプレート・キャッシュの更新 (Click here to open/close the operation details.Update OS Template Cache)|**失敗**|
|TIMESTAMP|環境「virtuozzo00.softlayer.local」およびパッケージ .redhat-as4-x86_64 の更新操作が開始されました (Operation update with the Env(s) "virtuozzo00.softlayer.local" and package(s) .redhat-as4-x86_64 is started)||
|TIMESTAMP|環境「virtuozzo00.softlayer.local」の更新操作がエラーで終了しました。パッケージを更新できません。実行が失敗しました。エラー: URL $RH_SERVER/download/mirrors/redhat-as4 のコンテンツの変数が未定義です。この変数は /etc/vztt/url.map で定義できます。(Operation update with the Env(s) "virtuozzo00.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $RH_SERVER/download/mirrors/redhat-as4 contents undefined variable You can define this variable in /etc/vztt/url.map.)|**失敗**|
|TIMESTAMP|ここをクリックして、操作の詳細をオープン/クローズしてください。OS テンプレート・キャッシュの更新 (Click here to open/close the operation details.Update OS Template Cache)|**失敗**|
|TIMESTAMP|環境「virtuozzo00.softlayer.local」およびパッケージ .redhat-el5-x86_64 の更新操作が開始されました (Operation update with the Env(s) "virtuozzo00.softlayer.local" and package(s) .redhat-el5-x86_64 is started)||
|TIMESTAMP|環境「virtuozzo00.softlayer.local」の更新操作がエラーで終了しました。パッケージを更新できません。実行が失敗しました。エラー: URL $RH_SERVER/download/mirrors/redhat-el5 のコンテンツの変数が未定義です。この変数は /etc/vztt/url.map で定義できます。(Operation update with the Env(s) "virtuozzo00.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $RH_SERVER/download/mirrors/redhat-el5 contents undefined variable You can define this variable in /etc/vztt/url.map.) 詳しくは、「Virtuozzo インストール・ガイド」を参照してください。(See Virtuozzo Installation Guide for more details.)|**失敗**|
|TIMESTAMP|ここをクリックして、操作の詳細をオープン/クローズしてください。OS テンプレート・キャッシュの更新 (Click here to open/close the operation details.Update OS Template Cache)|**失敗**|
|TIMESTAMP|環境「virtuozzo00.softlayer.local」およびパッケージ .sles-10-x86_64 の更新操作が開始されました (Operation update with the Env(s) "virtuozzo00.softlayer.local" and package(s) .sles-10-x86_64 is started)||
|TIMESTAMP|環境「virtuozzo00.softlayer.local」の更新操作がエラーで終了しました。パッケージを更新できません。実行が失敗しました。エラー: URL $SLES_SERVER/download/mirrors/suse-es10 のコンテンツの変数が未定義です。この変数は /etc/vztt/url.map で定義できます。(Operation update with the Env(s) "virtuozzo00.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $SLES_SERVER/download/mirrors/suse-es10 contents undefined variable You can define this variable in /etc/vztt/url.map.) 詳しくは、「Virtuozzo インストール・ガイド」を参照してください。(See Virtuozzo Installation Guide for more details.)|**失敗**|
|TIMESTAMP|ここをクリックして、操作の詳細をオープン/クローズしてください。OS テンプレート・キャッシュの更新 (Click here to open/close the operation details.Update OS Template Cache)|**失敗**|
|TIMESTAMP|環境「virtuozzo01.softlayer.local」およびパッケージ .redhat-as4-x86_64 の更新操作が開始されました (Operation update with the Env(s) "virtuozzo01.softlayer.local" and package(s) .redhat-as4-x86_64 is started)||
|TIMESTAMP|環境「virtuozzo01.softlayer.local」の更新操作がエラーで終了しました。パッケージを更新できません。実行が失敗しました。エラー: URL $RH_SERVER/download/mirrors/redhat-as4 のコンテンツの変数が未定義です。この変数は /etc/vztt/url.map で定義できます。(Operation update with the Env(s) "virtuozzo01.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $RH_SERVER/download/mirrors/redhat-as4 contents undefined variable You can define this variable in /etc/vztt/url.map.) 詳しくは、「Virtuozzo インストール・ガイド」を参照してください。(See Virtuozzo Installation Guide for more details.)|**失敗**|
|TIMESTAMP|ここをクリックして、操作の詳細をオープン/クローズしてください。OS テンプレート・キャッシュの更新 (Click here to open/close the operation details.Update OS Template Cache)|**失敗**|
|TIMESTAMP|環境「virtuozzo01.softlayer.local」およびパッケージ .redhat-el5-x86_64 の更新操作が開始されました (Operation update with the Env(s) "virtuozzo01.softlayer.local" and package(s) .redhat-el5-x86_64 is started)||
|TIMESTAMP|環境「virtuozzo01.softlayer.local」の更新操作がエラーで終了しました。パッケージを更新できません。実行が失敗しました。エラー: URL $RH_SERVER/download/mirrors/redhat-el5 のコンテンツの変数が未定義です。この変数は /etc/vztt/url.map で定義できます。(Operation update with the Env(s) "virtuozzo01.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $RH_SERVER/download/mirrors/redhat-el5 contents undefined variable You can define this variable in /etc/vztt/url.map.)|**失敗**|
|TIMESTAMP|ここをクリックして、操作の詳細をオープン/クローズしてください。OS テンプレート・キャッシュの更新 (Click here to open/close the operation details.Update OS Template Cache)          失敗<br/><br/>2008 年 10 月 29 日午後 1 時 16 分 56 秒     環境「virtuozzo01.softlayer.local」およびパッケージ .sles-10-x86_64 の更新操作が開始されました (Oct 29, 2008 01:16:56 PM     Operation update with the Env(s) "virtuozzo01.softlayer.local" and package(s) .sles-10-x86_64 is started)||
|TIMESTAMP|環境「virtuozzo01.softlayer.local」の更新操作がエラーで終了しました。パッケージを更新できません。実行が失敗しました。エラー: URL $SLES_SERVER/download/mirrors/suse-es10 のコンテンツの変数が未定義です。(Operation update with the Env(s) "virtuozzo01.softlayer.local" is finished with errors: Can not update packages: exec failed: Error: URL $SLES_SERVER/download/mirrors/suse-es10 contents undefined variable.)|**失敗**|
|TIMESTAMP|プロセスを完了しています (Completing the process)|**失敗**|

## ServiceVE を ping できるのに、SSH (PIM/PMC) でログインできないのはなぜですか?
{: #why-can-i-ping-my-serviceve-but-cannot-log-in-to-via-ssh-pim-pmc-}

ホスト・ノードに、ポータルにリストされている IP アドレス (パブリック IP 1 つとプライベート IP 1 つ [10.0.0.0/8 範囲内]) 以外の IP アドレスが割り当てられていないことを確認してください。

ホスト・ノードで構成された範囲ファイルがある場合は、それらのファイルを削除してから、ネットワーキングを再始動する必要があります。

    [root@virtuozzo ~]# mv –vi /etc/sysconfig/network-scripts/eth[0-9]-range[0-9]* ~/.sl-orig-configs/

    [root@virtuozzo ~]# /sbin/service network restart


`vzcp` は、サービス VE 内では開始されません。 `vzcp` は、ハードウェア・ノードからサービス VE 内で開始してください。

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp status

    vzcp is stopped

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp start

    Starting vzcpd: [  OK  ]

    Starting vzcp: [  OK  ]

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp status

    vzcp (pid 3775 3774 3771) is running...

    [root@virtuozzo ~]#
