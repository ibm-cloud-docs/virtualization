---
copyright:
  years: 2014, 2018
lastupdated: "2018-06-22"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Virtuelle Hyper-V-Maschine migrieren
{: #migrating-a-hyper-v-virtual-machine}

## Vorbereitende Schritte
{: #before-you-begin-migrating-a-hyper-v-virtual-machine}

Bevor Sie beginnen, sollten Sie prüfen, ob die folgenden Voraussetzungen erfüllt sind.

* Ein kompatibler Server (Windows 2008 Server DC Edition w/Hyper-V).
* Eine Methode zum Herstellen einer Verbindung vom Quellenserver zum Zielserver.
* Quellen- und Zielserver müssen sich in demselben VLAN befinden. Wenn sie nicht in demselben VLAN enthalten sind, ist die erneute Ausgabe einer IP-Adresse für die virtuelle Maschine erforderlich (dazu ist das Öffnen eines Support-Tickets nötig).
* Virtuelle Maschinen mit funktionierenden Uplinks für das Netz und installierter funktionierender/unterstützter Software.

## Gründe für Verlagerung der virtuellen Hyper-V-Maschine
Es gibt zwei allgemeine Gründe, die eine Verlagerung Ihrer virtuellen Hyper-V-Maschine erforderlich machen können:
* Die virtuelle Maschine befindet sich auf einer Hardwareeinheit, die nicht korrekt funktioniert.
* Auf dem aktuellen Host-Server ist ein Ressourcenengpass aufgetreten.
In beiden Fällen kann die Hyper-V-Migration schnell abgeschlossen werden, wenn die hier aufgeführten Voraussetzungen erfüllt sind. Führen Sie die folgenden Schritte aus.

1. Melden Sie sich beim Quellenserver an und öffnen Sie den Hyper-V Manager. Wählen Sie die virtuelle Maschine aus, die Sie auf den Zielserver migrieren möchten.
2. Führen Sie für die virtuelle Maschine, die migriert werden soll, einen Systemabschluss durch. Wählen Sie dann in der Liste der Serveraktionen **Exportieren** aus und geben Sie die Position der Exportdatei an.
3. Nun können Sie sich (unter Verwendung von RDP auf dem Quellenserver) auf dem Zielserver anmelden. Hierzu muss das Laufwerk C: angehängt sein, um die Datei übertragen zu können.

Sie übertragen die Datei mit einer Ressourcenmountoperation mithilfe von RDP. Sie können die am besten geeignete Übertragungsmethode für diesen Prozess auswählen (Windows Sharing, Ressourcenmountoperation via RDP, FTP oder eine andere Übertragungsmethode).
{:tip}

4. Vergewissern Sie sich beim Export der Datei auf den Zielserver, dass sie sich an der Standardposition für virtuelle Hyper-V-Festplatten (VHDs = Virtual Hard Disks) befindet, und importieren Sie sie. Die Standardposition ist `C:\Users\Public\Documents\Hyper-V\Virtual hard disks`. Wenn Sie diese Standardposition geändert haben und sie nicht mehr wissen, dann können Sie **Hyper-V-Einstellungen > Virtuelle Maschine importieren** auswählen, um sie anzuzeigen.
5. Nachdem Sie die exportierte Datei lokalisiert haben, klicken Sie auf **Importieren**. Die virtuelle Maschine wird mit allen Konfigurationseinstellungen und zuvor zugehörigen Dateien in den Hyper-V Manager geladen. Der Server ist jetzt online und arbeitet ordnungsgemäß. Wenn Ihr Server die Voraussetzungen nicht erfüllt hat und wenn sich der Zielserver in einem anderen VLAN befindet, dann müssen Sie der virtuellen Maschine eine neue IP mit einem portierbaren Teilnetz (Routing als 'Sekundär im VLAN') zuordnen.
