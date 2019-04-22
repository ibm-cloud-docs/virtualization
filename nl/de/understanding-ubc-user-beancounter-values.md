---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Informationen zu UBC-Werten (UBC = User Bean Counter)
{: #understanding-ubc-user-beancounter-values}

Wenn die Informationen zu UBC nicht interpretiert werden können, dann verwenden Sie den SLM-Modus. SLM ist einfacher strukturiert und bietet den gleichen Grad an Steuerungsmöglichkeiten.

|Name|Typ|Beschreibung|
|---|---|---|
|*Wichtigste Parameter*|||
|`numproc`|*System*|Anzahl der Prozesse und Threads|
|`numtcpsock`|*System*|Anzahl der TCP-Sockets|
|`numothersock`|*System*|Anzahl anderer Sockets (nicht TCP)|
|`vmguardpages`|*System*|Speicherzuordnungsgarantie|
|`cpuunits`|*cpu*|CPU-Leistung|
|`diskspace`|*Platte*|Plattenspeicherkontingent|
|`rate`|*Netz*|Netzbandbreite|
|`ratebound`|*Netz*|Ist die Netzbandbreite begrenzt?|
|*Zusätzliche Parameter*|||
|`kmemsize`|*System*|Größe des nicht auslagerbaren Kernelspeichers, der für Prozesse in diesem Container zugeordnet wurde|
|`tcpsndbuf`|*System*|Gesamtgröße der TCP-Sendepuffer|
|`tcprcvbuf`|*System*|Gesamtgröße der TCP-Empfangspuffer|
|`othersockbuf`|*System*|Gesamtgröße der Socketpuffer für UNIX-Domäne, UDP und andere Sendepuffer für Datagrammprotokoll|
|`dgramrcvbuf`|*System*|Empfangspuffer von UDP und anderen Datagrammprotokollen|
|`oomguardpages`|*System*|Garantierte Speicherkapazität bei Überbelegung des Speichers (Kill-Garantie bei Speicherengpass)|
|`privvmpages`|*System*|Speicherzuordnungsgrenzwert|
|`lockedpages`|*System*|Nicht auslagerbare Prozessseiten (mit [mlock(2) ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://linux.die.net/man/2/mlock) gesperrte Seiten)|
|`shmpages`|*System*|Gesamtgröße des gemeinsam genutzten Speichers (Interprozesskommunikation oder IPC, gemeinsam genutzte anonyme Zuordnungen und tmpfs-Objekte) in Seiten|
|`physpages`|*System*|Gesamtzahl der von Prozessen belegten Arbeitsspeicherseiten|
|`numfile`|*System*|Anzahl offener Dateien|
|`numflock`|*System*|Anzahl der Dateisperren|
|`numpty`|*System*|Anzahl der Pseudoterminals|
|`numsiginfo`|*System*|Anzahl der Siginfo-Strukturen|
|`dcachesize`|*System*|Gesamtgröße der im Speicher gesperrten Dentry- und I-Node-Strukturen|
|`numiptent`|*System*|Anzahl der NETFILTER-Einträge (IP-Paketfilterung)|
|`cpulimit`|*CPU*|Grenzwert für CPU-Belegung|
|`diskinodes`|*Platte*|I-Node-Kontingent der Platte (Dateisystemobjekt)|
|`quotatime`|*Platte*|Karenzzeit für Plattenkontingentüberschreitung|
|`quotaugidlimit`|*Platte*|Grenzwert für Anzahl der UID- und GID-Abrechnungseinträge|
<caption>Tabelle 1. SLM-Parameterbeschreibungen</caption>

Sie können auf die Datei 'VzLinuxUBCMgmt.pdf' zugreifen oder diese Datei im HTML-Format in PIM (Parallels Infrastructure Manager) auf Ihrem Server wie folgt anzeigen: **Management > Support > Downloads > Administratorhandbuch zum Management von UBC-Ressourcen**

Weitere Informationen zu UBC finden Sie in [OpenVZ Virtuozzo Containers, Category: UBC ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://wiki.openvz.org/Category:UBC).
**Hinweis:** Die Funktionen von OpenVZ und Virtuozzo sind nicht alle gleich.
