---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-05"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Veeam Backup and Replication アプリケーションの更新
{: #updating-veeam-backup-and-replication-application}

## 概説
{: #overview}

Veeam Backup and Replication 製品の新しい更新プログラムが提供されたら、以下の手順を使用して、サーバー上で実行している同製品のアプリケーションを更新します。

### 前提条件
{: #prerequisites-updating-veeam-backup-and-replication-application}

* 無料 Veeam アカウント。 [Veeam Registration ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.veeam.com/signin.html) で申請できます。
* Internet Explorer、Chrome、Firefox、または Safari。

### 更新プログラムのチェック
{: #checking-for-updates}

Veeam Backup and Replication アプリケーションには更新プログラム・チェッカーが組み込まれています。 更新プログラムについて通知されるようにするには、左上のメニュー・アイコンに移動して、**「General Options」** > **「Notifications」**をクリックします。 **「Check for product and hypervisor updates periodically」**を選択しておきます。

### Veeam の更新
{: #updating-veeam}

更新を開始するには、以下の手順に従います。
1. **「Backup Infrastructure」**ビューを開きます。
2. インベントリー・ペインで、**「Managed servers node」** > **「Missing Updates」**に移動します。
3. 更新プログラム・リンクをクリックします。

更新プログラムを開始する前のいくつかの注意点:

* すべての既存ジョブについて現在実行中のものが完了していることを確認してください。 失敗したジョブがある場合は、そのジョブを再実行してください。
* リストア・セッション、Instant VM Recovery セッション、SureBackup ジョブを含め、ジョブが実行されていないことを確認してください。 実行中のジョブを停止しないことをお勧めします。
* 定期ジョブやバックアップ・コピー・ジョブがアップグレード中に開始されないように一時的に無効にしてください。

### 次のステップ
{: #next-steps-updating-veeam-backup-and-replication-application}

これで、ダウンロードされた更新プログラムを解凍して、Veeam 更新プログラム・インストーラーをダブルクリックできる状態になりました。 ローカル Veeam サーバーと、Veeam エージェントが存在するハイパーバイザーの両方が、更新プログラムの対象になります。 プロセスが完了したら、Veeam Backup and Replication を開始し、バックアップ・ジョブを再度有効にしてください。
