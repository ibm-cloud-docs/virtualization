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

# Erste Schritte mit Virtuozzo
{: #getting-started-with-virtuozzo}

## Auf Parallels Infrastructure Manager (PIM) zugreifen
{: #accessing-the-parallels-infrastructure-manager-pim-}

1. Über das [{{site.data.keyword.slportal_full}} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://control.softlayer.com/){: new_window} können Sie Informationen zu Bare Metal Server abrufen.
    1. Wählen Sie im Menü **Einheiten** die Option **Einheitenliste** aus.
    2. Klicken Sie auf Ihren Server.
    3. Suchen Sie die öffentliche IP-Adresse und das zugehörige Kennwort.
5. Rufen Sie die öffentliche IP-Adresse des Servers im bevorzugten Browser auf. Verwenden Sie dazu HTTPS. Verwenden Sie Firefox, Opera oder Internet Explorer, um Virtuozzo über PIM zu verwalten.

JavaScript ist erforderlich. Ohne JavaScript können Sie auf eine nicht vorhandene Seite umgeleitet werden und PIM funktioniert möglicherweise nicht.
{:tip}

6. Geben Sie Ihren Benutzernamen und Ihr Kennwort ein, um sich bei PIM anzumelden.

Der Benutzername ist 'root' und das Kennwort ist das Rootkennwort für den Server, die im {{site.data.keyword.slportal}} aufgeführt werden.
{:tip}

## Vorlagen installieren und zwischenspeichern
{: #installing-and-caching-templates}

1. Wählen Sie in der PIM-Anzeige **Management** > **Aktualisierungen** aus.
2. Rufen Sie den Server auf, den Sie aktualisieren möchten.
3. Klicken Sie auf **Vorlagenaktualisierungen**.
4. Wählen Sie alle zusätzlichen Vorlagen aus, die Sie installieren oder aktualisieren möchten.
5. Klicken Sie auf **Vorlagen** > **Betriebssystemvorlagen**, um den Server auszuwählen, der aktualisiert werden soll.
6. Wählen Sie die Betriebssystemvorlagen aus, die Sie auf dem Server und im Cache verwenden wollen. In der Regel müssen Sie nicht alle Vorlagen im Cache zwischenspeichern, um beginnen zu können. Zwischenspeichern Sie nur die Vorlagen im Cache, die Sie auch benutzen möchten. Diese Vorlagen belegen eine erhebliche Menge an Speicherplatz in der Partition `/vz`.
7. Wenn Sie nur einen Knoten haben, klicken Sie auf **Nächster Cache**.
8. Warten Sie, bis die vollständigen Vorlagen-RPMs heruntergeladen und installiert wurden. Klicken Sie auf **Details**, um den Caching-Status anzuzeigen.

## Netzbereich für Container einrichten
{: #setting-up-network-range-for-containers}

Während der Server Vorlagen herunterlädt und im Cache zwischenspeichert, können Sie den Netzwerkbereich für die Container einrichten.

1. Klicken Sie auf **Auswählen** > **Netz**.
2. Wählen Sie **Neuer IP-Bereich** aus.
3. Fügen Sie zusätzliche IP-Adressen für Ihre Container hinzu. Wenn Sie keine zusätzlichen IP-Adressen für virtuelle Server erworben haben, dann rufen Sie das [{{site.data.keyword.slportal}} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://control.softlayer.com/){: new_window} auf. Wählen Sie **Netz** > **IP-Management** > **VLANs** > **IP bestellen** aus.
4. Rufen Sie den Hardwareknoten auf und klicken Sie auf **Container** > **Neuer Container**.
5. Geben Sie die Informationen an, die Ihren individuellen Anforderungen entsprechen. Verwenden Sie im vorliegenden Beispiel als Standardwert 'slm.512MB'.
6. Geben Sie den Hostnamen als vollständig qualifizierten Domänennamen ein, fügen Sie Ihren DNS-Server und bei Bedarf eine Suchdomäne hinzu.
7. Überprüfen Sie die Ressourcenparameter.

Die Funktionen des Containers zum Verarbeiten der Daten, des Plattenspeicherplatzes oder des Hauptspeichers dürfen nicht beeinträchtigt werden, indem zu viele Parameter hinzugefügt werden. Hierdurch kann es zu einem Ressourcenengpass auf dem Server kommen. Durch diesen Engpass kann es vorkommen, dass Serverprozesse sich verlangsamen oder unerwartet gestoppt werden. Wenn Sie diese Einstellungen nicht verstehen, wenden Sie sich an Ihren Serveradministrator oder an ein [anderes Managementunternehmen ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://developer.ibm.com/bluemix/){: new_window}.{:tip}

8. Klicken Sie auf **Validieren**, bevor Sie auf **Weiter** klicken. Die Validierung stellt sicher, dass Ihre Einstellungen nicht in Konflikt zueinander stehen.
9. Wählen Sie die Anwendungen aus, die Sie installieren möchten, und klicken Sie auf **Weiter**.
10. Überprüfen Sie Ihre Konfiguration und klicken Sie auf **Erstellen**. Anschließend wird ein Container erstellt.
11. Sie können jetzt zurück zu **Container** wechseln, um die neuen Container anzuzeigen und die automatisch zugeordnete IP-Adresse (aus den IPs, die Sie zuvor hinzugefügt haben) abzurufen.
12. Sie können sich nun auf Ihrem Server anmelden und ihn verwenden.

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
