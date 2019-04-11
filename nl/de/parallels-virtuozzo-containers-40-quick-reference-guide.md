---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization
---

# Parallels Virtuozzo Containers 4.0 - Kurzübersicht
{: #parallels-virtuozzo-containers-4-0-quick-reference-guide}

Verwenden Sie die folgenden Informationen, um die Virtuozzo-Containerdienstprogramme und allgemeinen Begriffe zu suchen.

## Allgemeine Dienstprogramme
{: #general-utilities}

Die allgemeinen Dienstprogramme dienen zur Ausführung von routinemäßigen Wartungsarbeiten.

|Allgemeine Dienstprogramme||
|---|---|
|vzctl|Dienstprogramm zur Containersteuerung.|
|vzlist|Dienstprogramm zum Anzeigen einer Liste der auf dem Knoten existierenden Container mit zusätzlichen Informationen.|
|vzquota|Dienstprogramm zur Steuerung der Datenträgerkontingente für Virtuozzo-Container.|
{: caption="Tabelle 1. Allgemeine Dienstprogramme" caption-side="top"}

## Lizenzierungsdienstprogramme
{: #licensing-utilities}

Die Lizenzierungsdienstprogramme werden zur Installation einer neuen Lizenz, zum Anzeigen des Lizenzstatus oder zum Generieren eine Lizenzanforderung für eine neue Lizenz verwendet.

|Lizenzierungsdienstprogramme||
|---|---|
|vzlicview|Dienstprogramm zum Anzeigen des Virtuozzo-Lizenzstatus und der entsprechenden Parameter.|
|vzlicload|Dienstprogramm zum Verwalten von Virtuozzo-Lizenzen auf dem Hardwareknoten.|
|vzlicupdate|Dienstprogramm zum Aktivieren der Installation für die Virtuozzo-Container, zum Aktualisieren der Virtuozzo-Lizenzen, die auf dem Hardwareknoten installiert sind, oder zum Übertragen der Virtuozzo-Lizenz vom Quellen- auf den Zielknoten.|
{: caption="Tabelle 2. Lizenzierungsdienstprogramme" caption-side="top"}

## Dienstprogramme für die Containermigration
{: #container-migration-utilities}

Die Tools für die Containermigration werden zum Migrieren von Containern zwischen verschiedenen Hardwareknoten oder innerhalb eines einzelnen Hardwareknotens verwendet.

|Dienstprogramme für die Containermigration||
|---|---|
|vzmigrate|Dienstprogramm zum Migrieren von Containern von einem Hardwareknoten auf einen anderen.|
|vzmlocal|Dienstprogramm für das lokale Klonen oder Verschieben der Container.|
|vzp2v|Dienstprogramm zum Migrieren eines physischen Servers in einen Container auf dem Knoten.|
|vzv2p|Dienstprogramm zum Migrieren eines Containers auf einen physischen Server.|
{: caption="Tabelle 3. Dienstprogramme für die Containermigration" caption-side="top"}

## Dienstprogramme für die Containersicherung
{: #container-backup-utilities}

Die Dienstprogramme für die Containersicherung werden zum Sichern und Wiederherstellen der privaten Containerbereiche, Konfigurationsdateien, Aktionsscripts und Kontingentinformationen verwendet.

|Dienstprogramme für die Containersicherung||
|---|---|
|vzbackup|Dienstprogramm zum Sichern von Containern.|
|vzrestore|Dienstprogramm zum Wiederherstellen gesicherter Container.|
|vzabackup|Dienstprogramm zum Sichern von Hardwareknoten und der zugehörigen Container. Für dieses Dienstprogramm ist die Software Parallels Agent erforderlich.|
|vzarestore|Dienstprogramm zum Wiederherstellen gesicherter Hardwareknoten und Container. Für dieses Dienstprogramm ist die Software Parallels Agent erforderlich.|
{: caption="Tabelle 4. Dienstprogramme für die Containersicherung" caption-side="top"}

## Dienstprogramme für das Vorlagenmanagement
{: #template-management-utilities}

Die Tools für das Vorlagenmanagement werden verwendet, um Vorlagen zu erstellen und Anwendungen zu verwalten und in einem Container zu installieren.

|Dienstprogramme für das Vorlagenmanagement||
|---|---|
|vzpkg|Dienstprogramm zum Verwalten von Betriebssystem- und Anwendungs-EZ-Vorlagen in Ihren Containern oder auf dem Hardwareknoten selbst.|
|vzmktmpl|Dienstprogramm zum Erstellen von Betriebssystem- und Anwendungs-EZ-Vorlagen.|
|vzveconvert|Dienstprogramm zum Konvertieren von Containern auf Basis von Virtuozzo-Standardvorlagen in vorlagenbasierte EZ-Container.|
|vzpkgproxy|Dienstprogramm zum Erstellen von Caching-Proxy-Servern für die Handhabung von Betriebssystem- und Anwendungs-EZ-Vorlagen.|
|vzrhnproxy|Dienstprogramm zum Erstellen von RHN-Proxy-Servern für die Handhabung der Pakete, die in den Betriebssystem-EZ-Vorlagen für RHEL 4 und RHEL 5 enthalten sind.|
|vzpkgls|Dienstprogramm zum Abrufen einer Liste der Vorlagen, die auf dem Hardwareknoten und in Containern verfügbar sind.|
|vzpkginfo|Dienstprogramm zum Abrufen der Informationen zu einer beliebigen Vorlage, die auf dem Hardwareknoten installiert ist.|
|vzpkgcreat| Dienstprogramm zum Erstellen einer neuen Paketgruppe aus binären RPM- oder DEB-Dateien.|
|vzpkgadd|Dienstprogramm zum Hinzufügen einer Vorlage zu einem Container.|
|vzpkglink|Dienstprogramm zum Ersetzen realer Dateien in einem Container durch symbolische Links zu den Dateien auf dem Knoten.|
|vzpkgrm|Dienstprogramm zum Entfernen einer Vorlage aus einem Container.|
|vzpkgcache|Dienstprogramm zum Aktualisieren einer Gruppe vorinstallierter Containerarchive nach der Installation einer neuen Vorlage.|
{: caption="Tabelle 5. Dienstprogramme für das Vorlagenmanagement" caption-side="top"}

## Ergänzende Tools
{: #supplementary-tools}

Die ergänzenden Tools werden zur Ausführung weiterer Tasks auf dem Hardwareknoten und im Containerkontext verwendet.

|Ergänzende Tools||
|---|---|
|vzup2date|Dienstprogramm zum Aktualisieren Ihrer Virtuozzo-Software und -Vorlagen.|
|vzup2date-mirror|Dienstprogramm zum Erstellen lokaler Spiegel des offiziellen Virtuozzo-Repositorys.|
|vzfsutil|Dienstprogramm für die VZFS-Optimierung und -Konsistenzprüfung.|
|vzcache|Dienstprogramm zur Gewinnung zusätzlichen Plattenspeicherplatzes durch Zwischenspeicherung identischer Dateien in unterschiedlichen Containern im Cache.|
|vzsveinstall|Dienstprogramm zum Erstellen des Service-Containers auf dem Hardwareknoten.|
|vzsveupgrade|Dienstprogramm zum Aktualisieren der Pakete im Service-Container.|
|vzps|Dienstprogramm zur Verwendung als Standarddienstprogramme ps und htop mit zusätzlichen Containerfunktionen.|
|vztop|Dienstprogramm zur Verwendung als Standarddienstprogramme ps und htop mit zusätzlichen Containerfunktionen.|
|vzsetxinetd|Dienstprogramm zum Umschalten des Modus für bestimmte Services zwischen dem Standalone-Modus und dem Modus `xinetddependent`.|
|vzdqcheck|Dienstprogramm zur Darstellung der aktuellen Auslastung des Dateispeicherplatzes in Bezug auf das geltende Kontingent.|
|vzdqdump|Dienstprogramm zum Erstellen eines Containerspeicherauszugs für die Benutzer- und Gruppenkontingentgrenzwerte und die Karenzzeiten des Kernels bzw. der Kontingentdatei oder zum Laden dieser Komponenten in eine Kontingentdatei.|
|vzdqload|Dienstprogramm zum Erstellen eines Containerspeicherauszugs für die Benutzer- und Gruppenkontingentgrenzwerte und die Karenzzeiten des Kernels bzw. der Kontingentdatei oder zum Laden dieser Komponenten in eine Kontingentdatei.|
|vznetstat|Dienstprogramm zum Ausgeben der Nutzungsstatistiken nach Containern für den Netzdatenverkehr.|
|vzcpucheck|Dienstprogramm zur Überprüfung der CPU-Auslastung nach Containern.|
|vzmemcheck|Dienstprogramm zur Überprüfung der aktuellen Speicherparameter für Hardwareknoten und Container.|
|vzcalc|Dienstprogramm zur Berechnung der Ressourcennutzung durch einen Container.|
|vzcheckovr|Dienstprogramm zur Überprüfung der aktuellen Überbelegung des Systems und Sicherheit der Einstellungen für die Steuerung aller Ressourcen.|
|vzstat|Dienstprogramm zur Überwachung der Hardwareknoten- und Containerressourcenauslastung in Echtzeit.|
|vzpid|Dienstprogramm zum Ausgeben der Container-ID, zu der der Prozess gehört.|
|vzsplit|Dienstprogramm zum Generieren des Beispiels für die Containerkonfigurationsdatei und zur 'Aufteilung' des Hardwareknotens in gleiche Teile.|
|vzcfgscale|Dienstprogramm zum Skalieren der Containerkonfiguration.|
|vzcfgvalidate|Dienstprogramm zum Validieren der Richtigkeit der Containerkonfigurationsdatei.|
|vzcfgconvert|Dienstprogramm zum Konvertieren von Virtuozzo 2.0.2-Containerkonfigurationsdateien ins Virtuozzo 2.5.x-Format.|
|vzstatrep|Dienstprogramm zum Analysieren der Protokolle, die von vzlmond erfasst werden, und zum Generieren von Statistikberichten auf Basis dieser Protokolle (im Text- und Grafikformat).|
|vzreport|Dienstprogramm zum Zusammenstellen eines Fehlerberichts und zum automatischen Senden des Fehlerberichts an das Support-Team von Parallels.|
|vzhwcalc|Dienstprogramm zum Scannen der Hauptressourcen auf einem beliebigen Linux-Server und zum Erstellen einer Datei, in der diese Informationen angegeben sind.|
|vzveconvert|Dienstprogramm zum Konvertieren von Containern auf Basis von Virtuozzo-Standardbetriebssystemvorlagen in vorlagenbasierte EZ-Komponenten.|
|vznetcfg|Dienstprogramm zum Verwalten von Netzeinheiten auf dem Hardwareknoten.|
|vzmtemplate|Dienstprogramm zum Migrieren der installierten Betriebssystem- und Anwendungsvorlagen von einem Hardwareknoten auf einen anderen.|
{: caption="Tabelle 6. Ergänzende Tools" caption-side="top"}

## Allgemeine Begriffe
{: #common-terms}

|Allgemeine Begriffe||
|---|---|
|Hardwareknoten|Ein Hardwareknoten ist ein physischer Computer, der zum Hosten von Containern dient.|
|Container|Ein Container ist funktional identisch mit einem isolierten Standalone-Server mit eigenen IP-Adressen, Prozessen, Dateien, Benutzern sowie einer eigenen Version, eigenen Konfigurationsdateien, eigenen Anwendungen, Systembibliotheken und weiteren Serverfunktionen. Container nutzen Hardwareknoten und denselben Betriebssystemkernel gemeinsam, sind jedoch voneinander isoliert. Jeder Container verfügt über eine eindeutige numerische ID, die mit 1 beginnt.|
|Hostbetriebssystem|Bei einem Hostbetriebssystem (oder Host) handelt es sich um ein Betriebssystem, das auf dem Hardwareknoten installiert ist. Es wird auch als Container 0 im Gegensatz zu realen Containern bezeichnet, die über IDs verfügen, die mit 1 beginnen.|
|Vorlage|Eine Vorlage (oder Paketgruppe) ist eine Gruppe von Originalanwendungsdateien, die neu gepackt werden, um mit einer Mountoperation über Virtuozzo File System (VZFS) angehängt zu werden. In Virtuozzo stehen zwei Vorlagentypen zur Verfügung: Betriebssystemvorlagen, die zum Erstellen neuer Container auf deren Basis verwendet werden können, und Anwendungsvorlagen, die nach der Erstellung zu Containern hinzugefügt werden können.|
|PIM|Parallels Infrastructure Manager ist ein webbasiertes Management-Tool für Hostadministratoren.|
|PMC|Parallels Management Console ist ein Tool für das Remote Management mit einer grafischen Benutzerschnittstelle für Hostadministratoren.|
|PPP|Parallels Power Panel ist ein webbasiertes Management-Tool für Containereigner.|
{: caption="Tabelle 7. Allgemeine Begriffe" caption-side="top"}
