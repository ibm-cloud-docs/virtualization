---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-14"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Virtuozzo コンテナーのバックアップ
{: #backing-up-a-virtuozzo-container}

1. 両方のハードウェア・ノードが PIM でセットアップされていることを確認します。
2. コンテナーが配置されているハードウェア・ノードおよびインフラストラクチャーに移動します。
3. 適切なコンテナーを選択して、**「Back up」**をクリックします。
4. このデータをバックアップする理由を説明する適切なコメントを追加します。
5. バックアップ・タイプを選択します。
   * Use Hardware Node Settings
   * Full (この例で使用します)
   * Incremental
   * Differential
6. 圧縮レベルを選択します。**注:** 圧縮レベルが高いほど、ディスク・スペースを節約できますが、より多くの CPU リソースが使用されます。
   * Use Hardware Node Settings
   * なし
   * Normal (choose normal minimize server load)
   * High
   * Maximum
7. バックアップ・ファイルを格納するハードウェア・ノードを選択します。 複数のハードウェア・ノードがある場合は、同じサーバーではなくこれらのノードにバックアップを保存することをお勧めします。
  * 他のオプションは管理者によって選択されます。
8. バックアップを実行します。 バックアップが完了します。 バックアップの詳細を確認するには、**「Hardware Node」、「Container」、「Backups」**タブに移動して、当該バックアップを選択します。
