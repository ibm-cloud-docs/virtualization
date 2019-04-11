---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-11"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Activation de l'accès public à VMware
{: #enabling-public-access-to-vmware}

Par défaut, l'hôte ESXi est installé avec la console de maintenance uniquement sur le réseau privé, ce qui signifie que le trafic public vers et depuis le réseau public est interdit sur l'hôte ESXi. 

Pour commencer, vous devez vous connecter au serveur à l'aide d'un client VMware vSphere sur l'interface privée du serveur en procédant comme suit :

1. Cliquez sur **ESX Host > Configuration Tab > Networking > Add Networking**
2. Sélectionnez Service Console.
3. Sélectionnez un commutateur virtuel (vSwitch) à utiliser. En l'occurrence, les interfaces externe/publique sont utilisées, par conséquent, sélectionnez **vSwitch1** (vmnic1 et vmnic3).
4. Renommez la console de maintenance et donnez-lui un nom facilement reconnaissable (par exemple, "Public Service Console").
5. Entrez l'adresse IP externe principale qui a été affectée au serveur, avec le masque de sous-réseau approprié. 
6. Cliquez sur **Edit** et ajoutez la passerelle par défaut qui a été affectée au serveur. 
