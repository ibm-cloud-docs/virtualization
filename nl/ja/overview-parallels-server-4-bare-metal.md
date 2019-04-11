---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Parallels Server Bare Metal 4 サーバーの概要
{: #overview-of-parallels-server-4-bare-metal}

Parallels Server Bare Metal は、Virtuozzo のソフトウェア仮想化に加えてハードウェア仮想化も提供する仮想化ソリューションであり、仮想マシンとコンテナーの両方のテクノロジーを利用できます*。
{:shortdesc}

**コマンド・ライン・インターフェース**

一般的な Virtuozzo コマンドのほかに、`pctl`、`pmigrate`、`pstat`、`prl_disk_tool` のコマンドも使用できます。

**マイグレーション**

Virtuozzo Containers を仮想マシンにマイグレーションしたり、物理サーバーを Virtuozzo Containers にマイグレーションしたり、物理サーバーを仮想マシンにマイグレーションしたりして、サービスを統合できます。 また、この機能により、ベアメタル・サーバー間でコンテナーや VM をマイグレーションしたり、別の仮想化環境で作成された VM を変換したり (V2V)、テンプレートから Windows VM を複製またはデプロイする際に Windows SID を変更したりできます。

**IP アドレスの使用方法と VLAN**

VM 内の Parallels Tools を使用して、物理サーバーの IP を VM に割り当てることができます。 また PSBM により、VM 内で仮想スイッチや VLAN を作成し、VM 内ネットワーク・トラフィックの保護を強化できます。

**コマンドの実行**

Virtuozzo Containers 内に加えて VM 内でも、物理サーバーの未加工のコマンドを直接実行できるようになりました (Parallels Tools がその VM 内にインストール済みの場合)。これには、ユーザー・パスワードのリセットも含まれます。

**プロセスのアカウンティング**

Parallels Server Bare Metal を使用して、VM に割り振られているリソースの優先順位 (CPU、ディスク入出力の優先順位) を即時に上げ下げできます。

**バックアップ**

Parallels Server Bare Metal は、ローカル・ベアメタル・サーバーまたはリモート・ベアメタル・サーバーに VM やコンテナーをバックアップしてリストアする機能を備えています。これには完全バックアップと増分バックアップが含まれます。

**ネットワーク・アカウンティング**

Parallels Server Bare Metal を使用して、現在や過去のネットワーク・スループットに基づいて VM やコンテナーを簡単に表示したり見つけたりすることができます。

**ネットワーク**

Parallels Server Bare Metal はポータブル IP アドレスを使用しますが、Virtuozzo はポータブル IP アドレスまたは静的 IP アドレスのいずれかを (構成に応じて) 使用します。

\* 注文フォームに特に記載しない限り、{{site.data.keyword.BluSoftlayer_full}} から提供されるライセンスは、Parallels Server 4 Bare Metal のハードウェア仮想マシンのライセンスのみです。
_VM_ = 仮想マシン。 _V_ = VPS またはコンテナー。
