---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Installation de VMware sur CentOS 5.x (64 bits)
{: #installing-vmware-on-centos-5-x-64-bit-}

Avant de commencer, connectez-vous au serveur en tant qu'utilisateur root.

## Préparation de l'installation de VMware
{: #preparing-to-install-vmware}

1. Téléchargez le [programme d'installation de VMware ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://vmware.com/download/server/){: new_window}. Cliquez sur le lien de téléchargement, acceptez le contrat CLUF et téléchargez le fichier LinuxTarball (dans cet exemple, VMware-server-1.0.3-44356.tar.gz) : 

* `# wget –O vmware-server.tar.gz https://download3.vmware.com/software/vmserver/VMware-server-1.0.3-44356.tar.gz`

2. Après avoir téléchargé le logiciel, vous devez vous procurer une clé de licence (gratuite dans la version gratuite de VMWare Server). Pour l'enregistrement, voir [VMware ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://register.vmware.com/content/registration.html){: new_window}.

3. Extrayez le fichier .tar :

* `# tar -xzvf vmware-server.tar.gz`

## Avant de commencer l'installation
{: #before-you-begin-installation}

Avant de commencer l'installation, passez en revue les conditions requises présentées ci-dessous :

```
# yum update
# yum install libXtst.i386
# yum install libXrender.i386
# yum install xinetd
```

## Installation de VMWare Server
{: #installing-vmware-server}

1. Accédez au répertoire :

* `# cd vmware-server-distrib/`

2. Exécutez le script d'installation de VMware :

* `# ./vmware-install.pl`

3. Le programme d'installation pose des questions de base et souhaite savoir dans quels répertoires créer et installer certaines parties de VMware. A partir d'ici, acceptez les valeurs par défaut. Acceptez le contrat de licence pour poursuivre l'installation.

4. Il se peut qu'une question semblable à la suivante vous soit posée : "Aucun des modules vmmon préconfigurés pour VMware Server n'est adapté au noyau en cours d'exécution. Souhaitez-vous que ce programme essaie de générer le module vmmon pour votre système (un compilateur en C doit être installé sur votre système) ?"
* Répondez **oui** à cette question (réponse par défaut). 

## Configuration de la mise en réseau de VMware
{: #setting-up-vmware-networking}

Vous êtes invité à répondre aux questions suivantes :

1. "Voulez-vous mettre vos machines virtuelles en réseau ?"
* Répondez **oui**. Vous devez créer une configuration de réseau pour votre périphérique de réseau public afin de pouvoir accéder à Internet sur vos machines virtuelles.

2. "Plusieurs interfaces Ethernet sont disponibles pour votre ordinateur : eth0, eth1. Laquelle souhaitez-vous utiliser pour établir un pont avec vmnet0 ?"
* N'oubliez pas que pour tous les serveurs {{site.data.keyword.cloud}}, le réseau public s'exécute sur eth1 et le réseau privé s'exécute sur eth0. Dans VMware, le périphérique de pont par défaut pour vmnet0 est eth0. Au lieu d'appuyer sur Entrée, tapez eth1.

## Etablissement d'un pont pour le réseau privé
{: #bridging-the-private-network}

Vous êtes invité à répondre par **oui** ou par **non** à la question suivante :
*"Souhaitez-vous configurer un autre réseau relié par un pont ?"

Si vous envisagez d'exécuter des services ou d'autres applications sur votre réseau privé, répondez "oui" à cette question (sauf si vous savez que vous n'utilisez pas le réseau privé). Un pont de réseau est créé pour votre réseau privé. Après que vous avez appuyé sur Entrée, eth0 est utilisé automatiquement comme interface, car c'est le seul élément restant disponible (puisqu'il n'y a que deux cartes réseau dans le serveur). 

## Autres paramètres de mise en réseau
{: #other-networking-settings}

Vous êtes encore invité à répondre à un petit nombre de questions concernant la configuration de réseau de VMware Server. Continuez avec les recommandations suivantes :

* *"Souhaitez-vous pouvoir utiliser la mise en réseau NAT dans vos machines virtuelles ?"*

- Répondez "oui".

* *"Souhaitez-vous que ce programme lance une analyse afin de détecter la présence d'un sous-réseau privé inutilisé ?"*

- Répondez "oui".

- Une fois ce processus terminé, prenez soin de ne pas configurer un autre réseau NAT.

* *"Souhaitez-vous pouvoir utiliser la mise en réseau invité uniquement dans vos machines virtuelles ?"*

- Répondez "oui".

* *"Souhaitez-vous que ce programme lance une analyse afin de détecter la présence d'un sous-réseau privé inutilisé ?"*

- Répondez "oui".

- Une fois ce processus terminé, prenez soin de ne pas configurer un autre réseau invité uniquement. 

## Spécification du port d'écoute
{: #specifying-listening-port}

Vous êtes ensuite invité à indiquer sur quel port VMware Server doit être en mode écoute. Vous pouvez conserver le port par défaut, 904.

## Stockage des machines virtuelles
{: #storing-the-virtual-machines}

Le programme d'installation vous pose une question semblable à la suivante : *"Dans quel répertoire souhaitez-vous conserver vos fichiers de machine virtuelle ?"*. L'emplacement par défaut est /var/lib/vmware/virtual machines. Prenez soin de stocker les machines virtuelles à un emplacement disposant de suffisamment d'espace disque, par exemple, une grappe RAID redondante ou un disque dur secondaire plus volumineux. Vous devez toujours vérifier que vous disposez de suffisamment d'espace pour une machine virtuelle. Dans le cas présent, vous pouvez utiliser un point de montage /data/vm qui est monté sur un disque volumineux. 

## Indication du numéro de série pour VMware
{: #providing-the-serial-number-for-vmware}

La dernière partie de l'installation nécessite d'insérer une clé de licence VMware et un numéro de série. Si vous ne disposez pas encore de clé de licence, consultez le [site VMware ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://register.vmware.com/content/registration.html){: new_window}. Si vous connaissez le numéro de série de ce système VMware Server, saisissez-le dans l'invite et appuyez sur Entrée. 

A présent, un message tel que celui indiqué ci-dessous s'affiche :

    "La configuration de VMware Server 1.0.3 build-44356 for Linux pour ce noyau en cours d'exécution a abouti."

VMware est maintenant configuré sur votre serveur. Vous devez à présent télécharger la console VMware Server, qui est le client d'interface graphique pour votre système VMware Server, puis configurer et installer des machines virtuelles. 

## Téléchargement de la console VMware Server
{: #downloading-vmware-server-console}

La console VMware Server est l'application client de VMware Server. Vous utilisez cette console pour gérer le système VMware Server dans lequel vous pouvez créer, configurer et installer des machines virtuelles. Pour installer cette application, téléchargez le package client VMware Server Windows depuis le [site de téléchargements  de VMware ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://vmware.com/download/server/){: new_window}. Ce package correspond au fichier .zip. Après avoir téléchargé le package client, vous pouvez l'extraire et installer le fichier VMware-console-1.0.3-x. Lorsque l'installation est terminée, vous avez fini d'installer la console VMware Server et vous êtes prêt à configurer votre système VMware Server.

## Connexion à la console VMware
{: #logging-in-to-the-vmware-console}

Ouvrez la console VMware Server depuis l'ordinateur sur lequel vous l'avez installée. Lors du chargement de la console, un écran "Commutateur hôte" (connexion) s'affiche. VMware Server utilise le nom d'utilisateur et le mot de passe du système Linux pour authentifier les utilisateurs, par conséquent, vous devez utiliser les noms d'utilisateur (plus spécifiquement, root) pour vous connecter à VMware. Utilisez les données d'identification suivantes :

* **Nom d'hôte** : Adresse IP plus port (par exemple, 67.228.160.201:904)<br />
* **Nom d'utilisateur** : root<br />
* **Mot de passe** : mot de passe (utilisez le mot de passe root réel du système)

## Configuration des règles de pare-feu (IPTables)
{: #configuring-the-firewall-rules-iptables-}

Si vous ne parvenez pas à vous connecter à VMware Server et s'il ne s'agit pas d'un problème d'authentification (si vous obtenez un message d'erreur lié au nom d'utilisateur ou au mot de passe, cela signifie que le nom d'utilisateur ou le mot de passe que vous avez utilisé est incorrect), il se peut que votre pare-feu vous empêche de vous connecter à VMware Server. Vous pouvez essayer de résoudre ce problème en ajoutant la règle IPTable ci-après dans votre fichier /etc/sysconfig/iptablesfile. **Remarque :** assurez-vous que la convention de dénomination utilisée respecte la configuration de votre serveur : 

- `# -A FWALL-INPUT -p tcp -m tcp -s 0/0 --dport 904 -j ACCEPT`
