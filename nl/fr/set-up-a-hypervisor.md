---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Configuration d'un hyperviseur
{: #setting-up-a-hypervisor}

Utilisez la procédure décrite ci-après pour configurer un hyperviseur. 

1. Connectez-vous au portail [{{site.data.keyword.slportal_full}} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](http://control.softlayer.com/){: new_window} à l'aide de vos données d'identification uniques. 
2. Dans le menu **Unités**, sélectionnez **Liste des unités** et recherchez votre hyperviseur. 
3. Connectez-vous au réseau privé via le [VPN sécurisé![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](http://www.softlayer.com/vpn-access){: new_window} pour accéder à votre hyperviseur. 

**Remarque :** les fournisseurs d'hyperviseur {{site.data.keyword.cloud}} incluent notamment XenServer, VMware et Hyper-V. Chaque fournisseur dispose de consoles de gestion uniques accessibles de différentes façons. Pour plus d'informations sur l'accès à et l'utilisation d'une console de gestion, voir les liens suivants :

   * [XenServer ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://support.citrix.com/en/products/xenserver){: new_window}
   * [VMware ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://www.vmware.com/support/vsphere-hypervisor.html){: new_window}
   * [Hyper-V ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](http://technet.microsoft.com/en-us/windowsserver/dd448604){: new_window}

4. Procurez-vous des adresses IP portables pour vos machines virtuelles. 
    * Des adresses IP portables sont requises pour les machines virtuelles. Des blocs d'adresses IP portables publiques et privées peuvent être commandés via le portail {{site.data.keyword.slportal}}.
    * Pour plus d'informations sur l'allocation d'adresses IP, voir [Initiation aux sous-réseaux et aux adresses IP](/docs/infrastructure/subnets?topic=subnets-getting-started-with-subnets-and-ips).

5. Etablissez un routage pour les machines virtuelles sur le réseau privé. Les spécifications suivantes sont requises pour les machines virtuelles afin de permettre leur routage vers d'autres machines virtuelles sur le réseau privé : 
    * Adresses IP privées portables
    * Route statique qui concerne la plage de réseau 10.0.0.0/8

Pour plus d'informations sur le processus de routage des machines virtuelles, voir [Configuration d'un réseau de machines virtuelles](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-a-virtual-machine-network). Les machines qui se trouvent sur le même VLAN peuvent communiquer une fois le réseau de machines virtuelles configuré. [Activez le spanning VLAN](/docs/infrastructure/vlans?topic=vlans-vlan-spanning) si les machines qui se trouvent sur des VLAN différents doivent communiquer. 

## Accès et stockage sécurisé des images ISO
{: #access-and-securely-store-isos}

Les machines virtuelles sur le réseau {{site.data.keyword.cloud_notm}} peuvent exécuter des images ISO préconfigurées ou personnalisées. Les machines virtuelles sur le réseau {{site.data.keyword.cloud_notm}} peuvent accéder au site miroir interne, qui est disponible exclusivement pour les utilisateurs {{site.data.keyword.cloud_notm}} et qui fournit les configurations populaires des systèmes d'exploitation les plus couramment utilisés. Si vous avez besoin d'une version ou d'une configuration spéciale pour un système d'exploitation donné, consultez le Web du fournisseur du système d'exploitation. 

Si vous exécutez une image ISO personnalisée sur votre machine virtuelle, téléchargez-la dans un emplacement sûr afin de pouvoir l'extraire si une unité tombe en panne. Un grand nombre d'utilisateurs choisissent de stocker les images ISO personnalisées sur le système local d'une unité à l'aide de SSH ou de RDP. Sinon, de l'espace est offert via des services de stockage qui possèdent différentes fonctions. 
