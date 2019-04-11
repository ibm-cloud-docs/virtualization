---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-05"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Anwendung 'Veeam Backup and Replication' aktualisieren
{: #updating-veeam-backup-and-replication-application}

## Übersicht
{: #overview}

Führen Sie die folgenden Schritte aus, um die Anwendung 'Veeam Backup and Replication' zu aktualisieren, die auf Ihrem Server ausgeführt wird, wenn neue Aktualisierungen für das Produkt verfügbar sind.

### Voraussetzungen
{: #prerequisites-updating-veeam-backup-and-replication-application}

* Ein kostenloses Veeam-Konto. Sie können sich über die
[Veeam-Registrierung ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.veeam.com/signin.html) anmelden.
* Internet Explorer, Chrome, Firefox oder Safari.

### Nach Aktualisierungen suchen
{: #checking-for-updates}

Die Anwendung 'Veeam Backup and Replication' umfasst ein Prüfprogramm für Aktualisierungen. Um sicherzustellen, dass Sie über Aktualisierungen informiert werden, rufen Sie das Menüsymbol oben links auf und klicken Sie dann auf **Allgemeine Optionen** > **Benachrichtigungen**. Vergewissern Sie sich, dass die Option zum **Suchen nach Produkt- und Hypervisoraktualisierungen in regelmäßigen Zeitabständen** ausgewählt ist.

### Veeam aktualisieren
{: #updating-veeam}

Führen Sie die folgenden Schritte aus, um mit der Aktualisierung zu beginnen.
1. Öffnen Sie die Ansicht für **Sicherungsinfrastruktur**.
2. Rufen Sie im Teilfenster für den Bestand die Option **Knoten verwalteter Server** > **Fehlende Aktualisierungen** auf.
3. Klicken Sie auf den Link für die Aktualisierung.

Folgende Faktoren sollten vor dem Starten der Aktualisierung berücksichtigt werden:

* Vergewissern Sie sich, dass die aktuelle Ausführung für alle Jobs abgeschlossen ist. Wenn Jobs fehlgeschlagen sind, dann führen Sie sie erneut aus.
* Vergewissern Sie sich, dass momentan keine Jobs (einschließlich Wiederherstellungssitzungen, Instant VM Recovery-Sitzungen und SureBackup-Jobs) ausgeführt werden. Das Stoppen momentan aktiver Jobs ist nicht zu empfehlen.
* Inaktivieren Sie vorübergehend alle in regelmäßigen Zeitabständen ausgeführten Jobs und alle Jobs zum Erstellen von Sicherungskopien, um zu verhindern, dass diese Jobs während der Durchführung der Aktualisierung gestartet werden.

### Nächste Schritte
{: #next-steps-updating-veeam-backup-and-replication-application}

Sie können jetzt die heruntergeladene Aktualisierung extrahieren und auf das Veeam-Aktualisierungsprogramm doppelklicken. Die Aktualisierung wird im Push-Modus sowohl auf den lokalen Veeam-Server als auch auf die Hypervisor übertragen, auf denen sich der Veeam-Agent befindet. Nach Abschluss des Prozesses müssen Sie Veeam Backup and Replication starten und die Sicherungsjobs wieder aktivieren.
