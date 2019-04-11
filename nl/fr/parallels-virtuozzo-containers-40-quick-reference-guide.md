---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization
---

# Parallels Virtuozzo Containers 4.0 - Aide-mémoire
{: #parallels-virtuozzo-containers-4-0-quick-reference-guide}

Cette rubrique décrit les utilitaires et la terminologie associés aux conteneurs Virtuozzo. 

## Utilitaires généraux
{: #general-utilities}

Les utilitaires généraux sont destinés aux tâches de maintenance quotidiennes.

|Utilitaires généraux||
|---|---|
|vzctl|Utilitaire permettant de contrôler les conteneurs.|
|vzlist|Utilitaire permettant d'afficher la liste des conteneurs figurant sur le noeud, avec des informations supplémentaires. |
|vzquota|Utilitaire permettant de contrôler les quotas de disque des conteneurs Virtuozzo.|
{: caption="Tableau 1. Utilitaires généraux" caption-side="top"}

## Utilitaires d'octroi de licence
{: #licensing-utilities}

Les utilitaires d'octroi de licence permettent d'installer une nouvelle licence, d'afficher l'état d'une licence ou de générer une demande en vue d'obtenir une nouvelle licence.

|Utilitaires d'octroi de licence||
|---|---|
|vzlicview|Utilitaire permettant d'afficher l'état et les paramètres de licence Virtuozzo. |
|vzlicload|Utilitaire permettant de gérer des licences Virtuozzo sur le noeud matériel. |
|vzlicupdate|Utilitaire permettant d'activer l'installation des conteneurs Virtuozzo, de mettre à jour les licences Virtuozzo installées sur le noeud matériel ou de transférer la licence Virtuozzo depuis le noeud source vers le noeud de destination. |
{: caption="Tableau 2. Utilitaires d'octroi de licence" caption-side="top"}

## Utilitaires de migration de conteneur
{: #container-migration-utilities}

Les utilitaires de migration de conteneur permettent de faire migrer des conteneurs entre des noeuds matériels ou au sein d'un noeud matériel. 

|Utilitaires de migration de conteneur||
|---|---|
|vzmigrate|Utilitaire permettant de faire migrer des conteneurs depuis un noeud matériel vers un autre. |
|vzmlocal|Utilitaire utilisé pour le clonage ou le déplacement local des conteneurs. |
|vzp2v|Utilitaire permettant de faire migrer un serveur physique vers un conteneur sur le noeud. |
|vzv2p|Utilitaire permettant de faire migrer un conteneur vers un serveur physique. |
{: caption="Tableau 3. Utilitaires de migration de conteneur" caption-side="top"}

## Utilitaires de sauvegarde de conteneur
{: #container-backup-utilities}

Les utilitaires de sauvegarde de conteneur permettent de sauvegarder et restaurer les zones privées, les fichiers de configuration, les scripts d'action et les informations de quota des conteneurs. 

|Utilitaires de sauvegarde de conteneur||
|---|---|
|vzbackup|Utilitaire permettant de sauvegarder des conteneurs. |
|vzrestore|Utilitaire permettant de restaurer des conteneurs sauvegardés.|
|vzabackup|Utilitaire permettant de sauvegarder des noeuds matériels et leurs conteneurs. Cet utilitaire requiert le logiciel Parallels Agent.|
|vzarestore|Utilitaire permettant de restaurer des noeuds matériels et des conteneurs sauvegardés. Cet utilitaire requiert le logiciel Parallels Agent.|
{: caption="Tableau 4. Utilitaires de sauvegarde de conteneur" caption-side="top"}

## Utilitaires de gestion de modèle
{: #template-management-utilities}

Les utilitaires de gestion de modèle permettent de créer des modèles et de gérer et d'installer des applications dans un conteneur. 

|Utilitaires de gestion de modèle||
|---|---|
|vzpkg|Utilitaire permettant de gérer des modèles EZ de système d'exploitation et d'application au sein de vos conteneurs ou sur le noeud matériel proprement dit.|
|vzmktmpl|Utilitaire permettant de créer des modèles EZ de système d'exploitation et d'application.|
|vzveconvert|Utilitaire permettant de convertir des conteneurs basés sur des modèles standard Virtuozzo en conteneurs basés sur des modèles EZ.|
|vzpkgproxy|Utilitaire permettant de créer des serveurs proxy de mise en cache pour gérer les modèles EZ de système d'exploitation et d'application.|
|vzrhnproxy|Utilitaire permettant de créer des serveurs proxy RHN pour gérer les packages inclus dans les modèles EZ de système d'exploitation RHEL 4 et RHEL 5. |
|vzpkgls|Utilitaire permettant d'obtenir une liste de modèles disponibles sur le noeud matériel et dans des conteneurs. |
|vzpkginfo|Utilitaire permettant d'obtenir les informations relatives à n'importe quel modèle installé sur le noeud matériel. |
|vzpkgcreat| Utilitaire permettant de créer un nouvel ensemble de packages à partir de fichiers RPM ou DEB binaires.|
|vzpkgadd|Utilitaire permettant d'ajouter un modèle à un conteneur.|
|vzpkglink|Utilitaire permettant de remplacer des fichiers réels au sein d'un conteneur par des liens symboliques vers les fichiers sur le noeud.|
|vzpkgrm|Utilitaire permettant de retirer un modèle d'un conteneur. |
|vzpkgcache|Utilitaire permettant de mettre à jour un ensemble d'archives de conteneur préinstallées après l'installation d'un nouveau modèle.|
{: caption="Tableau 5. Utilitaires de gestion de modèle" caption-side="top"}

## Utilitaires supplémentaires
{: #supplementary-tools}

Les utilitaires supplémentaires permettent d'effectuer diverses tâches dans le contexte du noeud matériel et du conteneur. 

|Utilitaires supplémentaires||
|---|---|
|vzup2date|Utilitaire permettant de mettre à jour votre logiciel et vos modèles Virtuozzo. |
|vzup2date-mirror|Utilitaire permettant de créer des miroirs en local du référentiel Virtuozzo officiel. |
|vzfsutil|Utilitaire permettant d'effectuer l'optimisation et le contrôle de cohérence VZFS. |
|vzcache|Utilitaire permettant de gagner de l'espace disque supplémentaire en mettant en cache les fichiers identiques qui se trouvent dans des conteneurs différents. |
|vzsveinstall|Utilitaire permettant de créer le conteneur de service sur le noeud matériel. |
|vzsveupgrade|Utilitaire permettant de mettre à jour les packages dans le conteneur de service. |
|vzps|Utilitaire qui fonctionne comme les utilitaires ps et top standard, avec des fonctions de conteneur ajoutées. |
|vztop|Utilitaire qui fonctionne comme les utilitaires ps et top standard, avec des fonctions de conteneur ajoutées. |
|vzsetxinetd|Utilitaire permettant de faire basculer certains services du mode autonome au mode `xinetddependent`. |
|vzdqcheck|Utilitaire permettant d'imprimer l'utilisation en cours de l'espace fichier du point de vue du quota. |
|vzdqdump|Utilitaire permettant de vider les limites de quota de groupe ou d'utilisateur et les délais de grâce de conteneur à partir du noyau ou du fichier de quota ou de les charger dans un fichier de quota. |
|vzdqload|Utilitaire permettant de vider les limites de quota de groupe ou d'utilisateur et les délais de grâce de conteneur à partir du noyau ou du fichier de quota ou de les charger dans un fichier de quota. |
|vznetstat|Utilitaire permettant d'imprimer les statistiques relatives à l'utilisation du trafic réseau par les conteneurs. |
|vzcpucheck|Utilitaire permettant de vérifier l'utilisation de l'unité centrale par les conteneurs. |
|vzmemcheck|Utilitaire permettant de vérifier les paramètres de mémoire en cours pour le noeud matériel et le conteneur.|
|vzcalc|Utilitaire permettant de calculer l'utilisation des ressources par un conteneur. |
|vzcheckovr|Utilitaire permettant de vérifier la mise à contribution excessive du système en cours et la sécurité des paramètres de contrôle du nombre total de ressources. |
|vzstat|Utilitaire permettant de surveiller la consommation en temps réel de ressources par le noeud matériel et le conteneur. |
|vzpid|Utilitaire permettant d'imprimer l'ID de conteneur auquel appartient le processus.|
|vzsplit|Utilitaire permettant de générer un exemple de fichier de configuration de conteneur, en "fractionnant" le noeud matériel en parts égales. |
|vzcfgscale|Utilitaire permettant de mettre à l'échelle la configuration de conteneur. |
|vzcfgvalidate|Utilitaire permettant de valider l'exactitude d'un fichier de configuration de conteneur. |
|vzcfgconvert|Utilitaire permettant de convertir les fichiers de configuration de conteneur Virtuozzo 2.0.2 au format Virtuozzo 2.5.x. |
|vzstatrep|Utilitaire permettant d'analyser les journaux qui sont collectés par vzlmond et de générer de rapports statistiques à partir de ces journaux (au format texte et graphique). |
|vzreport|Utilitaire permettant d'élaborer un rapport d'incident et de l'envoyer automatiquement à l'équipe de support Parallels. |
|vzhwcalc|Utilitaire permettant d'analyser les principales ressources sur n'importe quel serveur Linux et de créer un fichier dans lequel ces informations sont spécifiées. |
|vzveconvert|Utilitaire permettant de convertir des conteneurs basés sur des modèles de système d'exploitation standard Virtuozzo en conteneurs basés sur des modèles EZ.|
|vznetcfg|Utilitaire permettant de gérer des périphériques de réseau sur le noeud matériel. |
|vzmtemplate|Utilitaire permettant de faire migrer les modèles d'application et de système d'exploitation installés depuis le noeud matériel vers un autre noeud matériel. |
{: caption="Tableau 6. Utilitaires supplémentaires" caption-side="top"}

## Terminologie
{: #common-terms}

|Terminologie||
|---|---|
|Noeud matériel|Un noeud matériel est un ordinateur physique qui héberge des conteneurs. |
|Conteneur|Un conteneur s'apparente du point de vue fonctionnel à un serveur autonome isolé qui dispose de ses propres adresses IP, processus, fichiers, utilisateurs, fichiers de configuration, applications, bibliothèques système, ainsi que de sa propre version et d'autres fonctions serveur. Les conteneurs partagent des noeuds matériels et le même noyau de système d'exploitation, mais ils sont isolés les uns des autres. Chaque conteneur comporte un ID numérique unique qui commence par 1. |
|Système d'exploitation hôte|Un système d'exploitation hôte est un système d'exploitation qui est installé sur le noeud matériel. On le nomme également conteneur 0, pour le distinguer des conteneurs réels dont l'ID commence par 1. |
|Modèle|Un modèle (ou ensemble de packages) est un ensemble de fichiers d'application d'origine qui sont reconditionnés pour être montés sur Virtuozzo File System (VZFS). Deux types de modèle sont disponibles dans Virtuozzo : les modèles de système d'exploitation qui peuvent servir de base pour la création de nouveaux conteneurs et les modèles d'application qui peuvent être ajoutés à des conteneurs après leur création. |
|PIM|Parallels Infrastructure Manager (ou PIM) est un outil de gestion basé sur le Web qui est conçu pour les administrateurs d'hôte. |
|PMC|Parallels Management Console (ou PMC) est un outil de gestion à distance doté d'une interface graphique qui est conçu pour les administrateurs d'hôte. |
|PPP|Parallels Power Panel est un outil de gestion basé sur le Web qui est conçu pour les propriétaires de conteneur. |
{: caption="Tableau 7. Terminologie" caption-side="top"}
