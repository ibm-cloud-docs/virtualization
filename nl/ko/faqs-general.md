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

# FAQ: 일반
{: #faqs-general}

## 내 서버가 고객 포털의 가상 서버 목록에 나열되지 않은 이유가 무엇입니까?
{: #why-isn-t-my-server-listed-in-the-virtual-servers-list-in-the-customer-portal-}

서버에 **XenServer**, **Hyper-V** 또는 **Virtuozzo**가 있지만, 이것이 [포털 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://manage.softlayer.com/){: new_window}의 [하드웨어 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://manage.softlayer.com/Hardware/configuration){: new_window} 섹션 내의 [{{site.data.keyword.virtualmachinesshort}} ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://manage.softlayer.com/Virtual/live){: new_window} 목록에 나열되어 있지 않은 경우, 지원 부서와 함께 [표준 지원 티켓 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://manage.softlayer.com/Support/addTicket){: new_window}을 열어야 사용자 목록에 추가됩니다. 

이 요청은 일반 미국 CST 비즈니스 시간 동안 채워집니다.
{:tip}

## 클라이언트가 서비스 공급자인 경우, IBM Cloud에서 임대하는 호스트에 대해 고유한 VSPP 라이센싱을 사용할 수 있습니까?
{: #if-clients-are-a-service-provider-can-they-use-their-own-vspp-licensing-for-hosts-that-they-rent-in-ibm-cloud-}

아니오. 소켓 기반 라이센싱을 위해 {{site.data.keyword.cloud}} VSPP 또는 BYOL을 사용할 수 있습니다. 자체 VSPP 계약은 사용할 수 없습니다. 

## 가상 머신을 위해 서버와 함께 제공되는 보조 IP 블록을 사용할 수 있습니까?
{: #can-i-use-the-secondary-ip-block-that-came-with-the-server-for-virtual-machines-}

아니오. 서버와 함께 제공된 보조 IP는 가상 머신이 아닌 실제 서버에서 작동하도록 라우팅됩니다. 가상 머신을 네트워크에 연결하려면 휴대용 IP 블록이 필요합니다.
