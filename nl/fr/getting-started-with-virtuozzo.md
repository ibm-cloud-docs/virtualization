---



copyright:
  years: 2014, 2018
lastupdated: "2018-01-09"

subcollection: virtualization


---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Initiation à Virtuozzo
{: #getting-started-with-virtuozzo}

## Accès à Parallels Infrastructure Manager (PIM)
{: #accessing-the-parallels-infrastructure-manager-pim-}

1. Procurez-vous des informations à propos de votre serveur bare metal à partir du portail [{{site.data.keyword.slportal_full}} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://control.softlayer.com/){: new_window}. 
    1. Dans le menu **Unités**, sélectionnez **Liste d'unités**.
    2. Cliquez sur votre serveur.
    3. Localisez l'adresse IP publique et le mot de passe correspondant. 
5. Accédez à l'adresse IP publique du serveur dans le navigateur de votre choix à l'aide de HTTPS. Utilisez Firefox, Opera ou Internet Explorer pour gérer Virtuozzo via PIM.

JavaScript est requis. Si vous ne disposez pas de JavaScript, vous serez peut-être redirigé vers une page qui n'existe pas et PIM ne fonctionnera pas.
{:tip}

6. Entrez votre nom d'utilisateur et votre mot de passe pour vous connecter à PIM.

Le nom d'utilisateur est "root" et le mot de passe est le mot de passe root associé au serveur, comme indiqué dans le portail {{site.data.keyword.slportal}}.
{:tip}

## Installation et mise en cache de modèles
{: #installing-and-caching-templates}

1. Sur l'écran PIM, sélectionnez **Management** > **Updates**.
2. Accédez au serveur que vous souhaitez mettre à jour. 
3. Cliquez sur **Template Updates**.
4. Sélectionnez d'autres modèles que vous souhaitez installer ou mettre à jour. 
5. Pour sélectionner le serveur que vous souhaitez mettre à jour, cliquez sur **Templates** > **OS Templates**.
6. Sélectionnez les modèles de système d'exploitation que vous souhaitez utiliser sur le serveur et le cache. En général, il n'est pas nécessaire de mettre en cache tous les modèles pour commencer. Vous pouvez mettre en cache uniquement les modèles de cache que vous souhaitez utiliser. Ces modèles prennent beaucoup d'espace dans la partition `/vz`. 
7. Si vous ne disposez que d'un noeud, cliquez sur **Next Cache**.
8. Attendez que les packages RPM de modèle soient entièrement téléchargés pour procéder à l'installation. Cliquez sur **Details** pour afficher l'état du cache. 

## Configuration de la plage de réseau pour les conteneurs
{: #setting-up-network-range-for-containers}

Pendant que le serveur télécharge et met en cache les modèles, vous pouvez configurer le modèle de réseau pour les conteneurs. 

1. Cliquez sur **Select** > **Network**.
2. Sélectionnez **New IP Range**.
3. Ajoutez d'autres adresses IP pour vos conteneurs. Si vous n'avez pas acheté d'adresses IP supplémentaires pour vos serveurs virtuels, accédez au portail  [{{site.data.keyword.slportal}} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://control.softlayer.com/){: new_window}. Sélectionnez  **Network** > **IP Management** > **VLANs** > **Order IP**.
4. Accédez au noeud matériel et cliquez sur **Containers** > **New Container**.
5. Indiquez les informations en fonction de vos besoins. Pour cet exemple, utilisez slm.512MB comme valeur par défaut. 
6. Entrez le nom d'hôte sous la forme d'un nom de domaine complet, ajoutez votre serveur DNS et ajoutez un domaine de recherche, si besoin. 
7. Vérifiez les paramètres de ressource.

Prenez soin de ne pas entraver la capacité (espace disque ou mémoire) du conteneur à traiter les données en ajoutant un trop grand nombre de paramètres, ce qui risquerait d'affamer votre serveur de ressources. Votre serveur pourrait ralentir ou arrêter des processus de manière inattendue. Si vous ne comprenez pas ces paramètres, contactez votre administrateur de serveur ou une [société de gestion tierce ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://developer.ibm.com/bluemix/){: new_window}.
{:tip}

8. Cliquez sur **Validate** avant de cliquer sur **Next**. L'opération de validation permet de vérifier que vos paramètres ne sont pas incompatibles. 
9. Sélectionnez les applications que vous souhaitez installer et cliquez sur **Next**. 
10. Passez en revue votre configuration et cliquez sur **Create**. Un conteneur est créé. 
11. Vous pouvez à présent revenir dans **Containers** pour visualiser les nouveaux conteneurs et obtenir l'adresse IP affectée automatiquement (à partir des adresses IP que vous avez ajoutées précédemment). 
12. Vous pouvez à présent vous connecter à votre serveur et l'utiliser.

        myname/myserver[0]~$ ssh 1.234.123.4
        Warning: Permanently added '1.234.123.4' (DSA) to the list of known hosts.
        root@1.234.123.4's password:
        [root@test01 ~]# ip a s
        1: lo: mtu 16436 qdisc noqueue
            link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
            inet 127.0.0.1/8 scope host lo
            inet6 ::1/128 scope host
              valid_lft forever preferred_lft forever
        3: venet0: mtu 1500 qdisc noqueue
            link/void
            inet 127.0.0.1/32 scope host venet0
            inet 1.234.123.4/32 brd 1.234.123.4 scope global venet0:0
        [root@test01 ~]# ps auxww
        USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
        root         1  0.0  0.1  10328   744 ?        Ss   13:23   0:00 init [3]
        root     20419  0.0  0.1  12580   632 ?        Sroot     21726  0.0  0.2  60532  1220 ?        Ss   13:23   0:00 /usr/sbin/sshd
        root     21737  0.0  0.1  22512   892 ?        Ss   13:23   0:00 xinetd -stayalive -pidfile /var/run/xinetd.pid
        root     21756  0.0  0.3  62732  2052 ?        Ss   13:23   0:00 sendmail: accepting connections
        smmsp    21764  0.0  0.3  57608  1752 ?        Ss   13:23   0:00 sendmail: Queue runner@01:00:00 for /var/spool/clientmqueue
        root     21776  0.0  2.0 283796 10728 ?        Ss   13:23   0:00 /usr/sbin/httpd
        root     21786  0.0  0.2  20832  1144 ?        Ss   13:23   0:00 crond
        root     21796  0.0  0.1  46648   800 ?        Ss   13:23   0:00 /usr/sbin/saslauthd -m /var/run/saslauthd -a pam -n 2
        apache   21797  0.0  1.1 283932  5788 ?        S    13:23   0:00 /usr/sbin/httpd
        root     21798  0.0  0.1  46648   544 ?        S    13:23   0:00 /usr/sbin/saslauthd -m /var/run/saslauthd -a pam -n 2
        root     23573  0.0  0.6  85876  3240 ?        Rs   13:45   0:00 sshd: root@pts/0
        root     23594  0.0  0.3  12044  1604 pts/0    Ss   13:45   0:00 -bash
        root     23626  0.0  0.1  10700   952 pts/0    R+   13:45   0:00 ps auxww

        [root@test01 ~]# df -h
        Filesystem            Size  Used Avail Use% Mounted on
        vzfs                  1.0 G   64 M  961 M   7% /
        none                 1004 M  4.0 K 1004 M   1% /dev
        [root@test01 ~]#
