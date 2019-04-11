---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-16"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Qu'est-ce que Virtuozzo ?
{: #what-is-virtuozzo-}

Virtuozzo est une solution de virtualisation basée sur des conteneurs qui permet le partage de matériel au moyen d'une couche d'abstraction. Virtuozzo crée des conteneurs, également appelés VE ou VPS, qui simulent un serveur. Le conteneur agit et répond la plupart du temps comme s'il était un serveur autonome. Le conteneur est séparé des autres conteneurs qui sont sur le même serveur physique car ils ne peuvent pas accéder aux fichiers, ressources IPC ou à la mémoire des autres conteneurs. Le réseau peut être configuré pour être partagé entre plusieurs conteneurs ou isolé.
{:shortdesc}

Virtuozzo n'est pas un hyperviseur ni une interface logicielle avec un hyperviseur. Il fonctionne dans un mélange de noyau et d'espace utilisateur au sein d'un noyau de propriété. Cette configuration permet aux conteneurs d'emprunter des ressources essentiellement en cas de besoin, alors que les ressources sont disponibles sur le noeud principal. 

Pour plus d'informations, voir [Virtuozzo ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://virtuozzo.com/){: new_window}.
