---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-10"

subcollection: virtualization

keywords: hypervisor
---
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:table: .aria-labeledby="caption"}

# Hyper-V in Active Directory-Umgebung ausführen
{: #running-hyper-v-in-an-active-directory-environment}

<!--Running Hyper-V in an Active Directory environment is the best implementation of Hyper-V. Microsoft has truly shined with the ability to remotely manage a server.You can remotely manage the Hyper-V server allows for it to be installed on a Core installation of Windows freeing up those valuable resources from the system for use within the VM’s. When this is combined with an Active Directory Domain Controller
You manage all your Hyper-V servers from a single Hyper-V Manager that runs on any 2008 or Vista computer that is connected to the Domain.--> <!--Vista?? MS doesn't support Vista.-->

## Vorbereitende Schritte
{: #before-you-begin-running-hyper-v-in-an-active-directory-environment}

Bevor Sie mit der Ausführung von Hyper-V in einer Active Directory-Umgebung beginnen, benötigen Sie Folgendes:

* Windows 2008 Datacenter-Server (64-Bit), auf dem Hyper-V (vollständige Installation oder Kerninstallation des Betriebssystems) ausgeführt wird.
* Eine globale Gruppe in der Domäne, die für die Verwaltung von Hyper-V verwendet wird.
* Domänenadministratorzugriff zum Aktivieren von Änderungen auf dem Hyper-V-Server über den Computer, auf dem Sie arbeiten.

## Hyper-V-Server konfigurieren
{: #configuring-the-hyper-v-server}

1. Öffnen Sie eine Managementverbindung zum Hyper-V-Server.

2. Fügen Sie die Gruppe **Hyper-V** zur Gruppe **Distributed COM-Benutzer** hinzu.

3. Fügen Sie die Gruppe **Hyper-V** zu den Namensbereichen **CIMV2 und Virtualisierung** hinzu.

4. Fügen Sie die Gruppe **Hyper-V** zum Autorisierungsspeicher für Hyper-V auf dem Hyper-V-Server hinzu.

5. Geben Sie die Berechtigungen der Gruppe **Hyper-V** für das Hyper-V-Verzeichnis auf dem Hyper-V-Server an.

## Remote-Management-Verbindung einrichten
{: #setting-up-remote-management-connection}

Vergewissern Sie sich, dass Sie auf einem Computer angemeldet sind, der sich in der Domäne mit den Domänenadministratorberechtigungen befindet.

1. Rufen Sie **Systemsteuerung > Verwaltung > Computerverwaltung** auf.

2. Wählen Sie im Menü 'Aktion' die Option **Verbindung mit anderem Computer herstellen** aus.

3. Geben Sie den Servernamen oder die IP-Adresse ein und klicken Sie auf **OK**.

## Gruppe "Distributed COM-Benutzer" zum Hyper-V-Server hinzufügen
{: #adding-distributed-com-users-group-to-the-hyper-v-server}

Sie müssen zunächst die Gruppe 'Hyper-V' zur Gruppe 'Distributed COM-Benutzer' auf dem Hyper-V-Server hinzufügen.

1. Rufen Sie **Systemprogramme > Lokale Benutzer und Gruppen > Gruppen > Distributed COM-Benutzer > Zu Gruppe hinzufügen** auf.

2. Klicken Sie auf **Hinzufügen** und geben Sie den Gruppennamen für die Gruppe 'Hyper-V' ein und klicken Sie dann auf **OK**.

## Remotezugriff auf Server für Namensbereiche CIMV2 und Virtualisierung erteilen
{: #granting-remote-access-to-the-server-for-cimv2-and-virtualization-namespaces}

Sie können die Berechtigungen für den Remotezugriff auf den Server für die Virtualisierung und für CIMV2 aktualisieren.

1. Wählen Sie im Fenster 'Computerverwaltung' die Optionen **Service und Anwendungen > WMI-Steuerung** aus.

2. Klicken Sie mit der rechten Maustaste und rufen Sie dann **Eigenschaften > Sicherheit > Root > CIMV2 > Sicherheit** auf.

3. Fügen Sie die Gruppe 'Hyper-V' hinzu, wählen Sie sie dann aus und klicken Sie auf **Erweitert**.

4. Vergewissern Sie sich, dass die neue Gruppe ausgewählt ist, und klicken Sie dann auf **Bearbeiten**.

5. Ändern Sie **Anwenden auf:** in **Namensbereiche und alle Unternamensbereiche**.

6. Stellen Sie für **Konto aktivieren und Remoteaktivierung** sicher, dass **Zulassen** ausgewählt ist.

7. Wählen Sie **Berechtigungen nur für Objekte und/oder Container in diesem Container übernehmen** aus und klicken Sie auf **OK**.

8. Wiederholen Sie diese Schritte für die Virtualisierung.

## Autorisierungsspeicher aktualisieren
{: #updating-authorization-store}

Der Autorisierungsspeicher für Hyper-V ist die letzte Komponente, die Hyper-V eine Domänengruppe zuordnet.

1. Öffnen Sie den Autorisierungsmanager, indem Sie den Befehl `azman.msc` über das Menü 'Ausführen' oder die Eingabeaufforderung ausführen.

2. Wählen Sie im Menü 'Aktion' die Option **Autorisierungsspeicher öffnen** aus.

3. Vergewissern Sie sich, dass **XML** ausgewählt wurde. Nun müssen Sie remote auf die Datei `InitalStore.xml` auf dem Hyper-V-Server zugreifen. Verwenden Sie dazu den folgenden Pfad:

`\HOSTNAME\c$\ProgramData\Microsoft\Windows\Hyper-V\InitialStore.xml`

4. Rufen Sie **Hyper-V-Dienste > Rollenzuweisungen > Administrator** auf.

5. Wählen Sie im Menü **Aktion** die Optionen **Benutzer und Gruppen zuweisen > Von Windows und Active Directory** aus.

6. Fügen Sie die Gruppe 'Hyper-V' hinzu.

## Ordnerberechtigungen erteilen
{: #granting-folder-permissions}

Nachdem die Gruppe 'Hyper-V' nun über vollständige Berechtigungen zum Remote Management von Hyper-V verfügt, müssen Sie nun die Berechtigungen zum Ausführen von Schreiboperationen im Ordner `C:\Users\Public\Documents\Hyper-V` erteilen.

1. Öffnen Sie 'Arbeitsplatz' und rufen Sie die folgende Adresse auf:

`\HOSTNAME\c$\Users\Public\Documents`

2. Rufen Sie **Hyper-V > Eigenschaften > Sicherheit** auf.

3. Fügen Sie die Gruppe 'Hyper-V' hinzu und vergewissern Sie sich, dass sie über die Berechtigungen zum Lesen, Schreiben und Ausführen von Dateien aus diesem Verzeichnis verfügt. Im Allgemeinen ist es einfacher, **Vollzugriff** zuzuweisen.

## Konfiguration abschließen
{: #finalizing-configuration}

Alle Konfigurationsänderungen sind nun abgeschlossen. Zum Finalisieren der Konfiguration müssen Sie für den Hyper-V-Server einen Neustart durchführen. Nachdem der Server wieder online ist, stellen Sie eine Serververbindung über den lokalen Hyper-V-Manager her. Nun verfügen Sie über Vollzugriff zum Verwalten aller VMs und des Hyper-V-Service.
