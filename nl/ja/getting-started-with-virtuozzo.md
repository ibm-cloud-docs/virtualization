---



copyright:
  years: 2014, 2018
lastupdated: "2018-01-09"

subcollection: virtualization


---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Virtuozzo の準備
{: #getting-started-with-virtuozzo}

## Parallels Infrastructure Manager (PIM) にアクセスする
{: #accessing-the-parallels-infrastructure-manager-pim-}

1. ベアメタル・サーバーに関する情報を、[{{site.data.keyword.slportal_full}} ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://control.softlayer.com/){: new_window} から取得します。
    1. **「デバイス」**メニューから、**「デバイス・リスト」**を選択します。
    2. サーバーをクリックします。
    3. パブリック IP アドレスとパスワードを見つけます。
5. 任意のブラウザーで、HTTPS を使用してサーバーのパブリック IP アドレスにアクセスします。 PIM で Virtuozzo を管理するときには、Firefox、Opera、または Internet Explorer を使用してください。

JavaScript が必要です。 JavaScript がないと、存在しないページにリダイレクトされ、PIM を使用できない可能性があります。
{:tip}

6. ユーザー名とパスワードを入力して、PIM にログインします。

{{site.data.keyword.slportal}}にリストされているように、ユーザー名は「root」で、パスワードはサーバーの root のパスワードです。
{:tip}

## テンプレートをインストールおよびキャッシュする
{: #installing-and-caching-templates}

1. PIM 画面で、**「管理」**>**「更新」**を選択します。
2. 更新するサーバーに移動します。
3. **「テンプレートの更新 (Template Updates)」**をクリックします。
4. インストールまたは更新する追加のテンプレートを選択します。
5. 更新するサーバーを選択するには、**「テンプレート」**>**「OS テンプレート」**をクリックします。
6. サーバーとキャッシュで使用する OS テンプレートを選択します。 通常は、始めにすべてのテンプレートをキャッシュする必要はありません。 使用するテンプレートのみをキャッシュしてください。 こうしたテンプレートによって、`/vz` パーティションのかなりの量のスペースが消費されます。
7. ノードが 1 つしかない場合は、**「次のキャッシュ (Next Cache)」**をクリックします。
8. テンプレート RPM がすべてダウンロードされ、インストールされるまで待ちます。 **「詳細」**をクリックして、キャッシングのステータスを表示します。

## コンテナーのネットワーク範囲をセットアップする
{: #setting-up-network-range-for-containers}

サーバーでテンプレートのダウンロードとキャッシュが行われている間、コンテナーのネットワーク範囲をセットアップできます。

1. **「選択」**>**「ネットワーク」**をクリックします。
2. **「新しい IP 範囲」**をクリックします。
3. コンテナー用にさらに IP アドレスを追加します。 仮想サーバー用に追加の IP アドレスを購入していない場合は、[{{site.data.keyword.slportal}} ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://control.softlayer.com/){: new_window} に移動します。 **「ネットワーク」**>**「IP 管理」**>**「VLAN」**>**「IP の注文 (Order IP)」**をクリックします。
4. ハードウェア・ノードに移動して、**「コンテナ」**>**「新しいコンテナ」**をクリックします。
5. 要件に合わせて情報を入力します。 この例では、slm.512MB をデフォルトとして使用します。
6. 完全修飾ドメイン名としてホスト名を入力し、DNS サーバーを追加し、必要に応じて検索ドメインを追加します。
7. リソース・パラメーターを確認します。

パラメーターを追加しすぎて、サーバーがリソース不足になり、コンテナーのデータ処理能力、ディスク・スペース、メモリーが制限されないよう注意してください。 サーバーがリソース不足になると、処理速度が低下したりプロセスが予期せず停止したりするおそれがあります。 これらの設定について不明な点がある場合は、サーバー管理者または[サード・パーティー管理会社 ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://developer.ibm.com/bluemix/){: new_window} にお問い合わせください。
{:tip}

8. **「確認 (Validate)」**をクリックしてから、**「次へ」**をクリックします。 確認により、矛盾した設定がないことが確かめられます。
9. インストールするアプリケーションを選択して、**「次へ」**をクリックします。
10. 構成を確認して、**「作成」**をクリックします。 これでコンテナーが作成されます。
11. 次は、**「コンテナー」**に戻り、新規コンテナーを表示して、(前に追加した IP から) 自動的に割り当てられた IP アドレスを確認できます。
12. サーバーにログインして使用できるようになりました。

        myname/myserver[0]~$ ssh 1.234.123.4
        Warning: Permanently added '1.234.123.4' (DSA) to the list of known hosts.
        root@1.234.123.4's password:
        [root@test01 ~]# ip a s
        1: lo: mtu 16436 qdisc noqueue
            link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
            inet 127.0.0.1/8 scope host lo
            inet6 ::1/128 scope host
              valid_lft forever preferred_lft forever
        3: venet0: mtu 1500 qdisc noqueue
            link/void
            inet 127.0.0.1/32 scope host venet0
            inet 1.234.123.4/32 brd 1.234.123.4 scope global venet0:0
        [root@test01 ~]# ps auxww
        USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
        root         1  0.0  0.1  10328   744 ?        Ss   13:23   0:00 init [3]
        root     20419  0.0  0.1  12580   632 ?        Sroot     21726  0.0  0.2  60532  1220 ?        Ss   13:23   0:00 /usr/sbin/sshd
        root     21737  0.0  0.1  22512   892 ?        Ss   13:23   0:00 xinetd -stayalive -pidfile /var/run/xinetd.pid
        root     21756  0.0  0.3  62732  2052 ?        Ss   13:23   0:00 sendmail: accepting connections
        smmsp    21764  0.0  0.3  57608  1752 ?        Ss   13:23   0:00 sendmail: Queue runner@01:00:00 for /var/spool/clientmqueue
        root     21776  0.0  2.0 283796 10728 ?        Ss   13:23   0:00 /usr/sbin/httpd
        root     21786  0.0  0.2  20832  1144 ?        Ss   13:23   0:00 crond
        root     21796  0.0  0.1  46648   800 ?        Ss   13:23   0:00 /usr/sbin/saslauthd -m /var/run/saslauthd -a pam -n 2
        apache   21797  0.0  1.1 283932  5788 ?        S    13:23   0:00 /usr/sbin/httpd
        root     21798  0.0  0.1  46648   544 ?        S    13:23   0:00 /usr/sbin/saslauthd -m /var/run/saslauthd -a pam -n 2
        root     23573  0.0  0.6  85876  3240 ?        Rs   13:45   0:00 sshd: root@pts/0
        root     23594  0.0  0.3  12044  1604 pts/0    Ss   13:45   0:00 -bash
        root     23626  0.0  0.1  10700   952 pts/0    R+   13:45   0:00 ps auxww

        [root@test01 ~]# df -h
        Filesystem            Size  Used Avail Use% Mounted on
        vzfs                  1.0 G   64 M  961 M   7% /
        none                 1004 M  4.0 K 1004 M   1% /dev
        [root@test01 ~]#
