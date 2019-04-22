---

copyright:
years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: vsphere, Veeam
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# VMware vSphere-Umgebung mit Veeam sichern
{: #backing-up-your-vmware-vsphere-environment-by-using-veeam}

Sie können Ihre VMware vSphere-Umgebung mithilfe einer Hybridlösung sichern, die aus den folgenden Services besteht:

* {{site.data.keyword.cloud}} Object Storage Service
* NetApp AltaVault Cloud Storage Gateway
* Veeam Backup & Replication-Software

Veeam Backup & Replication ermöglicht eine Hybridlösung, die die NetApp AltaVault Cloud-Integrated Storage-Appliance und {{site.data.keyword.cos_full}} umfasst. Die Software erstellt und verwaltet virtuelle Umgebungen und stellt diese im Bedarfsfall anhand von Sicherungen auch wieder her. Bei gemeinsamer Nutzung mit einer NetApp AltaVault Cloud-Integrated Storage-Appliance erstellen Sie Sicherungen, die lokal (On-Premises) gespeichert werden. Die Sicherung wird zeitgleich auch in
{{site.data.keyword.cos_full_notm}} repliziert. Mit dieser Hybridlösung werden zwei Sicherungskopien erstellt, wobei lediglich eine lokal gespeichert wird.

## AltaVault Cloud-Integrated Storage Gateway
{: #altavault-cloud-integrated-storage-gateway}

Sie können AltaVault Cloud Storage Gateway verwenden, um Ihre lokale Umgebung mit der Cloud zu integrieren, ohne dass hierzu mithilfe von REST-APIs Scripts oder Anwendungen für {{site.data.keyword.cos_full_notm}} geschrieben werden müssen. Sie können Einheiten anhängen oder auf die Mountpunkte verweisen und mit dem sicheren Kopieren von Daten in die Cloud beginnen.

### AltaVault lokal bereitstellen
{: #deploying-altavault-on-premises}

Führen Sie die folgenden Schritte aus, um AltaVault als lokale Sicherungslösung für {{site.data.keyword.cos_full_notm}} bereitzustellen.

Sie können AltaVault entweder als physische oder virtuelle Appliance erwerben. Im vorliegenden Text wird die Bereitstellung der Testversion VMware vSphere ESXi-Based AltaVault Virtual Appliance behandelt.
{:tip}

<a name="prerequisites"></a>
#### Voraussetzungen

Überprüfen Sie, ob die folgenden Voraussetzungen erfüllt sind:

* Vorhandensein einer Kopie von AltaVault Virtual Appliance. Hierbei handelt es sich um eine einzelne Datei mit der Dateierweiterung 'OVA'. Wenden Sie sich an den zuständigen NetApp-Ansprechpartner, wenn Sie die Appliance benötigen. Alternativ hierzu können Sie auch die 90 Tage gültige Testversion von der
[NetApp AltaVault-Website ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.netapp.com/us/products/protection-software/altavault/){: new_window} herunterladen.
* Eine vorhandene lokale vSphere ESXi 5.5-Umgebung mit der Mindestkonfiguration in Bezug auf CPU, Hauptspeicher und Plattenspeicherbedarf steht für die AltaVault-Appliance zur Verfügung. Wenn Sie die Testversion nutzen möchten, dann ergeben sich folgende Voraussetzungen: Vier virtuelle CPUs (vCPUs), 24 GB Hauptspeicher und bis zu 8 TB Festplattenspeicher.
* Zwei verfügbare 10-Gbps Netzschnittstellencontroller (NICs = Network Interface Controllers) in der vSphere-Umgebung. Ein NIC wird für die Dateneingabe und der andere für die Datenreplikation auf {{site.data.keyword.cos_full_notm}} verwendet.
* Zwei Netze, die den beiden NICs (VLANs) zugeordnet sind, die in der vSphere-Umgebung definiert sind. Das Replikationsnetz darf nicht demselben Netz wie das Dateneingabenetz zugeordnet werden, da es sonst zu einer Routing-Schleife kommen kann.
* Eine Gruppe mit {{site.data.keyword.cos_full_notm}}-Berechtigungsnachweisen. Diese Berechtigungsnachweise umfassen einen {{site.data.keyword.cloud_notm}}-Benutzernamen, einen {{site.data.keyword.cos_full_notm}}-Benutzernamen und den API-Schlüssel, der dem
{{site.data.keyword.cloud_notm}}-Benutzernamen zugeordnet ist.
* Kenntnisse der VMware Sphere-Terminologie und zur Verwaltung von vSphere ESXi-Umgebungen. Diese Kenntnisse umfassen, sind jedoch nicht beschränkt auf die Verwendung von vSphere Web Client, des vSphere-Clients und zur Zuweisung von Hardwareressourcen einschließlich Netzbetrieb und Speicher.

### OVA-Datei für AltaVault bereitstellen
{: #deploying-altavault-ova}

Sie können die OVA-Datei für AltaVault in der vSphere-Umgebung bereitstellen, nachdem alle Voraussetzungen erfüllt sind. Anweisungen zur OVA-Bereitstellung finden Sie im
[Installations- und Servicehandbuch für NetApp AltaVault ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}.

1. Bearbeiten Sie die virtuelle Maschine (VM) von AltaVault, nachdem die Bereitstellung der OVA-Datei abgeschlossen ist.
2. Ändern Sie den zugeordneten Speicher so, dass eine Übereinstimmung mit der im Bearbeitungsfenster angezeigten AltaVault-Version hergestellt wird. Wenn Sie die Testversion nutzen, dann ordnen Sie 24 GB Hauptspeicher zu und fügen Sie eine Platte hinzu, deren Kapazität kleiner-gleich 8 TB ist. **Hinweis:** Diese sekundäre Plattenspeichereinheit wird zur Speicherung von deduplizierten Sicherungsdaten verwendet.
* Ordnen Sie der AltaVault-Appliance unterschiedliche Netze (VLANs) zu, nachdem die Hauptspeicher- und Plattenkonfigurationen geändert wurden.

Den NICs werden die folgenden Schnittstellenfunktionen zugewiesen:

* Primär. Wird als Managementschnittstelle verwendet.
* e0a. Eine Schnittstelle, die verwendet wird, um Daten aus der AltaVault-Appliance in die Cloud zu replizieren.
* e0b. Eine Schnittstelle, die zum Exportieren des Mountpunkts für die SMB/CIFS- oder NFS-Freigabe verwendet wird.
* e0c. Eine optionale Schnittstelle, die zum Exportieren des Mountpunkts für die SMB/CIFS- oder NFS-Freigabe verwendet wird.

In der vorliegenden Beispielkonfiguration verwendet die AltaVault-Appliance die Schnittstelle **e0a** als Schnittstelle zum Replizieren in die Cloud. Die Schnittstelle **e0b** wird zum Exportieren eines CIFS/SMB-Mountpunkts verwendet. **Hinweis:** Eine CIFS/SMB-Freigabe und eine NFS-Freigabe können nicht verwendet werden, um auf dieselben Daten zuzugreifen. Dies bedeutet, dass auf Daten, die in einer CIFS/SMB-Freigabe platziert werden, nicht über eine NFS-Freigabe (und umgekehrt) zugegriffen werden kann.

Weitere Informationen zum Bereitstellen der AltaVault-Appliance und zur Konfiguration der VM-Einstellungen der Appliance finden Sie im [Installations- und Servicehandbuch für NetApp AltaVault ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}.

### Erstkonfiguration der AltaVault-Appliance <!--initial configuration?-->
{: #initial-configuration-of-the-altavault-appliance-initial-configuration-}

Sie können die AltaVault-VM einschalten, nachdem sie mit der passenden Hardware konfiguriert wurde. **Hinweis:** Es dauert eine gewisse Zeit, bis die AltaVault-VM erstmalig gestartet wird, da die AltaVault-Appliance die Platte für den sekundären Metadatencache formatiert.

1. Nachdem der Startprozess für die Appliance abgeschlossen ist, müssen Sie sich an der AltaVault-Konsole anmelden. Verwenden Sie ``*admin`` als **Benutzername** und ``password`` als **Kennwort**. Sie können diese Berechtigungsnachweise nach Abschluss der Erstkonfiguration ändern.
2. Nach der Anmeldung werden Sie gefragt, ob zur Erstkonfiguration der Assistent verwendet werden soll. Geben Sie **y** (Ja) ein und drücken Sie dann die
**Eingabetaste**, um die Änderungen zu speichern.

Verwenden Sie die Informationen in Tabelle 1, nachdem der Assistent geöffnet wurde.

|Frage|Antwort|
|---|---|
|Schritt 1: Administratorkennwort?|Geben Sie ein neues Administratorkennwort ein (es darf nicht "password" lauten)|
|Schritt 2: Hostname?|Geben Sie den Hostnamen ein, den Sie verwenden möchten|
|Schritt 3: DHCP in der primären Schnittstelle verwenden?|Geben Sie **n** (Nein) ein|
|Schritt 4: Primäre IP-Adresse?|Geben Sie die primäre IP-Netzadresse ein. In der Beispielkonfiguration stellt die primäre IP-Adresse das Netz dar, das für die Cloudreplikation und das Appliance-Management verwendet wird (192.168.50.15)|
|Schritt 5: Netzmaske?|Geben Sie die Netzmaske ein (255.255.255.0)|
|Schritt 6: Standardgateway?|Geben Sie das Standardgateway ein (192.168.50.1)|
|Schritt 7: Primärer DNS-Server?|Geben Sie den primären DNS-Server (DNS = Domain Name System) in Ihrer Umgebung ein|
|Schritt 8: Domänenname?|Geben Sie den Domänennamen Ihrer Umgebung ein (testenv.org)|
{: caption="Tabelle 1. AltaVault-Erstkonfigurationswerte" caption-side="top"}

### AltaVault für Object Storage konfigurieren
{: #configuring-altavault-for-object-storage}

Führen Sie die folgenden Schritte aus, um die Appliance so zu konfigurieren, dass eine Verbindung zu {{site.data.keyword.cos_full_notm}} Service hergestellt wird.

1. Öffnen Sie einen Web-Browser und geben Sie die IP-Adresse der primären Schnittstelle der AltaVault-Appliance ein (siehe vorherigen Schritt).
2. Melden Sie sich mit den Administratorberechtigungsnachweisen bei der Konsole an. Bei der ersten Anmeldung wird das Dashboard des Assistenten angezeigt.
3. Wählen Sie **Systemeinstellungen** aus und stellen Sie sicher, dass die Informationen in der nächsten Anzeige korrekt sind. Passen Sie die Zeitzone an, um die Zeitzone Ihrer Umgebung wiederzugeben.
4. Klicken Sie auf **Weiter > Speichern und anwenden > Beenden**. Das System ruft wieder das Assistentendashboard auf.
5. Wählen Sie **Cloudeinstellungen** aus und klicken Sie auf **Provider**. Wählen Sie **IBM Cloud Object Storage** aus.
6. Wählen Sie eine passende **Object Storage-Region** aus. **Hinweis:** Nicht alle Regionen (z. B. Melbourne) werden angezeigt. Der Hostname des {{site.data.keyword.cos_short}}-Service wird jedoch mithilfe des Felds **Hostname** geändert. Beispiel: Wenn Sie Melbourne als Region verwenden wollen, dann können Sie im Dropdown-Menü **Region** den Eintrag **San Jose 1** auswählen und den Wert im Feld **Hostname** in **mel01.objectstorage.softlayer.net** ändern.
7. Geben Sie Ihre {{site.data.keyword.cos_full_notm}}-Berechtigungsnachweise im Feld **Benutzername** ein. Der Benutzername muss im Format
`object_storage_username:IBM_Cloud_username` angegeben werden. Beispiel: **ABC-DE123456-7:user**. Sie finden den Object Storage-Benutzernamen unter **Speicher > Object Storage** im [{{site.data.keyword.slportal_full}} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://control.softlayer.com/){: new_window}.
8. Geben Sie einen Wert für **Bucketname** ein, um die Daten zu speichern. Der Bucketname ist der Containername, in dem die Daten in
{{site.data.keyword.cos_full_notm}} gespeichert werden sollen.
* Führen Sie keine Änderungen am Standardport durch, es sei denn, Sie erhalten entsprechende Anweisungen vom zuständigen Netzadministrator. Im Feld **Archivierung aktivieren** wird der Standardwert **Nein** angegeben. Klicken Sie auf **Weiter**.
9. Geben Sie einen Wert für **Lizenzanforderungstoken** ein (falls erforderlich) und klicken Sie dann auf **Weiter**.
10. Geben Sie einen **Verschlüsselungsschlüssel** ein. Sie können zulassen, dass AltaVault einen neuen Verschlüsselungsschlüssel generiert oder einen bereits vorhandenen Verschlüsselungsschlüssel eingeben, mit dem Daten ver- und entschlüsselt werden können. Klicken Sie auf **Weiter**.
11. Überprüfen Sie, ob alle Einstellungen korrekt sind, und klicken Sie anschließend auf **Fertigstellen und anwenden**. AltaVault versucht nun, den {{site.data.keyword.cos_full_notm}} Service zu kontaktieren. Hierzu werden die Eingaben und Einstellungen verwendet, die im Assistenten für die Cloudeinstellungen angegeben wurden. Schlägt die Verbindungsherstellung fehl, dann prüfen Sie die Einstellungen und vergewissern Sie sich, dass Sie über die erforderlichen Zugriffsberechtigungen für den Service verfügen.
12. Klicken Sie auf **Beenden**, nachdem die Verbindung hergestellt werden konnte, um zum Assistentendashboard zurückzukehren, und klicken Sie dann auf **Assistent beenden**, um wieder auf die Statusseite der AltaVault-Appliance zu gelangen.
13. Überprüfen Sie, ob der *Speicheroptimierungsservice* aktiv ist und vergewissern Sie sich, dass sein Status 'Bereit' lautet. **Hinweis:** Es kann einige Minuten dauern, bis der Status in *Bereit* geändert wird.

Die AltaVault-Appliance wird so konfiguriert, dass sie mit {{site.data.keyword.cos_full_notm}} Service kommunizieren kann.

### CIFS/SMB-Mountpunkt in AltaVault konfigurieren
{: #configuring-the-cifs-smb-mount-point-in-altavault}

Die Schnittstelle **e0b** muss so konfiguriert werden, dass ein CIFS/SMB-Mountpunkt erstellt wird. Führen Sie die folgenden Schritte aus, um **e0b** zu konfigurieren.

1. Rufen Sie **Einstellungen > Datenschnittstellen** auf und erweitern Sie die Ansicht der Schnittstelle **e0b**. Aktivieren Sie die Option **Datenschnittstelle aktivieren** und geben Sie Werte für **IP-Adresse, Teilnetzmaske** und **Gateway** ein, die Sie für den Mount der CIFS/SMB-Freigabe verwenden wollen.
2. Übernehmen Sie für **MTU** den Standardwert **1500 Byte**.<br/><br/>Obwohl die standardmäßige maximale Übertragungseinheit (MTU) auf 1.500 gesetzt ist, können Sie sie auf 9.000 ändern, wenn Sie Jumbo-Frames verwenden. **Hinweis:** Ihr ESXi-Host und die physische Infrastruktur müssen hierzu die Verwendung von Jumbo-Frames unterstützen. Standardmäßig unterstützt {{site.data.keyword.cloud_notm}} bereits eine MTU-Größe von 9.000 Byte ohne Konfigurationsänderungen. 
3. Klicken Sie auf **Anwenden**. Der Mountpunkt ist bereit für die Konfiguration.
4. Wählen Sie **Speicher > CIFS > CIFS-Freigabe hinzufügen** aus und geben Sie einen eindeutigen Namen ein.
5. Klicken Sie auf das Dropdown-Menü **Pin-Freigabe** und wählen Sie dann **Ja** aus. **Hinweis:** Veeam Backup & Replication-Sicherungen können bei Freigaben ohne Pin fehlschlagen.
6. Geben Sie einen eindeutigen Pfad für die Freigabe im Feld **Pfad** ein. Sie sollten den Freigabenamen als Pfad verwenden. Wenn der Freigabename
`cifs_share0` lautet, dann geben Sie als Pfad '/cifs_share0' ein.
7. Nehmen Sie die Auswahl von **Allen Zugriff erlauben** zurück, wenn keine Sicherheitsprobleme bestehen. Clients, die eine CIFS/SMB-Freigabe verwenden, sollten auf die Whitelist gesetzt werden. Andernfalls sollten Sie die Option **Allen Zugriff erlauben** ausgewählt lassen, wenn Sicherheitsprobleme bestehen, und dann auf **Freigabe hinzufügen** klicken.
8. Klicken Sie auf **CIFS-Benutzer hinzufügen**, um Konten für berechtigte Benutzer zu erstellen, und füllen Sie die Felder **Benutzername** und **Kennwort** aus.
9. Erweitern Sie die neue CIFS-Freigabe und klicken Sie auf **Benutzer oder Gruppe hinzufügen**, um die berechtigen Benutzerkonten hinzuzufügen.
10. Rufen Sie **Globale CIFS-Einstellungen** auf und klicken Sie auf das Dropdown-Menü **Empfangsbereite Schnittstelle**, um dann
**e0b** auszuwählen. Klicken Sie anschließend auf **Anwenden**.

Die AltaVault-Appliance ist so konfiguriert, dass die Kommunikation zwischen der Appliance, {{site.data.keyword.cos_full_notm}} und dem Computer möglich ist, auf dem Veeam Backup & Replication ausgeführt wird. Sie sollten die Konfiguration der AltaVault-Appliance ggf. exportieren, um zukünftige Bereitstellungen schneller ausführen zu können.

Führen Sie die folgenden Schritte aus, um die AltaVault-Appliance-Konfiguration zu exportieren.

1. Klicken Sie auf **Einstellungen > Installationsassistent**, um auf das Assistentendashboard der Web-Management-Konsole der lokalen AltaVault-Appliance zuzugreifen.
2. Klicken Sie auf **Konfiguration exportieren** und dann auf **Konfiguration exportieren**.
3. Speichern Sie die Konfigurationsdatei (tarball/.tar) an einer sicheren Position.

## Veeam Backup & Replication
{: #veeam-backup-replication-backup}

Die Veeam Backup & Replication-Software bietet Funktionen für Gesamtsicherungen, die Replikation und die Wiederherstellung für VMs und deren Daten. Die Sicherung kann vollständig mit AltaVault Cloud Gateway Appliance integriert werden.

### Veeam auf neuem Server bereitstellen
{: #provisioning-veeam-on-a-new-server}

Sie können Veeam bestellen, wenn Sie einen neuen VSI-Server oder Bare Metal Server bereitstellen. Verwenden Sie für die Bereitstellung die folgenden Informationen.
  * Veeam ist nur mit monatlicher Abrechnung verfügbar
  * Veeam ist nur mit einem Windows-Betriebssystem verfügbar

Gehen Sie wie folgt vor, um Veeam hinzuzufügen:
  * Klicken Sie im Abschnitt 'System-Add-ons' unter 'Betriebssystemspezifische Add-ons' auf die Registerkarte für Veeam und wählen Sie dann eine der Veeam-Optionen aus.
  * Wählen Sie im Abschnitt 'System-Add-ons' unter 'CDP-Add-on' alle weiteren Veeam-Optionen aus, die hinzugefügt werden sollen. <br><br>**Hinweis**: Wenn Sie *Veeam Backup and Replication 9.5 Update 3* auswählen, dann müssen Sie mindestens eine Option in der Liste mit den CDP-Add-ons auswählen.

### Veeam auf vorhandenem Server bestellen
{: #ordering-veeam-on-an-existing-server}

Sie können Veeam mithilfe der Prozedur zum [erneuten Laden des Betriebssystems (OS Reload)](/docs/infrastructure/software?topic=software-reloading-the-os) zu einem vorhandenen Server hinzufügen. Auf dem Server muss ein Windows-Betriebssystem ausgeführt werden, das für die monatliche Rechnungsstellung eingerichtet wurde.

Gehen Sie wie folgt vor, um Veeam hinzuzufügen:
1. Klicken Sie im Abschnitt 'System-Add-ons' unter 'Betriebssystemspezifische Add-ons' auf die Registerkarte für Veeam und wählen Sie dann eine der Veeam-Optionen aus.
2. Wählen Sie im Abschnitt 'System-Add-ons' unter 'CDP-Add-on' alle weiteren Veeam-Optionen aus, die hinzugefügt werden sollen. <br><br>**Hinweis**: Wenn Sie *Veeam Backup and Replication 9.5 Update 3* auswählen, dann müssen Sie mindestens eine Option in der Liste mit den CDP-Add-ons auswählen.

### Veeam Backup & Replication bereitstellen
{: #deploying-veeam-backup-replication}

Im vorliegenden Beispiel wird eine Testversion von Veeam Backup & Replication Version 8 verwendet.

#### Voraussetzungen
{: #prerequisites-deploying-veeam-backup-replication}

Bevor Sie mit der Bereitstellung fortfahren, überprüfen Sie, ob die folgenden Voraussetzungen erfüllt sind:

* Sie verfügen über eine vorhandene AltaVault-Appliance, die zur Verwendung mit {{site.data.keyword.cos_full_notm}} und mit Veeam Backup & Replication konfiguriert wurde.
* Sie haben eine Kopie von Veeam Backup & Replication für VMware-Umgebungen angefordert, die als eine einzige ausführbare Datei bereitgestellt wird. Wenden Sie sich an den zuständigen Veeam-Ansprechpartner, wenn Sie eine Kopie benötigen, oder laden Sie eine [30-Tage-Testversion ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window} herunter.
* Fordern Sie eine Lizenzdatei zur Verwendung mit Veeam Backup & Replication an. In den meisten Fällen wird diese Datei per E-Mail an die E-Mail-Adresse gesendet, die zum Download von Veeam Backup & Replication verwendet wird. Wenn Sie diese Datei nicht erhalten haben, wenden Sie sich an Ihren Veeam-Ansprechpartner.<br/><br/>Die Lizenzdatei wird verwendet, um alle Funktionen von Veeam Backup & Replication zu aktivieren. Wenn diese Datei während der Programminstallation nicht bereitgestellt wird, werden alle Komponenten und Funktionen auf die 30-Tage-Testversion zurückgesetzt.
* Sie verfügen über einen vorhandenen Sicherungsserver, der entweder vor Ort oder außerhalb des Standortes implementiert sein kann. Für den Server gelten die Spezifikationen, die in Tabelle 2 enthalten sind. Bei dem installierten Betriebssystem muss es sich um eine 64-Bit-Version handeln.

||Minimum|Empfohlen|
|---|---|---|
|**Betriebssystem**|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>WIndows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|
|**Anzahl der Kerne oder vCPUs**|2|4|
|**Hauptspeicher**|4 GB Basisarbeitsspeicher (RAM) plus 500 MB für jeden gleichzeitigen Sicherungsjob.|16 GB Basisarbeitsspeicher (RAM) plus 4 GB für jeden gleichzeitigen Sicherungsjob.|
|**Plattenspeicherplatz**|2 GB für Produktinstallation; 10 GB pro 100 VMs für Katalogdaten des Gastdateisystems (persistente Daten).|2 GB für Produktinstallation; 10 GB pro 100 VMs für den Katalog des Gastdateisystems (persistente Daten).|
|**Netz**|1-Gbps-LAN für Sicherung und Replikation vor Ort; 1-Mbps-WAN für Sicherung und Replikation an anderen Standorten.|1-Gbps-LAN für Sicherung und Replikation vor Ort; 1-Mbps-WAN für Sicherung und Replikation an anderen Standorten.|
{: caption="Tabelle 2. Systemvoraussetzungen für Sicherungsserver von Veeam Backup & Replication" caption-side="top"}

#### Veeam Backup & Replication installieren
{: #installing-veeam-backup-replication}

Führen Sie die folgenden Schritte aus, um Veeam Backup & Replication auf dem Sicherungsserver zu installieren, nachdem alle Voraussetzungen erfüllt sind.

1. Doppelklicken Sie zum Starten des Installationsassistenten auf die ausführbare Datei von Veeam und klicken Sie dann auf **Veeam Backup & Replication - Installieren**.
2. Klicken Sie auf **Weiter** und akzeptieren Sie die Bedingungen in der Lizenzvereinbarung.
3. Klicken Sie auf **Weiter** und dann für **Veeam Backup & Replication** auf **Installieren**.
4. Geben Sie die Position der Lizenzdatei ein, die zuvor abgerufen wurde, und klicken Sie dann auf **Weiter**.
5. Wählen Sie die Veeam Backup & Replication-Komponenten aus, die Sie installieren wollen, und geben Sie die Installationsposition in der Anzeige für die **Veeam Backup & Replication-Installation** an. **Veeam Backup & Replication und der Veeam Backup-Katalog** sind erforderliche Komponenten. Klicken Sie auf **Weiter**. Der Installationsassistent führt eine Reihe von Prüfungen durch, um sicherzustellen, dass alle erforderlichen Programmframeworks und Unterstützungskomponenten installiert wurden. Fehlen solche Komponenten, dann zeigt der Installationsassistent eine Option zum automatischen Installieren der Komponenten an. Wenn Sie fehlende Frameworks und Komponenten installieren müssen, dann klicken Sie auf **Installieren**.
6. Überprüfen Sie, ob alle Komponenten die Systemprüfung **bestanden** haben und klicken Sie dann auf **Weiter**.
7. Wählen Sie das **Servicekonto (Benutzer)** aus, unter dem der Veeam-Sicherungsservice ausgeführt wird. **Hinweis:** Das Standardservicekonto ist das Konto *LOCAL SYSTEM*. Klicken Sie auf **Weiter**.
8. Wählen Sie die **SQL Server-Instanz** aus, die zum Erstellen und Speichern der Veeam Backup & Replication-Datenbanken verwendet wird.<!--Contact your database administrator for more information, if necessary. --> Klicken Sie auf **Weiter**.
9. Geben Sie den **Katalogserviceport** und den **Veeam Backup-Serviceport** (Standardports sind **9393** und **9392**) ein.<!--Contact your network administrator for more information, if necessary.--> Klicken Sie auf **Weiter**.
10. Wählen Sie die Verzeichnisse aus, in denen der Katalog des Gastdateisystems (persistente Daten) und der vPower-NFS-Schreibcache (nicht persistente Daten) gespeichert werden. Klicken Sie auf **Weiter**.
11. Überprüfen Sie, ob alle Einstellungen und Werte korrekt sind, und klicken Sie auf **Installieren**, um die Installation zu starten. Klicken Sie nach Abschluss der Installation auf **Fertigstellen**.

### Veeam Backup & Replication für Sicherungen konfigurieren
{: #configuring-veeam-backup-replication-for-backups}

Nachdem Sie Veeam Backup & Replication installiert haben, können Sie eine Verbindung zum vSphere ESXi-Host herstellen, der AltaVault Virtual Appliance enthält.

1. Starten Sie Veeam Backup & Replication.
2. Klicken Sie links unten in der Anzeige auf **Sicherungsinfrastruktur > Verwaltete Server**.
3. Klicken Sie auf **Server hinzufügen** und doppelklicken Sie dann auf **VMware vSphere**.
4. Geben Sie einen Wert für **DNS-Name oder IP-Adresse** und **Beschreibung** für vCenter Server ein und klicken Sie dann auf **Weiter**.
5. Geben Sie die **Berechtigungsnachweise** eines lokalen Kontos ein, das über Administratorrechte auf dem vSphere-Server verfügt. **Hinweis:** Der Kontobenutzername muss bei Domänenkonten das Format **DOMAIN\USER** und bei lokalen Konten das Format **HOST\USER** aufweisen. Klicken Sie zum Hinzufügen eines Kontos auf **Hinzufügen** und geben Sie dann den Kontobenutzernamen und das Kennwort ein.<br/>Ändern Sie den **standardmäßigen VMware-Web-Service-Port** während der Installation von Veeam Backup & Replication nicht, es sei denn, Sie erhalten entsprechende Anweisungen vom zuständigen Netzadministrator.
6. Klicken Sie auf **Weiter**. Veeam Backup & Replication stellt eine Verbindung zum VMware vSphere-Server her. Schlägt die Herstellung der Verbindung fehl, dann überprüfen Sie, ob das Konto vorhanden ist und über Administratorrechte auf dem VMware vSphere-Server verfügt, bevor Sie erneut versuchen, eine Verbindung herzustellen.
7. Klicken Sie im Fenster mit der **Zusammenfassung** auf **Fertigstellen** und überprüfen Sie, ob der vSphere-Server erfolgreich hinzugefügt wurde, indem Sie auf **Verwaltete Server > VMware vSphere** klicken.

### Sicherungsrepository zu Veeam Backup & Replication hinzufügen
{: #adding-a-backup-repository-to-veeam-backup-replication}

Standardmäßig erstellt Veeam Backup & Replication während der Programminstallation ein lokales Sicherungsrepository auf Laufwerk C:\ des Veeam Backup & Replication-Sicherungsservers.

Führen Sie die folgenden Schritte aus, um ein Sicherungsrepository zu erstellen, in dem alle Sicherungen auf der AltaVault-Appliance gespeichert werden sollen. Wenn Sie das standardmäßige Sicherungsrepository verwenden möchten, überspringen Sie die folgenden Schritte.

1. Klicken Sie unten links in der Anzeige **Sicherungsinfrastruktur** auf **Sicherungsinfrastruktur > Sicherungsrepositorys > Repository hinzufügen**.
2. Geben Sie im Feld **Name** einen eindeutigen Repository-Namen ein. Optional können Sie auch eine **Beschreibung** eingeben. Klicken Sie auf **Weiter**.
3. Wählen Sie **Freigegebener Ordner** aus. Die CIFS/SMB-Freigabe, die Sie erstellt haben, ist die Freigabe, die von diesem Sicherungsrepository verwendet wird. Klicken Sie auf **Weiter**.
4. Geben Sie die Position der CIFS/SMB-Freigabe auf der AltaVault-Appliance an. Um die Position zu ermitteln, öffnen Sie einen Web-Browser und geben Sie die IP-Adresse der AltaVault-Appliance ein. Rufen Sie **Speicher > CIFS** auf und notieren Sie den **Freigabepfad** für die Freigabe. **Hinweis:** Dieser Pfad ist NICHT mit dem lokalen Pfad der Freigabe identisch.<br/><br/>Das Format des Freigabepfads ist `\\<AltaVault appliance hostname>\<share name>`. Ersetzen Sie den Hostnamen der AltaVault-Appliance im Freigabepfad durch die IP-Adresse der Netzschnittstelle **e0b** (Mountpunkt der Freigabe) der AltaVault-Appliance.<br/><br/>Klicken Sie zum Suchen der IP-Adresse der Schnittstelle **e0b** im Managementfenster der AltaVault-Appliance auf **Einstellungen > Datenschnittstellen**. Der Freigabepfad, der in Veeam Backup & Replication angegeben wird, lautet `\\192.168.50.16\cifs_test2`.
5. Rufen Sie erneut Veeam Backup & Replication auf, geben Sie den Freigabepfad des Mountpunkts im Feld **Freigegebener Ordner** ein und klicken Sie auf **Weiter**. Veeam Backup & Replication versucht, eine Verbindung zum Mountpunkt herzustellen. Wenn die Verbindung fehlschlägt, kehren Sie zum vorherigen Schritt zurück und überprüfen Sie, ob die Netzeinstellungen für die AltaVault-Appliance korrekt sind, bevor Sie versuchen, die Verbindung erneut herzustellen.
6. Geben Sie einen Wert ein, um die maximale Anzahl gleichzeitig ablaufender Tasks auf die Anzahl verfügbarer Ressourcen zu begrenzen. Dieser Wert stellt die maximale Anzahl an Tasks dar, die von einem Sicherungsproxy an die ausgewählte Freigabe gesendet werden können. Die standardmäßige Anzahl gleichzeitig ablaufender Tasks beträgt 4. In AltaVault wird empfohlen, mit fünf gleichzeitig ablaufenden Tasks zu beginnen und diesen Wert je nach Ressourcenverfügbarkeit zu erhöhen oder zu reduzieren. Dieser Wert kann angepasst werden, nachdem das Sicherungsrepository erstellt wurde.
7. Klicken Sie auf **Weiter**.
8. Geben Sie bei Bedarf die **vPower-NFS-Einstellungen** an. Wenn für **vPower-NFS-Server aktivieren** keine Auswahl getroffen wird, dann verwendet Veeam Backup & Replication vPower für die Wiederherstellung und die Verifizierung der Wiederherstellung. Klicken Sie auf **Weiter**.
9. Bestätigen Sie in der Anzeige **Überprüfung**, dass alle Ihre Einstellungen korrekt sind, und klicken Sie auf **Weiter**.
10. Klicken Sie auf **Fertigstellen**, um den Assistenten zu beenden. Nun können Sie mit der Sicherung Ihrer Daten beginnen.

### Umgebung sichern
{: #backing-up-the-environment}

Führen Sie die folgenden Schritte aus, um eine Sicherung einer vollständigen virtuellen Umgebung zu erstellen.

1. Klicken Sie in der Anzeige **Sicherungsinfrastruktur** auf **Sicherung & Replikation**.
2. Klicken Sie im Fenster **Sicherung & Replikation** auf **Jobs > Sicherungsjob**.
3. Geben Sie im Feld **Name** einen eindeutigen Namen ein. Optional können Sie auch eine **Beschreibung** eingeben. Klicken Sie auf **Weiter**.
4. Wählen Sie die VMs aus, die Sie sichern möchten, indem Sie auf **Objekte hinzufügen** klicken und dann durch die Baumstruktur klicken, um die VMs auszuwählen. Klicken Sie auf **Hinzufügen**, nachdem Sie die entsprechenden VMs ausgewählt haben.<br/>Wenn nur bestimmte Teile der VMs gesichert werden sollen (z. B. die Startplatte), klicken Sie auf **Ausschlüsse** und geben Sie die Teile an. Klicken Sie andernfalls auf **Weiter**.
5. Wählen Sie das von Ihnen erstellte Sicherungsrepository im Dropdown-Menü **Sicherungsrepository** aus.

**Hinweis:** Zur Optimierung der Leistung müssen Sie sicherstellen, dass die Einstellungen für die Datendeduplizierung und die Komprimierung geändert werden. Führen Sie die folgenden Schritte aus, um die Leistung zu optimieren.

1. Klicken Sie auf **Erweitert**, währen Sie die Registerkarte **Speicher** aus und nehmen Sie die Auswahl von **Inline-Datenduplizierung aktivieren** zurück. Die Leistung verbessert sich, weil die AltaVault-Appliance für die Veeam Backup & Replication-Sicherungen, die die Appliance durchlaufen, eine Deduplizierung auf Blockebene durchführt.
2. Wählen Sie im Dropdown-Menü **Komprimierungsstufe** die Option **Keine** und im Dropdown-Menü **Speicheroptimierung** die Option **LAN-Ziel** aus.<br/>**Hinweis:** Wenn für die Netzadresse der CIFS/SMB-Freigabe Einschränkungen bestehen, können durch Beibehaltung der Option **Inline-Datendeduplizierung aktivieren** zwar Probleme mit der Netzleistung gemildert werden. Dies hat jedoch geringere Durchsatzraten bei der Datendeduplizierung in der AltaVault-Appliance zur Folge.
3. Klicken Sie auf **Weiter**.
4. Wählen Sie das entsprechende Kontrollkästchen aus, wenn Sie eine anwendungsbezogene Verarbeitung und/oder eine Gastdateisystemindexierung durchführen wollen. Legen Sie die **Berechtigungsnachweise für das Gastbetriebssystem** [Benutzername und Kennwort des Gastbetriebssystems der VMs, die gesichert werden] fest, falls dies erforderlich ist. Klicken Sie auf **Weiter**.
5. Wählen Sie das Kontrollkästchen **Job automatisch ausführen** aus, wenn Sie regelmäßig Sicherungen ausführen, und geben Sie die gewünschten Intervalle an. Klicken Sie andernfalls auf **Erstellen** und dann auf **Fertigstellen**.

#### Manuelle Sicherung starten
{: starting-a-manual-backup}
Zum manuellen Starten einer Sicherung müssen Sie mit der rechten Maustaste auf den Sicherungsjob klicken und dann **Starten** auswählen. Alternativ können Sie **Aktiv voll** auswählen, wenn Sie eine neue Sicherung wünschen.

**Hinweis:** Veeam Backup & Replication kann virtuelle Umgebungen anhand einer Sicherung wiederherstellen. Weitere Informationen zum Wiederherstellen virtueller Umgebungen finden Sie auf der Website für [Veeam Backup & Replication ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}.
