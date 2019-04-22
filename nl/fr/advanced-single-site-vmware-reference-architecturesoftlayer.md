---
copyright:
  years: 2014, 2019
lastupdated: "2019-01-15"

subcollection: virtualization

keywords: vmware, ESXi
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# Architecture de référence d'un environnement VMware mono-site avancé
{: #advanced-single-site-vmware-reference-architecture}

Procédez comme indiqué ci-après pour créer un environnement vSphere mono-site avancé. La procédure décrite ci-après permet de définir un déploiement d'architecture de référence {{site.data.keyword.cloud}} en appliquant les meilleures pratiques recommandées par VMware.

L'architecture de référence a pour objectif de mettre à disposition un environnement qui utilise le stockage partagé, VMware High Availability (HA) et vSphere Distributed Resource Scheduler (DRS), ainsi qu'une passerelle ou un pare-feu {{site.data.keyword.cloud_notm}}. L'architecture de référence d'un environnement VMware mono-site avancé convient à la plupart des déploiements de production qui peuvent être mis à l'échelle en fonction de la charge de travail et peut remplacer une implémentation sur site ou être étendue à un scénario informatique hybride.

## Présentation de l'environnement
{: #environment-overview}

L'environnement VMware avancé présenté se compose d'un centre de données qui gère deux clusters distincts dédiés à la gestion et à la capacité. La configuration peut être définie à l'aide d'un cluster à 4 noeuds unique qui varie en fonction des besoins. Le cluster de gestion contient les machines virtuelles suivantes qui sont utilisées pour la gestion de l'infrastructure :

* VMware vCenter Server 5.5 ou 6.0
* Microsoft Windows 2012 R2 Standard avec des rôles Active Directory et Domain Name System (DNS)

Le cluster de capacité contient les ressources et l'infrastructure nécessaires pour créer et exécuter des machines virtuelles. Pour la mise en réseau, l'environnement est composé de trois VLAN internes privés et d'un VLAN public unique qui est utilisé pour la communication externe. Le tableau 1 spécifie les types de VLAN et les noms de VLAN qui sont utilisés dans l'environnement.

|Type de VLAN|Nom de VLAN|Description|                                                                             |
|---|---|---|
|Privé principal| Gestion| Utilisé pour la gestion et l'accès aux hôtes ESXi physiques et aux serveurs virtuels.|
|Privé principal| Stockage| Utilisé pour la gestion et l'accès au stockage partagé qui est associé à chaque hôte ESXi.|
|Privé principal| Accès MV | Utilisé pour les machines virtuelles qui s'exécutent sur chaque hôte ESXi.|
|Public principal | Public | Utilisé pour les machines virtuelles et d'autres unités qui nécessitent un accès à partir du réseau public.|
<caption>Tableau 1. VLAN principaux</caption>

Pour le stockage partagé, vous pouvez utiliser OS Nexus QuantaStor, un serveur de stockage partagé à service exclusif, ou les services de stockage {{site.data.keyword.cloud_notm}} Endurance ou Performance. Dans tous les cas, l'unité de stockage partagé est utilisée pour stocker les machines virtuelles sur les clusters de gestion et de capacité. Pour plus d'informations sur les options de stockage, voir [Solutions de stockage dans {{site.data.keyword.cloud_notm}} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://www.ibm.com/cloud/storage){: new_window}.

L'environnement de stockage est configuré pour prendre en charge les volumes NFS.

## Etape 1 Commande de VLAN privés et publics principaux
{: #step-1-ordering-primary-public-and-private-vlans}

**Remarque :** les commandes de VLAN sont soumises à un processus de révision et d'approbation. Il n'existe aucune condition ou exigence spécifique permettant l'approbation automatique d'une commande de VLAN. Les commandes de VLAN peuvent être refusées pour différentes raisons.

Quatre VLAN sont créés lors de cette étape, un pour la gestion, un pour le stockage, un pour les machines virtuelles et un pour l'accès public. Il est recommandé de créer ces quatre VLAN avant de commander les serveurs. Commander les VLAN permet de faire en sorte que les serveurs soient placés sur les VLAN appropriés et dans le centre de données adéquat.

L'environnement est composé de cinq hôtes ESXi (deux pour le cluster de gestion et trois pour le cluster de capacité) et d'un serveur virtuel. Le VLAN de gestion privé est composé d'un sous-réseau doté de 16 adresses IP. Le VLAN privé principal dédié au stockage et au trafic des machines virtuelles comprend huit adresses car l'environnement contient un serveur de stockage et les machines virtuelles utilisent un sous-réseau portable. Si une plage de sous-réseaux plus vaste est nécessaire pour le réseau de gestion, modifiez-la en calculant le nombre d'hôtes ESXi et en multipliant ce chiffre par 2. En outre, prenez soin de spécifier le centre de données pour lequel ces VLAN sont créés.

Une fois connecté à {{site.data.keyword.cloud_notm}}, ouvrez un ticket de demande de service pour les VLAN de gestion et de machine virtuelle en sélectionnant **Support > Ajouter un ticket**. Renseignez les zones avec les informations contenus dans le **tableau 2**.

|Zone|Valeur|
|---|---|
|Objet|Question de réseau privé|
|Titre|Commander VLAN|
|Détails|Mettez à disposition trois VLAN privés principaux et un VLAN public principal. Associez le schéma d'adressage suivant à chaque VLAN : <br/><ul><li>Associez 1x18 (16 adresses) à un VLAN privé principal</li><li>Associez 2x29 (huit adresses) à un VLAN privé principal</li><li>Associez 1x29 (huit adresses) au VLAN public principal</li></ul>|
|Pour quelle raison ai-je besoin de ces VLAN supplémentaires ?|Pour placer des hôtes, du stockage et des machines virtuelles sur un autre réseau pour un environnement VMware.
|Ai-je besoin de VLAN publics et/ou privés ?|VLAN Privés et publics|
|De combien de VLAN ai-je besoin ?|Privés = 3, Public = 1|
|De combien d'adresses IP ai-je besoin ?|8 à 16 pour chaque VLAN|
|Derrière quel routeur doivent figurer les VLAN ?|Peu importe si les VLAN figurent tous dans le même pod.|
|Dans quels pods les VLAN doivent-ils être placés ?|Ils sont tous placés dans le même pod dans <NOM CENTRE DE DONNEES>.|
<caption>Tableau 2. Informations de ticket de demande de service</caption>

Une fois les VLAN mis à disposition, notez leurs numéros, la plage de sous-réseaux et la passerelle, puis affectez-les à des réseaux vSphere logiques. Vous pouvez utiliser la feuille de travail contenue dans la section Annexe A : Feuille de calcul VLAN pour enregistrer le VLAN et les informations qui lui sont associées. Exemple :

|Type de VLAN|Numéro de VLAN|Plage d'adresses IP|Passerelle|Objectif|
|---|---|---|---|---|
|Privé principal|1101|10.X.Y.Z/28|10.X.Y.1|Gestion|
|Privé principal|1102|10.A.B.C/29|10.A.B.1|Stockage|
|Privé principal|1103|10.Q.R.S/29|10.Q.R.1|Machines virtuelles|
|Public principal|2101|75.S.T.U/29|75.S.T.1|Accès public|
<caption>Tableau 3. Exemple de VLAN principal</caption>

**Remarque :** ne passez pas à l'étape suivante tant que les VLAN ne sont pas mis à disposition.

## Etape 2 Commande d'adresses IP privées portables
{: #step-2-ordering-portable-private-ip-addresses}

L'étape 2 consiste à créer des sous-réseaux privés portables pour les machines virtuelles dans le cluster de gestion, pour l'accès au stockage de noyau de machine virtuelle et pour les machines virtuelles dans le cluster de capacité. Vous devez déterminer le nombre d'adresses dont vous avez besoin pour chaque sous-réseau VLAN. Dans l'environnement, commandez les adresses suivantes :

* VLAN de gestion - 1x8 adresses/29 – Une adresse pour vCenter Appliance ; une adresse pour DNS et Active Directory.
* VLAN de stockage - 1x16 adresses/28 – Créez deux sous-réseaux sur le même VLAN pour le stockage et deux ports de noyau de machine virtuelle sur chaque hôte ESXi en utilisant différents sous-réseaux pour accéder aux unités de  stockage partagé.
* VLAN de machine virtuelle - 1x32 adresses/27 – Ces adresses sont utilisées pour affecter des adresses IP à des machines virtuelles dans le cluster de capacité.

Lorsque vous commandez une quantité, prenez soin de déterminer le nombre d'adresses IP dont vous avez besoin au cours des 30 prochains jours et des 6 prochains mois. Il est également important de signaler que {{site.data.keyword.cloud_notm}} réserve trois à quatre adresses IP par bloc de sous-réseau portable. Par conséquent, commander quatre adresses IP rapporte une adresse IP ou zéro si le pod prend en charge le protocole Hot Standby Router Protocol.

Utilisez la procédure suivante pour commander un bloc d'adresses IP portables pour chaque VLAN pour chaque sous-réseau que vous souhaitez créer :

1. Ouvrez une fenêtre de navigateur et connectez-vous à {{site.data.keyword.cloud_notm}}.
2. Sélectionnez **Compte > Passer une commande**.
3. Dans la fenêtre en incrustation, accédez à **Réseau > Sous-réseaux / Adresses IP > Commander**.
4. Dans le menu déroulant, sélectionnez **Privé portable**.
5. Sélectionnez **XX adresses IP privées portables** et cliquez sur **Continuer**. **Remarque : **_XX_ indique le nombre d'adresses IP.
6. Sélectionnez le VLAN à associer au  bloc d'adresses IP et cliquez sur **Continuer**.
7. Terminez de renseigner les informations à l'écran et cliquez sur **Continuer**.

La création d'adresses IP est assez rapide et vous pouvez afficher le résultat de cette action en sélectionnant **Sous-réseaux** à partir de **Réseau > Gestion IP**. Vous pouvez enregistrer ces adresses IP dans la feuille de travail de la section Annexe A : Feuille de calcul VLAN.

## Etape 3 Commande d'un serveur virtuel
{: #step-3-ordering-a-virtual-server}

Un serveur virtuel Windows 2012 R2 Standard est mis à disposition pour être utilisé en tant que serveur d'utilitaire pour les images ISO et pour fournir un accès à l'environnement dans cette étape.

1. Ouvrez une fenêtre de navigateur et connectez-vous à {{site.data.keyword.cloud_notm}}.
2. Sélectionnez **Compte > Passer une commande**.
3. Accédez à **Serveur virtuel > Horaire ou mensuel**.
4. Sélectionnez le centre de données approprié (celui sur lequel les VLAN ont été créés) pour mettre à disposition le serveur virtuel et indiquez les spécifications suivantes pour chaque option :
  * Instance de calcul - 1x2.0 GHz coeurs
  * Mémoire RAM : 4 Go
  * Système d'exploitation : Windows Server 2012 R2 Standard Edition (64 bits)
  * Premier disque : 100 Go (SAN)
  * Vitesses de port de liaison montante - Liaisons montantes de 1 Gbit/s sur réseau public et privé
5. Cliquez sur **Continuer votre commande** et sélectionnez les VLAN Backend et Frontend sur l'écran **Récapitulatif de la commande et facturation
**. **Remarque :** la sélection de VLAN est essentielle car elle permet de placer l'utilitaire dans le pod approprié dans le centre de données. Pour l'exemple d'environnement, le VLAN Backend est le VLAN de gestion (1101) et le VLAN Frontend est le VLAN public (2101). 
6. Entrez un nom d'hôte et un nom de domaine pour le serveur, puis cliquez sur **Passer une commande**.

## Etape 4 Commande d'hôtes ESXi et d'un dispositif de passerelle/pare-feu
{: #step-4-ordering-esxi-hosts-and-gateway-firewall}

Vous devez commander les hôtes ESXi et le dispositif de passerelle et de pare-feu Brocade vRouter (Vyatta) pendant la mise à disposition du serveur virtuel. Commandez tous ces serveurs en même temps de sorte qu'ils soient placés simultanément dans le même pod. Si vous commandez du matériel à différents moments, les hôtes et les pare-feu risquent de se retrouver dans des pods distincts dans le centre de données {{site.data.keyword.cloud_notm}}. 

### Hôtes ESXi
{: #esxi-hosts}

Pour chaque hôte ESXi commandé pour l'environnement, VMware ESXi 5.5 i est le système d'exploitation. Si vous souhaitez utiliser des licences {{site.data.keyword.cloud_notm}} vSphere, des frais mensuels vous sont facturés sur la base de l'utilisation. 

Une autre option consiste à installer ESXi à l'aide de votre propre image ISO. Cette procédure est décrite dans la rubrique [Installation de VMware vSphere ESXi via une console distante et des supports virtuels](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi). Si vous souhaitez installer ESXi en utilisant votre propre image ISO, prenez soin de sélectionner **Sans système d'exploitation** pour les hôtes de gestion et de capacité durant le processus de commande. 

**Remarque :** cette implémentation requiert une licence Enterprise Plus pour permettre l'utilisation de commutateurs virtuels distribués VSphere. Si votre licence n'est pas valide pour Enterprise Plus, vous devez utiliser la licence VSPP (VMware Service Provider Program) fournie par {{site.data.keyword.cloud_notm}}. 

### Commande d'hôtes de gestion
{: #ordering-management-hosts}

Utilisez la procédure suivante pour commander les serveurs hôte de gestion :

1. Ouvrez une fenêtre de navigateur et connectez-vous à {{site.data.keyword.cloud_notm}}.
2. Sélectionnez **Compte > Passer une commande**.
3. Sélectionnez **Serveurs bare metal > Mensuel**.
4. Choisissez un serveur approprié répondant aux exigences du cluster de gestion sur l'écran Liste de serveurs. **Remarque :** ESXi 5.5 requiert au moins un processeur dual-core, 4 Go de mémoire RAM et un contrôleur Ethernet 1 gigabit. 
5. Sélectionnez le centre de données approprié (celui sur lequel les VLAN ont été créés) pour mettre à disposition les serveurs ESXi et indiquez les spécifications suivantes pour chaque option :
  * Quantité : 2
  * Mémoire RAM : 32 Go
  * Système d'exploitation : VMware ESXi 5.5 ('Sans système d'exploitation' si vous utilisez la procédure [Installation de VMware vSphere ESXi via une console distante et des supports virtuels](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi))
  * Disques durs : 2 disques SATA 500 Go dans la configuration RAID-1
  * Bande passante publique : Réseau privé uniquement -> 0 Go de bande passante
  * Vitesses de port de liaison montante : Liaisons montantes de 10 Gbps sur réseau privé redondant
6. Cliquez sur **Continuer votre commande**.
7. Cliquez sur **Ajouter un serveur** pour commencer à ajouter des hôtes ESXi pour le cluster de capacité à la commande. 

### Commande d'hôtes de capacité
{: #ordering-capacity-hosts}

1. Choisissez un serveur approprié répondant aux exigences du cluster de capacité sur l'écran Liste de serveurs. **Remarque :** ESXi 5.5 requiert au moins un processeur dual-core, 4 Go de mémoire RAM et un contrôleur Ethernet 1 gigabit. 
2. Sélectionnez le centre de données approprié (celui sur lequel les VLAN ont été créés) pour mettre à disposition les serveurs ESXi et indiquez les spécifications suivantes pour chaque option :
  * Quantité : 3
  * Mémoire RAM : 128 Go
  * Système d'exploitation : VMware ESXi 5.5 ('Sans système d'exploitation' si vous utilisez la procédure [Installation de VMware vSphere ESXi via une console distante et des supports virtuels](/docs/infrastructure/vmware?topic=VMware-installing-vsphere-esxi#installing-vsphere-esxi))
  * Disques durs : 2 disques SATA 500 Go dans la configuration RAID-1
  * Bande passante publique : Réseau privé uniquement > 0 Go de bande passante
  * Vitesses de port de liaison montante : Liaisons montantes de 10 Gbps sur réseau privé redondant
3. Cliquez sur **Continuer votre commande**.

### Finalisation de la configuration
{: #completing-configuration}

Votre panier contient désormais des hôtes ESXi. Pour que les unités soient correctement mises à disposition, vous devez leur affecter le VLAN public (si applicable), le VLAN privé, le nom d'hôte et le domaine. 

1. Affectez les VLAN suivants et créez des noms d'hôte pour les unités :

* Hôtes ESXi – VLAN Backend : (1101)
* VLAN Backend : (1101)
* VLAN Frontend : (2101)

2. Sélectionnez votre méthode de paiement, acceptez les dispositions, puis cliquez sur **Finaliser la commande**. **Important :** ne passez pas à l'étape 5 tant que les serveurs ne sont pas mis à disposition et accessibles via un VPN ou un serveur virtuel qui a été commandé lors de l'étape précédente. 

## Etape 5 Jonction de VLAN sur des commutateurs BCS
{: #step-5-trunking-vlans-on-bcs-switches}

Par défaut, les ports sont placés sur des commutateurs BCS (Backend Customer Switch) (autrement dit, un commutateur de réseau privé dans un pod, tel qu'un commutateur BCS (Backend Customer Switch)) en mode d'accès. Par conséquent, vous devez relier les ports qui sont connectés aux hôtes ESXi de sorte que ces derniers puissent accéder au stockage et que les machines virtuelles puissent communiquer sur le réseau privé. 

Avant d'ouvrir le ticket pour joindre les VLAN, vous devez identifier les interfaces réseau qui se trouvent sur le réseau privé. Pour déterminer l'interface, accédez à **Détails d'unité** pour chaque serveur ESXi et accédez à **Réseau > Privé**. Pour cet environnement, `eth0` et `eth2` sont les adaptateurs de réseau privé. 

Outre la jonction des VLAN pour les hôtes ESXi, vous devez également annuler la liaison des contrôleurs NIC pour les hôtes de gestion et de capacité. Vous annulez la liaison des contrôleurs NIC car le protocole LACP (Link Aggregation Control Protocol) n'est pas compatible avec une solution multi-accès iSCSI logicielle. 

Pour lier les VLAN et annuler la liaison des contrôleurs NIC, vous devez ouvrir un ticket en procédant comme suit :

1. Ouvrez une fenêtre de navigateur et connectez-vous à {{site.data.keyword.cloud_notm}}.
2. Sélectionnez **Support, Ajouter un ticket**.
3. Entrez les informations suivantes :
  * Objet : Question de réseau privé
  * Titre : Lier des VLAN et annuler la liaison de contrôleurs NIC
  * Détails: Liez des VLAN `<Management VLAN>`, `<Storage VLAN>` et `<VM VLAN>` sur une paire de contrôleurs NIC eth0 et eth2 pour les hôtes suivants [répertoriez chaque hôte ESXi]. En outre, annulez la liaison (retirez LACP) des contrôleurs NIC privés (eth0 et eth2) sur les serveurs suivants : [répertoriez chaque hôte ESXi].
4. Cliquez sur **Ajouter ticket**.

Prenez soin de modifier les VLAN désignés dans <> et de spécifier vos VLAN réels.

## Etape 6 Configuration de la mise en réseau des hôtes de gestion
{: #step-6-configuring-management-host-networking}

Une fois les serveurs mis à disposition et les VLAN liés, vous devez configurer la mise en réseau sur les hôtes du cluster de gestion. Pour cela, vous utilisez les commutateurs vSphere standard pour le cluster de gestion. Sauf pour les groupes de ports vMotion et e tolérance aux pannes, vous utilisez des adresses IP portables pour configurer les ports de groupes du noyau de machine virtuelle. **Remarque :** vous utilisez votre propre schéma IP pour vMotion et la tolérance aux pannes car le trafic n'a pas besoin d'être routé. Toutefois, tous les hôtes doivent se trouver sur le même sous-réseau pour que les autres hôtes du cluster puissent utiliser vMotion et la tolérance aux pannes. Si le sous-réseau doit être routé, il est recommandé d'utiliser des adresses IP portables {{site.data.keyword.cloud_notm}}. 

La configuration des groupes de ports nécessite que le client vSphere soit installé sur le serveur virtuel d'utilitaire ou le poste de travail qui accède aux hôtes via le VPN de gestion {{site.data.keyword.cloud_notm}}. 

1. Ouvrez une fenêtre de navigateur et connectez-vous à {{site.data.keyword.cloud_notm}}.
2. Une fois que vous êtes connecté au serveur d'utilitaire ou au VPN {{site.data.keyword.cloud_notm}}, démarrez le client vSphere. 
3. Entrez l'adresse IP, le nom d'utilisateur et le mot de passe de l'un des hôtes ESXi de gestion. (Vous trouverez le mot de passe root pour l'hôte ESXi en sélectionnant **Détails d'unités > Mots de passe**.)
4. Accédez à **Configurations > Mise en réseau** et créez ou modifiez les groupes de ports suivants sur les commutateurs vSphere standard (le plus souvent, vSwitch0).

Procédez comme suit pour chaque hôte du cluster de gestion :

### Propriétés vSwitch0
{: #vswitch0-properties}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tableau 4. Propriétés vSwitch0</caption>
<tbody>
<tr><th>Adaptateur de réseau</th><th>vmnicX et vmnicY</th></tr>
<tr><td>Equilibrage de charge</td><td>Routeur basé sur l'ID de port virtuel d'origine</td></tr>
<tr><td>Adaptateurs actifs</td><td>vmnicX et vmnicY</td></tr>
</tbody>
</table>

### Edition d'un groupe de ports de machine virtuelle existant
{: #edit-existing-virtual-machine-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tableau 5. Groupe de ports vmPG-Management</caption>
<tbody>
<tr><th>Libellé de réseau</th><th>*vmPG-Management*</th></tr>
</tbody>
</table>

### Edition d'un groupe de ports de noyau de machine virtuelle
{: #edit-existing-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tableau 6. Groupe de ports vmkPG-Management</caption>
<tbody>
<tr><th>Libellé de réseau</th><th>*vmkPG-Management*</th></tr>
</tbody>
</table>

### Ajout d'un groupe de ports de noyau de machine virtuelle vMotion
{: #add-vmotion-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tableau 7. Groupe de ports vMotion</caption>
<tbody>
<tr><th>Type de connexion</th><th>Noyau de machine virtuelle</th></tr>
<tr><td>Libellé de réseau</td><td>*vmkPG-vMotion*</td></tr>
<tr><td>ID VLAN</td><td>*&lt;VLAN de stockage&gt; (1102)*</td></tr>
<tr><td>Trafic vMotion</td><td>Activé</td></tr>
<tr><td>Adresse IP</td><td>*172.16.10.X/24*<br/><br/>*Adresse définie par l'utilisateur qui peut être un autre sous-réseau. Assurez-vous que les autres adresses vMotion sur chaque hôte se trouvent sur le même sous-réseau.*</td></tr>
<tr><td>Masque de sous-réseau</td><td>255.255.255.0</td></tr>
</tbody>
</table>

### Ajout d'un groupe de ports de noyau de machine virtuelle de tolérance aux pannes
{: #add-fault-tolerance-vm-kernel-port-group}

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tableau 8. Groupe de ports FT</caption>
<tbody>
<tr><th>Type de connexion</th><th>Noyau de machine virtuelle</th></tr>
<tr><td>Libellé de réseau</td><td>*vmkPG-FT*</td></tr>
<tr><td>ID VLAN</td><td>*&lt;VLAN de stockage&gt; (1102)*</td></tr>
<tr><td>Journalisation de la tolérance aux pannes</td><td>Activé</td></tr>
<tr><td>Adresse IP</td><td>*172.16.20.X/24*<br/><br/>*Adresse définie par l'utilisateur qui peut être un autre sous-réseau si nécessaire. Assurez-vous que les autres adresses FT sur chaque hôte se trouvent sur le même sous-réseau.*</td></tr>
<tr><td>Masque de réseau</td><td>255.255.255.0</td></tr>
</tbody>
</table>

### Ajout d'un groupe de ports de noyau de machine virtuelle de stockage
{: #add-storage-vm-kernel-port-group}

Mettez à jour la section **Remarques** de chaque adresse IP portable avec le nom de l'hôte et le port de noyau de machine virtuelle affectés. Pour accéder à la section **Remarques**, accédez à {{site.data.keyword.slportal_full}} et sélectionnez **Réseau > Gestion IP > Sous-réseaux > [Sous-réseau]**.

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tableau 9. Groupe de ports de stockage</caption>
<tbody>
<tr><th>Type de connexion</th><th>Noyau de machine virtuelle</th></tr>
<tr><td>Libellé de réseau</td><td>*vmkPG-Storage*</td></tr>
<tr><td>ID VLAN</td><td>*&lt;VLAN de stockage&gt; (1102)*</td></tr>
<tr><td>Adresse IP</td><td>*Adresse privée portable*<br/><br/>*Adresse IP sélectionnée parmi les adresses privées portables qui sont liées au VLAN de stockage.*</td></tr>
<tr><td>Masque de réseau</td><td>*Masque de sous-réseau associé à la plage d'adresses IP*</td></tr>
</tbody>
</table>

### Ajout d'un groupe de ports d'adresse publique
{: #add-public-address-port-group}

Mettez à jour la section **Remarques** de chaque adresse IP portable avec le nom de l'hôte et le port de noyau de machine virtuelle affectés. Pour accéder à la section **Remarques**, accédez à {{site.data.keyword.slportal}} et sélectionnez **Réseau > Gestion IP > Sous-réseaux > [Sous-réseau]**.

<table border="1" cellpadding="0" cellspacing="0">
<caption>Tableau 10. Groupe de ports public</caption>
<tbody>
<tr><th>Type de connexion</th><th>Machine virtuelle</th></tr>
<tr><td>Libellé de réseau</td><td>vmPG-Public</td></tr>
<tr><td>ID VLAN</td><td>&lt;VLAN public principal&gt; (par exemple, 2101)</td></tr>
</tbody>
</table>

## Etape 7 Réception par téléchargement ou envoi par téléchargement d'images ISO et vCenter Virtual Appliance
{: #step-7-download-or-upload-iso-images-and-vcenter-virtual-appliance}

A présent, l'environnement est prêt pour déployer VMware vCenter Virtual Appliance et installer une machine virtuelle pour DNS, Active Directory ou BIND. Toutefois, avant de procéder au déploiement, vous devez recevoir par téléchargement les images. Pour cela, ouvrez une session via la fonction de bureau à distance sur le serveur virtuel précédemment mis à disposition et recevez par téléchargement les images appropriées sur le serveur virtuel pour votre environnement :

* Active Directory / Windows DNS : Image ISO Windows Server 2008R2/Windows Server 2012 (votre support sous licence)
* Linux BIND : Image d'installation [CentOS ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://isoredirect.centos.org/centos/6/isos/x86_64/){: new_window}
* [vCenter Virtual Appliance ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://my.vmware.com/web/vmware/info/slug/datacenter_cloud_infrastructure/vmware_vsphere/5_5){: new_window} (requiert un abonnement VMware valide)

Après avoir reçu par téléchargement l'image ISO, vous devez l'envoyer par téléchargement à un magasin de données hôte de gestion afin de pouvoir la connecter à une machine virtuelle. Utilisez le client vSphere pour vous connecter à un hôte de gestion et créez un répertoire d'images ISO sur le magasin de données local. 

## Etape 8 Déploiement de DNS
{: #step-8-deploying-dns}

Vous devez déployer une machine virtuelle (sur un hôte de gestion) et installer des services DNS. Utilisez le client vSphere traditionnel pour créer une machine virtuelle sur l'hôte ESXi de gestion où se trouve l'image ISO Windows ou Linux. Connectez l'image ISO appropriée (Windows ou CentOS) pour déployer un serveur DNS sur la machine virtuelle. Prenez soin d'associer la machine virtuelle au groupe de ports de machine virtuelle (vmpg) de gestion qui a été créé lors d'une étape précédente. 

Une fois la machine virtuelle installée, affectez une adresse IP et une passerelle par défaut à partir du groupe de sous-réseaux privés portables. Si vous utilisez la feuille de travail VLAN de l'annexe A, il s'agit du sous-réseau de machines virtuelles de gestion. Mettez à jour la section **Remarques** de chaque adresse IP portable avec le nom de la machine virtuelle affectée. Pour accéder à la section **Remarques**, accédez à {{site.data.keyword.slportal}} et sélectionnez **Réseau > Gestion IP > Sous-réseaux > [Sous-réseau]**.

Bien que la description des étapes nécessaires à l'activation de DNS dépasse le cadre du présent document, les conseils suivants sont fournis :

1. Affectez à **DNS Forwarding** les hôtes DNS locaux service.softlayer.com :
  * `rs1.service.softlayer.com 10.0.80.11`
  * `rs2.service.softlayer.com 10.0.80.12`
2. Après avoir configuré DNS, créez une zone DNS locale (dal06.mycompany.local) et une zone de recherche inversée pour tous les sous-réseaux portables et principaux qui sont mis à disposition. 
3. Ajoutez un enregistrement HOST pour chaque adresse IP de gestion d'hôte (vmk0 sur vmkPG-management).
4. Ajoutez un enregistrement HOST à partir du sous-réseau privé portable qui est lié au VLAN de gestion pour votre vCenter Virtual Appliance.
5. Mettez à jour la section **Remarques** du sous-réseau IP portable que vous avez affecté à vCenter.

Pour plus d'informations, voir les liens suivants : 
* [Windows DNS and Active Directory ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://social.technet.microsoft.com/wiki/contents/articles/12370.step-by-step-guide-for-setting-up-a-windows-server-2012-domain-controller.aspx){: new_window}.
<!--* [CentOS BIND ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.centos.org/docs/2/rhl-rg-en-7.2/s1-bind-configuration.html){: new_window}.-->

## Etape 9 Déploiement et configuration de vCenter Virtual Appliance
{: #step-9-deploying-and-configuring-vcenter-virtual-appliance}

Maintenant que DNS est configuré, vous pouvez déployer et configurer vCenter Server Appliance. Pour déployer ce dispositif, procédez comme suit :

1. Ouvrez une session sur le serveur virtuel via la fonction de bureau à distance et ouvrez le client vSphere. 
2. Connectez-vous à un hôte de gestion et sélectionnez **File, Deploy OVF Template**.
3. Suivez l'assistant pour achever le déploiement.

<!--For more information about OVF Template deployment, see [VMware documentation ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://pubs.vmware.com/vsphere-55/index.jsp?topic=%2Fcom.vmware.vsphere.hostclient.doc%2FGUID-6C847F77-8CB2-4187-BD7F-E7D3D5BD897B.html&resultof=%22thick%22%20%22thin%22%20){: new_window}.-->

Comme aucun serveur DHCP (Dynamic Host Configuration Protocol) n'est disponible pour affecter une adresse IP au dispositif virtuel vCenter lors de la mise sous tension, vous devez utiliser la console racine pour configurer le dispositif. **Remarque :** un message `NO NETWORKING DETECTED` s'affiche sur la console du dispositif virtuel vCenter.  

Pour configurer le dispositif, procédez comme suit :

1. Connectez-vous à la console avec le **nom d'utilisateur** 'root' et le **mot de passe** 'vmware'.
2. Exécutez `/opt/vmware/share/vami/vami_config_net` et suivez les instructions de l'assistant pour configurer les propriétés d'adresse IP, de sous-réseau et de serveur DNS. N'oubliez pas d'utiliser l'adresse IP du serveur DNS ou BIND qui a été créé à l'**étape 8 : Déploiement de DNS**.
3. Sauvegardez les paramètres de réseau, quittez la console et ouvrez un navigateur sur le serveur virtuel. 
4. Accédez à l'adresse IP que vous avez associée au dispositif VCVA (vCenter Virtual Appliance) à laquelle le port 5480 est ajouté<!-- (https://:5480)-->.
5. Acceptez le contrat CLUF dans l'assistant, répondez à la question **Customer Improvement Experience Program** et sélectionnez **Configure Options, Set custom configuration**.
6. Cliquez sur **Next** et entrez les valeurs suivantes :
<table border="1" cellpadding="0" cellspacing="0"><caption>Tableau 11. Assistant de configuration du dispositif VCVA</caption><tbody><tr><th>Menu de l'assistant</th><th>Option</th><th>Valeur</th></tr><tr><td>Database Settings</td><td>Database Type</td><td>imbriqué</td></tr><tr><td>SSO Settings</td><td>SSO Deployment Type</td><td>imbriqué</td></tr><tr><td>SSO Settings</td><td>New administrator password *(pour administrator@vsphere.local)*</td><td>&lt;Entrez un mot de passe&gt;</td></tr><tr><td>SSO Settings</td><td>Retype the new password</td><td>&lt;Entrez le mot de passe utilisé précédemment&gt;</td></tr><tr><td>Time synchronization</td><td>NTP synchronization</td><td>servertime.service.softlayer.com</td></tr></tbody></table>
7. Cliquez sur **Start**. Le dispositif VCVA est configuré. 
8. Modifiez le mot de passe root à l'aide des options **Admin**.
9. Déconnectez-vous de la page Web de configuration du dispositif VCVA. 
10. Accédez au client Web vSphere en entrant l'adresse IP du dispositif VCVA à laquelle est ajouté `9443/vsphere-client`<!-- (https://:9443/vsphere-client)-->.
11. Connectez-vous à la console racine et sélectionnez **Administration, Licenses**.
12. Entrez votre licence VMware vCenter. 

## Etape 10 Création de clusters vCenter et de commutateurs virtuels distribués
{ #step-10-create-vcenter-clusters-and-distributed-virtual-switch}

Maintenant que le dispositif VCVA est configuré et sous licence, vous pouvez créer les constructions de centre de données et de cluster et les commutateurs virtuels distribués pour le cluster de capacité. 

### Création d'un centre de données et de clusters
{: #creating-data-center-and-clusters}

1. Sur le client vSphere, accédez à l'écran **Home**. 
2. Sélectionnez **Getting Started** et cliquez sur **click here**.
3. Cliquez sur **Create Datacenter**.
4. Entrez un nom de centre de données (l'exemple d'environnement est le centre de données Toronto 01, utilisez `Toronto 01` comme nom de centre de données). 
5. Une fois le centre de données créé, cliquez sur **Create a cluster** sur la page **Getting Started**.
6. Nommez le premier cluster `Management`. Laissez les autres options désélectionnées et cliquez sur **OK**.
7. Créez un autre cluster lorsque vous avez terminé en procédant comme pour le cluster de gestion. 

Vous pouvez à présent ajouter les hôtes de gestion et de capacité aux clusters de gestion et de capacité, en utilisant l'option **Add a host**. Prenez soin d'utiliser le nom de domaine complet (FQDN) et du serveur et non son adresse IP de sorte que le serveur DNS soit utilisé lorsque chaque hôte est ajouté. 

Une fois les hôtes ESXi ajoutés à vCenter, il se peut que des messages d'avertissement s'affichent concernant l'activation de l'interpréteur de commandes et de SSH sur chaque hôte ESXi. Pour supprimer ces messages d'avertissement, cliquez sur **Suppress Warning > Yes** dans la fenêtre en incrustation. Si le lien Suppress Warnings ne s'affiche pas, procédez comme suit : 

1. Accédez à l'onglet **Manage** de l'hôte ESXi. 
2. Sélectionnez **Settings**, puis cliquez sur **Advanced System Settings**.
3. Localisez la clé **UserVars.SupressShellWarning** et remplacez la valeur par **1**.
4. Cliquez sur **OK**.

Une fois les hôtes de gestion et de capacité ajoutés à leurs clusters respectifs, accédez à chacun d'eux et configurez DNS et NTP. Pour configurer DNS, procédez comme suit :

1. Cliquez sur un hôte et sélectionnez **Manage > Networking**.
2. Sélectionnez la pile système par défaut (**TCP/IP configuration**) et cliquez sur l'icône en forme de crayon. 
3. Entrez l'adresse IP du serveur DNS que vous avez créé précédemment, ainsi que le nom d'hôte et de domaine. 

Pour les paramètres NTP :

1. Accédez à **Manage, Settings, Time Configuration**.
* Entrez `servertime.service.softlayer.com` comme serveur NTP. 
* Affectez à **NTP Service Startup Policy** la valeur `Start and stop with host`.

***Création d'un commutateur virtuel distribué pour les hôtes de capacité***

1. Utilisez le client Web vSphere pour accéder à **Networking** et cliquez avec le bouton droit de la souris sur le nom du centre de données. 
2. Sélectionnez **New Distributed Switch**.
3. Donnez un nom au commutateur distribué, puis cliquez sur **Next**.
4. Sélectionnez la version de commutateur distribué appropriée, puis cliquez sur **Next**.
5. Sur l'écran **Edit Settings**, entrez `2` comme nombre de liaisons montantes et désélectionnez l'option **Create a default port group**. 
6. Cliquez sur **Next > Finish** et créez le commutateur virtuel distribué. 

***Création de groupes de ports pour un commutateur virtuel distribué***

Maintenant que le commutateur virtuel distribué existe, vous devez créer des groupes de ports pour vMotion, la tolérance aux pannes, les machines virtuelles et le stockage.

***Création d'un groupe de ports dvpg-Private-VM Management***

1. Accédez à la section Networking à l'aide du client Web vSphere. 
2. Cliquez avec le bouton droit de la souris sur le commutateur virtuel distribué (DVS).
3. Cliquez sur **New Distributed Port Group…** et entrez les informations dans le tableau 14 pour le premier groupe de ports. 
4. Conservez les valeurs par défaut pour les options qui ne sont pas spécifiées dans le tableau.

| Menu New Distributed Port Group | Zone | Valeur |
| --- | --- | --- |
| Name and Location | Name | dvpg-Private-VM Management |
| Configure Settings | Advanced | Cochez Customize default policies configuration |
| Configure Policies (Teaming and Failover)| Load Balancing| Route qui est basée sur le chargement NIC physique |
| Configure Policies (Teaming and Failover)| Failover Order| Liaisons montantes actives : Liaison montante 1 & Liaison montante 2 |
<caption>Tableau 12. Groupe de ports de gestion de machine virtuelle pour commutateur virtuel distribué</caption>

Après avoir créé le premier groupe de ports, créez les autres ports de groupes à l'aide des options de configuration ci-après (tableau 15 au tableau 19).

***Création d'un groupe de ports dvpg-Private-vMotion***

| Menu New Distributed Port Group |Zone|Valeur|
|---|---|---|
| Name and Location | Name |dvpg-Private-vMotion|
| Configure Settings |VLAN Type|VLAN|
|Configure Settings|VLAN ID|&lt;VLAN de stockage&gt;|
|Configure Settings|Advanced| Cochez Customize default policies configuration |
| Configure Policies (Teaming and Failover)|Load Balancing| Route qui est basée sur le chargement NIC physique |
| Configure Policies (Teaming and Failover)| Failover Order| Liaisons montantes actives : Liaison montante 1 & Liaison montante 2 |
<caption>Tableau 13. Groupe de ports vMotion pour commutateur virtuel distribué</caption>

***Création d'un groupe de ports dvpg-Private-Fault Tolerance***

| Menu New Distributed Port Group |Zone|Valeur|
|---|---|---|
| Name and Location | Name |dvpg-Private-Fault Tolerance|
|Configure Settings|VLAN Type|VLAN|
|Configure Settings|VLAN ID|&lt;VLAN de stockage&gt;|
|Configure Settings|Advanced| Cochez Customize default policies configuration |
| Configure Policies (Teaming and Failover)|Load Balancing| Route qui est basée sur le chargement NIC physique |
| Configure Policies (Teaming and Failover)| Failover Order| Liaisons montantes actives : Liaison montante 1 & Liaison montante 2 |
<caption>Tableau 14. Groupe de ports FT pour commutateur virtuel distribué</caption>

***Création d'un groupe de ports dvpg-Private-VM Access***

| Menu New Distributed Port Group |Zone|Valeur|
|---|---|---|
| Name and Location | Name |dvpg-Private-VM Access|
|Configure Settings|VLAN Type|VLAN|
|Configure Settings|VLAN ID|&lt;VLAN de machine virtuelle&gt;|
|Configure Settings|Advanced| Cochez Customize default policies configuration |
|Configure Policies (Teaming and Failover)|Load Balancing| Route qui est basée sur le chargement NIC physique |
|Configure Policies (Teaming and Failover)|Failover Order| Liaisons montantes actives : Liaison montante 1 & Liaison montante 2 |
<caption>Tableau 15. Groupe de ports d'accès de machine virtuelle pour commutateur virtuel distribué</caption>

***Création de dvpg-Private-Storage***

| Menu New Distributed Port Group |Zone|Valeur|
|---|---|---|
|Name and Location|Name|dvpg-Private-Storage Path A|
|Configure Settings|VLAN Type|VLAN|
|Configure Settings|VLAN ID|&lt;VLAN de stockage&gt;|
|Configure Settings|Advanced| Cochez Customize default policies configuration |
|Configure Policies (Teaming and Failover)|Load Balancing| Route qui est basée sur le chargement NIC physique |
|Configure Policies (Teaming and Failover)|Failover Order| Liaisons montantes actives : Liaison montante 1 & Liaison montante 2 |
<caption>Tableau 16. Groupe de ports de stockage pour commutateur virtuel distribué</caption>

***Création de dvpg-Primary-Public***

|Menu New Distributed Port Group|Zone|Valeur|
|Name and Location|Name|dvpg-Private-Storage|
|Configure Settings|VLAN Type|VLAN
|Configure Settings|VLAN ID|&lt;VLAN public principal&gt;|
|Configure Settings|Advanced|Cochez Customize default policies configuration|
|Configure Policies (Teaming and Failover)|Load Balancing|Route qui est basée sur le chargement NIC physique|
|Configure Policies (Teaming and Failover)|Failover Order|Liaisons montantes actives : Liaison montante 1 & Liaison montante 2|
<caption>Tableau 17. Groupe de ports B de chemin de stockage pour commutateur virtuel distribué</caption>

## Etape 11 Migration d'hôtes ESXi d'un cluster de capacité vers un commutateur virtuel distribué
{: #step-11-migrating-esxi-hosts-in-capacity-cluster-to-distributed-virtual-switch}

Maintenant que les hôtes de capacité ont été ajoutés au cluster de capacité, vous pouvez faire migrer la configuration de commutateurs virtuels standard vers les commutateurs virtuels distribués que vous avez créés à l'*étape 10 : Création de clusters vCenter et de commutateurs virtuels distribués*. La migration est effectuée pour un hôte et vous appliquez un profil d'hôte ultérieurement pour configurer le reste du cluster. 

Avant de commencer à ajouter des adaptateurs VMkernel, affectez les contrôleurs NIC de machine virtuelle aux liaisons montantes sur le commutateur virtuel distribué. 

1. Cliquez sur **vCenter Inventory Lists, Distributed Switches**.
2. Sélectionnez le commutateur distribué approprié pour les hôtes de capacité. 
3. Cliquez sur **Add and manage hosts** sur la page **Getting Started**. 
4. Utilisez les paramètres suivants pour ajouter des liaisons montantes et faire migrer le VMkernel existant associé à la gestion de l'hôte. 
  - <table border="1" cellpadding="0" cellspacing="0"><caption>Tableau 18. Ajout d'hôtes pour commutateur virtuel distribué</caption><tbody><tr><th>Menu</th><th>Zone</th><th>Valeur</th></tr><tr><td>Select Task</td><td>Select Tasks</td><td>Add Hosts</td></tr><tr><td>Select Hosts</td><td>Cliquez sur **New Hosts**</td><td>Cliquez sur Capacity Host</td></tr><tr><td>Sélectionnez Network Adapter Tasks</td><td>Sélectionnez Network Adapter Tasks</td><td>Sélectionnez Manage physical adapters et Manage VMkernel adapters</td></tr></tbody></table>
5. Sélectionnez l'un des contrôleurs NIC de machine virtuelle privés et cliquez sur **Manage physical network adapters > Assign uplink**.
6. Sélectionnez `uplink1` dans la fenêtre en incrustation et cliquez sur **OK**.
7. Répétez ces étapes pour l'autre contrôleur NIC de machine virtuelle privé et affectez-le à `uplink2`.
8. Cliquez sur **Next**, mettez en évidence l'adaptateur vmk0 VMkernel, puis cliquez sur **Assign port group**.
9. Sélectionnez **dvpg-Private-VM Management** dans la fenêtre en incrustation et cliquez sur **OK**.
10. Cliquez deux fois sur **Next**, puis cliquez sur **Finish** pour terminer la migration vers le commutateur virtuel distribué. **Remarque :** il se peut que vous perdiez temporairement la connectivité du réseau avec l'hôte. La connexion est rétablie rapidement. 

Après avoir fait migré l'adaptateur vmk0 vers le commutateur virtuel distribué, vous pouvez ajouter des noyaux de machine virtuelle à chaque groupe de ports du commutateur virtuel distribué. 

11. Cliquez sur **Manage > Networking** sur l'hôte dans vCenter.
12. Accédez à **VM Kernel adapters > Add host networking** et ajoutez les adaptateurs de noyau de machine virtuelle contenus dans les tableaux 19 et 21. Pour ajouter ces adaptateurs, accédez au menu "VM Kernel adapters" de l'onglet **Manage > Networking** sur l'hôte dans vCenter. Cliquez ensuite sur l'icône "Add host networking" et ajoutez mes adaptateurs de noyau de machine virtuelle suivants : 

***Ajout de vmk1 pour vMotion***

|Menu|Zone|Valeur|
|---|---|---|
|Select connection type|Select connection type|VMKernel Network Adapter|
|Select target device|Select an existing distributed port group|dvpg-Private-vMotion|
|Select network adapter tasks|Select network adapter tasks|Sélectionnez **Manage physical adapters** et **Manage VM kernel adapters**|
|Port Properties|Enable Services|Cochez vMotion traffic|
|IPv4 Settings|IPv4 Address|*172.16.10.X/24*<br/><br/>*Adresse définie par l'utilisateur qui peut être un autre sous-réseau si nécessaire. Assurez-vous que les autres adresses vMotion sur chaque hôte se trouvent sur le même sous-réseau.*|
|IPv4 Settings|Subnet Mask|255.255.255.0|
<caption>Tableau 19. Mise en réseau hôte pour vMotion</caption>

***Ajout de vmk2 pour la tolérance aux pannes***

|Menu|Zone|Valeur|
|---|---|---|
|Select connection type|Select connection type|VMKernel Network Adapter|
|Select target device|Select an existing distributed port group|dvpg-Private-Fault Tolerance|
|Select network adapter tasks|Select network adapter tasks|Sélectionnez **Manage physical adapters** et **Manage VMKernel adapters**|
|Port Properties|Enable Services|Cochez Fault Tolerance Logging|
|IPv4 Settings|IPv4 Address|*172.16.20.X/24*<br/><br/>*Adresse définie par l'utilisateur qui peut être un autre sous-réseau si nécessaire. Assurez-vous que les autres adresses FT sur chaque hôte se trouvent sur le même sous-réseau.*|
|IPv4 Settings|Subnet Mask|255.255.255.0|
<caption>Tableau 20. Mise en réseau hôte pour la tolérance aux pannes</caption>

***Ajout de vmk3 pour le stockage***

|Menu|Zone|Valeur|
|---|---|---|
|Select connection type|Select connection type|VMkernel Network Adapter|
|Select target device|Select an existing distributed port group|dvpg-Private-Storage|
|Select network adapter tasks|Select network adapter tasks|Sélectionnez Manage physical adapters et Manage VMkernel adapters|
|IPv4 Settings|IPv4 Address|*Adresse privée portable*<br/><br/>*Adresse IP sélectionnée parmi les adresses privées portables qui sont liées au VLAN de stockage. Cette adresse doit figurer sur un autre sous-réseau que le chemin de stockage B.*|
|IPv4 Settings|Subnet Mask|Masque de sous-réseau associé à la plage d'adresses IP|
<caption>Tableau 21. Mise en réseau hôte pour le stockage</caption>

***Création d'un profil d'hôte***

Pour créer un profil d'hôte, vous devez le capturer à partir de l'unique hôte de capacité que vous avez configuré précédemment. 

1. Accédez à l'écran d'accueil du client Web vSphere et cliquez sur l'icône **Host Profiles**. 
2. Cliquez sur le signe plus (+) vert, **Extract profile from a host**, et sélectionnez l'hôte de capacité précédemment configuré sur la fenêtre en incrustation. 
3. Cliquez sur **Next**. 
4. Donnez au profil d'hôte un nom approprié (Capacity01 Host Profile), entrez une description, puis cliquez sur **Next**.
5. Passez en revue les paramètres, puis cliquez sur **Finish**. 

Après avoir créé le profil d'hôte, vous devez le modifier de manière à ne pas être invité par le système à entrer des adresses MAC lorsque le profil est appliqué au reste des hôtes dans le cluster de capacité. 

1. Cliquez avec le bouton droit de la souris sur le profil d'hôte que vous avez créé et sélectionnez **Edit Settings > Edit Host Profile, Host virtual NIC**.
2. Dans le panneau de droite, remplacez **Determine how MAC address for vmknic is decided** par **User must explicitly choose the policy option**.
3. Cliquez sur **Next**, puis sur **Finish**.

***Association du profil d'hôte au cluster de capacité***

Maintenant que vous avez créé un profil d'hôte, vous devez l'associer au cluster. Les clusters sont associés afin de pouvoir être appliqués aux hôtes de capacité.

1. Accédez à la vue **Host and Clusters** dans le client Web vSphere. 
2. Activez le mode maintenance pour chaque hôte du cluster. **Remarque :** les profils ne peuvent être appliqués qu'à des hôtes qui sont en mode maintenance. 
3. Cliquez avec le bouton droit de la souris sur le cluster de capacité et sélectionnez **Attach Host Profile** dans le menu contextuel. 
4. Sélectionnez le profil d'hôte que vous avez créé et cliquez sur **Next**.
5. Entrez les informations appropriées sur l'écran **Customize host** et cliquez sur **Finish**.
6. Désactivez le mode maintenance pour les hôtes. 

## Etape 12 Commande, configuration et association de stockage partagé
{: #step-12-order-configure-and-attach-shared-storage}

Avant de continuer, il est impératif de commander, configurer et associer du stockage partagé qui sera utilisé avec les clusters et les hôtes de gestion et de capacité dans l'environnement. Si vous souhaitez utiliser la solution de stockage partagé à service partagé {{site.data.keyword.cloud_notm}} (Stockage de fichiers), consultez le [guide d'architecture pour IBM File Storage for {{site.data.keyword.cloud_notm}} with VMware](/docs/infrastructure/FileStorage?topic=FileStorage-architectureguide).

## Etape 13 Activation de la haute disponibilité/fonction DRS et de svMotion vCVA
{: #step-13-enabling-ha-drs-and-svmotion-vcva}

***Activation de la haute disponibilité/fonction DRS sur les clusters de gestion et de capacité***

Maintenant que vous avez configuré le stockage partagé, vous devez activer la haute disponibilité et la fonction DRS pour renforcer la protection et l'équilibrage de sur les machines virtuelles du cluster de gestion. 

1. Accédez au client Web vSphere.
* Sélectionnez Manage, Settings pour le cluster de gestion. 
* Sélectionnez vSphere DRS et cliquez sur Edit. Vérifiez que les paramètres suivants sont sélectionnés : **Turn on vSphere DRS**, Automation Level - **Fully Automated**, Migration threshold slider setting - midway, virtual machine automation - **Enable individual virtual machine automation levels.**, Power Management - **Off**.  
* Sur l'écran vSphere HA Settings, assurez-vous que les paramètres suivants sont sélectionnés : **Turn on vSphere HA**, **Host Monitoring**, VM restart priority - **Medium**, Host isolation response - **Leave powered on**.  
***Storage vMotion the vCenter Virtual Appliance***

Maintenant que vous avez configuré le stockage sur le cluster de gestion et activé la haute disponibilité et la fonction DRS, vous devez définir le stockage partagé pour le dispositif vCenter Virtual Appliance. 

1. Cliquez avec le bouton droit de la souris sur le dispositif approprié et sélectionnez **Migrate** dans le menu contextuel. 
2. Sélectionnez **Change data store** et cliquez sur **Next**.
3. Sélectionnez le volume iSCSI que vous avez précédemment monté sur le cluster de gestion et cliquez sur **Next**.
4. Passez en revue les sélections et cliquez sur **Next**.

L'environnement VMware mono-site avancé est terminé. 

## Récapitulatif
{: #summary}

Vous disposez désormais d'un environnement VMware qui s'exécute dans un centre de données IBM Cloud. Votre environnement VMware peut exécuter des charges de travail et prendre en charge un déploiement IBM Cloud sur site. L'environnement adopte les meilleures pratiques VMware et active des fonctions telles que VMware DRS, Haute disponibilité, Storage DRS et la redondance de mise en réseau. Vous pouvez étendre cette implémentation d'architecture de référence à des hôtes de gestion ou de capacité plus grands et à davantage de stockage. 

Pour plus d'informations sur VMware, voir [Déploiement de VMware](/docs/infrastructure/vmware?topic=VMware-using-cookbooks-for-vmware-deployments) et  [Foire aux questions sur VMware](/docs/infrastructure/vmware?topic=VMware-faqs-vmware)

## Annexe A : Feuille de calcul VLAN
{: #appendix-a-vlan-worksheet}

|Type de VLAN|Numéro de VLAN|Plage d'adresses IP|Passerelle|Objectif|
|---|---|---|---|---|
|Privé principal||||Gestion|
|Privé principal||||Stockage|
|Privé principal||||Machines virtuelles|
|Public principal||||Accès public|
|Privé Portable||||Machines virtuelles de gestion|
|Privé Portable||||Stockage|
|Privé Portable||||Machines virtuelles|
|vMotion|||Sans objet||
|Tolérance aux pannes|||Sans objet|||
<caption>Feuille de travail VLAN</caption>
