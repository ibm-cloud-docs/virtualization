---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# UBC (ユーザー Bean カウンター) 値について
{: #understanding-ubc-user-beancounter-values}

UBC がわからない場合は SLM モードを使用してください。 SLM のほうが簡単ですが、制御タイプは同じです。

|名前|タイプ|説明|
|---|---|---|
|*メイン・パラメーター*|||
|`numproc`|*システム*|プロセスとスレッドの数|
|`numtcpsock`|*システム*|TCP ソケットの数|
|`numothersock`|*システム*|TCP 以外のソケットの数|
|`vmguardpages`|*システム*|メモリー割り振り保証|
|`cpuunits`|*CPU*|CPU 能力|
|`diskspace`|*ディスク*|ディスク・スペース割り当て量|
|`rate`|*ネットワーク*|ネットワーク帯域幅|
|`ratebound`|*ネットワーク*|ネットワーク帯域幅が制限されているかどうか|
|*その他のパラメーター*|||
|`kmemsize`|*システム*|このコンテナー内のプロセスに割り振られたスワップ不可カーネル・メモリーのサイズ|
|`tcpsndbuf`|*システム*|TCP 送信バッファーの合計サイズ|
|`tcprcvbuf`|*システム*|TCP 受信バッファーの合計サイズ|
|`othersockbuf`|*システム*|UNIX ドメインのソケット・バッファーと UDP などのデータグラム・プロトコルの送信バッファーの合計サイズ|
|`dgramrcvbuf`|*システム*|UDP などのデータグラム・プロトコルの受信バッファー|
|`oomguardpages`|*システム*|メモリーがオーバー・ブッキング状態になった場合に保証されるメモリー量 (メモリー不足のための強制終了の保証)|
|`privvmpages`|*システム*|メモリー割り振り制限|
|`lockedpages`|*システム*|プロセス・ページのスワップアウトを許可しない (ページは [mlock(2) ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](http://linux.die.net/man/2/mlock) によってロックされる)|
|`shmpages`|*システム*|ページ内の共有メモリー (プロセス間通信 (IPC)、共有匿名マッピング、tmpfs オブジェクト) の合計サイズ|
|`physpages`|*システム*|プロセスによって使用される RAM ページの総数|
|`numfile`|*システム*|開かれているファイルの数|
|`numflock`|*システム*|ファイル・ロックの数|
|`numpty`|*システム*|疑似端末の数|
|`numsiginfo`|*システム*|siginfo 構造体の数|
|`dcachesize`|*システム*|メモリー内でロックされている dentry 構造体と inode 構造体の合計サイズ|
|`numiptent`|*システム*|NETFILTER (IP パケット・フィルタリング) 項目の数|
|`cpulimit`|*CPU*|CPU 使用量制限|
|`diskinodes`|*ディスク*|ディスク inode (ファイル・システム・オブジェクト) 割り当て量|
|`quotatime`|*ディスク*|ディスク割り当て量猶予期間|
|`quotaugidlimit`|*ディスク*|uid アカウンティング項目と gid アカウンティング項目の数の制限|
<caption>表 1. SLM パラメーターの説明</caption>

サーバー上の PIM 内で
**「管理」>「サポート」>「ダウンロード」>「UBC リソースの管理 管理者ガイド (Management of UBC Resources Administrator's Guide)」**に移動して、VzLinuxUBCMgmt.pdf を利用したり HTML 形式で表示したりできます。

UBC について詳しくは、[OpenVZ Virtuozzo Containers, Category: UBC ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](http://wiki.openvz.org/Category:UBC) を参照してください。
**注:** OpenVZ のすべてが Virtuozzo と同じというわけではありません。
