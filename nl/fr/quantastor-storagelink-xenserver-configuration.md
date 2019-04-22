---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: QuantaStor
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Installation de QuantaStor StorageLink Xenserver
{: #installing-quantastor-storagelink-xenserver}

## Installation de QuantaStor Adapter for Citrix StorageLink
{: #installing-the-quantastor-adapter-for-citrix-storagelink}

1. Assurez-vous que votre installation de XenServer est correctement concédée sous licence pour StorageLink.

Seuls les clients qui s'abonnent aux licences "Enterprise" ou "Platinum" via Citrix peuvent utiliser la fonction StorageLink. Les clients qui possèdent la licence Citrix XenServer "Advanced"  ne peuvent pas accéder à la fonction StorageLink.
{:tip}

2. Arrêtez le service StorageLink en entrant la commande ci-après à l'invite de l'administrateur. Vous pouvez aussi arrêter le service StorageLink à partir du panneau Services dans XenServer.

        C:\> net stop storagelink

3. Téléchargez et exécutez l'adaptateur StorageLink disponible à partir du site [OSNexus ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://www.osnexus.com/trynow/){: new_window}.
4. Une fois le service arrêté, vous pouvez installer l'adaptateur QuantaStor StorageLink en exécutant la commande suivante :

        osn_quantastor_csladapter.exe

5. Ajoutez ce bloc XML à la fin de la configuration de StorageLink, à l'emplacement suivant :
  * `C:\Program Files\Citrix\StorageLink\Server\scsi_device_id_config.xml`
  * `OSNEXUS`
  * `QUANTASTOR1`
  * Au début de ce fichier figure un numéro de version similaire à _2.0.0.4_. Augmentez le numéro de version, par exemple, 2.0.0.5. Le numéro de version indique à StorageLink qu'il doit mettre à niveau vos systèmes XenServer avec ces nouvelles règles d'identification d'unité. 
6. Redémarrez le service Citrix StorageLink :

        C:\> net start storagelink

## Vérification du chargement de StorageLink
{: #verifying-that-storagelink-loaded-properly}

1. Connectez-vous à Citrix StorageLink Manager, sélectionnez l'arborescence "Administration" sur la gauche et recherchez OS NEXUS QuantaStor dans la liste [QuantaStor ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://svn.osnexus.com/mediawiki/images/thumb/c/c8/Storagelink_admin.png/640px-Storagelink_admin.png){: new_window}. 
2. Ajoutez vos données d'identification de système de stockage de sorte que StorageLink puisse configurer et allouer dynamiquement des volumes de stockage pour votre système de stockage QuantaStor. 

## Installation des paramètres de configuration multi-accès XenServer
{: #installing-the-xenserver-multipath-configuration-settings}

XenServer utilise le pilote multi-accès de programme de mappage d'unité Linux (`dmmp`) pour activer la prise en charge d'une solution multi-accès de matériel. Le pilote `dmmp` utilise un fichier de configuration nommé `multipath.conf` qui contient le mode multi-accès et les règles de basculement de chaque fournisseur. 

Pour QuantaStor, quelques règles spéciales doivent être ajoutées au fichier `multipath.conf`. Editez le fichier `/etc/multipath-enabled.conf` qui se trouve sur chacun des noeuds XenServer `dom0`. Dans ce fichier, ajoutez la strophe suivante après la dernière section device { } :

    device {
          vendor                  "OSNEXUS"
          product                 "QUANTASTOR"
          getuid_callout          "/sbin/scsi_id -g -u -s /block/%n"
          path_selector           "round-robin 0"
          path_grouping_policy    multibus
          failback                immediate
          rr_weight               uniform
          rr_min_io               100
          path_checker            tur
    }
