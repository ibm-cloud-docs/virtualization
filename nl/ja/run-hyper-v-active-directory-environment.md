---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-10"

subcollection: virtualization

keywords: hypervisor
---
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# Active Directory 環境での Hyper-V の実行
{: #running-hyper-v-in-an-active-directory-environment}

<!--Running Hyper-V in an Active Directory environment is the best implementation of Hyper-V. Microsoft has truly shined with the ability to remotely manage a server.You can remotely manage the Hyper-V server allows for it to be installed on a Core installation of Windows freeing up those valuable resources from the system for use within the VM’s. When this is combined with an Active Directory Domain Controller
You manage all your Hyper-V servers from a single Hyper-V Manager that runs on any 2008 or Vista computer that is connected to the Domain.--> <!--Vista?? MS doesn't support Vista.-->

## 始める前に
{: #before-you-begin-running-hyper-v-in-an-active-directory-environment}

Active Directory 環境で Hyper-V を実行する前に、以下を用意しておく必要があります。

* Hyper-V を実行する 64 ビット Windows 2008 Datacenter サーバー (OS のフル・インストールまたはコア・インストール)。
* Hyper-V の管理に使用するドメイン上のグローバル・グループ。
* 作業に使用しているコンピューターから Hyper-V サーバーを変更することを可能にするドメイン管理者のアクセス権限。

## Hyper-V サーバーの構成
{: #configuring-the-hyper-v-server}

1. Hyper-V サーバーへの管理接続を開きます。

2. **Hyper-V** グループを **Distributed COM Users** グループに追加します。

3. **Hyper-V** グループを **CIMV2 および Virtualization** 名前空間に追加します。

4. **Hyper-V** グループを Hyper-V サーバー上の Hyper-V の承認ストアに追加します。

5. **Hyper-V** グループに Hyper-V サーバー上の Hyper-V ディレクトリーへの許可を付与します。

## リモート管理接続をセットアップする
{: #setting-up-remote-management-connection}

必ず、ドメイン管理者特権でドメインのコンピューターにログインしてください。

1. **「コントロール パネル」>「管理ツール」>「コンピューターの管理」**に移動します。

2. 「操作」メニューで**「別のコンピューターへ接続」**を選択します。

3. サーバー名または IP を入力し、**「OK」**をクリックします。

## Hyper-V サーバーに Distributed COM Users グループを追加する
{: #adding-distributed-com-users-group-to-the-hyper-v-server}

まずは、Hyper-V サーバーの Distributed COM Users グループに Hyper-V グループを追加する必要があります。

1. **「システム ツール」>「ローカル ユーザーとグループ」>「グループ」>「Distributed COM Users」>「グループに追加」**に移動します。

2. **「追加」**をクリックし、Hyper-V グループのグループ名を入力して、**「OK」**をクリックします。

## CIMV2 名前空間および Virtualization 名前空間についてのサーバーへのリモート・アクセス許可を付与する
{: #granting-remote-access-to-the-server-for-cimv2-and-virtualization-namespaces}

Virtualization および CIMV2 についてのサーバーへのリモート・アクセス許可を更新できます。

1. 「コンピューターの管理」ウィンドウで**「サービスとアプリケーション」>「WMI コントロール」**を選択します。

2. 右クリックし、**「プロパティ」>「セキュリティ」>「Root」>「CIMV2」>「セキュリティ」**に移動します。

3. Hyper-V グループを追加してから選択し、**「詳細設定」**をクリックします。

4. 新しいグループが選択されていることを確認して、**「編集」**をクリックします。

5. **「適用先:」**を**「この名前空間と副名前空間」**に変更します。

6. **「アカウントの有効化」と「リモートの有効化」**について**「許可」**が選択されていることを確認します。

7. **「これらのアクセス許可を、このコンテナーの中にあるオブジェクトやコンテナーにのみ適用する」**を選択し、**「OK」**をクリックします。

8. Virtualization について上記の手順を繰り返します。

## 承認ストアを更新する
{: #updating-authorization-store}

Hyper-V の承認ストアは、ドメイン・グループを Hyper-V に提供する最後のコンポーネントです。

1. 「ファイル名を指定して実行」メニューまたはコマンド・プロンプトから `azman.msc` コマンドを実行して、承認マネージャーを開きます。

2. 「操作」メニューから**「承認ストアを開く」**を選択します。

3. **「XML」**が選択されていることを確認します。 ここでは、Hyper-V サーバー上の `InitalStore.xml` にリモートからアクセスする必要があります。 次のパスを使用します。

`&#xa5;HOSTNAME&#xa5;c$&#xa5;ProgramData&#xa5;Microsoft&#xa5;Windows&#xa5;Hyper-V&#xa5;InitialStore.xml`

4. **「Hyper-V サービス」>「役割の割り当て」>「管理者」**に移動します。

5. **「操作」**メニューで**「ユーザーとグループの割り当て」>「Windows と Active Directory を使用」**を選択します。

6. Hyper-V グループを追加します。

## フォルダーのアクセス許可の付与
{: #granting-folder-permissions}

Hyper-V グループが Hyper-V をリモートから管理するための完全なアクセス許可を持つようになったので、次は `C:&#xa5;Users&#xa5;Public&#xa5;Documents&#xa5;Hyper-V` フォルダーへの書き込み許可を付与する必要があります。

1. 「マイ コンピューター」を開き、次のアドレスに移動します。

`&#xa5;HOSTNAME&#xa5;c$&#xa5;Users&#xa5;Public&#xa5;Documents`

2. **「Hyper-V」>「プロパティ」>「セキュリティ」**に移動します。

3. Hyper-V グループを追加し、そのディレクトリーのファイルの読み取り、書き込み、および実行ができるようにします。 一般的に、**「フル コントロール」**を割り当てると簡単です。

## 構成を終える
{: #finalizing-configuration}

すべての構成変更が完了しました。 構成を終えるには、Hyper-V サーバーを再始動する必要があります。 サーバーがオンラインに戻ったら、ローカル Hyper-V マネージャーからサーバーに接続します。 これで、すべての VM と Hyper-V サービスを完全に管理できるようになりました。
