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

# Foire aux questions : Généralités
{: #faqs-general}

## Pour quelle raison mon serveur n'apparaît-il pas dans la liste Serveurs virtuels sur le portail client ?
{: #why-isn-t-my-server-listed-in-the-virtual-servers-list-in-the-customer-portal-}

Si **XenServer**, **Hyper-V** ou **Virtuozzo** est installé sur votre serveur mais n'apparaît pas dans la liste [{{site.data.keyword.virtualmachinesshort}} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://manage.softlayer.com/Virtual/live){: new_window}, dans la section [Matériel ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://manage.softlayer.com/Hardware/configuration){: new_window} du [portail ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://manage.softlayer.com/){: new_window}, vous devez ouvrir un [ticket de demande de service standard ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://manage.softlayer.com/Support/addTicket){: new_window} auprès du service de support afin de demander qu'il soit ajouté à votre liste. 

Cette demande est traitée durant les heures ouvrables CST aux Etats-Unis.
{:tip}

## Si un client est un fournisseur de services, peut-il utiliser sa propre fonction d'octroi de licence VSPP pour les hôtes qu'il loue dans IBM Cloud ?
{: #if-clients-are-a-service-provider-can-they-use-their-own-vspp-licensing-for-hosts-that-they-rent-in-ibm-cloud-}

Non. Vous pouvez utiliser {{site.data.keyword.cloud}} VSPP ou BYOL pour l'octroi de licence basé sur des sockets. Vous ne pouvez pas utiliser votre propre contrat VSPP. 

## Puis-je utiliser le bloc d'adresses IP secondaires qui est fourni avec le serveur pour des machines virtuelles ?
{: #can-i-use-the-secondary-ip-block-that-came-with-the-server-for-virtual-machines-}

Non. L'adresse IP secondaire qui est fournie avec votre serveur est routée pour fonctionner sur le serveur physique et non sur une machine virtuelle. Vous devez vous servir de blocs d'adresses IP portables pour connecter votre machine virtuelle au réseau. 
