---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Octroi de droits d'accès au réseau privé à des conteneurs via PIM
{: #giving-containers-access-to-the-private-network-through-pim}

Vous pouvez octroyer des droits d'accès à des conteneurs afin de leur permettre d'accéder à votre réseau privé. Mais, vous devez comprendre ce qu'implique d'autoriser vos clients à accéder à votre infrastructure interne. En effet, cela peut engendrer des problèmes de sécurité. 

1. Accédez au conteneur auquel vous souhaitez octroyer des droits d'accès au réseau privé. 
2. Cliquez sur l'onglet **Network**, puis cliquez sur **Configure**.
3. Vous pouvez désormais configurer la section Bridged Network :
  * **Bridged Network** : Cochez cette case. 
  * **Connect to** : Il n'est pas nécessaire d'octroyer des droits d'accès au réseau privé {{site.data.keyword.cloud}}. 
  * **Get IP address by DHCP** : Cochez cette case. 
  * **IP address / Subnet Mask** : Choisissez une adresse IP INTERNE dans votre pool d'adresses privées. 
  * Cliquez sur **Submit**.

Vous devez à présent actualiser la page à partir du lien situé dans l'angle supérieur droit, et vous avez terminé.
