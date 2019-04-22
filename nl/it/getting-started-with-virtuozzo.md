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

# Introduzione a Virtuozzo
{: #getting-started-with-virtuozzo}

## Accesso a PIM (Parallels Infrastructure Manager)
{: #accessing-the-parallels-infrastructure-manager-pim-}

1. Ottieni le informazioni dal [{{site.data.keyword.slportal_full}} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://control.softlayer.com/){: new_window} sul tuo server bare metal.
    1. Dal menu **Devices**, seleziona **Device list**.
    2. Fai clic sul tuo server.
    3. Individua l'indirizzo IP pubblico e la password.
5. Vai all'indirizzo IP pubblico del server nel tuo browser preferito utilizzando HTTPS. Utilizza Firefox, Opera o Internet Explorer per gestire Virtuozzo tramite PIM.

JavaScript è obbligatorio. Senza JavaScript, puoi essere reindirizzato a una pagina non esistente e PIM non funzionerà.
{:tip}

6. Immetti i tuoi nome utente e password per eseguire l'accesso a PIM.

Il nome utente è "root" e la password è la password root per il server, come elencato nel {{site.data.keyword.slportal}}.
{:tip}

## Installazione e memorizzazione nella cache di template
{: #installing-and-caching-templates}

1. Nella schermata di PIM, seleziona **Management** > **Updates**.
2. Vai al server che desideri aggiornare.
3. Fai clic su **Template Updates**.
4. Seleziona gli eventuali template aggiuntivi che desideri installare o aggiornare.
5. Per selezionare il server che desideri aggiornare, fai clic su **Templates** > **OS Templates**.
6. Seleziona i template del sistema operativo che desideri utilizzare sul server e memorizzare in cache. Di norma, non hai bisogno di memorizzare in cache tutti i template per iniziare. Memorizza in cache solo i template che desideri utilizzare. Questi template occupano molto spazio nella partizione `/vz`.
7. Se hai un solo nodo, fai clic su **Next Cache**.
8. Attendi il download e l'installazione degli RPM di template completi. Fai clic su **Details** per visualizzare lo stato della memorizzazione in cache.

## Configurazione dell'intervallo di rete per i contenitori
{: #setting-up-network-range-for-containers}

Mentre il server scarica e memorizza in cache i template, puoi configurare l'intervallo di rete per i contenitori.

1. Fai clic su **Select** > **Network**.
2. Seleziona **New IP Range**.
3. Aggiungi ulteriori indirizzi IP per i tuoi contenitori. Se non hai acquistato ulteriori indirizzi IP per i tuoi server virtuali, vai al [{{site.data.keyword.slportal}} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://control.softlayer.com/){: new_window}. Seleziona **Network** > **IP Management** > **VLANs** > **Order IP**.
4. Vai al nodo hardware e fai clic su **Containers** > **New Container**.
5. Completa le informazioni in base alle tue esigenze. Per questo esempio, utilizza slm.512MB come valore predefinito.
6. Immetti il nome host come un nome dominio completo, aggiungi il tuo server DNS e aggiungi un dominio di ricerca, se necessario.
7. Controlla i parametri di risorsa.

Fai attenzione a non inibire la capacità del contenitore di elaborare i dati, il suo spazio su disco o la memoria aggiungendo troppi parametri, cosa che potrebbe deprivare il server di risorse. Se deprivato di risorse, il tuo server potrebbe rallentare o arrestare i processi in modo imprevisto. Se non comprendi queste impostazioni, rivolgiti all'amministratore del tuo server oppure a una [società di gestione terza![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://cloud.ibm.com/docs){: new_window}.
{:tip}

8. Fai clic su **Validate** prima di fare clic su **Next**. La convalida assicura che le tue impostazioni non siano in conflitto tra loro.
9. Seleziona le applicazioni che desideri installare e fai clic su **Next**.
10. Controlla la tua configurazione e fai clic su **Create**. Viene quindi creato un contenitore.
11. Puoi ora tornare a **Containers** per visualizzare i nuovi contenitori e ottenere l'indirizzo IP assegnato automaticamente (dagli IP che hai aggiunto in precedenza).
12. Puoi ora eseguire l'accesso al tuo server e utilizzarlo.

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
