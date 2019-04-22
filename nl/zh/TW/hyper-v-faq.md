---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# 常見問題：Hyper-V
{: #faqs-hyper-v}

## 何謂 Hyper-V？
{: #what-is-hyper-v-}

Hyper-V 是適用於 Windows 2008 Server Datacenter Edition 的虛擬化系統，可讓單一實體伺服器管理多個虛擬機器，全都執行其自己的作業系統。

## 執行 Hyper-V 的需求為何？
{: #what-are-the-requirements-to-run-hyper-v-}

Hyper-V 必須安裝在 Windows 2008 Server 64 位元 Datacenter Edition 上。所有 {{site.data.keyword.cloud}} 硬體都符合 Hyper-V 的系統需求。Microsoft 建議至少要有 2 GB RAM 用於伺服器。

## 在 2008 的任何其他版本上可以使用 Hyper-V 嗎？
{: #is-hyper-v-available-on-any-other-versions-of-2008-}

{{site.data.keyword.cloud_notm}} 只在 Datacenter Edition 上提供 Hyper-V。

## Hyper-V 可在 Windows 2003 上執行嗎？
{: #can-hyper-v-run-on-windows-2003}

Hyper-V 是針對 Windows 2008 開發的，無法在 Windows 2003 上執行。

## 虛擬機器上可以安裝哪些作業系統？
{: #what-operating-systems-can-be-installed-on-a-virtual-machine}

{{site.data.keyword.cloud_notm}} 在 Hyper-V 上支援下列作業系統：

* Windows 2003 及 2008 Server 的所有版本
* CentOS、Fedora 及 Ubuntu Linux 發行套件

## 一部伺服器可以執行多少個虛擬機器？
{: #how-many-virtual-machines-can-a-server-run-}

一部伺服器可以執行的虛擬機器數目，視伺服器處理器、RAM 及硬碟空間而定。

## 是否可在每一個虛擬機器上自訂 RAM 嗎？
{: #is-the-ram-customizable-on-each-virtual-machine-}

是。您可以使用 Hyper-V 來自訂每一個虛擬機器的系統資源，包括記憶體。

## 虛擬機器需要多少 RAM？
{: #how-much-ram-does-a-virtual-machine-need-}

需要多少 RAM 根據虛擬機器的需求而有所不同。請檢查來賓作業系統的系統需求。提供給虛擬機器的記憶體數量可以隨時變更。

## 虛擬機器可以存取多個處理器嗎？
{: #can-a-virtual-machine-access-to-more-than-one-processor-}

您可以將存取權授與虛擬機器，讓任何 Windows 虛擬機器可以存取多個處理器。不過，Linux 虛擬機器會限制為只能存取單一處理器。

## 在建立虛擬機器之後，硬碟大小是否會變更？
{: #can-hard-disk-sizes-change-after-a-virtual-machine-is-created-}

是。

## 每一個虛擬作業系統都需要有授權嗎？
{: #does-each-virtual-operating-system-need-to-have-a-license-}

Windows 2003 及 2008 虛擬機器是透過 {{site.data.keyword.cloud_notm}} 授權。Linux 虛擬機器是免費授權的，不需要任何動作。

## 虛擬機器是否有權存取專用網路？
{: #will-the-virtual-machines-have-access-to-the-private-network-}

是。虛擬機器可以同時連接至公用及專用網路。

## 將專用網路存取權提供給虛擬機器有哪些優點？
{: #what-advantages-are-there-to-providing-private-network-access-to-virtual-machines-}

將專用網路存取權提供給虛擬機器，可讓虛擬機器彼此通訊。專用網路存取權也容許虛擬機器與其他內部系統（例如 NAS 及 iSCSI）進行通訊，以及與您所擁有之與 {{site.data.keyword.cloud_notm}} 搭配使用的任何其他伺服器進行通訊。

## 虛擬機器是否可以使用伺服器隨附的次要 IP 區塊？
{: #can-virtual-machines-use-the-secondary-ip-block-that-came-with-the-server-}

否。您伺服器隨附的次要 IP 會專門遞送至實體伺服器上的工作，而非虛擬機器上的工作。您需要可攜式 IP 區塊，才能將虛擬機器連接至網路。

## 何謂可攜式 IP 位址？
{: #what-are-portable-ip-addresses-}

如需可攜式 IP 位址的相關資訊，請參閱[開始使用子網路及 IP](/docs/infrastructure/subnets?topic=subnets-getting-started-subnets-ips#getting-started-subnets-ips)。

## 如何訂購 Hyper-V？
{: #how-do-i-order-hyper-v-}

您可以在現有伺服器上安裝 Hyper-V，方法為發出「作業系統重新載入」，並將作業系統變更為「Windows 2008 Server Datacenter Edition（含 Hyper-V）」。若要佈建新伺服器搭配 Hyper-V，請訂購新的伺服器，並選取「Windows 2008 Server Datacenter Edition（含 Hyper-V）」作為作業系統。

## Hyper-V 是否已隨 Windows 2008 Server Datacenter Edition 預先安裝？
{: #is-hyper-v-preinstalled-with-windows-2008-server-datacenter-edition-}

Hyper-V 未預先安裝在 Windows 2008 伺服器上。如果您想要將 Hyper-V 安裝在 Windows 2008 伺服器上，則需要發出「作業系統重新載入」，並選取「Windows 2008 Server Datacenter Edition（含 Hyper-V）」作為作業系統。

## 為 Hyper-V 重新載入作業系統已完成。下一步為何？
{: #the-os-reload-for-hyper-v-is-done-what-s-next-}

需要設定 Hyper-V。如需設定 Hyper-V 的相關資訊，請參閱[設定 Hyper-V](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-hyper-v)。

## 虛擬機器不會啟動。「無法起始設定機器遠端系統：錯誤：未指定的錯誤」如何解決此問題？
{: #a-virtual-machine-does-not-start-cannot-initialize-machine-remoting-system-error-unspecified-error-how-do-i-resolve-this-issue-}

若要解決此問題，請停止 Hyper-V 服務並重新啟動它。當伺服器啟動時，若 Hyper-V 嘗試啟動，通常就會造成此問題。重新啟動服務會在啟動完成之後讓虛擬機器回復正常運作。
