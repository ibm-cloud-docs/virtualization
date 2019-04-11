---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Xenserver
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Connexion à XenServer via une interface publique
{: #connecting-to-xenserver-over-a-public-interface}

Par défaut, l'installation {{site.data.keyword.BluSoftlayer}} de XenServer est configurée pour utiliser l'interface de réseau privé pour toutes les connexions de gestion. Si vous rencontrez des problèmes de connexion sur le réseau privé, ouvrez un ticket de demande de service. 

Il n'est pas recommandé de modifier cette configuration car cela présente un risque pour la sécurité.
{:tip}

Si vous souhaitez reconfigurer XenServer pour l'utilisation de l'interface publique, exécutez les commandes suivantes en tant qu'utilisateur root à partir de l'hôte (l'hôte ne peut pas être intégré à un pool) : .

1. `xe pif-list`

2. Notez l'UUID de l'interface que vous souhaitez activer (probablement eth1 ou bond1).

3. Exécutez la commande suivante :

        `xe host-management-reconfigure pif-uuid=`

Ces modifications sont immédiatement prises en compte et il n'est pas nécessaire de redémarrer le système. 
