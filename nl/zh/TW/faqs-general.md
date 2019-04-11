---



copyright:
  years: 2014, 2018
lastupdated: "2018-01-04"

subcollection: virtualization


---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# 常見問題：一般
{: #faqs-general}

## 為何我的伺服器未列示在「客戶入口網站」的「虛擬伺服器」清單中？
{: #why-isn-t-my-server-listed-in-the-virtual-servers-list-in-the-customer-portal-}

如果您的伺服器上有 **XenServer**、**Hyper-V** 或 **Virtuozzo**，但在[入口網站 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://manage.softlayer.com/){: new_window} 的[硬體 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://manage.softlayer.com/Hardware/configuration){: new_window} 區段內，它未列示在 [{{site.data.keyword.virtualmachinesshort}} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://manage.softlayer.com/Virtual/live){: new_window} 清單中，則您需要使用「支援部門」開立[標準支援問題單 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://manage.softlayer.com/Support/addTicket){: new_window}，才能將它新增至您的清單。

此要求是在正常美國 CST 營業時間填寫的。
{:tip}

## 如果客戶是服務提供者，他們是否可以對他們在 IBM Cloud 租賃的主機使用自己的 VSPP 授權？
{: #if-clients-are-a-service-provider-can-they-use-their-own-vspp-licensing-for-hosts-that-they-rent-in-ibm-cloud-}

否。您可以對 Socket 型授權使用 {{site.data.keyword.cloud}} VSPP 或 BYOL。您無法使用自己的 VSPP 合約。

## 是否可以對虛擬機器使用伺服器隨附的次要 IP 區塊？
{: #can-i-use-the-secondary-ip-block-that-came-with-the-server-for-virtual-machines-}

否。您伺服器隨附的次要 IP 會遞送至實體伺服器上的工作，而非虛擬機器上的工作。您需要具有「可攜式 IP」區塊，才能將虛擬機器連接至網路。
