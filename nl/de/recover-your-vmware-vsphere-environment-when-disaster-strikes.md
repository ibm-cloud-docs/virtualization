---
copyright:
  years: 2014, 2018
lastupdated: "2018-06-22"

subcollection: virtualization

keywords: disaster recovery
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:table: .aria-labeledby="caption"}

# VMware vSphere-Umgebung wiederherstellen
{: #recovering-your-vmware-vsphere-environment}

Die überwiegend genutzte Methode zur Aufrechterhaltung der Datensicherheit und -integrität besteht momentan in der Erstellung von Datensicherungen. Im Idealfall werden diese Sicherungskopien niemals benötigt. Leider kommt es aber in der Realität immer wieder zu Unfällen und Katastrophen. In solchen Notfällen ist es von zentraler Bedeutung, die Systemausfallzeiten auf ein Minimum zu beschränken und die Funktionsfähigkeit des Systems schnellstmöglich wiederherzustellen.

In der Vergangenheit stellte die Speicherung von Sicherungen auf Hochleistungsspeichereinheiten oder -bandsystemen eine gangbare Lösung dar. Heute streben Unternehmen jedoch nach einer Reduzierung des Kapital- und Betriebskostenaufwands, der mit lokalen physischen Speicherlösungen verbunden ist, indem solche Lösungen entweder durch Object Storage-Lösungen ergänzt oder durch solche Lösungen ersetzt werden. Mit Object Storage besteht die Herausforderung bei der Wiederherstellung anhand von Sicherungskopien nicht mehr einzig in der Lokalisierung und Verwendung der richtigen Sicherungskopie für die Wiederherstellung, weil diese Sicherungskopien sich nicht mehr auf lokalen Speichereinheiten befinden. Glücklicherweise gibt es eine Hybridlösung, die den überwiegenden Anteil des Aufwands bei der Wiederherstellung anhand von Sicherungskopien, die auf einem Object Storage-System gespeichert sind, abfängt. Die Lösung setzt sich aus folgenden Komponenten zusammen:

* {{site.data.keyword.cos_full}}
* NetApp AltaVault Cloud Storage Gateway von NetApp (zuvor Riverbed SteelStore)
* Veeam Backup & Replication-Software

Tritt der Katatrophenfall ein und versetzt Ihre Primärumgebung in den Offlinemodus, dann kann ein zweiter Wiederherstellungstandort in {{site.data.keyword.cloud}} erstellt werden, um die Wiederherstellung der Primärumgebung durchzuführen. Sie können die Veeam Backup & Replication-Software und eine cloudintegrierte AltaVault-Speicherappliance bereitstellen, um die Kommunikation mit {{site.data.keyword.cos_full_notm}} und den Zugriff auf frühere Sicherungen der Primärumgebung zu ermöglichen. Die Software stellt eine Verbindung zur Speicher-Appliance her, um die Sicherungen an einer neuen Position wiederherzustellen und so die Primärumgebung wieder in den Onlinemodus zu versetzen, sodass weitere Ausfallzeiten vermieden werden können.

Die lokalen IP-Adressen werden beibehalten, wenn Sie die virtuellen Maschinen (VMs) am {{site.data.keyword.cloud_notm}}-Wiederherstellungsstandort wiederherstellen. Demzufolge ist es wichtig, die IP-Adressen entweder nach erfolgreicher Wiederherstellung zu ändern, oder eine BYOIP-Infrastruktur zu entwerfen. Wenn Sie Unterstützung in diesen Fällen benötigen, dann wenden Sie sich an den zuständigen IBM Ansprechpartner.

Bei der folgenden Prozedur liegt der Schwerpunkt auf dem koordinierten Einsatz einer NetApp AltaVault Cloud Storage Gateway-Appliance, der {{site.data.keyword.cloud_notm}}-Infrastruktur und von Veeam Backup & Replication, um so die vollständige Wiederherstellung einer ausgefallenen VMware vSphere-Umgebung zu ermöglichen. Die Software geht davon aus, dass mindestens eine Sicherung der Umgebung mithilfe der drei vorgenannten Technologien erstellt wurde, die zum Zeitpunkt der Wiederherstellung auf dem {{site.data.keyword.cos_full_notm}}-System verfügbar ist.

Die AltaVault-Appliance wird auf die beiden folgenden Arten identifiziert. Erstens als 'lokale AltaVault-Appliance'. Hiermit ist die ursprüngliche, lokale AltaVault-Appliance gemeint, auf der der Fehler aufgetreten ist und von der Sicherungen wiederhergestellt oder zurückgeschrieben werden sollen. Zweitens als '{{site.data.keyword.cloud_notm}}-AltaVault-Appliance'. Hierbei handelt es sich um die AltaVault-Appliance, die zum Zurückschreiben von Sicherungen von der ausgefallenen lokalen AltaVault-Appliance verwendet wird. Die {{site.data.keyword.cloud_notm}}-AltaVault-Appliance wird in einer vSphere-Umgebung mithilfe eines Utility Servers bereitgestellt.

## Einführung einer Hybridlösung
{: #introducing-a-hybrid-solution}

Veeam Backup & Replication ermöglicht Ihnen eine Hybridlösung, die die cloudintegrierte NetApp AltaVault-Speicher-Appliance und {{site.data.keyword.cos_full_notm}} umfasst. Primäres Ziel ist hierbei die Erstellung, Verwaltung und Wiederherstellung von Sicherungen für virtuelle Umgebungen. Die cloudintegrierte NetApp AltaVault-Speicher-Appliance stellt eine Softwarelösung dar, mit der eine reibungslose Integration einer lokalen Umgebung mit einer privaten oder öffentlichen Cloud möglich wird. Bei gemeinsamem Einsatz können Sie mit Veeam Backup & Replication Sicherungen erstellen, die von der cloudintegrierten AltaVault-Speicher-Appliance auf lokalen (On-Premises) Speichereinheiten gehalten und gleichzeitig auf {{site.data.keyword.cos_full_notm}} repliziert werden. Darüber hinaus bieten das nutzungsabhängige {{site.data.keyword.cloud}}-Preismodell und die vollständige Integration mit CDN (Content Delivery Network; Netz zur Bereitstellung von Inhalten) Ihnen die Möglichkeit, Daten auf bis zu 24 Knoten an unterschiedlichen geografischen Standorten zu speichern und somit zu verteilen.

### IBM Cloud Object Storage
{: #ibm-cloud-object-storage}

Um auf die in {{site.data.keyword.cos_full_notm}} gesicherten Datendateien zugreifen zu können, werden diese Datendateien wie folgt angefordert:

* Mit der AltaVault-Appliance, die sich bereits am lokalen Standort befindet.
* Mit einer sekundären AltaVault-Appliance, die in {{site.data.keyword.cloud_notm}} enthalten ist.

Gehen Sie wie folgt vor, um eine sekundäre AltaVault-Appliance in {{site.data.keyword.cloud_notm}} bereitzustellen, um eine primäre, lokale AltaVault-Appliance, die ausgefallen ist, wiederherzustellen.

Die {{site.data.keyword.cloud_notm}}-Umgebung besteht aus einem einzelnen ESXi-Host mit lokalen Speichereinheiten, um die sekundäre AltaVault-Appliance aufzunehmen und auszuführen. Die Infrastruktur ist repräsentativ für eine grundlegende Single-Site-Architektur mit einem einzelnen ESXi-Host, der von einem vCenter Server-System in einer VSI (Virtual Server-Instanz) von {{site.data.keyword.cloud_notm}} verwaltet wird.

Wenn Sie mit einer umfangreicheren Infrastruktur arbeiten, dann benötigen Sie gemeinsam genutzte Speichereinheiten und/oder Unterstützung für die folgenden Komponenten:

* vSphere High Availability (HA)
* vSphere Distributed Resource Scheduler (DRS)
* vSphere vMotion

#### Voraussetzungen
{: #prerequisites-ibm-cloud-object-storage}

Überprüfen Sie, ob die folgenden Voraussetzungen erfüllt sind, bevor Sie fortfahren. **Hinweis:** Für dieses Beispiel wird eine Testversion einer AltaVault AVA-v8-Applicane mit AltaVault Cloud-Integrated Storage 4.1 verwendet.

* Vergewissern Sie sich, dass die vorhandene Umgebung aus einem einzelnen ESXi-Host besteht, der von einem vCenter Server-System in einer VSI von {{site.data.keyword.cloud_notm}} verwaltet wird.
* Kenntnisse in Bezug auf die VMware Sphere-Terminologie und in Bezug auf die Verwaltung von vSphere ESXi-Umgebungen. Diese Kenntnisse sollten neben anderen Bereichen Kenntnisse zur Verwendung von vSphere Web Client sowie des vSphere-Clients und zur Zuweisung von Hardwareressourcen für den Netzbetrieb und die Speicherung umfassen.

#### Zwei portierbare private Netze bestellen
{: #order-two-portable-private-networks}

Für AltaVault müssen sich die Netzschnittstellen in unterschiedlichen Netzen innerhalb der Umgebung befinden. Führen Sie die folgenden Schritte aus, um zwei portierbare private Netze über {{site.data.keyword.slportal_full}} zu bestellen.

1. Greifen Sie auf das [{{site.data.keyword.slportal_full}} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://control.softlayer.com/){: new_window} zu und verwenden Sie dazu Ihre eindeutigen Berechtigungsnachweise.
2. Klicken Sie auf **Konto > Bestellen**.
3. Wählen Sie den Abschnitt **Netz** aus und klicken Sie dann auf **Subnets/IPs > Bestellen**.
4. Wählen Sie im Dropdown-Menü **Portierbar privat** aus. Wenn Sie 'Portierbar privat' auswählen, dann wird eine Option zur Auswahl der Anzahl der portierbaren privaten IP-Adressen aufgerufen, die angezeigt werden sollen. **Hinweis:** {{site.data.keyword.cloud_notm}} reserviert automatisch mindestens drei IP-Adressen in dem Adressblock für jedes portierbare private Netz. Die reservierten Adressen sind für die Netzadresse, die Gateway-Adresse und die Broadcastadresse vorgesehen. Diese Adressen müssen direkt an das virtuelle LAN (VLAN) gebunden werden. <!--If your IBM Cloud pod supports Hot Standby Ready Protocol (HSRP), then the number of usable IP addresses is reduced. For instance, if you select **4 Portable Private IP Addresses** will yield only one usable IP address or zero if the pod supports HSRP.--> Weitere Informationen zu portierbaren IP-Adressen finden Sie im Abschnitt mit der [Einführung zu Teilnetzen und IPs](/docs/infrastructure/subnets?topic=subnets-getting-started-with-subnets-and-ips).
5. Klicken Sie auf **Weiter**, nachdem Sie die Anzahl der portierbaren privaten IP-Adressen ausgewählt haben.
6. Wählen Sie das VLAN aus, in dem die portierbaren privaten IP-Adressen weitergeleitet werden, und klicken Sie dann auf **Weiter**.
7. Füllen Sie die erforderlichen Kontaktinformationen aus und klicken Sie dann auf **Bestellen**.
8. Wiederholen Sie den Bestellablauf, um das zweite erforderliche portierbare private Netz anzufordern.
9. Klicken Sie auf der Seite für das {{site.data.keyword.slportal}} auf **Netz > IP-Management > Teilnetze**, um Ihre portierbaren privaten Netze und IP-Adressen nach der Zuordnung anzuzeigen. Sie sollten die Zuordnung Ihrer IP-Adressen unbedingt dokumentieren. Verwenden Sie den Abschnitt **Anmerkungen** der Seite **Teilnetze** für jede IP-Adresse, um das System festzuhalten, dem sie zugeordnet wurde.

#### vSphere konfigurieren
{: #configuring-vsphere}

Sie müssen vSphere so konfigurieren, dass die Hinzufügung der portablen privaten Netze nachvollzogen wird. Außerdem müssen Sie sicherstellen, dass die einzelnen Netze isoliert voneinander sind.

1. Wechseln Sie in Ihre vSphere-Umgebung und erstellen Sie eine Portgruppe für virtuelle Maschinen, um die Hinzufügung eines der (insgesamt zwei) portierbaren IP-Blöcke widerzuspiegeln.

Die VM-Portgruppe trägt die Bezeichnung **Share Network**. Ein portierbarer IP-Block wird für VMs benutzt, die in der standardmäßigen (primären) VM-Portgruppe platziert wurden. Der andere wird für die Datenübertragung zwischen einer AltaVault-Appliance und einem exportierten Mountpunkt für Common Internet File System (CIFS)/Server Message Block (SMB) verwendet.

## AltaVault Cloud Storage Gateway
{: #altavault-cloud-storage-gateway}

Sie können AltaVault verwenden, um Ihre lokale Umgebung mit der Cloud zu integrieren, ohne dass hierzu mithilfe von REST-APIs Scripts oder Anwendungen für {{site.data.keyword.cos_full_notm}} geschrieben werden müssen. AltaVault stellt einen CIFS/SMB-Mountpunkt oder NFS-Mountpunkt (NFS = Network File System) im Front-End zur Verfügung und stellt eine sichere Verbindung zur {{site.data.keyword.cos_full_notm}}-Schnittstelle im Back-End her. Anschließend können Sie Einheiten über die Mountpunkte anhängen oder auf die Mountpunkte verweisen. Außerdem können Sie nun Daten sicher in die bzw. aus der Cloud kopieren oder Umgebungen auf diesem Wege wiederherstellen.<!-- , as shown in Figure 1.-->

<!-- ![Figure 1](images/veeam_restore_fig2.png)
<br/>`Figure 1: AltaVault end-to-end flow with Veeam and IBM Cloud`-->

### AltaVault in Disaster-Recovery-Konfiguration bereitstellen
{: #deploying-altavault-in-a-disaster-recovery-configuration}

Führen Sie die folgenden Schritte aus, um AltaVault als Disaster-Recovery-Lösung mit {{site.data.keyword.cos_full_notm}} bereitzustellen.

#### Vorbereitende Schritte
{: #before-you-get-started-deploying-altavault-in-a-disaster-recovery-configuration}

Bevor Sie fortfahren, müssen Sie überprüfen, ob die folgenden Voraussetzungen erfüllt sind:

* Fordern Sie eine Kopie von AltaVault Virtual Appliance an und vergewissern Sie sich, dass sich diese Kopie auf dem Utility Server befindet. Hierbei handelt es sich um eine einzelne Datei mit der Dateierweiterung 'OVA'. Wenden Sie sich an den zuständigen NetApp-Ansprechpartner, wenn Sie die Appliance benötigen. Alternativ hierzu können Sie auch die 90 Tage gültige Testversion von der
[NetApp AltaVault-Website ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](http://www.netapp.com/us/products/protection-software/altavault/){: new_window} herunterladen.
* Richten Sie eine lokale vSphere ESXi 5.5-Umgebung mit der Mindestkonfiguration in Bezug auf CPU, Hauptspeicher und Plattenspeicherbedarf für die AltaVault-Appliance ein. Wenn Sie die Testversion nutzen, dann ergibt sich folgender Ressourcenbedarf: Vier virtuelle CPUs (vCPUs), 24 GB Hauptspeicher und bis zu 8 TB Festplattenspeicher.
* Zwei verfügbare 10-Gbps-Netzschnittstellencontroller (NICs = Network Interface Controllers) in der vSphere-Umgebung. Ein NIC wird für den Datenzugriff und der andere für die Datenreplikation auf {{site.data.keyword.cos_full_notm}} verwendet.
* Zwei portierbare private Netze, die den beiden NICs entsprechen, die in der vSphere-Umgebung definiert sind. Das Replikationsnetz darf nicht demselben Netz wie das Datenzugriffsnetz zugeordnet werden, da es sonst zu einer Routing-Schleife kommen kann.
* Sie verfügen über die {{site.data.keyword.cos_full_notm}}-Berechtigungsnachweise. Diese Berechtigungsnachweise umfassen einen {{site.data.keyword.cloud_notm}}-Benutzernamen, einen {{site.data.keyword.cos_full_notm}}-Benutzernamen und den API-Schlüssel, der dem
{{site.data.keyword.cloud_notm}}-Benutzernamen zugeordnet ist.
* Setzen Sie die Datenreplikation auf der lokalen AltaVault-Appliance aus oder trennen Sie die Verbindung der Appliance zum Container/Bucket, der bzw. das für den Zugriff auf das Sicherungsarchiv in {{site.data.keyword.cloud_notm}} verwendet wird, wenn die lokale Umgebung noch intakt ist.
  * Verwenden Sie eine der beiden folgenden Methoden, um die Replikation auszusetzen oder zu stoppen:
    * Inaktivieren Sie die lokale AltaVault-Appliance.
    * Rufen Sie die Web-Management-Konsole der AltaVault-Appliance auf und klicken Sie auf **Speicher > Cloudeinstellungen > Replikation**. Wählen Sie **Replikation aussetzen** aus.
    * Rufen Sie eine Kopie der Konfigurationsdatei auf der lokalen AltaVault-Appliance ab und stellen Sie eine Zugriffsmöglichkeit über eine URL her. Die Konfigurationsdatei ist eine TAR-Datei (.tar).
  * Rufen Sie die Web-Management-Konsole der AltaVault-Appliance auf und klicken Sie auf **Konfigurieren > Installationsassistent > Konfiguration exportieren > Konfiguration exportieren**. Speichern Sie die Konfigurationsdatei (TAR-Datei) und übertragen Sie sie auf den Utility Server oder stellen Sie eine Zugriffsmöglichkeit über eine URL her.
    * Kenntnisse in Bezug auf die VMware Sphere-Terminologie und in Bezug auf die Verwaltung von vSphere ESXi-Umgebungen. Diese Kenntnisse sollten neben anderen Bereichen Kenntnisse zur Verwendung von vSphere Web Client sowie des vSphere-Clients und zur Zuweisung von Hardwareressourcen für den Netzbetrieb und die Speicherung umfassen.

### AltaVault-OVA bereitstellen
{: #deploying-altavault-ova-disaster}

Stellen Sie die OVA-Datei für AltaVault in der vSphere-Umgebung mithilfe des Utility Servers bereit, nachdem alle Voraussetzungen erfüllt sind. Anweisungen zur OVA-Bereitstellung finden Sie im [Installations- und Servicehandbuch für NetApp AltaVault ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}. Die Testversion von AltaVault ist mit vier virtuellen CPUs, 6 GB Hauptspeicher und einer Primärplatte mit 150 GB konfiguriert. Verwenden Sie das Installations- und Servicehandbuch, um die folgenden Schritte auszuführen:

1. Erhöhen Sie die Hauptspeicherkapazität auf 24 GB.
2. Fügen Sie eine sekundäre Plattenspeichereinheit hinzu, deren Kapazität kleiner-gleich 8 TB ist, um deduplizierte Sicherungsdaten zu speichern.
3. Ordnen Sie der AltaVault-Appliance unterschiedliche portierbare private Netze zu, nachdem die Hauptspeicher- und Plattenkonfigurationen geändert wurden.

Den NICs werden die folgenden Schnittstellenfunktionen zugewiesen:

* **Primär**: Wird für das AltaVault-Appliance-Management und die Datenreplikation in die Cloud verwendet. In der Beispielumgebung erfolgt eine Zuordnung zur Portgruppe **Primäres Netz**.
* **e0a**: Eine optionale Schnittstelle, die zum Replizieren von Daten von der AltaVault-Appliance in die Cloud verwendet wird.
* **e0b**: Eine Schnittstelle, die zum Exportieren des Mountpunkts für die SMB/CIFS- oder NFS-Freigabe verwendet wird. In der Beispielumgebung erfolgt eine Zuordnung zur Portgruppe **Share Network**.
* **e0c**: Eine optionale Schnittstelle, die zum Exportieren des Mountpunkts für die SMB/CIFS- oder NFS-Freigabe verwendet wird.

In der Beispielkonfiguration in dieser Prozedur verwendet die AltaVault-Appliance die Schnittstelle **Primary** als Schnittstelle zum Replizieren in die Cloud. Die Schnittstelle **e0b** wird zum Exportieren eines CIFS/SMB-Mountpunkts verwendet. **Hinweis:** Eine CIFS/SMB-Freigabe und eine NFS-Freigabe können nicht zusammen verwendet werden, um auf dieselben Daten zuzugreifen. Das verwendete Protokoll muss mit dem Protokoll identisch sein, das für die lokale AltaVault-Appliance verwendet wird, um Daten in die Cloud zu replizieren.

<!-- ![Figure 2](images/veeam_restore_fig3.png)
<br/>`Figure 2: IBM Cloud setup with VMware`-->

Weitere Informationen zum Bereitstellen der AltaVault-Appliance und zur Konfiguration der VM-Einstellungen finden Sie im [Installations- und Servicehandbuch für NetApp AltaVault ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://library.netapp.com/ecm/ecm_download_file/ECMLP2317733){: new_window}.

### AltaVault-Appliance konfigurieren
{: #configuring-the-altavault-appliance}

1. Sie können die AltaVault-VM einschalten, nachdem sie konfiguriert wurde. Es kann eine gewisse Zeit dauern, bis die VM gestartet wird, da die AltaVault-Appliance die Zusatzspeicherplatte formatiert.

2. Melden Sie sich bei der AltaVault-Konsole mit der Angabe **admin** für **Benutzername** und der Angabe **password** für **Kennwort** an, nachdem die Appliance den Bootprozess abgeschlossen hat. Sie können diese Berechtigungsnachweise nach Abschluss der Erstkonfiguration ändern. Siehe hierzu Tabelle 1: AltaVault-Erstkonfigurationswerte.

3. Nach der Anmeldung bei der Appliance werden Sie gefragt, ob zur Konfiguration der Assistent verwendet werden soll. Geben Sie *y* (Ja) ein.
4. Verwenden Sie die Informationen in Tabelle 1, nachdem der Assistent geöffnet wurde.
5. Drücken Sie die **Eingabetaste**, um Ihre Änderungen zu speichern.

|Frage|Antwort|
|---|---|
|Schritt 1: Administratorkennwort?|Geben Sie ein neues Administratorkennwort ein (es darf nicht **password** lauten).|
|Schritt 2: Hostname?|Geben Sie den entsprechenden Hostnamen ein.|
|Schritt 3: DHCP in der primären Schnittstelle verwenden?|Geben Sie **n** oder **no** (Nein) ein.|
|Schritt 4: Primäre IP-Adresse?|Geben Sie die primäre IP-Netzadresse ein. In der Beispielkonfiguration ist dies das Netz, das für die Managementschnittstelle verwendet wird (z. B. 10.120.108.132).|
|Schritt 5: Netzmaske?|Geben Sie die Netzmaske ein (z. B. 255.255.255.192).|
|Schritt 6: Standardgateway?|Geben Sie das Standardgateway ein (z. B. 10.120.108.129).|
|Schritt 7: Primärer DNS-Server?|Geben Sie den primären DNS-Server (DNS = Domain Name System) in Ihrer Umgebung ein.|
|Schritt 8: Domänenname?|Geben Sie den Domänennamen Ihrer Umgebung ein (z. B. 'testenv.org').|
{: caption="Tabelle 1. AltaVault-Erstkonfigurationswerte" caption-side="top"}

### AltaVault für IBM Cloud Object Storage konfigurieren
{: #configuring-altavault-for-ibm-cloud-object-storage}

Die AltaVault-Appliance muss nach der Konfiguration mit {{site.data.keyword.cos_full_notm}} Service verbunden werden. In der lokalen Konfiguration wird das öffentliche DNS verwendet, um eine Verbindung zum Object Storage Service herzustellen. In dieser Konfiguration wird stattdessen der interne DNS-Name verwendet.

1. Öffnen Sie einen Web-Browser und geben Sie die IP-Adresse der AltaVault-Appliance ein.
2. Melden Sie sich mit den Administratorberechtigungsnachweisen bei der Konsole an. Bei der ersten Anmeldung wird das **Assistentendashboard** angezeigt.
3. Wählen Sie **Systemeinstellungen** aus und stellen Sie sicher, dass alle Netzeinstellungen korrekt sind. Passen Sie die Zeitzone an, um die Zeitzone Ihrer Umgebung wiederzugeben.
4. Wählen Sie **Weiter > Speichern und anwenden > Beenden** aus. Das System ruft wieder das **Assistentendashboard** auf und Sie werden von Ihrer aktuellen Browsersitzung abgemeldet.
5. Melden Sie sich wieder bei der AltaVault-Appliance an und klicken Sie dann auf **Einstellungen > Installationsassistent > Konfiguration importieren** und geben Sie die Position der Konfigurationsdatei der lokalen AltaVault-Appliance an.
6. Wählen Sie **Nur gemeinsam genutzte Daten importieren** aus, sodass nur die gemeinsam genutzten Einstellungen wie beispielsweise die Cloud- und E-Mail-Einstellungen importiert werden.
7. Geben Sie im Textfeld **Kennphrase für Schlüssel** die Kennphrase für den Verschlüsselungsschlüssel ein, der während der Erstellung der lokalen AltaVault-Appliance angegeben wird (sofern vorhanden).
8. Klicken Sie auf **Konfiguration importieren > Beenden**.

<!--The AltaVault appliance web console displays a message indicating a successful import. If the import was not successful, verify that the configuration file is in the correct format and that the passphrase is correct before trying again.-->

Sie haben nun die lokale AltaVault-Appliance-Konfiguration in die AltaVault-Appliance von {{site.data.keyword.cloud_notm}} importiert.

Führen Sie die folgenden Schritte aus, um die Cloudeinstellungen der AltaVault-Appliance von {{site.data.keywrod.cloud_notm}} zu ändern, um den Zugriff auf {{site.data.keyword.cos_full_notm}} Service über das private Netz zu ermöglichen.

1. Rufen Sie in AltaVault die Optionen **Speicher > Cloudeinstellungen > Cloud** auf und ändern Sie den Hostnamen, um die private Adresse für {{site.data.keyword.cos_full_notm}} Service wiederzugeben.
  * Der Name des privaten Netzes wird im Syntaxformat '<location>.objectstorage.service.networklayer.com' angegeben. Hierbei steht *<location>* für den abgekürzten Namen des Rechenzentrums (z. B. steht 'mel01' für das Rechenzentrum Melbourne 01).
2. Klicken Sie auf **Anwenden**. Die AltaVault-Appliance versucht, eine Verbindung zu {{site.data.keyword.cos_full_notm}} Service herzustellen. Schlägt die Herstellung dieser Verbindung fehl, dann überprüfen Sie, ob die Einstellungen des Cloud-Providers korrekt sind, bevor Sie erneut versuchen, eine Verbindung herzustellen.

Nach Herstellung der Verbindung zu {{site.data.keyword.cos_full_notm}} muss die Appliance in den Wiederherstellungsmodus versetzt werden, um den Metadateninhalt der ursprünglichen Sicherungsdaten von {{site.data.keyword.cos_full_notm}} zu synchronisieren.

Führen Sie die folgenden Schritte aus, um die AltaVault-Appliance in den Wiederherstellungsmodus zu versetzen.

Die AltaVault-Appliance synchronisiert Daten aus {{site.data.keyword.cos_full_notm}}. **Hinweis:** Der für die Synchronisation erforderliche Zeitaufwand hängt von der Anzahl und vom Umfang der Sicherungen ab, die wiederhergestellt werden sollen.

1. Rufen Sie die AltaVault-Appliance auf, melden Sie sich an und geben Sie die Befehle ein, die in Abbildung 3 dargestellt werden:<br/>![Abbildung 3](images/veeam_restore_fig5.png)<br/>`Abbildung 5: Befehle für den Wiederherstellungsmodus`
2. Rufen Sie wieder die Webkonsole der AltaVault-Appliance auf, nachdem der Synchronisationsprozess abgeschlossen ist.
3. Überprüfen Sie, ob der **Speicheroptimierungsservice** **aktiv** ist und ob er sich im **Status** **Bereit** befindet. Die Änderung des **Status** in **Bereit** kann einige Minuten in Anspruch nehmen.

### CIFS/SMB-Mountpunkt in AltaVault konfigurieren
{: #configure-the-cifs-smb-mount-point-in-altavault}

Nach Herstellung der internen Verbindung zu {{site.data.keyword.cos_full_notm}} müssen Sie die Schnittstelle **e0b** für den Zugriff auf einen CIFS/SMB-Mountpunkt konfigurieren.

1. Rufen Sie in der AltaVault-Webkonsole die Optionen **Einstellungen > Datenschnittstellen** auf.
2. Erweitern Sie die Ansicht der Schnittstelle **e0b** und wählen Sie ggf. **Datenschnittstelle aktivieren** aus.
3. Geben Sie die IP-Adresse, die Teilnetzmaske und das Gateway an, die für den Mount der CIFS/SMB-Freigabe verwendet werden sollen. Vergewissern Sie sich, dass ein anderes Teilnetz verwendet wird, als für die primäre Schnittstelle.
4. Übernehmen Sie den Standardwert für **MTU**. Dieser lautet **1500 Byte**.
  * Obwohl die standardmäßige maximale Übertragungseinheit (MTU = Maximum Transmission Unit) auf 1.500 gesetzt ist, können Sie sie auf 9.000 ändern, wenn Sie Jumbo-Frames verwenden. Ihr ESXi-Host und die physische Infrastruktur müssen hierzu die Verwendung von Jumbo-Frames unterstützen. Standardmäßig unterstützt {{site.data.keyword.cloud_notm}} eine MTU-Größe von 9.000 Byte ohne Konfigurationsänderungen. 
5. Klicken Sie auf **Anwenden**. 

Die Mindestkonfiguration der AltaVault-Appliance ist nun erstellt, sodass die Kommunikation zwischen der Appliance, {{site.data.keyword.cos_full_notm}} und Veeam Backup & Replication ausgeführt werden kann.

## Veeam Backup & Replication
{: #veeam-backup-replication-recover}

Die Veeam Backup & Replication-Software bietet Funktionen für Gesamtsicherungen, die Replikation und die Wiederherstellung für VMs und deren Daten. Veeam Backup & Replication kann vollständig mit AltaVault Cloud Gateway Appliance integriert werden, sodass sich eine reibungslose Sicherungs- und Wiederherstellungserfahrung für den Benutzer ergibt.

### Veeam Backup & Replication bereitstellen
{: #deploy-veeam-backup-replication}

Im vorliegenden Beispiel wird eine Testversion von Veeam Backup & Replication Version 8 verwendet.

#### *Voraussetzungen*
{: #prerequisites-deploy-veeam-backup-replication}

Bevor Sie mit der Bereitstellung fortfahren, überprüfen Sie, ob die folgenden Voraussetzungen erfüllt sind:

* Sie verfügen über eine vorhandene AltaVault-Appliance, die zur Verwendung mit IBM Cloud Object Storage und mit Veeam Backup & Replication konfiguriert wurde.
* Sie haben eine Kopie von Veeam Backup & Replication für VMware-Umgebungen angefordert, die als eine einzige ausführbare Datei bereitgestellt wird. Wenden Sie sich an den zuständigen Veeam-Ansprechpartner, wenn Sie eine Kopie benötigen, oder laden Sie eine [30-Tage-Testversion ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](http://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window} herunter.
* Fordern Sie eine Lizenzdatei zur Verwendung mit Veeam Backup & Replication an. In den meisten Fällen wird diese Datei per E-Mail an die E-Mail-Adresse gesendet, die zum Download von Veeam Backup & Replication verwendet wurde. Wenn Sie diese Datei nicht erhalten haben, wenden Sie sich an Ihren Veeam-Ansprechpartner.<br/><br/>Die Lizenzdatei wird verwendet, um alle Funktionen von Veeam Backup & Replication zu aktivieren. Wenn diese Datei während der Programminstallation nicht bereitgestellt wird, werden alle Komponenten und Funktionen auf die der 30-Tage-Testversion zurückgesetzt.
* Sie verfügen über eine Virtual Server-Instanz (VSI), die in IBM Cloud mit den Spezifikationen bereitgestellt wird, die in Tabelle 2 enthalten sind. **Hinweis:** Bei dem installierten Betriebssystem muss es sich um eine 64-Bit-Version handeln.

||Mindestanforderung|Empfehlung|
|---|---|---|
|**Betriebssystem**|<ul><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows Server 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|<ul><li>Windows Servers 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2 SP1</li><li>Windows 2008 SP2</li><li>Windows 8.x</li><li>Windows 7 SP1</li></ul>|
|**Anzahl der Kerne oder vCPUs**|2|4|
|**Hauptspeicher**|4 GB Basisarbeitsspeicher (RAM) plus 500 MB für jeden gleichzeitigen Sicherungs- und Wiederherstellungsjob.|16 GB Basisarbeitsspeicher (RAM) plus 4 GB für jeden gleichzeitigen Sicherungs- und Wiederherstellungsjob.|
|**Plattenspeicherplatz**|2 GB für Produktinstallation, 10 GB pro 100 VMs für den Ordner des Gastsystemkatalogs (persistente Daten).|2 GB für Produktinstallation, 10 GB pro 100 VMs für den Ordner des Gastsystemkatalogs (persistente Daten).|
|**Netz**|1-Gbps-LAN für Sicherung und Replikation vor Ort, 1-Mbps-WAN für Sicherung und Replikation an anderen Standorten.|1-Gbps-LAN für Sicherung und Replikation vor Ort, 1-Mbps-WAN für Sicherung und Replikation an anderen Standorten.|
{: caption="Tabelle 2. Systemvoraussetzungen für {{site.data.keyword.cloud_notm}} VSI" caption-side="top"}

Stellen Sie die VSI (Virtual Server-Instanz) in demselben Rechenzentrum wie die AltaVault-Appliance und die vSphere-Umgebung bereit. Die VSI muss über Zugriff auf die AltaVault-Appliance und vCenter verfügen.

### Veeam Backup & Replication installieren
{: #installing-veeam-backup-replication-disaster}

Führen Sie die folgenden Schritte aus, um Veeam Backup & Replication auf der Virtual Server-Instanz (VSI) von {{site.data.keyword.cloud_notm}} zu installieren, nachdem alle Voraussetzungen erfüllt sind.

1. Doppelklicken Sie auf die ausführbare Datei des Programms und klicken Sie dann auf **Veeam Backup & Replication - Installieren**. Der Installationsassistent wird geöffnet.
* Klicken Sie auf **Weiter** und wählen Sie **Ich akzeptiere die Bedingungen der Lizenzvereinbarung** aus.
* Klicken Sie auf **Weiter** und geben Sie die Position der Lizenzdatei an, die unter 'Veeam Backup & Replication bereitstellen' angefordert werden kann.
* Klicken Sie auf **Weiter**. Wählen Sie dann die Veeam Backup & Replication-Komponenten aus, die Sie installieren wollen, und geben Sie die Installationsposition dieser Komponenten in der Anzeige für die **Programmkomponenten** an. **Hinweis:** **Veeam Backup & Replication** und **Veeam Backup Catalog** sind erforderliche Komponenten.
* Klicken Sie auf **Weiter**.<br/>Der Installationsassistent führt eine Reihe von Prüfungen durch, um sicherzustellen, dass alle erforderlichen Programmframeworks und Unterstützungskomponenten installiert wurden. Fehlen solche Komponenten, dann zeigt der Installationsassistent eine Option zum Installieren der betreffenden Komponenten an.<!--Click **Install** if this is the case.-->
2. Überprüfen Sie, ob alle Komponenten die Systemprüfung **bestanden** haben und klicken Sie dann auf **Weiter**.
3. Wählen Sie das **Servicekonto (Benutzer)** aus, unter dem der Veeam-Sicherungsservice ausgeführt werden soll. Das Standardservicekonto ist das Konto **LOCAL SYSTEM**. Klicken Sie auf **Weiter**.
4. Wählen Sie die **SQL Server-Instanz** aus, die zum Erstellen und Speichern der Veeam Backup & Replication-Datenbanken verwendet werden soll. Weitere Informationen erhalten Sie beim zuständigen Datenbankadministrator. Klicken Sie auf **Weiter**.
5. Geben Sie den **Katalogserviceport** (9393) und den **Veeam Backup-Serviceport** (9392) ein. Klicken Sie auf **Weiter**.
6. Wählen Sie die Verzeichnisse aus, in denen der Katalog des Gastdateisystems (persistente Daten) und der vPower-NFS-Schreibcache (nicht persistente Daten) gespeichert werden sollen. Klicken Sie auf **Weiter**.
7. Überprüfen Sie, ob alle Einstellungen und Werte korrekt sind, und klicken Sie dann auf **Installieren**. Klicken Sie nach Abschluss der Installation auf **Fertigstellen**.

### Veeam Backup & Replication konfigurieren
{: #configuring-veeam-backup-replication}

Nach der Installation von Veeam Backup & Replication sind Sie bereit für die Herstellung der Verbindung zur AltaVault-Appliance.

1. Starten Sie Veeam Backup & Replication.
2. Klicken Sie links unten in der Anzeige auf **Sicherungsinfrastruktur**.
3. Klicken Sie im Fenster **Sicherungsinfrastruktur** auf **Verwaltete Server > Verwaltete Server**.
4. Klicken Sie im oberen Menüband auf **Server hinzufügen** und doppelklicken Sie dann auf **VMware vSphere**.
5. Geben Sie die private IP-Adresse des vSphere-Servers ein und klicken Sie auf **Weiter**.
6. Geben Sie die Kontoberechtigungsnachweise eines lokalen Kontos ein, das über Administratorberechtigungen auf dem vSphere-Server verfügt, der zuvor angegeben wurde. **Hinweis:** Der Kontobenutzername muss bei Domänenkonten das Format 'DOMAIN\USER' und bei lokalen Konten das Format 'HOST\USER' aufweisen. Klicken Sie zum Hinzufügen eines Kontos auf **Hinzufügen** und geben Sie dann den entsprechenden Benutzernamen und das Kennwort ein. Ändern Sie den standardmäßigen VMware-Web-Service-Port nicht, es sei denn, Sie erhalten entsprechende Anweisungen vom zuständigen Netzadministrator. <!-- otherwise during [Veeam Backup & Replication installation](#_Installing_Veeam_Backup).--> Klicken Sie auf **Weiter**.
  * Veeam Backup & Replication versucht, eine Verbindung zum vSphere-Server herzustellen. Schlägt die Herstellung der Verbindung fehl, dann überprüfen Sie, ob das Konto vorhanden ist und über Administratorberechtigungen auf dem vSphere-Server verfügt, bevor Sie erneut versuchen, eine Verbindung herzustellen.
7. Klicken Sie auf **Fertigstellen**.
8. Überprüfen Sie, ob der vSphere-Server erfolgreich hinzugefügt wurde, indem Sie auf **Verwaltete Server > VMware vSphere** klicken.

### Sicherungsrepository zu Veeam Backup & Replication hinzufügen
{: #adding-a-backup-repository-to-veeam-backup-replication-disaster}

Führen Sie die folgenden Schritte aus, um ein neues Sicherungsrepository zu erstellen, in dem wiederhergestellte Sicherungen auf der AltaVault-Appliance gespeichert werden sollen. **Hinweis:** Für das vorliegende Beispiel wird eine CIFS/SMB-Freigabe verwendet, weil der Veeam Backup & Replication-Sicherungsserver auf einem System gehostet werden muss, auf dem Windows ausgeführt wird. Das Freigabeprotokoll, das zur Wiederherstellung von Sicherungen in der {{site.data.keyword.cloud_notm}}-AltaVault-Appliance verwendet wird, muss mit dem Freigabeprotokoll identisch sein, das für diese Sicherungen in der lokalen AltaVault-Appliance (CIFS/SMB-Freigabe) verwendet wird.

1. Klicken Sie unten links in der Anzeige auf **Sicherungsinfrastruktur > Sicherungsrepositorys**.
2. Klicken Sie im oberen Menüband auf **Repository hinzufügen**.
3. Ordnen Sie dem Repository einen eindeutigen **Namen** und eine passende **Beschreibung** zu. Klicken Sie auf **Weiter**.
4. Wählen Sie den Freigabetyp aus, der dem Freigabetyp entspricht, der in der lokalen AltaVault-Appliance verwendet wird. Beispiel: Wenn in der lokalen AltaVault-Appliance der Freigabetyp 'CIFS/SMB' verwendet wird, dann wählen Sie **Freigegebener Ordner** aus.
5. Geben Sie die Position an, unter der die Sicherungen der CIFS/SMB-Freigabe auf der AltaVault-Appliance wiederhergestellt werden sollen. Die Freigabe hat denselben Namen wie die Freigabe, über die die Sicherungen auf der lokalen AltaVault-Appliance verarbeitet wurden. Wenn z. B. die Freigabe mit dem Namen `cifs_test1` für Sicherungen auf der lokalen AltaVault-Appliance verwendet wird, dann geben Sie die Position der Freigabe `cifs_test1` auf der {{site.data.keyword.cloud_notm}}-AltaVault-Appliance an.
  * Öffnen Sie einen Web-Browser und geben Sie die IP-Adresse der AltaVault-Appliance ein, um die Position festzulegen.
  * Rufen Sie **Speicher > CIFS** auf und notieren Sie den **Freigabepfad** für die Freigabe. **Hinweis:** Dieser Pfad ist NICHT mit dem lokalen Pfad der Freigabe identisch. Das Format des Freigabepfads ist `\\<AltaVault appliance hostname>\<share name>`. Ersetzen Sie den Hostnamen der AltaVault-Appliance im Freigabepfad durch die IP-Adresse der Netzschnittstelle 'e0b' (Mountpunkt der Freigabe) der AltaVault-Appliance.<br/>
   * Klicken Sie im Fenster für das AltaVault-Appliance-Management auf **Einstellungen > Datenschnittstellen**, um die IP-Adresse der Schnittstelle
**e0b** zu suchen. Weitere Informationen zur Konfiguration von IFS/SMB-Mounts finden Sie im Abschnitt zur Konfiguration des CIFS/SMB-Mountpunkts in AltaVault.
   * Der Freigabepfad, der in Veeam Backup & Replication angegeben wird, lautet dann `\\10.120.108.133\cifs_test1` und nicht `\\restore-appliance\cifs_test1`.
6. Rufen Sie erneut Veeam Backup & Replication auf, geben Sie den Freigabepfad des Mountpunkts im Feld **Freigegebener Ordner** ein und klicken Sie auf **Weiter**. Veeam Backup & Replication versucht, eine Verbindung zum Mountpunkt herzustellen. Wenn die Verbindung fehlschlägt, kehren Sie zum vorherigen Schritt zurück und überprüfen Sie, ob die Netzeinstellungen für die AltaVault-Appliance korrekt sind, bevor Sie versuchen, die Verbindung erneut herzustellen.
7. Geben Sie in der Anzeige **Neues Sicherungsrepository** einen Wert zum **Begrenzen der maximalen Anzahl gleichzeitiger Tasks** auf die Anzahl der verfügbaren Ressourcen ein. Dieser Wert stellt die maximale Anzahl an Tasks dar, die von einem Sicherungsproxy an die ausgewählte Freigabe gesendet werden können. Die Standardanzahl paralleler Tasks beträgt vier. In AltaVault wird empfohlen, mit fünf gleichzeitig ablaufenden Tasks zu beginnen und diesen Wert je nach Ressourcenverfügbarkeit zu erhöhen oder zu reduzieren.
8. Klicken Sie auf **Weiter**.
9. Geben Sie die optionalen vPower-NFS-Einstellungen in der Anzeige **vPower NFS** an. Wenn das Kontrollkästchen **vPower-NFS-Server aktivieren** nicht ausgewählt wird, dann verwendet Veeam Backup & Replication vPower für die Wiederherstellung und die Verifizierung der Wiederherstellung. Klicken Sie auf **Weiter**.
10. Wählen Sie **Vorhandene Sicherungen automatisch importieren** aus, es sei denn, Sie verwenden die manuelle Auswahl von VM-Sicherungen.
11. Überprüfen Sie, ob alle Einstellungen korrekt sind, und klicken Sie dann auf **Weiter**.
* Klicken Sie auf **Fertigstellen**, um den Assistenten zu beenden.
12. Klicken Sie auf **Ja**, um fortzufahren.

Veeam Backup & Replication wird konfiguriert. Sie können nun mit der Wiederherstellung von Sicherungen beginnen.

### Umgebung wiederherstellen
{: #restoring-the-environment}

Führen Sie die folgenden Schritte aus, um einen VM-Restore vollständig wiederherzustellen.

1. Klicken Sie unten links in der Anzeige auf **Sicherung & Replikation**.
2. Klicken Sie im oberen Menüband auf **Wiederherstellen**.
3. Wählen Sie **Vollständige VM (einschließlich Registrierung)** aus. **Hinweis:** Treffen Sie unter **Aus Replikat wiederherstellen** keine Auswahl. Klicken Sie auf **Weiter**.
4. Klicken Sie auf **VM hinzufügen > Aus Sicherung**. Daraufhin werden die VMs angezeigt, die zuvor über die lokale AltaVault-Appliance in
{{site.data.keyword.cos_full_notm}} repliziert wurden, und außerdem die CIFS/SMB-Freigabe. Klicken Sie auf **Hinzufügen > Weiter**.
5. Stellen Sie die VMs an einer neuen Position <!--[because the original location of the VM(s) failed]--> wieder her, indem Sie die Option zum **Wiederherstellen an einer neuen Position oder mit anderen Einstellungen** auswählen. Klicken Sie auf **Weiter**.
6. Wählen Sie in der Anzeige **Host** einen Zielhost für jede VM aus, die Sie wiederherstellen. Wählen Sie eine VM aus und klicken Sie dann auf **Host**.
7. Wählen Sie unter **Host auswählen** den Zielhost aus und klicken Sie dann auf **OK**.<br/>Veeam Backup & Replication versucht, die Zielhosts zu kontaktieren, um sicherzustellen, dass sie sich im Onlinemodus befinden und bereit für den Datenempfang sind. Wenn dieser Versuch fehlschlägt, dann überprüfen Sie, ob alle Netzeinstellungen korrekt sind, bevor Sie die Operation wiederholen. Klicken Sie nach Abschluss der Überprüfung auf **Weiter**.
8. Wählen Sie für jede VM einen optionalen **Ressourcenpool** aus. Klicken Sie auf **Weiter**.
9. Wählen Sie einen Zieldatenspeicher und einen Plattentyp für jede VM aus, die Sie wiederherstellen wollen. Klicken Sie auf **Weiter**.<br/>Veeam Backup & Replication versucht, die Zieldatenspeicher zu überprüfen. Schlägt die Validierung fehl, dann müssen Sie überprüfen, ob der Datenspeicher über genügend Kapazitäten für die VMs verfügt, die wiederhergestellt werden sollen. Außerdem müssen Sie dann prüfen, ob alle Netzeinstellungen korrekt sind, bevor Sie den Verbindungsaufbau erneut versuchen.
10. Wählen Sie einen Zielordner in den Zieldatenspeichern für jede VM aus, die Sie wiederherstellen möchten. Klicken Sie auf **Weiter**.
11. Geben Sie Netzverbindungen und Einstellungen für jede VM an. Klicken Sie auf **Weiter**.
12. Geben Sie optional einen **Wiederherstellungsgrund** an, um zu begründen, warum die Wiederherstellungsoperation ausgeführt werden soll. Klicken Sie auf **Weiter**.<br/>Veeam Backup & Replication versucht, die VMs zu überprüfen, die von Ihnen wiederhergestellt werden. Warten Sie, bis diese Validierung erfolgreich abgeschlossen wurde, bevor Sie fortfahren.
13. Überprüfen Sie die Wiederherstellungsoperationen und -einstellungen im Fenster **Sicherung & Replikation** und klicken Sie dann auf **Fertigstellen**. Veeam Backup & Replication beginnt automatisch mit der Wiederherstellung der ausgewählten VMs.

## Nächste Schritte
{: #next-steps-recovering-your-vmware-vsphere-environment}

<!--The recent rise in Object Storage can be heavily attributed to enterprises' desire to reduce or even eliminate the operational and capital expenditures associated with comparatively expensive on-premises storage. While Object Storage is a highly cost-effective solution, it introduces a new problem of how to access stored files. In the case of stored data backups, the issue is how to use them to recover an environment that has failed for whatever reason.-->

<!--This problem is handily resolved by Veeam Backup & Replication, along with a NetApp AltaVault Cloud Gateway appliance, and {{site.data.keyword.cos_full_notm}}. When used together, they create a seamless backup and recovery experience that can be tailored to fit a wide variety of environmental requirements and constraints. By following the steps outlined in this procedure, enterprises can quickly realize the benefits of cloud-based storage, while reducing the complexities involved in shifting from on-premises storage to Object Storage using the three aforementioned technologies.-->

Weitere Informationen zu den Komponenten dieser Hybridlösung finden Sie in den folgenden Quellen:

* [NetApp AltaVault-Website ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](http://www.netapp.com/us/products/protection-software/altavault/){: new_window}
* [Veeam Backup & Replication-Website ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.veeam.com/vm-backup-recovery-replication-software.html){: new_window}
* [IBM Cloud Object Storage-Website ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.ibm.com/cloud/storage){: new_window}

*Utility Server* bezeichnet einen Server, der den VMware vSphere-Client enthält, der für den Zugriff auf eine private Umgebung verwendet wird und über den die AltaVault-Appliance, die für die Datenwiederherstellungsoperationen verwendet wird, bereitgestellt wird.
