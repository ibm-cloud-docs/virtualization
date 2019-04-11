---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Migration d'un conteneur vers un autre noeud matériel
{: #migrating-a-container-to-a-different-hardware-node}

1. Assurez-vous que vous disposez de deux noeuds matériels configurés dans PIM.
2. Accédez à **Infrastructure** et sélectionnez le noeud matériel sur lequel figure le conteneur. 
3. Sélectionnez le conteneur, puis cliquez sur **Migrer**.
4. Effectuez une sélection dans la zone **Noeud de destination**.
5. Indiquez si vous souhaitez que la migration soit effectuée de façon active. 

**Remarque :** en fonction de la taille du conteneur, une migration active ne sera peut-être pas réalisable. La plupart des informations qui se trouvent dans les conteneurs doivent être conservés en mémoire RAM tant que la migration n'est pas terminée.

6. Sous **Avancé**, vous pouvez indiquer si vous souhaitez ou non retirer les fichiers de conteneur qui se trouvent sur le noeud source après la migration. Indiquez que le conteneur ne doit pas démarrer automatiquement sur le noeud de destination et que la migration ne doit pas être forcée. Forcer la migration peut entraîner des problèmes si vous disposez d'un autre noeud qui utilise la même adresse IP. Un autre problème de migration peut se produire lorsque les modèles de système d'exploitation ou d'application qui sont installés et mis en cache dans le noeud de destination ne sont pas valides. 
7. Vous pouvez consulter la page **Détails** pour surveiller le processus de migration. 
8. La migration est terminée. Le conteneur est transféré automatiquement sur le noeud de destination, qui se trouve sous **Infrastructure** dans le panneau de menu de gauche. 
