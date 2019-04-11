---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization

keywords: ESXi, VMware
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Activation de l'accès public à VMware ESXi
{: #enabling-public-access-to-vmware-esxi}

Par défaut, l'hôte ESXi est installé avec la console de maintenance uniquement sur le réseau privé. Cela permet d'empêcher tout trafic public vers et depuis le réseau public sur l'hôte ESXi. 

Pour commencer, vous devez vous connecter au serveur à l'aide d'un client VMware vSphere sur l'interface privée pour le serveur. 

Vous devez disposer de l'adresse IP publique principale du serveur pour finaliser la configuration. Pour toute information sur l'adresse IP publique du serveur, accédez au [portail {{site.data.keyword.slportal_full}} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://control.softlayer.com/){: new_window}.
{:tip}

Une fois connecté à l'hôte ESXi, procédez comme suit pour activer l'interface publique :

1. Vous pouvez être amené à vous connecter au serveur via SSH à l'aide de l'adresse IP privée pour corriger le routage.
* Exécutez `esxcfg-route -l` pour déterminer les routes en cours. 
* Exécutez `esxcfg-route -a 10.0.0.0/8 [adresse IP de passerelle privée de vos serveurs]`.
* Exécutez `esxcfg-route [passerelle publique de vos serveurs]` pour faire en sorte que la passerelle publique soit proposée par défaut.

Connectez-vous à vSphere et procédez comme suit :

1. Cliquez sur l'onglet Configuration et cliquez sur **Networking**.
2. Pour vSwitch1, cliquez sur **Properties > Add**
3. Choisissez **VM Kernel** et cliquez sur **Next**
* Vous pouvez laisser les paramètres tels quels. Vous pouvez remplacer le libellé du réseau par "VMKernelPublic" pour mieux l'identifier. 
4. Entrez les informations relatives à l'adresse IP du serveur. 
5. Pour la passerelle par défaut du noyau de machine virtuelle, cliquez sur **Edit**, entrez l'adresse IP de la passerelle publique, puis cliquez sur **OK**.
* Cliquez sur **OK**. **Remarque :** Une déconnexion est normale et prévue.*

Vous pouvez maintenant accéder au serveur à l'aide des adresses IP publique et privée.

L'activation de l'accès public à ESXi présente des risques de sécurité. Prenez soin d'effectuer les étapes appropriées pour restreindre l'accès à ESXi aux seuls utilisateurs qui en ont besoin.
{:tip}
