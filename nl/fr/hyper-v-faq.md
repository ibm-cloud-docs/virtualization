---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Foire aux questions : Hyper-V
{: #faqs-hyper-v}

## Qu'est-ce que Hyper-V ?
{: #what-is-hyper-v-}

Hyper-V est un système de virtualisation pour Windows 2008 Server Datacenter Edition qui permet à un seul et même serveur physique d'héberger plusieurs machines virtuelles qui exécutent toutes leur propre système d'exploitation.

## Quelle est la configuration requise pour l'exécution de Hyper-V ?
{: #what-are-the-requirements-to-run-hyper-v-}

Hyper-V doit être installé sur Windows 2008 Server 64-bit Datacenter Edition. Tout le matériel {{site.data.keyword.BluSoftlayer}} doit répondre à la configuration système requise de Hyper-V. Microsoft recommande d'utiliser au moins 2 Go de mémoire RAM pour le serveur. 

## Hyper-V est-il disponible sur n'importe quelle autre version de 2008 ?
{: #is-hyper-v-available-on-any-other-versions-of-2008-}

{{site.data.keyword.BluSoftlayer_notm}} offre Hyper-V uniquement sur Datacenter Edition.

## Hyper-V peut-il s'exécuter sur Windows 2003 ?
{: #can-hyper-v-run-on-windows-2003}

Hyper-V a été développé pour Windows 2008 et ne peut pas s'exécuter sur Windows 2003.

## Quels systèmes d'exploitation peuvent-être installés sur une machine virtuelle ?
{: #what-operating-systems-can-be-installed-on-a-virtual-machine}

{{site.data.keyword.BluSoftlayer_notm}} prend en charge les systèmes d'exploitation suivants pour Hyper-V :

* Toutes les versions de Windows 2003 et 2008 Server
* CentOS, Fedora et Ubuntu Linux Distributions

## Combien de machines virtuelles un serveur peut-il exécuter ?
{: #how-many-virtual-machines-can-a-server-run-}

Le nombre de machines virtuelles qu'un serveur peut exécuter varie en fonction des processeurs de serveur, de la mémoire RAM et de l'espace sur le disque dur. 

## La mémoire RAM est-elle personnalisable sur chaque machine virtuelle ?
{: #is-the-ram-customizable-on-each-virtual-machine-}

Oui. Vous pouvez utiliser Hyper-V pour personnaliser les ressources système pour chaque machine virtuelle, y compris la mémoire. 

## De quelle quantité de mémoire RAM une machine virtuelle a-t-elle besoin ?
{: #how-much-ram-does-a-virtual-machine-need-}

Les besoins en mémoire RAM varient en fonction des exigences de la machine virtuelle. Vérifiez la configuration système requise pour votre système d'exploitation invité. La quantité de mémoire fournie à une machine virtuelle peut être modifiée à tout moment. 

## Une machine virtuelle peut-elle accéder à plus d'un processeur ?
{: #can-a-virtual-machine-access-to-more-than-one-processor-}

Vous pouvez permettre à une machine virtuelle d'accéder à plusieurs processeurs avec n'importe quelle machine virtuelle Windows. Toutefois, les machines virtuelles Linux sont limitées à un seul processeur. 

## Les tailles de disque dur peuvent-elles être modifiées après la création d'une machine virtuelle 
{: #can-hard-disk-sizes-change-after-a-virtual-machine-is-created-}

Oui.

## Une licence est-elle requise pour chaque système d'exploitation virtuel ?
{: #does-each-virtual-operating-system-need-to-have-a-license-}

Les machines virtuelles Windows 2003 et 2008 sont concédés sous licence via {{site.data.keyword.BluSoftlayer_notm}}. Les machines virtuelles Linux sont concédées sous licence libre et aucune action n'est requise. 

## Les machines virtuelles auront-elles accès au réseau privé ?
{: #will-the-virtual-machines-have-access-to-the-private-network-}

Oui. Les machines virtuelles peuvent se connecter à un réseau public et à un réseau privé. 

## Quels sont les avantages présentés par l'accès des machines virtuelles à un réseau privé ?
{: #what-advantages-are-there-to-providing-private-network-access-to-virtual-machines-}

Le fait de fournir à des machines virtuelles un accès à un réseau privé leur permet de communiquer entre elles. Cela leur permet également de communiquer avec d'autres systèmes internes, tels que NAS et iSCSI, et avec d'autres serveurs dont vous disposez avec {{site.data.keyword.BluSoftlayer_notm}}.

## Les machines virtuelles peuvent-elles utiliser le bloc d'adresses IP secondaires qui est fourni avec le serveur ?
{: #can-virtual-machines-use-the-secondary-ip-block-that-came-with-the-server-}

Non. L'adresse IP secondaire qui a été fournie avec votre serveur est routée spécifiquement pour fonctionner sur le serveur physique et non sur une machine virtuelle. Vous devez vous servir de blocs d'adresses IP portables pour connecter votre machine virtuelle au réseau. 

## Que sont des adresses IP portables ?
{: #what-are-portable-ip-addresses-}

Pour toute information sur les adresses IP portables, voir [Initiation aux sous-réseaux et aux adresses IP](/docs/infrastructure/subnets?topic=subnets-getting-started-with-subnets-and-ips).

## Comment commander Hyper-V? 
{: #how-do-i-order-hyper-v-}

Vous pouvez installer Hyper-V sur un serveur existant en exécutant un rechargement de système d'exploitation et en remplaçant le système d'exploitation par "Windows 2008 Server Datacenter Edition with Hyper-V". Pour mettre à disposition Hyper-V sur un nouveau serveur, commandez celui-ci et sélectionnez "Windows 2008 Server Datacenter Edition with Hyper-V" comme système d'exploitation. 

## Hyper-V est-il préinstallé avec Windows 2008 Server Datacenter Edition ?
{: #is-hyper-v-preinstalled-with-windows-2008-server-datacenter-edition-}

Hyper-V n'est pas préinstallé sur les serveurs Windows 2008. Si vous souhaitez installer Hyper-V sur votre serveur Windows 2008, vous devez exécuter un rechargement de système d'exploitation et sélectionnez "Windows 2008 Server Datacenter Edition with Hyper-V" comme système d'exploitation. 

## Le rechargement de système d'exploitation pour Hyper-V est terminé. Que faire ensuite ?
{: #the-os-reload-for-hyper-v-is-done-what-s-next-}

Hyper-V doit être configuré. Pour plus d'informations sur la configuration de Hyper-V, voir [Configuration de Hyper-V](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-hyper-v).

## Une machine virtuelle ne démarre pas. Le message "Cannot initialize machine remoting system: Error:'Unspecified error'" s'affiche. Comment résoudre ce problème ?
{: #a-virtual-machine-does-not-start-cannot-initialize-machine-remoting-system-error-unspecified-error-how-do-i-resolve-this-issue-}

Pour résoudre ce problème, arrêtez et redémarrez le service Hyper-V. Ce problème se produit généralement lorsque Hyper-V tente de démarrer alors que l'activation du serveur a lieu. Redémarrer le service permet de restaurer la machine virtuelle une fois l'activation terminée.
