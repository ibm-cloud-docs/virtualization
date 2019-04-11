---



copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Foire aux questions : Virtuozzo
{: #faqs-virtuozzo}

## Ai-je besoin d'adresses IP statiques ou portable pour Virtuozzo ?
{: #do-i-need-static-or-portable-ip-addresses-for-virtuozzo-}

* Adresse IP statique : Utilisez une adresse IP statique si vous ne disposez que d'un seul noeud matériel (par VLAN ou au total)
* Adresse IP portable :
    * Routée vers VLAN : Utilisez cette méthode si vous possédez plusieurs noeuds principaux dans le même VLAN. Vous utilisez également cette méthode pour faire migrer un conteneur vers un autre noeud matériel au sein du même VLAN sans modifier l'adresse IP du conteneur. 
    * Secondaire sur VLAN : Cette méthode fonctionne, mais vous n'avez pas besoin d'une passerelle secondaire. Vous perdez 3 des adresses IP en raison de l'ID hôte, de la passerelle et de la diffusion. 

Les coûts par adresse IP peuvent varier en fonction du type routé. Les prix sont affichés sur le portail une fois que vous avez sélectionné le serveur pour lequel vous commandez les adresses IP. Il est possible de modifier le routage à tout moment en ouvrant un ticket de demande de service.
{:tip}

## Puis-je faire migrer un noeud matériel Virtuozzo Container 3 vers un noeud matériel Virtuozzo Container 4 sans indisponibilité ?
{: #can-i-migrate-a-virtuozzo-3-container-to-a-virtuozzo-container-4-hardware-node-with-no-downtime-}

**Linux :
          **

Oui. Par défaut, Virtuozzo Containers for Linux 4 s'exécute en mode compatibilité. Si Virtuozzo Containers ne s'exécute pas en mode compatibilité, vous pouvez le démarrer en utilisant la commande suivante sur le noeud matériel via SSH :

`/usr/sbin/vzagent_compat_ctl start`

**Windows (non pris en charge) :**

Avec Virtuozzo Containers 4 Windows, une indisponibilité se produit durant la transition (entre 20 secondes et 5 minutes) en l'absence d'option en ligne pour la transmission. 

Si vous utilisez Virtuozzo 3.0 for Windows, vous devez mettre à niveau le noeud matériel vers Virtuozzo 3.5.1 for Windows au minimum.

Virtuozzo Containers 4 for Windows, par défaut, s'exécute en mode compatibilité. 

La restauration de sauvegardes à partir de la version 3.5.1 sur un noeud matériel Virtuozzo Containers 4 peut également être effectuée en copiant la sauvegarde intégrale sur le noeud de destination, en exécutant `vzbackupsync.exe` et en restaurant la sauvegarde comme d'habitude. 

Assurez-vous que les mêmes modèles sont installés sur le nouveau noeud matériel.
{:tip}

## Les conteneurs sont-ils isolés les uns des autres et de l'hôte ?
{: #are-containers-isolated-from-each-other-and-the-host-}

Vos conteneurs ne peuvent pas charger des LKM, exécuter une opération `chroot(“../../../”)` hors de leur propre environnement et ils ne partagent pas de ressources IPC (ensembles de sémaphores et segments de mémoire partagée). 

La façon dont Virtuozzo gère les ressources et isole les processus ne présente aucun risque en matière de sécurité. Avec une configuration de réseau routé, tous les conteneurs sur un seul et même VLAN (sur le même noeud matériel) peuvent voir le trafic de chacun des autres conteneurs. Il est fortement recommandé d'utiliser le mode routé. 

## Pour quelle raison ne puis-je pas effectuer de mise en cache dans les modèles de système d'exploitation RedHat et SUSE dans Virtuozzo ?
{: #why-can-t-i-cache-redhat-and-suse-os-templates-in-virtuozzo-}

Vous pouvez rencontrer des problèmes lors de la mise en cache dans les modèles de système d'exploitation RedHat et SUSE si vous utilisez Virtuozzo. Ces modèles de système d'exploitation nécessitent des licences supplémentaires, non fournies par {{site.data.keyword.cloud_notm}}. Si vous recevez l'un des messages d'erreur ci-après, vous devez disposer de vos propres comptes auprès des fournisseurs respectifs et configurer les variables correspondantes dans /etc/vztt/url.map (/etc/vztt/vztt.conf dans Virtuozzo 3.)

||||
|---|---|---|
|TIMESTAMP|Cliquez ici pour ouvrir/fermer les détails de l'opération. Mettez à jour la mémoire cache du modèle de système d'exploitation|**Echec**|
|TIMESTAMP|Opération de mise à jour avec les environnements "virtuozzo00.softlayer.local" et les packages. redhat-as4-x86_64 est démarré||
|TIMESTAMP|Opération de mise à jour avec les environnements "virtuozzo00.softlayer.local" terminée avec des erreurs : impossible de mettre à jour les packages : échec de l'exec : Erreur : variable non définie dans le contenu de l'URL $RH_SERVER/download/mirrors/redhat-as4 Vous pouvez définir cette variable dans /etc/vztt/url.map. .|**Echec**|
|TIMESTAMP|Cliquez ici pour ouvrir/fermer les détails de l'opération. Mettez à jour la mémoire cache du modèle de système d'exploitation|**Echec**|
|TIMESTAMP|Opération de mise à jour avec les environnements "virtuozzo00.softlayer.local" et les packages. redhat-el5-x86_64 est démarré||
|TIMESTAMP|Opération de mise à jour avec les environnements "virtuozzo00.softlayer.local" terminée avec des erreurs : impossible de mettre à jour les packages : échec de l'exec : Erreur : variable non définie dans le contenu de l'URL $RH_SERVER/download/mirrors/redhat-el5 Vous pouvez définir cette variable dans /etc/vztt/url.map. Pour plus de détails, voir le document Virtuozzo Installation Guide. .|**Echec**|
|TIMESTAMP|Cliquez ici pour ouvrir/fermer les détails de l'opération. Mettez à jour la mémoire cache du modèle de système d'exploitation|**Echec**|
|TIMESTAMP|Opération de mise à jour avec les environnements "virtuozzo00.softlayer.local" et les packages. sles-10-x86_64 est démarré||
|TIMESTAMP|Opération de mise à jour avec les environnements "virtuozzo00.softlayer.local" terminée avec des erreurs : impossible de mettre à jour les packages : échec de l'exec : Erreur : variable non définie dans le contenu de l'URL $LES_SERVER/download/mirrors/suse-es10 Vous pouvez définir cette variable dans /etc/vztt/url.map. Pour plus de détails, voir le document Virtuozzo Installation Guide. .|**Echec**|
|TIMESTAMP|Cliquez ici pour ouvrir/fermer les détails de l'opération. Mettez à jour la mémoire cache du modèle de système d'exploitation|**Echec**|
|TIMESTAMP|Opération de mise à jour avec les environnements "virtuozzo01.softlayer.local" et les packages. redhat-as4-x86_64 est démarré||
|TIMESTAMP|Opération de mise à jour avec les environnements "virtuozzo01.softlayer.local" terminée avec des erreurs : impossible de mettre à jour les packages : échec de l'exec : Erreur : variable non définie dans le contenu de l'URL $RH_SERVER/download/mirrors/redhat-as4 Vous pouvez définir cette variable dans /etc/vztt/url.map. Pour plus de détails, voir le document Virtuozzo Installation Guide. .|**Echec**|
|TIMESTAMP|Cliquez ici pour ouvrir/fermer les détails de l'opération. Mettez à jour la mémoire cache du modèle de système d'exploitation|**Echec**|
|TIMESTAMP|Opération de mise à jour avec les environnements "virtuozzo01.softlayer.local" et les packages. redhat-el5-x86_64 est démarré||
|TIMESTAMP|Opération de mise à jour avec les environnements "virtuozzo01.softlayer.local" terminée avec des erreurs : impossible de mettre à jour les packages : échec de l'exec : Erreur : variable non définie dans le contenu de l'URL $RH_SERVER/download/mirrors/redhat-el5 Vous pouvez définir cette variable dans /etc/vztt/url.map. .|**Echec**|
|TIMESTAMP|Cliquez ici pour ouvrir/fermer les détails de l'opération. Mettez à jour la mémoire cache du modèle de système d'exploitation  Echec<br/><br/>29 oct 2008 13:16:56  Opération de mise à jour avec les environnements "virtuozzo01.softlayer.local" et les packages. sles-10-x86_64 est démarré||
|TIMESTAMP|Opération de mise à jour avec les environnements "virtuozzo01.softlayer.local" terminée avec des erreurs : impossible de mettre à jour les packages : échec de l'exec : Erreur : variable non définie dans le contenu de l'URL $LES_SERVER/download/mirrors/suse-es10 Vous pouvez définir cette variable dans /etc/vztt/url.map. |**Echec**|
|TIMESTAMP|Fin du processus|**Echec**|

## Pour quelle raison puis-je exécuter une commande PING sur Service VE et ne puis-je pas me connecter via SSH (PIM/PMC) ?
{: #why-can-i-ping-my-serviceve-but-cannot-log-in-to-via-ssh-pim-pmc-}

Assurez-vous qu'aucune adresse IP autre que celles répertoriées dans le portail (1 adresse publique et 1 adresse privée [situées dans 10.0.0.0/8]) n'est affectée au noeud principal.

Si des fichiers de plage sont configurés sur le noeud principal, vous devez retirer ces fichiers et redémarrer la mise en réseau. 

    [root@virtuozzo ~]# mv –vi /etc/sysconfig/network-scripts/eth[0-9]-range[0-9]* ~/.sl-orig-configs/

    [root@virtuozzo ~]# /sbin/service network restart


`vzcp` n'est pas démarré dans Service VE. Démarrez `vzcp` dans Service VE à partir du noeud matériel. 

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp status

    vzcp est arrêté

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp start

    Starting vzcpd: [  OK  ]

    Starting vzcp: [  OK  ]

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp status

    vzcp (pid 3775 3774 3771) is running...

    [root@virtuozzo ~]#
