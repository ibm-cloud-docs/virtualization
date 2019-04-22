---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-14"

subcollection: virtualization

keywords: Avago SafeStore, encryption
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Laufwerksicherheit mit Avago SafeStore Encryption Services aktivieren
{: #enabling-drive-security-by-using-avago-safestore-encryption-services}

Die Einrichtung der Laufwerksicherheit ermöglicht es Ihnen, den Zugriff auf Daten, die auf entfernten Platten gespeichert sind, ohne einen Sicherheitsschlüssel zu verhindern. Ohne diesen Schlüssel können die auf dem Laufwerk gespeicherten Daten nicht wiederhergestellt werden. {{site.data.keyword.cloud}} stellt in ausgewählten Rechenzentren SEDs (Self-Encrypting Drives) für die Laufwerke zur Verfügung, die für Bare Metal Server erworben werden können. In den Rechenzentren in USA werden SATA-Laufwerke mit einer Kapazität von 10 TB angeboten.

## Voraussetzungen
{: #prerequisites-enabling-drive-security-by-using-avago-safestore-encryption-services}

* Bare Metal Server mit SEDs – 10 TB SATA
* LSI/AVAGO MegaRAID SAS 9361 -8i oder vergleichbare LSI/AVAGO RAID-Karten
* Installierte Software für Mega RAID Storage Manager

## Laufwerksicherheit mit Mega RAID Storage Manager (MSM) aktivieren
{: #enabling-drive-security-by-using-megaraid-storage-manager-msm-}

Sie können den Sicherheitsschlüssel und die Sicherheitsdaten darin mithilfe von MegaRAID Storage Manager festlegen. Sie können auch die WebBIOS-Schnittstelle benutzen. Hierzu muss beim Start des Servers auf das MegaRAID-Karten-BIOS zugegriffen werden, um die Sicherheitseinstellung des Laufwerks zu konfigurieren. Weitere Informationen zu MegaRAID Controller Card SAS 9361-8i finden Sie auf der Broadcom-Site unter [MegaRAID SAS 9361-8i ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://www.broadcom.com/products/storage/raid-controllers/megaraid-sas-9361-8i#documentation).

### Vorinstallierte SEDs identifizieren
{: #identifying-preinstalled-sed-drives}

MegaRAID Storage Manager wird für die meisten unterstützten Betriebssysteme vorinstalliert bereitgestellt. Andernfalls können Sie die Software über die Broadcom-Site manuell installieren. Weitere Informationen zu diesem Thema finden Sie in den [Downloads für MegaRAID SAS 9361-8i](https://www.broadcom.com/products/storage/raid-controllers/megaraid-sas-9361-8i#downloads).

Sie können MegaRAID Storage Manager öffnen, indem Sie die Systemberechtigungsnachweise angeben. Im vorliegenden Beispiel wird ein Windows-System verwendet und MSM (Mega RAID Storage Manager) ist vorinstalliert.

Wenn Sie MSM starten, müssen Sie Werte für **Benutzername** und **Kennwort** eingeben. Hierbei muss der privilegierte Benutzer (Administrator) und das zugehörige Kennwort verwendet werden.

<!--![Figure 1](images/1_adapter_login.jpg)-->

Klicken Sie auf die Registerkarte **Physisch**. Klicken Sie dann auf die Laufwerke, die auf dem System verfügbar sind. Im Teilfenster **Eigenschaften** finden Sie die **Eigenschaften für die Laufwerksicherheit** und das Feld für die **Unterstützung der vollständigen Plattenverschlüsselung**, das auf **Ja** eingestellt ist. Im verwendeten Beispiel werden zwei Nicht-SEDs und vier SEDs verwendet.

<!--![Figure 2](images/1_fde_capable_drives.jpg)-->

### Laufwerksicherheit auf Controller aktivieren
{: #enabling-drive-security-at-the-controller}

1. Zum Aktivieren der Laufwerksicherheit müssen Sie mit der rechten Maustaste auf der Registerkarte **Physisch** auf **Controller 0 :AVAGO MegaRAID SAS 9361-8i** klicken und dann **Laufwerksicherheit aktivieren** auswählen.
  * Nun können Sie die **Sicherheitsschlüssel-ID** und den **Sicherheitsschlüssel** eingeben. Wenn Sie über mehrere Sicherheitsschlüssel verfügen, dann kann eine Sicherheitsschlüssel-ID hilfreich sein, um den benötigten Sicherheitsschlüssel zu identifizieren. Sie müssen den Sicherheitsschlüssel an einem sicheren Ort aufbewahren. Der Sicherheitsschlüssel ist erforderlich, wenn Sie Laufwerke neu konfigurieren und z. B. entfernen oder erneut einsetzen wollen. Ohne den Sicherheitsschlüssel können die Daten, die auf einem Datenträger gespeichert sind, der auf Basis der SEDs erstellt wurde, nicht abgerufen werden. Ein vergessener Sicherheitsschlüssel kann nicht wieder abgerufen werden. Außerdem kann ein Startkennwort festgelegt werden, mit dem das System blockiert werden kann, bis das hier definierte Kennwort eingegeben wird. Das Startkennwort ist optional. Wird es festgelegt, dann müssen Sie sich bei IPMI anmelden und es bei jedem Reboot des Systems angeben. Blättern Sie nach unten und aktivieren Sie das Kontrollkästchen **Ich habe die Sicherheitseinstellungen für zukünftige Bedarfsfälle aufgezeichnet** und klicken Sie auf **Ja**, um die Laufwerksicherheit zu aktivieren.
  * Wenn die Laufwerksicherheit aktiviert ist, wird ein gelbes Schlüsselsymbol für **Controller 0 AVAGO MegaRAID SAS 9361-8i** angezeigt.
2. Erstellen Sie nun mithilfe der SEDs einen sicheren Datenträger. Sie können auf der Registerkarte **Logisch** auf **Controller0** klicken und dann Folgendes auswählen:  
**Virtuelles Laufwerk erstellen**.
3. Wählen Sie die Option **Erweitert** aus. In der Anzeige muss die **RAID-Stufe** und die **Methode für die Laufwerksicherheit** mit **Vollständige Plattenverschlüsselung (FDE)** angegeben werden. Wählen Sie die erforderlichen FDE-Laufwerke aus und klicken Sie auf **Hinzufügen** > **Laufwerkgruppe erstellen** > **Weiter** aus.
4. Überprüfen Sie die Einstellungen des virtuellen Laufwerks und nehmen Sie die erforderlichen Änderungen vor. Die vorgeschlagene Einstellung für die **Leserichtlinie** lautet **Immer vorauslesen**. Die vorgeschlagene Einstellung für die **Schreibrichtlinie** ist **Zurückschreiben**. Klicken Sie auf **Virtuelles Laufwerk erstellen**. Akzeptieren Sie die Auswirkungen der Richtlinie zum Zurückschreiben aufgrund von BBU, indem Sie auf **Ja** klicken. Klicken Sie auf **Weiter** und überprüfen Sie die Daten in der Anzeige mit der Zusammenfassung. Klicken Sie auf **Fertigstellen**.
5. Um zu bestätigen, dass die virtuelle Platte gesichert wurde, müssen Sie auf die Registerkarte **Logisch** und das virtuelle Laufwerk klicken, das erstellt wurde. Daraufhin werden die **Eigenschaften der Laufwerksicherheit** angezeigt. Im Feld **Gesichert** ist **Ja** angegeben.

<!--![Figure 3](images/2_enable_drive_security.jpg)-->
<!--![Figure 4](images/3_security_key_details_page.jpg)-->
<!--![Figure 5](images/4_security_key_set_0.jpg)-->
<!--![Figure 6](images/9_create_vd_with_fde_drives.jpg)-->
<!--![Figure 7](images/10_create_vd_advanced_select_raid_drive_encryption_0.jpg)-->
<!--![Figure 8](images/create_vd_settings.jpg)-->
<!--![Figure 9](images/6_vd_secured_confirmation_0.jpg)-->

Wenn zum Lieferumfang des Servers RAID-Datenträger gehören, die bereits mithilfe von SEDs erstellt wurden, dann können Sie den Datenträger wie folgt als gesichert definieren.

Klicken Sie auf der Registerkarte **Logisch** mit der rechten Maustaste auf **Laufwerkgruppe** und wählen Sie dann **Mit FDE sichern** aus. Wenn Sie für einen Datenträger über FDE- und Nicht-FDE-Laufwerke verfügen, dann wird diese Option nicht angezeigt.

<!--![Figure 10](images/5_secure_existing_vd_with_fde_drives_0.jpg)-->

Um die Laufwerksicherheit zu entfernen, müssen Sie zuerst die gesicherten virtuellen Platten löschen und dann mit der rechten Maustaste auf Controller 0 klicken (**Laufwerksicherheit inaktivieren**). Mit dieser Funktion werden die gespeicherten Daten auf sichere Weise gelöscht und die Laufwerksicherheit wird entfernt.

Sie können die Laufwerksicherheit auch mithilfe von webBIOS und durch Anmeldung über die IPMI während des Startvorgangs einrichten. Greifen Sie dazu auf das RAID-BIOS zu. Weitere Informationen hierzu finden Sie im Abschnitt zu **Avago SafeStore Encryption Services** im **Benutzerhandbuch für 12 Gb/s MegaRAID SAS Software**.
