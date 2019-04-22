---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# ハイパーバイザーのセットアップ
{: #setting-up-a-hypervisor}

ハイパーバイザーをセットアップするには、以下の手順を使用します。

1. ユーザー固有の資格情報を使用して、[{{site.data.keyword.slportal_full}} ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://control.softlayer.com/){: new_window} にログインします。
2. **「デバイス」**メニューから**「デバイス・リスト」**を選択して、対象のハイパーバイザーを見つけます。
3. [セキュア VPN ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.softlayer.com/vpn-access){: new_window} 経由でプライベート・ネットワークに接続してハイパーバイザーにアクセスします。

**注:** {{site.data.keyword.cloud}} ハイパーバイザー・プロバイダーとしては、XenServer、VMware、Hyper-V があります。 プロバイダーごとに固有の管理コンソールがあり、アクセス方法はそれぞれ異なります。 管理コンソールにアクセスして操作する方法について詳しくは、以下のリンクを参照してください。

   * [XenServer ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://support.citrix.com/en/products/xenserver){: new_window}
   * [VMware ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://www.vmware.com/support/vsphere-hypervisor.html){: new_window}
   * [Hyper-V ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://technet.microsoft.com/en-us/windowsserver/dd448604){: new_window}

4. 仮想マシン用のポータブル IP を取得します。
    * VM にはポータブル IP アドレスが必要です。 パブリックおよびプライベートのポータブル IP のブロックを、{{site.data.keyword.slportal}}から注文できます。
    * IP アドレスの割り振りについて詳しくは、[サブネットおよび IP の概説](/docs/infrastructure/subnets?topic=subnets-getting-started-subnets-ips#getting-started-subnets-ips)を参照してください。

5. プライベート・ネットワークによる VM のルーティングを設定します。 プライベート・ネットワークを使用して他の VM にルーティングするために、VM には以下を指定する必要があります。
    * ポータブル・プライベート IP
    * 10.0.0.0/8 ネットワーク範囲に関連する静的ルート

VM ルーティング・プロセスについて詳しくは、[仮想マシン・ネットワークのセットアップ
](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-a-virtual-machine-network)を参照してください。 同一 VLAN 上の各マシンが通信できるようになるのは、この VM ネットワークをセットアップした後です。 VLAN が異なるマシン間で通信しなければならない場合は、[VLAN スパンニングを有効にする](/docs/infrastructure/vlans?topic=vlans-vlan-spanning)必要があります。

## ISO へのアクセスと安全な保管
{: #access-and-securely-store-isos}

{{site.data.keyword.cloud_notm}} ネットワーク上の VM は、事前構成された ISO またはカスタム ISO を実行できます。 {{site.data.keyword.cloud_notm}} ネットワーク上の VM は、{{site.data.keyword.cloud_notm}} ユーザー専用として提供されている内部ミラー・サイトにアクセスできます。このサイトには、最もよく使用されるオペレーティング・システムの一般的な構成が用意されています。 特定のオペレーティング・システムの特別なバージョンまたは構成が必要な場合は、OS ベンダーの Web サイトを参照してください。

VM に対してカスタム ISO を実行する場合は、デバイスに障害が起きた場合に取り出せるように、安全な場所に ISO をアップロードしてください。 多くのユーザーは、SSH または RDP を使用してデバイスのローカル・システムにカスタム ISO を保管しています。 また、スペースは、さまざまな機能を備えたストレージ・サービスで提供されています。
