---



copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Häufig gestellte Fragen: Virtuozzo
{: #faqs-virtuozzo}

## Benötige ich für Virtuozzo statische oder portierbare IP-Adressen?
{: #do-i-need-static-or-portable-ip-addresses-for-virtuozzo-}

* Statische IP-Adresse: Verwenden Sie eine statische IP-Adresse, wenn Sie über nur einen Hardwareknoten (pro VLAN oder insgesamt) verfügen.
* Portierbare IP-Adresse:
    * Mit VLAN-Routing: Verwenden Sie diese Methode, wenn Sie über mehrere Hostknoten innerhalb eines VLAN verfügen. Sie können diese Methode auch zum Migrieren eines Containers auf einen anderen Hardwareknoten innerhalb desselben VLAN verwenden, ohne dass hierzu die IP des Containers geändert werden muss.
    * Sekundär im VLAN: Diese Methode kann angewendet werden, Sie benötigen jedoch kein sekundäres Gateway. Sie verlieren drei der IP-Adressen zur Angabe der Host-ID, des Gateways und der Broadcastfunktion.

Die Kosten pro IP können abhängig vom weitergeleiteten Typ variieren. Die Preise werden im Portal angezeigt, nachdem Sie den Server ausgewählt haben, für den die IP-Adressen bestellt werden. Das Routing kann jederzeit durch Öffnen eines Support-Tickets geändert werden.
{:tip}

## Kann ich einen Virtuozzo 3-Container ohne Ausfallzeiten auf einen Virtuozzo Containers 4-Hardwareknoten migrieren?
{: #can-i-migrate-a-virtuozzo-3-container-to-a-virtuozzo-container-4-hardware-node-with-no-downtime-}

**Linux:**

Ja. Standardmäßig wird Virtuozzo Containers for Linux 4 im Kompatibilitätsmodus ausgeführt. Wenn Virtuozzo Containers nicht im Kompatibilitätsmodus ausgeführt wird, können Sie das Produkt mit dem folgenden Befehl über SSH auf dem Hardwareknoten starten.

`/usr/sbin/vzagent_compat_ctl start`

**Windows (nicht unterstützt):**

Mit Virtuozzo Containers 4 Windows treten während der Überführung Ausfallzeiten (zwischen 20 Sekunden und 5 Minuten) auf, weil keine Onlineoption für die Übergabe vorhanden ist.

Wenn Sie Virtuozzo 3.0 for Windows verwenden, dann müssen Sie ein Upgrade für den Hardwareknoten auf mindestens Virtuozzo 3.5.1 for Windows ausführen.

Virtuozzo Containers 4 for Windows wird standardmäßig im Kompatibilitätsmodus ausgeführt.

Die Wiederherstellung von Sicherungen von Version 3.5.1 auf einem Virtuozzo Containers 4-Hardwareknoten kann auch ausgeführt werden, indem die Gesamtsicherung auf den Zielknoten kopiert wird. Führen Sie hierzu die Datei `vzbackupsync.exe` aus und stellen Sie die Sicherung normal wieder her.

Stellen Sie sicher, dass auf dem neuen Hardwareknoten die gleichen Vorlagen installiert sind.
{:tip}

## Sind Container gegenüber anderen Containern und gegenüber dem Host isoliert?
{: #are-containers-isolated-from-each-other-and-the-host-}

Ihre Container sind in Bezug auf das Laden von LKMs und in Bezug auf die Ausführung von `chroot(“../../../”)` außerhalb der eigenen Umgebung beschränkt und können IPC-Ressourcen (Semaphorsets und gemeinsam genutzte Speichersegmente) nicht gemeinsam nutzen.

Es treten keine Sicherheitsprobleme mit der Art und Weise auf, in der Virtuozzo Ressourcen verwaltet und Prozesse isoliert. Mit einer Konfiguration mit einem überbrückten Netz können alle Container innerhalb desselben VLAN (auf demselben Hardwareknoten) den Datenverkehr der jeweils anderen Container anzeigen. Die Verwendung des Modus mit Routing wird dringend empfohlen.

## Warum kann ich RedHat- und SUSE OS-Vorlagen nicht in Virtuozzo zwischenspeichern?
{: #why-can-t-i-cache-redhat-and-suse-os-templates-in-virtuozzo-}

Möglicherweise können Probleme auftreten, wenn Sie RedHat- und SUSE OS-Vorlagen in Virtuozzo im Cache zwischenspeichern. Für diese Betriebssystemvorlagen ist eine zusätzliche Lizenzierung erforderlich, die in {{site.data.keyword.cloud_notm}} nicht verfügbar ist. Wenn Sie einen der folgenden Fehler erhalten, dann müssen Sie über eigene Konten bei den entsprechenden Lieferanten verfügen und die jeweiligen Variablen in '/etc/vztt/url.map' ('/etc/vztt/vztt.conf' in Virtuozzo 3) konfigurieren.

||||
|---|---|---|
|TIMESTAMP|Hier klicken zum Öffnen/Schließen der Operationsdetails. Aktualisierung des Caches für Betriebssystemvorlagen|**Fehlgeschlagen**|
|TIMESTAMP|Aktualisierung der Operation mit Env(s) "virtuozzo00.softlayer.local" und Paket(en) .redhat-as4-x86_64 wird gestartet||
|TIMESTAMP|Aktualisierung der Operation mit Env(s) "virtuozzo00.softlayer.local" wurde mit Fehlern abgeschlossen: Die Pakete können nicht aktualisiert werden: exec fehlgeschlagen: Fehler: URL $RH_SERVER/download/mirrors/redhat-as4 enthält nicht definierte Variable. Sie können diese Variable in '/etc/vztt/url.map' definieren.|**Fehlgeschlagen**|
|TIMESTAMP|Hier klicken zum Öffnen/Schließen der Operationsdetails. Aktualisierung des Caches für Betriebssystemvorlagen|**Fehlgeschlagen**|
|TIMESTAMP|Aktualisierung der Operation mit Env(s) "virtuozzo00.softlayer.local" und Paket(en) .redhat-el5-x86_64 wird gestartet||
|TIMESTAMP|Aktualisierung der Operation mit Env(s) "virtuozzo00.softlayer.local" wurde mit Fehlern abgeschlossen: Die Pakete können nicht aktualisiert werden: exec fehlgeschlagen: Fehler: URL $RH_SERVER/download/mirrors/redhat-el5 enthält nicht definierte Variable. Sie können diese Variable in '/etc/vztt/url.map' definieren. Weitere Details finden Sie im Virtuozzo-Installationshandbuch.|**Fehlgeschlagen**|
|TIMESTAMP|Hier klicken zum Öffnen/Schließen der Operationsdetails. Aktualisierung des Caches für Betriebssystemvorlagen|**Fehlgeschlagen**|
|TIMESTAMP|Aktualisierung der Operation mit Env(s) "virtuozzo00.softlayer.local" und Paket(en) .sles-10-x86_64 wird gestartet||
|TIMESTAMP|Aktualisierung der Operation mit Env(s) "virtuozzo00.softlayer.local" wurde mit Fehlern abgeschlossen: Die Pakete können nicht aktualisiert werden: exec fehlgeschlagen: Fehler: URL $SLES_SERVER/download/mirrors/suse-es10 enthält nicht definierte Variable. Sie können diese Variable in '/etc/vztt/url.map' definieren. Weitere Details finden Sie im Virtuozzo-Installationshandbuch.|**Fehlgeschlagen**|
|TIMESTAMP|Hier klicken zum Öffnen/Schließen der Operationsdetails. Aktualisierung des Caches für Betriebssystemvorlagen|**Fehlgeschlagen**|
|TIMESTAMP|Aktualisierung der Operation mit Env(s) "virtuozzo01.softlayer.local" und Paket(en) .redhat-as4-x86_64 wird gestartet||
|TIMESTAMP|Aktualisierung der Operation mit Env(s) "virtuozzo01.softlayer.local" wurde mit Fehlern abgeschlossen: Die Pakete können nicht aktualisiert werden: exec fehlgeschlagen: Fehler: URL $RH_SERVER/download/mirrors/redhat-as4 enthält nicht definierte Variable. Sie können diese Variable in '/etc/vztt/url.map' definieren. Weitere Details finden Sie im Virtuozzo-Installationshandbuch.|**Fehlgeschlagen**|
|TIMESTAMP|Hier klicken zum Öffnen/Schließen der Operationsdetails. Aktualisierung des Caches für Betriebssystemvorlagen|**Fehlgeschlagen**|
|TIMESTAMP|Aktualisierung der Operation mit Env(s) "virtuozzo01.softlayer.local" und Paket(en) .redhat-el5-x86_64 wird gestartet||
|TIMESTAMP|Aktualisierung der Operation mit Env(s) "virtuozzo01.softlayer.local" wurde mit Fehlern abgeschlossen: Die Pakete können nicht aktualisiert werden: exec fehlgeschlagen: Fehler: URL $RH_SERVER/download/mirrors/redhat-el5 enthält nicht definierte Variable. Sie können diese Variable in '/etc/vztt/url.map' definieren.|**Fehlgeschlagen**|
|TIMESTAMP|Hier klicken zum Öffnen/Schließen der Operationsdetails. Aktualisierung des Caches für Betriebssystemvorlagen          Fehlgeschlagen<br/><br/>Oct 29, 2008 01:16:56 PM     Aktualisierung der Operation mit Env(s) "virtuozzo01.softlayer.local" und Paket(en) .sles-10-x86_64 wird gestartet||
|TIMESTAMP|Aktualisierung der Operation mit Env(s) "virtuozzo01.softlayer.local" wurde mit Fehlern abgeschlossen: Die Pakete können nicht aktualisiert werden: exec fehlgeschlagen: Fehler: URL $SLES_SERVER/download/mirrors/suse-es10 enthält nicht definierte Variable.|**Fehlgeschlagen**|
|TIMESTAMP|Prozess wird abgeschlossen|**Fehlgeschlagen**|

## Warum kann ich ein Pingsignal an meinen Service VE senden, mich aber nicht über SSH (PIM/PMC) anmelden?
{: #why-can-i-ping-my-serviceve-but-cannot-log-in-to-via-ssh-pim-pmc-}

Vergewissern Sie sich, dass auf dem Hostknoten keine IP-Adressen außer denen zugeordnet sind, die im Portal aufgelistet werden (1 öffentliche IP und 1 private IP [Position - 10.0.0.0/8]).

Wenn Sie über Bereichsdateien verfügen, die auf dem Hostknoten konfiguriert sind, dann müssen Sie diese Dateien entfernen und den Netzbetrieb neu starten.

    [root@virtuozzo ~]# mv –vi /etc/sysconfig/network-scripts/eth[0-9]-range[0-9]* ~/.sl-orig-configs/

    [root@virtuozzo ~]# /sbin/service network restart


`vzcp` wurde im Service VE nicht gestartet. Starten Sie `vzcp` im Service VE über den Hardwareknoten.

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp status

    vzcp is stopped

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp start

    Starting vzcpd: [  OK  ]

    Starting vzcp: [  OK  ]

    [root@virtuozzo ~]# vzctl exec2 1 service vzcp status

    vzcp (pid 3775 3774 3771) is running...

    [root@virtuozzo ~]#
