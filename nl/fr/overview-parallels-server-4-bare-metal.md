---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Présentation de Parallels Server 4 Bare Metal
{: #overview-of-parallels-server-4-bare-metal}

Parallels Server Bare Metal est une solution de virtualisation qui fournit la virtualisation matérielle et la virtualisation logicielle de Virtuozzo, offrant une technologie à la fois pour les machines virtuelles et les conteneurs*.
{:shortdesc}

**Interface de ligne de commande**

Outre les commandes Virtuozzo habituelles, d'autres commandes sont disponibles : `pctl`, `pmigrate`, `pstat` et `prl_disk_tool`.

**Migration**

Vous pouvez désormais faire migrer des conteneurs Virtuozzo vers des machines virtuelles, des serveurs physiques vers de conteneurs Virtuozzo et des serveurs physiques vers des machines virtuelles pour permettre la consolidation de services. Cette fonctionnalité permet également de faire migrer des conteneurs ou des machines virtuelles entre des serveurs bare metal, de convertir des machines virtuelles qui ont été créées sur différents environnements virtualisés (V2V) et de gérer des SID Windows lorsque vous clonez ou déployez une machine virtuelle Windows à partir d'un modèle. 

**Utilisation d'adresse IP et VLAN**

Vous pouvez affecter des machines virtuelles à une adresse IP à partir du serveur physique à l'aide des outils Parallels qui se trouvent dans la machine virtuelle. En outre, PSBM permet de créer des commutateurs virtuels et des VLAN dans les machines virtuelles afin de renforcer la sécurité du trafic réseau entre les machines virtuelles. 

**Exécution de commandes**

Vous pouvez exécuter des commandes brutes depuis le serveur physique directement dans les conteneurs Virtuozzo et à présent dans les machines virtuelles (lorsque les outils Parallels sont installés dans les machines virtuelles), y compris les réinitialisations de mot de passe utilisateur. 

**Comptabilité de processus**

Vous utilisez Parallels Server Bare Metal pour augmenter et réduire rapidement la priorité des ressources qui sont allouées (unité centrale, priorité d'entrée-sortie de disque) sur une machine virtuelle. 

**Sauvegarde**

Parallels Server Bare Metal fournit une fonction permettant de sauvegarder et restaurer des machines virtuelles et des conteneurs sur le serveur bare metal local ou sur un serveur bare metal distant afin d'inclure des sauvegardes intégrales et incrémentielles. 

**Comptabilité de réseau**

Vous utilisez Parallels Server Bare Metal pour afficher et localiser facilement des machines virtuelles ou des conteneurs en fonction du débit réseau historique et en cours. 

**Réseau**

Parallels Server Bare Metal utilise des adresses IP portables, tandis que Virtuozzo utilise des adresses IP portables ou statiques (selon la configuration). 

\* {{site.data.keyword.BluSoftlayer_full}} ne concède sous licence que des instances HVM (Hardware Virtual Machine) sur Parallels Server 4 Bare Metal, sauf indication contraire sur le formulaire de commande.
_MV_ = machine virtuelle. _V_ = VPS ou conteneur. 
