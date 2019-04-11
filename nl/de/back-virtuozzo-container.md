---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-14"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Virtuozzo-Container sichern
{: #backing-up-a-virtuozzo-container}

1. Vergewissern Sie sich, dass beide Hardwareknoten in PIM (Parallels Infrastructure Manager) konfiguriert wurden.
2. Rufen Sie die Infrastruktur und die Hardwareknoten auf, in der bzw. auf denen der Container sich befindet.
3. Wählen Sie den entsprechenden Container aus und klicken Sie auf **Sichern**.
4. Fügen Sie einen entsprechenden Kommentar hinzu, der den Grund für die Sicherung dieser Daten beschreibt.
5. Wählen Sie den Sicherungstyp aus:
   * Verwendung der Hardwareknoteneinstellungen
   * Gesamtsicherung (im vorliegenden Beispiel)
   * Inkrementelle Sicherung
   * Differenzsicherung
6. Wählen Sie die Komprimierungsstufe aus. **Hinweis:** Eine höhere Komprimierung kann Plattenspeicherplatz einsparen, führt jedoch zur Verwendung von mehr CPU-Ressourcen.
   * Verwendung der Hardwareknoteneinstellungen
   * Keine
   * Normal (Auswahl der normalen minimierten Serverauslastung)
   * Hoch
   * Maximum
7. Wählen Sie den Hardwareknoten aus, auf dem die Sicherungsdatei gespeichert werden soll. Wenn Sie über mehrere Hardwareknoten verfügen, dann sollten Sie die Sicherungen auf diesen Knoten anstatt auf demselben Server sichern.
  * Die Auswahl weiterer Optionen unterliegt der Entscheidung des Administrators.
8. Sicherung. Die Sicherung ist abgeschlossen. Sie können die Details der Sicherung anzeigen, indem Sie die Registerkarte **Hardwareknoten, Container und Sicherungen** aufrufen und die gewünschte Sicherung auswählen.
