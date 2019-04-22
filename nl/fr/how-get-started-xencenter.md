---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-24"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Initiation à XenCenter
{: #getting-started-with-xencenter}

## Avant de commencer
{: #before-you-begin-xencenter}

Avant de commencer, passez en revue les conditions requises présentées ci-dessous :

- Vous devez disposer de nouvelles plages d'adresses IP portables (publique et privée) qui sont routées en tant que "adresses secondaires sur le VLAN". Pour cet exemple de solution pour la configuration d'une nouvelle machine virtuelle, il est indispensable de disposer d'adresses IP utilisables sur le réseau privé. Vous pouvez commander des sous-réseaux sur le portail [{{site.data.keyword.slportal_full}} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://control.softlayer.com/network/subnets/order).
- Vous devez pouvoir vous connecter au réseau privé {{site.data.keyword.cloud}} via le VPN. Pour plus d'informations sur l'accès à un VPN, voir [Activation de l'accès au réseau privé de l'infrastructure {{site.data.keyword.cloud_notm}}](/docs/customer-portal?topic=customer-portal-getting-started#enable-private-network).
- Citrix XenCenter doit être installé sur votre système local. <!-- . https://downloads.service.softlayer.com/citrix/xen/-->

**Remarque :** votre serveur est préconfiguré avec différents modèles pour vous aider à commencer rapidement à utiliser XenServer. 

## Création d'une machine virtuelle avec XenCenter
{: #creating-a-virtual-machine-with-xencenter}

Pour vous aider à créer une machine virtuelle avec XenCenter, procédez comme suit :

1. Vérifiez que vous êtes connecté au réseau privé {{site.data.keyword.cloud_notm}} via le VPN. Etablissez une connexion via SSL VPN ou PPTP.
2. Ouvrez XenCenter et cliquez sur **"Add your XenServer"**.
3. Entrez le nom de votre hôte de serveur, votre nom d'utilisateur et votre mot de passe. Vous devez utiliser l'adresse IP privée de votre serveur (par exemple, 10.x.x.x), le nom d'utilisateur `root`, puis le mot de passe root de votre serveur. Ces informations sont disponibles dans le portail {{site.data.keyword.slportal}}. Pour les consulter, accédez au panneau [Devices ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://control.softlayer.com/devices){: new_window}, puis cliquez sur le nom de votre serveur XenServer. Cliquez sur **Connect**.
4. Si vous recevez un écran d'activation de licence gratuite, indiquez les informations demandées maintenant. 
5. Cliquez avec le bouton droit de la souris le nom de votre {{site.data.keyword.cloud_notm}} et sélectionnez **New VM...**.<!--You can now create your first Virtual Machine. Create a CentOS virtual machine with a disk of 10 GB and have both Public and Private Networks functioning-->
6. Sélectionnez le système d'exploitation que vous souhaitez utiliser pour la nouvelle machine virtuelle, puis cliquez sur **Next**. **Remarque :** pour certains modèles, vous devez fournir vos propres supports.<!--Because you are using CentOS, you can use {{site.data.keyword.cloud_notm}} private mirrors for CentOS to get our installation going.Select a version of CentOS and then click **Next**.-->
7. Entrez un nom et une description appropriés pour la nouvelle machine virtuelle. 
8. Entrez l'emplacement du support d'installation du système d'exploitation invité, puis cliquez sur **Next**.<!-- In the example, {{site.data.keyword.cloud_notm}} a CentOS mirror is used as installation media. Provide the Install URL of: https://mirrors.service.softlayer.com/centos/5/os/x86_64 and click **Next**.
  *A trailing ‘/’ at the end of the URL can sometimes break the installation.*
  *This mirror is available only on the {{site.data.keyword.cloud_notm}} Private Network. The full mirror's contents are  available here: https://mirrors.service.softlayer.com/.-->
9. Sélectionnez le nombre d'unités centrales et la quantité d'allocation de mémoire. (Vous créez un système rationalisé et vous n'avez pas besoin de beaucoup de mémoire RAM. Par conséquent 512 Mo de mémoire RAM suffiront.). Cliquez sur **Next**. 
10. Sélectionnez vos disques virtuels afin d'allouer de l'espace disque à votre machine virtuelle.<!--Remember that this is like adding hard disks, it is not like partitioning your system. Partitioning is done during the installation of the OS.--> La taille par défaut est de 8 Go pour ce modèle. Ce volume est plus que suffisant pour le serveur que vous créez. *Facultatif : en fonction de vos besoins, vous pouvez définir une taille plus grande pour le disque en mettant celui-ci en évidence et en cliquant sur **Edit...**.* Cliquez sur **Next**. 
11. Ajoutez ou retirez des interfaces de réseau virtuel pour la machine virtuelle. Les valeurs par défaut sont acceptables, sauf si vous ne voulez pas que votre système puisse communiquer sur le réseau privé. Dans le cadre de cet exemple, vous devez laisser les deux interfaces sur le système, mais un administrateur Xen expérimenté peut retirer l'une d'entre elles pour ses propres besoins. Dans ce cas, vous pouvez mettre en évidence et supprimer une interface si vous le souhaitez. Cliquez sur **Next**. 
12. La configuration est terminée. Laissez l'option "Start VM automatically" sélectionnée et cliquez sur **Finish**. L'installation démarre. Vous revenez à l'écran principal. La nouvelle machine virtuelle est affichée sur la gauche.
13. Sélectionnez votre nouvelle machine virtuelle et cliquez sur l'onglet **Console**.<!--You can now see that your system is booted into the CentOS installer awaiting your input.-->
14. <!--All of the parameters of a CentOS installation are outside of the scope of this article and will need to be customized by your System Administrator, but this article will provide some specific pieces of information that you need to complete the installation. Select your language to get started. The CentOS installer will then ask you for assistance in configuring the Networking Devices in the system.--> Sélectionnez **eth0 - xen Virtual Ethernet** et cliquez sur **OK**.
  <!--![14](images/14.png)-->
15. <!--In the pre-requisite notes, we made sure that we already had a set of Portable IP Addresses routed as "Secondary on VLAN" ready for this installation.--> Vérifiez que vous disposez des informations nécessaires pour configurer TCP/IP. Vous devez configurer manuellement l'interface avec le support IPV4 et désactiver le support IPV6. 
  <!--[15](images/15.png)-->
16. Vérifiez que vous savez comment utiliser votre sous-réseau IP privé (10.17.37.240/29) et cliquez sur **OK**<!-- to go to the CentOS installer-->. Vous et votre administrateur système pouvez installer le système d'exploitation invité conformément à vos propres instructions. Pour plus d'informations sur les blocs d'adresses IP statiques et portables, voir [Initiation aux sous-réseaux et aux adresses IP](/docs/infrastructure/subnets?topic=subnets-getting-started-subnets-ips#getting-started-subnets-ips).

Vous avez créé une nouvelle machine virtuelle.
