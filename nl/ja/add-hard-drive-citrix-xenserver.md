---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-14"

subcollection: virtualization

keywords: Citrix XenServer
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Citrix XenServer へのハード・ディスク・ドライブの追加
{: #adding-a-hard-disk-drive-to-citrix-xenserver}

XenServer での新規ハード・ディスク・ドライブの追加は、従来の Linux でのプロセスとは異なります。 XenServer の場合は、「ストレージ・リポジトリー」と呼ばれるコンテナーを作成して、特定のストレージ・ターゲット (ハード・ディスクなど) を定義する必要があります。 このコンテナーには、VM の仮想ディスク・イメージ (VDI) が格納されます。 VDI は、VM のハード・ディスクとして機能する抽象化されたストレージ・スペースです。

Xen ストレージ・リポジトリーは、リモート・ストレージ用の iSCSI、NFS、SAS、およびファイバー・チャネルに加えて、ローカル接続された IDE、SATA、SCSI、および SAS のドライブをサポートしています。

## XenServer でのストレージ・リポジトリーの作成
{: #creating-a-storage-repository-in-a-xenserver}

1. XenServer に root として SSH で接続します。

2. 次のコマンドを使用して新しいデバイスのディスク ID を確認します。

       # cat /proc/partitions

  このコマンドを実行すると、すべての HDD とパーティションが一覧表示されます。 新しいローカル・ディスクを確認します。このディスクはおそらく「sdx」(通常は sdb) または「/cciss/c0d1p0」です。 次のコマンドを使用して、サーバーにあるすべてのパーティションと HDD のディスク ID を一覧表示します。

       # ll /dev/disk/by-id

  「sdx」または「cciss/c0d1」というディスクのディスク ID を確認します。 「scsi-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx」または「cciss-xxxxxxxxxxxxxxxxxxxxxxxxxx」という形式が必要です。

3. 次のコマンドを使用して、XenServer 内の「host-uuid」を確認します。

        #xe host-list

  uuid (RO) が必要な「host-uuid」です。

4. ストレージ・リポジトリー (SR) を作成します。

  > **注:** [sr-create ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://support.citrix.com/article/CTX121313){: new_window} コマンドは、新しいハード・ディスク・ドライブを追加するためのものです。 新しいハード・ディスク・ドライブの作成は破壊的なプロセスであり、このプロセスによってそのドライブがパーティション化およびフォーマットされて、そのドライブ上のデータはすべて失われます。 既存のデータが配置されているドライブを再導入するには、[sr-introduce ![外部リンク・アイコン](../../icons/launch-glyph.svg "外部リンク・アイコン")](https://support.citrix.com/article/CTX121896){: new_window} を使用します。

  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`<br/>
  \- または -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/disk/by-id/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`<br/>
  \- または -<br/>
  `# xe sr-create content-type=user device-config:device=/dev/ host-uuid= name-label=”Local Storage 2” shared=false type=lvm`

## XenCenter でのストレージ・リポジトリーの検証
{: #verifying-the-storage-repository-in-xencenter}

XenCenter からストレージ・リポジトリーを検証するには、次の手順を実行します。

1. XenCenter に接続します。

2. **「Storage」**に移動して、すべてのストレージ・リポジトリーの詳細を確認します。 リストに「Local Storage2」というストレージが表示されて、ディスクが追加されたことがわかります。 これで、このストレージ上で VDI の作成を開始できます。
