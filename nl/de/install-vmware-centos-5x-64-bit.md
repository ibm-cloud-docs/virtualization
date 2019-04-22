---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# VMware unter CentOS 5.x (64-Bit) installieren
{: #installing-vmware-on-centos-5-x-64-bit-}

Bevor Sie beginnen, müssen Sie sich beim Server als Rootbenutzer anmelden.

## Vorbereiten der VMware-Installation
{: #preparing-to-install-vmware}

1. Laden Sie das [VMware-Installationsprogramm ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://vmware.com/download/server/){: new_window} herunter. Klicken Sie auf den Download-Link, akzeptieren Sie den Endbenutzerlizenzvertrag und laden Sie LinuxTarball (im vorliegenden Beispiel 'VMware-server-1.0.3-44356.tar.gz') herunter:

* `# wget –O vmware-server.tar.gz https://download3.vmware.com/software/vmserver/VMware-server-1.0.3-44356.tar.gz`

2. Nach dem Herunterladen der Software müssen Sie einen Lizenzschlüssel anfordern (der in der freien Version von VMWare Server kostenlos ist). Informationen zur Registrierung finden Sie im Abschnitt zu [VMware ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://register.vmware.com/content/registration.html){: new_window}.

3. Extrahieren Sie die TAR-Datei:

* `# tar -xzvf vmware-server.tar.gz`

## Vorbereitende Schritte für die Installation
{: #before-you-begin-installation}

Bevor Sie mit der Installation beginnen, sollten Sie die folgenden Voraussetzungen prüfen:

```
# yum update
# yum install libXtst.i386
# yum install libXrender.i386
# yum install xinetd
```

## VMWare Server installieren
{: #installing-vmware-server}

1. Rufen Sie das folgende Verzeichnis auf:

* `# cd vmware-server-distrib/`

2. Führen Sie das VMware-Installationsscript aus:

* `# ./vmware-install.pl`

3. Während der Installation müssen Sie grundlegende Fragen beantworten und die Verzeichnisse angeben, in denen bestimmte Teile von VMware installiert werden sollen. Übernehmen Sie hier die Standardwerte. Akzeptieren Sie die Lizenzvereinbarung, um mit der Installation fortzufahren.

4. Möglicherweise erhalten Sie folgende Frage: 'Keines der vordefinierten vmmon-Module für VMware Server eignet sich für Ihren aktiven Kernel. Soll das Programm das vmmon-Modul für Ihr System erstellen (dazu muss auf dem System ein C-Compiler installiert sein)?'
* Antworten Sie mit **Ja** auf diese Frage (Standardwert).

## VMware-Netzbetrieb einrichten
{: #setting-up-vmware-networking}

Sie werden aufgefordert, die folgenden Fragen zu beantworten:

1. Sollen Ihre virtuellen Maschinen ins Netz eingebunden werden?
* Antworten Sie mit **Ja**. Daraufhin wird eine Netzkonfiguration für die öffentliche Netzeinheit erstellt, damit Sie über Ihre virtuellen Maschinen auf das Internet zugreifen können.

2. Ihr Computer verfügt über mehrere Ethernet-Schnittstellen: 'eth0', 'eth1'. Welche soll als Brücke zu 'vmnet0' verwendet werden?
* Geben Sie 'eth1' ein und drücken Sie dann die Eingabetaste. Beachten Sie hierbei, dass alle {{site.data.keyword.cloud}}-Server so eingerichtet sind, dass das öffentliche Netz auf 'eth1' und das private Netz auf 'eth0' ausgeführt wird. In VMware ist die Standardbrückeneinheit für 'vmnet0' die Einheit 'eth0'. Geben Sie 'eth1' ein, anstatt die Eingabetaste zu drücken.

## Privates Netz überbrücken
{: #bridging-the-private-network}

Sie erhalten die folgende Frage, die mit **Ja** oder **Nein** beantwortet werden kann:
*Wollen Sie ein weiteres überbrücktes Netz konfigurieren?

Wenn Sie beabsichtigen, Services oder andere Anwendungen in Ihrem privaten Netz auszuführen, antworten Sie mit 'Ja' auf diese Frage (es sei denn, Sie wissen, dass Sie das private Netz nicht verwenden). Das System erstellt eine Netzbrücke zu Ihrem privaten Netz. Nachdem Sie die Eingabetaste gedrückt haben, wird automatisch 'eth0' als Schnittstelle verwendet, da dies die einzige verfügbare Schnittstelle ist (da Sie nur zwei Netzkarten im Server haben).

## Weitere Netzbetriebseinstellungen
{: #other-networking-settings}

Sie erhalten einige weitere Fragen zur Netzkonfiguration des VMware Server-Systems. Fahren Sie mit den folgenden Empfehlungen fort:

* *Wollen Sie den NAT-Netzbetrieb auf Ihren virtuellen Maschinen verwenden?*

- Antworten Sie mit 'Ja' auf diese Frage.

* *Soll dieses Programm nach einem nicht verwendeten privaten Teilnetz suchen?*

- Antworten Sie mit 'Ja' auf diese Frage.

- Nach Abschluss dieses Prozesses sollten Sie sicherstellen, dass kein weiteres NAT-Netz konfiguriert wird.

* *Wollen Sie den reinen Host-Netzbetrieb auf Ihren virtuellen Maschinen nutzen?*

- Antworten Sie mit 'Ja' auf diese Frage.

* *Soll dieses Programm nach einem nicht verwendeten privaten Teilnetz suchen?*

- Antworten Sie mit 'Ja' auf diese Frage.

- Nach Abschluss dieses Prozesses sollten Sie sicherstellen, dass kein weiteres reines Hostnetz konfiguriert wird.

## Empfangsport angeben
{: #specifying-listening-port}

Die nächste Frage betrifft den Port, an dem das VMware Server-System empfangsbereit sein soll. Sie können den Standardport '904' übernehmen.

## Virtuelle Maschinen speichern
{: #storing-the-virtual-machines}

Die nächste Frage, die vom Installationsprogramm gestellt wird, lautet *In welchem Verzeichnis sollen die Dateien Ihrer virtuellen Maschine aufbewahrt werden?*. Die Standardspeicherposition ist '/var/lib/vmware/virtual machines'. Stellen Sie sicher, dass die virtuellen Maschinen an einem Ort platziert werden, an dem genügend Plattenspeicherplatz verfügbar ist, z. B. auf einem redundanten RAID-Array oder auf einer großen sekundären Festplatte. Vergewissern Sie sich stets, dass genügend Speicherplatz für eine virtuelle Maschine zur Verfügung steht. In diesem Fall können Sie den Mountpunkt '/data/vm' verwenden, um einen Mount an einer großen Platte durchzuführen.

## VMware-Seriennummer angeben
{: #providing-the-serial-number-for-vmware}

Der abschließende Teil der Installation erfordert die Eingabe eines VMware-Lizenzschlüssels und einer Seriennummer. Wenn Sie noch nicht über einen Lizenzschlüssel verfügen, dann rufen Sie die [VMware-Site ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://register.vmware.com/content/registration.html){: new_window} auf. Wenn für dieses VMware Server-System eine Seriennummer verfügbar ist, dann geben Sie sie an der Eingabeaufforderung ein und drücken Sie dann die Eingabetaste.

Anschließend wird die folgende Anweisung angezeigt:

    “The configuration of VMware Server 1.0.3 build-44356 for Linux for this running kernel completed successfully.”

Auf Ihrem Server ist VMware nun eingerichtet. Jetzt müssen Sie VMware Server Console herunterladen, bei der es sich um den GUI-Client für VMware Server handelt. Außerdem müssen Sie die virtuellen Maschinen konfigurieren und installieren.

## VMware Server Console herunterladen
{: #downloading-vmware-server-console}

Bei VMware Server Console handelt es sich um die Clientanwendung für VMware Server. Sie können die Konsole verwenden, um das VMware Server-System zu verwalten, in dem Sie virtuelle Maschinen erstellen, konfigurieren und installieren können. Zur Installation dieser Anwendung müssen Sie das Paket für den VMware Server Windows-Client über die [Website für die VMware-Downloads ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://vmware.com/download/server/){: new_window} herunterladen. Dieses Paket wird als ZIP-Datei bereitgestellt. Nachdem der Download abgeschlossen ist, müssen Sie das Paket extrahieren und die Datei 'VMware-console-1.0.3-x' installieren. Nach Abschluss der Installation ist die Installation von VMware Server Console abgeschlossen und Sie sind bereit zur Konfiguration von VMware Server.

## Bei VMware-Konsole anmelden
{: #logging-in-to-the-vmware-console}

Öffnen Sie VMware Server Console auf dem Computer, auf dem die Installation ausgeführt wurde. Während des Ladevorgangs wird eine Anzeige zum Wechseln des Hosts (Anmeldung) angezeigt. VMware Server verwendet den Linux-Systembenutzernamen und das zugehörige Kennwort zur Benutzerauthentifizierung. Sie müssen demzufolge die Benutzernamen (insbesondere Root) verwenden, um die Anmeldung bei VMware durchzuführen. Verwenden Sie die folgenden Berechtigungsnachweise:

* **Hostname:** IP-Adresse plus Port (z. B. 67.228.160.201:904)<br />
* **Benutzername:** root<br />
* **Kennwort:** Kennwort (reales Rootkennwort des Systems verwenden)

## Firewallregeln (IPTables) konfigurieren
{: #configuring-the-firewall-rules-iptables-}

Wenn bei der Herstellung der Verbindung zu VMware Server Probleme auftreten und wenn es sich dabei nicht um ein Authentifizierungsproblem handelt (d. h., wenn ein Problem aufgrund eines falschen Benutzernamens und Kennworts auftritt), dann wird die Verbindung zu VMware Server möglicherweise durch die Firewall blockiert. Versuchen Sie den Fehler zu beheben, indem Sie die folgende IPTable-Regel zu '/etc/sysconfig/iptablesfile' hinzufügen. **Hinweis:** Stellen Sie sicher, dass die Namenskonvention den entsprechenden Vorgaben Ihrer Serverkonfiguration entspricht:

- `# -A FWALL-INPUT -p tcp -m tcp -s 0/0 --dport 904 -j ACCEPT`
