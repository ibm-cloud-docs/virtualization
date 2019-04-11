---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-16"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 何謂 Virtuozzo？
{: #what-is-virtuozzo-}

Virtuozzo 是一種容器型虛擬化解決方案，可讓您使用抽象層來共用硬體。Virtuozzo 會建立用來模擬伺服器的容器（也稱為 VES 或 VPS）。容器通常會有所行動及回應，就像它是獨立式伺服器一樣。此容器會與其他在相同實體伺服器上的容器分開，因此無法存取其他容器檔案、IPC 資源或記憶體。網路可以配置為在多個容器之間共用或隔離。
{:shortdesc}

Virtuozzo 不是 Hypervisor，也不是 Hypervisor 的軟體介面。它運作時會混合核心及專屬核心內的使用者空間。必要時，此配置可讓容器在本質上借用資源，而資源則在主機節點上提供。

可在 [Virtuozzo ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://virtuozzo.com/){: new_window} 找到相關資訊。
