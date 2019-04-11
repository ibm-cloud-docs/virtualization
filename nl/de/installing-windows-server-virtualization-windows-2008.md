---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-23"

subcollection: virtualization

keywords: Windows 2008
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Windows Server-Virtualisierung unter Windows 2008 installieren
{: #installing-windows-server-virtualization-on-windows-2008}

<!--Windows 2008 64-bit edition comes with the option to install Windows next generation virtualization application codenamed Veridian. By default the application will not be a selectable option when Adding and Removing Roles from Windows 2008.

Note: At this time, this is only available in full installation of 64-bit editions of Windows 2008. 32-bit versions and Server-core installations do not support this.-->

## Vorbereitende Schritte
{: #before-you-begin-win-2008}

Bevor Sie beginnen, sollten Sie prüfen, ob die folgenden Voraussetzungen erfüllt sind.

### Hardware
{: #hardware}

* Aktivierung der hardwaregestützten Virtualisierungstechnologie im BIOS
* Intel VT
* AMD-V
* Aktivierung von Data Execution Prevention im BIOS
* Intel Execute Disable (XD)
* AMD No Execute (NX)

### Software
{: #software}

Zum Aktivieren der Option **Rolle** für die Windows Server-Virtualisierung müssen verschiedene Patches installiert werden.
1. Öffnen Sie ein Browserfenster und navigieren Sie zu '%sysdir%\Windows\wsv' (normalerweise 'C:\Windows\wsv'). Dieser Ordner enthält zwei Dateien:
    * Windows6.0-KB939853-x64.msu
    * Windows6.0-KB939854-x64.msu
2. Diese Patches können in beliebiger Reihenfolge installiert werden. Installieren Sie beide Patches und führen Sie dann für das System einen Neustart durch.

## Installation
{: #installation}

1. Nach dem Neustart des Systems müssen Sie die **Rolle** zum System hinzufügen.
2. Nachdem Sie die Rolle hinzugefügt haben, wird das Dialogfenster **Virtuelle Netzwerke erstellen** aufgerufen.
**Hinweis:** Die Netzkonnektivität geht für kurze Zeit verloren.
3. Wählen Sie **LAN-Verbindung** für den Adapter des privaten Netzes aus. Klicken Sie auf **Weiter**. Die Installation wird fortgesetzt und fordert die Ausführung eines Neustarts an.
4. Nach dem Neustart müssen Sie sich am System über die öffentliche Verbindung anmelden. **Hinweis:** Sie müssen sich mit den gleichen Anmeldeberechtigungsnachweisen anmelden, die zur Installation dieser Verbindung benutzt wurden. Die Netzkonnektivität zur Schnittstelle geht verloren. Andernfalls wird möglicherweise der folgende Fehler ausgegeben:
    * *Die Konfiguration der Windows Server-Virtualisierung ist mit Fehlercode 0x80078000 fehlgeschlagen.*
Zur Behebung des Fehlers rufen Sie **Start > Programme > Verwaltung > Windows-Virtualisierungsmanagement** in der Managementkonsole für die Windows-Virtualisierung auf.
5. Klicken Sie im rechten Teilfenster auf den gewünschten Server.
6. Klicken Sie dann im Aktionsteilfenster auf **Virtual Network Manager**. Nun wird **Switch-Management für virtuelles Netz** angezeigt.
7. Klicken Sie im linken Teilfenster unter **Neu hinzufügen** auf den gewünschten Netzswitch.
8. Benennen Sie den Netzswitch in 'Privat' um.
9. Wählen Sie den physischen Netzadapter für **Verbindung** und dann den ersten Netzadapter aus. Die Bindung aller Netzprotokolle zur Schnittstelle des privaten Netzes wird nun aufgehoben.
10. **WICHTIG:** Um die Netzkonnektivität für das private Netz wiederherzustellen, müssen Sie die neue Switcheinheit und **NICHT** die private Schnittstelle konfigurieren. Rufen Sie **Start > Einstellungen > Netzwerkverbindungen** auf. Daraufhin wird eine neue Einheit mit dem Namen **LAN-Verbindung 2** angezeigt.
11. Klicken Sie mit der rechten Maustaste auf diese Verbindung und wählen Sie dann **Eigenschaften** aus.
12. Wählen Sie **ipv4** und die zugehörigen Eigenschaften aus. Sie müssen diese Einheit mit der IP-Adresse, der Netzmaske und den DNS-Servern der Schnittstelle des privaten Netzes konfigurieren.
13. Klicken Sie auf **OK > Schließen**. Diese Konfiguration aktiviert den Netzbetrieb auf der privaten Seite erneut. Sie können überprüfen, ob das Netz privat ist, indem Sie ein Pingsignal an die private IP senden.

Senden Sie ein RDP-Signal an die private IP, um das öffentliche Netz einzurichten.

### Öffentlichen Switch hinzufügen
{: #adding-a-public-switch}

Das Hinzufügen eines öffentlichen Switchs erfolgt über den gleichen Prozess wie das Hinzufügen des privaten Switchs.
1. Rufen Sie wieder **Switch-Management für virtuelles Netz** auf und wählen Sie **Neuen Netzswitch hinzufügen** aus.
2. Wählen Sie als Typ des Netzswitchs **Extern** aus und klicken Sie auf **Hinzufügen**.
3. Benennen Sie den Switch in **Öffentlich** um und wählen Sie dann **Physischer Netzadapter** aus.
4. Wählen Sie den zweiten Netzadapter aus und klicken Sie auf **OK**. Diese Konfiguration bewirkt, dass der öffentliche Port nicht auf Anforderungen des Netzes reagiert. Konfigurieren Sie die Schnittstelle des neuen öffentlichen Switchs wie beim privaten Switch mit den entsprechenden Einstellungen.
