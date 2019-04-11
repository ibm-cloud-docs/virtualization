---
copyright:
  years: 2014, 2018
lastupdated: "2018-06-22"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Migration d'une machine virtuelle Hyper-V
{: #migrating-a-hyper-v-virtual-machine}

## Avant de commencer
{: #before-you-begin-migrating-a-hyper-v-virtual-machine}

Avant de commencer, passez en revue les conditions requises présentées ci-dessous.

* Vous devez disposer d'un serveur compatible (Windows 2008 Server DC Edition avec Hyper-V).
* Vous devez disposer d'une méthode de connexion depuis le serveur source vers le serveur de destination.
* Le serveur source et le serveur de destination doivent résider sur le même VLAN. Si tel n'est pas le cas, une nouvelle adresse IP doit être émise pour la machine virtuelle (pour cela, il est nécessaire d'ouvrir un ticket de demande de service). 
* Vous devez disposer de machines virtuelles dotées de liaisons montantes de réseau actives et de logiciels pris en charge/actifs installés. 

## Raisons pour lesquelles il peut s'avérer nécessaire de déplacer une machine virtuelle Hyper-V
Il existe deux raisons possibles pour lesquelles vous pouvez être amené à déplacer votre machine virtuelle Hyper-V 
* La machine virtuelle se trouve sur du matériel qui ne fonctionne pas correctement. 
* Les ressources du serveur hôte en cours sont insuffisantes.
Dans les deux cas, la migration Hyper-V peut être effectuée rapidement si les conditions requises décrites précédemment sont remplies. Procédez comme suit : 

1. Connectez-vous au serveur source et ouvrez Hyper-V Manager. Sélectionnez la machine virtuelle que vous souhaitez faire migrer vers le serveur de destination.
2. Arrêtez la machine virtuelle que vous souhaitez faire migrer. Ensuite, sélectionnez **Exporter** dans la liste d'actions du serveur et entrez l'emplacement du fichier d'exportation. 
3. A présent, en utilisant le protocole RDP sur le serveur source, vous pouvez vous connecter au serveur de destination avec l'unité C: montée pour le transfert du fichier. 

Vous transférez le fichier via un montage de ressource à l'aide du protocole RDP. Vous pouvez choisir la méthode de transfert qui vous convient le mieux pour ce processus (partage Windows, montage de ressource via RDP, FTP, ainsi que d'autres méthodes de transfert).
{:tip}

4. Le fichier étant exporté vers le serveur de destination, assurez-vous qu'il se trouve bien dans l'emplacement par défaut des disques durs virtuels Hyper-V et importez-le. L'emplacement par défaut est `C:\Users\Public\Documents\Hyper-V\Virtual hard disks`. Si vous avez modifié cet emplacement par défaut et que vous ne vous en souvenez plus, vous pouvez l'afficher en sélectionnant **Paramètres Hyper-V > Importer une machine virtuelle**. 
5. Après que vous avez localisé le fichier exporté et cliqué sur **Importer**, la machine virtuelle est chargée dans votre Hyper-V Manager avec l'ensemble de ses précédentes configurations et de ses précédents fichiers. Le serveur est maintenant en ligne et il fonctionne. Si les conditions requises n'étaient pas remplies sur votre serveur et si le serveur de destination réside dans un autre LAN, vous devez émettre une nouvelle adresse IP pour la machine virtuelle avec un sous-réseau portable (routage secondaire sur VLAN). 
