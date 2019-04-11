---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 讓容器可以透過 PIM 存取專用網路
{: #giving-containers-access-to-the-private-network-through-pim}

您可以讓容器存取專用網路。但是，您必須瞭解讓客戶可以存取您內部基礎架構的影響，因為這樣做可能導致安全問題。

1. 移至您要授與專用網路存取權的容器。
2. 按一下**網路**標籤，然後按一下**配置**。
3. 您現在可以設定「橋接網路區段」：
  * **橋接網路**：選取勾選框。
  * **連接目標**：不需要提供對 {{site.data.keyword.cloud}} 專用網路的存取權。
  * **藉由 DHCP 取得 IP 位址**：清除這個勾選框。
  * **IP 位址/子網路遮罩**：從專用位址的儲存區中選擇「內部」IP 位址。
  * 按一下**提交**

您現在需要從右上方的鏈結重新整理頁面，表示您已完成。
