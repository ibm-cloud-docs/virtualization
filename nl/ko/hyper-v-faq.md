---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# FAQ: Hyper-V
{: #faqs-hyper-v}

## Hyper-V의 개념
{: #what-is-hyper-v-}

Hyper-V는 Windows 2008 Server Datacenter Edition의 가상화 시스템으로, 단일 물리적 서버가 자체 운영 체제를 모두 실행 중인 여러 가상 머신을 호스팅할 수 있도록 해줍니다.

## Hyper-V 실행을 위한 요구사항은 무엇입니까?
{: #what-are-the-requirements-to-run-hyper-v-}

Hyper-V는 Windows 2008 Server 64비트 Datacenter Edition에 설치해야 합니다. 모든 {{site.data.keyword.cloud}} 하드웨어는 Hyper-V의 시스템 요구사항을 충족합니다. Microsoft는 서버에 최소 2GB의 RAM을 권장합니다.

## Hyper-V를 2008의 다른 버전에서 사용할 수 있습니까?
{: #is-hyper-v-available-on-any-other-versions-of-2008-}

{{site.data.keyword.cloud_notm}}의 경우 Datacenter Edition에서만 Hyper-V를 제공합니다.

## Hyper-V를 Windows 2003에서 실행할 수 있습니까?
{: #can-hyper-v-run-on-windows-2003}

Hyper-V는 Windows 2008용으로 개발되었으며 Windows 2003에서는 실행할 수 없습니다.

## 가상 머신에 설치할 수 있는 운영 체제는 무엇입니까?
{: #what-operating-systems-can-be-installed-on-a-virtual-machine}

{{site.data.keyword.cloud_notm}}의 경우 다음과 같은 운영 체제를 Hyper-V에서 지원합니다.

* Windows 2003 및 2008 Server의 모든 버전 
* CentOS, Fedora 및 Ubuntu Linux Distributions

## 한 서버가 몇 개의 가상 머신을 실행할 수 있습니까? 
{: #how-many-virtual-machines-can-a-server-run-}

서버가 실행할 수 있는 가상 머신의 수는 서버 프로세서, RAM 및 하드 디스크 공간에 따라 다릅니다.

## 각 가상 머신에서 RAM을 사용자 정의할 수 있습니까? 
{: #is-the-ram-customizable-on-each-virtual-machine-}

예. 메모리를 포함하여 각 가상 머신의 시스템 리소스를 사용자 정의하기 위해 Hyper-V를 사용할 수 있습니다.

## 가상 머신에 얼마나 많은 RAM이 필요합니까?
{: #how-much-ram-does-a-virtual-machine-need-}

필요한 RAM은 가상 머신에 대한 요구사항에 따라 다릅니다. 게스트 운영 체제에 대한 시스템 요구사항을 확인하십시오. 가상 머신에 제공되는 메모리 양은 언제든지 변경할 수 있습니다.

## 한 가상 머신이 둘 이상의 프로세서에 액세스할 수 있습니까?
{: #can-a-virtual-machine-access-to-more-than-one-processor-}

임의의 Windows 가상 머신을 사용하여 여러 프로세서에 가상 머신 액세스 권한을 부여할 수 있습니다. 그러나 Linux 가상 머신은 단일 프로세서로 제한됩니다.

## 가상 머신을 작성한 후에 하드 디스크 크기를 변경할 수 있습니까?
{: #can-hard-disk-sizes-change-after-a-virtual-machine-is-created-}

예. 

## 각 가상 운영 체제가 라이센스를 가지도록 해야 합니까? 
{: #does-each-virtual-operating-system-need-to-have-a-license-}

Windows 2003 및 2008 가상 머신은 {{site.data.keyword.cloud_notm}}를 통해 라이센스가 부여됩니다. Linux 가상 머신은 무료로 라이센스가 부여되며 별도의 조치가 필요하지 않습니다.

## 가상 머신에 사설 네트워크에 대한 액세스 권한이 있습니까?
{: #will-the-virtual-machines-have-access-to-the-private-network-}

예. 가상 머신은 공용 및 사설 네트워크에 연결할 수 있습니다.

## 가상 머신에 사설 네트워크 액세스를 제공하는 데 있어 어떠한 이점이 있습니까?
{: #what-advantages-are-there-to-providing-private-network-access-to-virtual-machines-}

가상 머신에 대한 사설 네트워크 액세스를 제공하면 가상 머신이 서로 통신할 수 있습니다. 사설 네트워크 액세스는 또한 NAS 및 iSCSI, 그리고 {{site.data.keyword.cloud_notm}}와 함께 가지고 있는 다른 서버와 같이 가상 머신이 다른 내부 시스템과 통신하도록 허용합니다.

## 가상 머신은 서버와 함께 제공되는 보조 IP 블록을 사용할 수 있습니까?
{: #can-virtual-machines-use-the-secondary-ip-block-that-came-with-the-server-}

아니오. 서버와 함께 제공된 보조 IP는 특별히 가상 머신이 아닌 실제 서버에서 작동하도록 라우팅됩니다. 가상 머신을 네트워크에 연결하려면 휴대용 IP 블록이 필요합니다.

## 휴대용 IP 주소는 무엇입니까?
{: #what-are-portable-ip-addresses-}

휴대용 IP 주소에 대한 정보는 [서브넷 및 IP 시작하기](/docs/infrastructure/subnets?topic=subnets-getting-started-subnets-ips#getting-started-subnets-ips)를 참조하십시오.

## Hyper-V를 주문하려면 어떻게 해야 합니까?
{: #how-do-i-order-hyper-v-}

OS 다시 로드를 실행하고 운영 체제를 "Windows 2008 Server Datacenter Edition with Hyper-V"로 변경하여 기존 서버에 Hyper-V를 설치할 수 있습니다. 새 서버를 Hyper-V로 프로비저닝하려면 새 서버를 주문하고 "Windows 2008 Server Datacenter Edition with Hyper-V"를 운영 체제로 선택하십시오.

## Hyper-V는 Windows 2008 Server Datacenter Edition과 함께 사전 설치되어 있습니까?
{: #is-hyper-v-preinstalled-with-windows-2008-server-datacenter-edition-}

Hyper-V는 Windows 2008 서버에 사전 설치되어 있지 않습니다. Windows 2008 Server에 Hyper-V를 설치하려는 경우, OS 다시 로드를 발행하고 운영 체제로 "Windows 2008 Server Datacenter Edition with Hyper-V"를 선택해야 합니다. 

## Hyper-V에 대한 OS 다시 로드가 완료되었습니다. 그 다음엔 어떻게 됩니까?
{: #the-os-reload-for-hyper-v-is-done-what-s-next-}

Hyper-V를 설정해야 합니다. Hyper-V 설정에 대한 자세한 정보는 [Hyper-V 설정](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-hyper-v)을 참조하십시오.

## 가상 머신이 시작되지 않습니다. “Cannot initialize machine remoting system: Error:'Unspecified error'” 이 문제를 해결하려면 어떻게 해야 합니까? 
{: #a-virtual-machine-does-not-start-cannot-initialize-machine-remoting-system-error-unspecified-error-how-do-i-resolve-this-issue-}

이 문제를 해결하려면 Hyper-V 서비스를 중지하고 다시 시작하십시오. 이 문제점은 일반적으로 서버 활성화가 발생하는 동안 Hyper-V를 시작하려고 할 때 발생합니다. 서비스를 다시 시작하면 활성화가 완료된 후 가상 머신이 다시 가동됩니다.
