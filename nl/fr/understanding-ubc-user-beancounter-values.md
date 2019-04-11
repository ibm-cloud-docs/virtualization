---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Compréhension des valeurs UBC (User Bean Counter)
{: #understanding-ubc-user-beancounter-values}

Utilisez le mode SLM si UBC n'est pas compris. SLM est plus facile et fournit le même type de contrôle. 

|Nom|Type|Description|
|---|---|---|
|*Paramètres principaux*|||
|`numproc`|*système*|Nombre de processus et d'unités d'exécution|
|`numtcpsock`|*système*|Nombre de sockets TCP|
|`numothersock`|*système*|Nombre de sockets autres que TCP|
|`vmguardpages`|*système*|Allocation de mémoire garantie|
|`cpuunits`|*unité centrale*|Puissance de l'unité centrale|
|`diskspace`|*disque*|Quota d'espace disque|
|`rate`|*réseau*|Bande passante de réseau|
|`ratebound`|*réseau*|Indique si la bande passante de réseau est limitée|
|*Paramètres supplémentaires*|||
|`kmemsize`|*système*|Taille de la mémoire de noyau non permutable allouée pour les processus dans ce conteneur|
|`tcpsndbuf`|*système*|Taille totale des mémoires tampon d'envoi TCP|
|`tcprcvbuf`|*système*|Taille totale des mémoires tampon de réception TCP|
|`othersockbuf`|*système*|Taille totale des mémoires tampon de domaine UNIX, de protocole UDP et d'autres mémoires tampon d'envoi de protocole de datagramme|
|`dgramrcvbuf`|*système*|Mémoires tampon de réception de protocole UDP et d'autres protocole de datagramme|
|`oomguardpages`|*système*|Quantité de mémoire garantie au cas où la mémoire serait surchargée (si la mémoire est insuffisante, la garantie n'existe plus)|
|`privvmpages`|*système*|Limite d'allocation de mémoire|
|`lockedpages`|*système*|Pages de processus non permutables (pages verrouillées par [mlock(2) ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](http://linux.die.net/man/2/mlock))|
|`shmpages`|*système*|Taille totale de mémoire partagée (communication interprocessus ou IPC, mappages anonymes partagés et objets tmpfs) dans les pages|
|`physpages`|*système*|Nombre total de pages RAM utilisées par les processus|
|`numfile`|*système*|Nombre de fichiers ouverts|
|`numflock`|*système*|Nombre de verrous de fichier|
|`numpty`|*système*|Nombre de pseudo-terminaux|
|`numsiginfo`|*système*|Nombre de structures siginfo|
|`dcachesize`|*système*|Taille totale des structures dentry et i-node verrouillées en mémoire|
|`numiptent`|*système*|Nombre d'entrées NETFILTER (filtrage par paquets IP)|
|`cpulimit`|*unité centrale*|Limite de la consommation d'unité centrale|
|`diskinodes`|*disque*|Quota d'i-node de disque (objet système de fichiers)|
|`quotatime`|*disque*|Délai de grâce de quota de disque|
|`quotaugidlimit`|*disque*|Limite du  nombre d'entrées statistiques UID et GID|
<caption>Tableau 1. Description des paramètres SLM</caption>

Vous pouvez accéder à VzLinuxUBCMgmt.pdf ou afficher ce fichier au format HTML, dans PIM sur votre serveur en accédant à :
**Management > Support > Downloads > Management of UBC Resources Administrator's Guide**

Pour plus d'informations sur UBC, voir [OpenVZ Virtuozzo Containers, Category: UBC ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](http://wiki.openvz.org/Category:UBC).
**Remarque :** OpenVZ et Virtuozzo ne sont pas exactement identiques.
