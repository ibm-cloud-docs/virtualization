---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-14"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Einführung zu VMware ESXi
{: #getting-started-with-vmware-esxi}

Nach der Bereitstellung des ESX-Servers müssen Sie als ersten Schritt für den Zugriff auf Ihren Server eine Verbindung zu {{site.data.keyword.cloud}} Private Network VPN herstellen. Standardmäßig verfügen alle von {{site.data.keyword.cloud_notm}} bereitgestellten VMware Server-Systeme nur über die Schnittstelle für das private Netz oder über eine Bindung, die mit einer IP-Adresse konfiguriert wurde. Diese Bereitstellung ist eine zusätzliche Sicherheitsmaßnahme, die angewendet wird, um die Zugriffsmöglichkeiten des öffentlichen Internets auf Ihren neuen ESX-Server einzuschränken. Aufgrund dieser zusätzlichen Sicherheitsfunktion ist der Server nur über eine private {{site.data.keyword.cloud_notm}}-IP-Adresse im Format 10.x.x.x emfangsbereit. Auf diese Adresse kann nur über das {{site.data.keyword.cloud_notm}}-VPN oder einen anderen vorhandenen {{site.data.keyword.cloud_notm}}-Host mit Zugriff auf dasselbe private VLAN zugegriffen werden, in dem sich auch Ihr neues VMware Server-System befindet.

Weitere Informationen zum {{site.data.keyword.cloud_notm}}-VPN finden Sie im Abschnitt mit der [Einführung zu Virtual Private Networking (VPN)](/docs/infrastructure/iaas-vpn?topic=VPN-getting-started-with-virtual-private-networking-vpn-).

## Auf ESX-Host zugreifen
{: #accessing-your-esx-host}

Nach der Herstellung der Verbindung zum {{site.data.keyword.cloud_notm}}-VPN können Sie mit dem neuen VMware Server-System kommunizieren. ESX wird von einem vSphere-Client verwaltet. Sie können Daten von Ihrem neuen VMware Server-System abrufen, indem Sie die IP-Adresse des privaten Netzes in einem Web-Browser eingeben und dann auf der daraufhin aufgerufenen Seite auf den Link für den Download des vSphere-Clients klicken.

Nach dem vSphere-Client-Download und der Installation des Produkts auf Ihrer lokalen Workstation können Sie die Anwendung starten und die private Adresse und die Anmeldeberechtigungsnachweise Ihres Servers eingeben. Ihre Anmeldeberechtigungsnachweise werden auf der Seite 'Einheiten' im {{site.data.keyword.slportal_full}} angezeigt, nachdem Sie auf die Registerkarte 'Kennwörter' geklickt haben.

1. Geben Sie die private IP-Adresse, den Rootbenutzer und das Kennwort des Servers in den entsprechenden Feldern des vSphere-Clients ein und klicken Sie dann auf **Anmelden**, um eine Verbindung herzustellen.
2. Nachdem die Verbindung zum Server hergestellt wurde, rufen Sie den neuen ESX-Host auf, indem Sie **Bestand** auswählen.
3. Ihr ESX-Host wird auf der daraufhin aufgerufenen Seite als verfügbarer Knoten für die Konfiguration angezeigt. Von dort aus haben Sie verschiedene Optionen zum Bereitstellen einer virtuellen Maschine. Sie können z. B. eine Installations-ISO Ihres bevorzugten Betriebssystems in den lokalen Datenspeicher des Servers hochladen. Nach dem ISO-Upload können Sie die ISO als Installationsdatenträger für die Erstellung einer VM auswählen.  

## ISO hochladen
{: #uploading-an-iso}

Führen Sie die folgenden Schritte aus, um eine ISO in den Datenspeicher des Servers hochzuladen.

1. Markieren Sie den Server (dargestellt durch ein Serversymbol und seine private IP-Adresse) im linken Teilfenster und wählen Sie die Registerkarte **Konfiguration** im rechten Teilfenster aus.
2. Wählen Sie **Hardware > Speicher** aus. Vergewissern Sie sich, dass für **Anzeigen** die Einstellung **Datenspeicher** ausgewählt ist.
3. Klicken Sie mit der rechten Maustaste auf den entsprechenden Datenspeicher und klicken Sie dann auf **Datenspeicher anzeigen**.
4. Auf der daraufhin angezeigten Seite ist ein Dateimanager zum Anzeigen, Hochladen und Herunterladen von Dateien in den und aus dem Datenspeicher verfügbar.  
  * Wenn Sie eine ISO hochladen möchten, klicken Sie auf das Symbol für Datenträger (mit dem Aufwärtspfeil) und wählen Sie **Datei hochladen** aus.
5. Wählen Sie die ISO-Datei, die Sie in den Datenspeicher hochladen möchten, in Ihrem lokalen Dateisystem aus und klicken Sie auf **Öffnen**.
6. Die ISO Ihrer Wahl befindet sich jetzt im Datenspeicher.
7. Da sich Ihr Installationsdatenträger jetzt auf dem VMWare Server-System befindet, können Sie mit dem [Erstellen der virtuellen Maschine](/docs/infrastructure/vmware?topic=VMware-creating-a-vmware-esx-virtual-machine) fortfahren.
