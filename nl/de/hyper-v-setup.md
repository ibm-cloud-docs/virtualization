---



copyright:
  years: 2014, 2018
lastupdated: "2018-08-16"

subcollection: virtualization


---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}

# Hyper-V einrichten
{: #setting-up-hyper-v}

Der Prozess zum Einrichten von Hyper-V umfasst die Erstkonfiguration von Hyper-V, die Erstellung einer virtuellen Maschine und die Netzkonfiguration auf der neuen virtuellen Maschine. In den folgenden Anleitungen werden diese Prozesse erläutert.

## Hyper-V Manager verwenden
{: #using-hyper-v-manager}

Hyper-V Manager ist die Managementkonsole, die Sie zum Arbeiten mit Hyper-V verwenden. Über den Hyper-V-Manager können Sie jede Ihrer virtuellen Maschinen erstellen, starten, stoppen, löschen und konfigurieren. In der Managementkonsole können Sie außerdem die Standardposition für die Speicherung der virtuellen Festplatten und die Standardposition für die Speicherung der Konfigurationsdateien der virtuellen Maschine festlegen.

Der Hyper-V Manager kann durch Auswahl von **Verwaltung** in der Windows-Systemsteuerung aufgerufen werden. Bei einer Neuinstallation von Windows 2008 finden Sie die Option **Verwaltung** unter 'Programme' im Startmenü. Nachdem Sie das Hyper-V Manager-Programm geöffnet haben, wird ein neues Fenster in der Anzeige geöffnet. Machen Sie sich mit dieser Anzeige vertraut. Alle Konfigurationsmaßnahmen sowie die Verwendung der virtuellen Maschinen finden in dieser Anzeige statt.

## Hyper-V konfigurieren
{: #configuring-hyper-v}

Als erstes muss in der Konfiguration die Speicherposition für die virtuellen Festplatten geprüft werden. Im Fenster für den Hyper-V Manager finden Sie auf der linken Seite den **Hyper-V Manager** und den Servernamen. Klicken Sie auf den Namen des gewünschten Servers. Daraufhin werden die Informationen angezeigt, die für den Hyper-V-Service auf Ihrem Server zur Verfügung stehen. Momentan ist der mittlere Bereich der Anzeige leer, da noch keine virtuellen Maschinen installiert sind. Auf der rechten Seite der Anzeige befindet sich ein Link für die **Hyper-V-Einstellungen**. Wenn Sie auf den Link klicken, dann werden die grundlegenden Einstellungen für Hyper-V angezeigt.

Die primäre Einstellung, die Sie prüfen müssen, ist **Virtuelle Festplatten**. Auf der rechten Seite finden Sie die Standardspeicherposition für Ihre virtuellen Festplatten. Die Standardposition lautet wie folgt:

`C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks`

Bei der Erstellung einer virtuellen Maschine können Sie eine spezielle Position angeben, wenn Sie eine bestimmte virtuelle Festplatte nicht unter der Standardposition speichern möchten. Beispiel: Sie möchten die virtuellen Festplatten auf einem zweiten Laufwerk mit einer höheren Speicherkapazität (Laufwerk D im vorliegenden Beispiel) speichern. Diese virtuellen Laufwerke sollen im Ordner **VirtualMachine** gespeichert werden. Geben Sie im Feld hierzu `D:\VirtualMachine` ein.

****

Sie können die Ordner auch manuell durchsuchen, um die gewünschte Position auszuwählen. Klicken Sie auf **OK**, nachdem Sie eine Position ausgewählt haben.

## Netzeinheiten konfigurieren
{: #configuring-network-devices}

Vor der Konfiguration von virtuellen Netzeinheiten müssen Sie feststellen, welche Netzadapter mit dem privaten und dem öffentlichen Netz verbunden sind. Klicken Sie im Startmenü auf **Netzwerkverbindungen**. Daraufhin werden zwei Netzeinheiten angezeigt. Notieren Sie die Namen der Einheiten, nämlich **PrivateNetwork** und **PublicNetwork**. Klicken Sie mit der rechten Maustaste auf **PrivateNetwork** und wählen Sie dann **Eigenschaften** aus. Daraufhin wird **Verbinden über:** angezeigt. Hier finden Sie den Einheitennamen für den Adapter für das private Netz. Notieren Sie diesen Namen, da Sie ihn benötigen, wenn Sie die Einheit für Ihr virtuelles privates Netz erstellen.

### Einheit für privates Netz konfigurieren
{: #configuring-a-private-network-device}

Nachdem Sie nun die Standardposition zum Speichern der virtuellen Festplatten konfiguriert haben, müssen Sie die Einheiten des virtuellen Netzes konfigurieren, die von den virtuellen Maschinen benutzt werden sollen. Klicken Sie zum Öffnen von Virtual Network Manager auf der rechten Seite des Fensters für den Hyper-V Manager auf **Virtual Network Manager**.

Wenn Sie dieses Fenster zum ersten Mal öffnen, sehen Sie die virtuellen Netze links mit der einzigen aufgelisteten Option **Neues virtuelles Netzwerk**. Auf der rechten Seite stehen Ihnen drei Optionen zur Verfügung:
* Extern
* Intern
* Privat.
Stellen Sie sicher, dass **Extern** ausgewählt ist, und klicken Sie auf **Hinzufügen**.

Fahren Sie mit der Eingabe von Informationen in die verbleibenden Felder fort:
* **Name** Der Name, der dieser Netzeinheit zugeordnet ist. Im vorliegenden Beispiel können Sie _Privat_ verwenden, um anzugeben, dass diese Einheit mit dem privaten IBM Cloud-Netz verbunden ist.
* **Verbindungstyp** Wählen Sie die Einheit aus, die Ihrem privaten Netz zugeordnet ist. Dieser Name ist der Einheitenname, der zuvor im Fenster **Netzwerkverbindungen** angezeigt wurde. Nach Auswahl der korrekten Einheit können Sie auf **OK** klicken. Daraufhin wird ein Warnung angezeigt, in der Sie darüber informiert werden, dass die Netzverbindung möglicherweise unterbrochen wird. Klicken Sie auf **Ja**, um fortzufahren.

### Einheit für öffentliches Netz konfigurieren
{: #configuring-a-public-network-device}

Ihre Einheit für das private Netz ist jetzt installiert. Wiederholen Sie die hier aufgeführten Schritte für das öffentliche Netz. Verwenden Sie im vorliegenden Beispiel den Namen **Öffentlich** als Einheitennamen. Wählen Sie unter 'Verbindungstyp' die andere Netzeinheit aus, die in der Konfiguration des privaten Netzes nicht verwendet wurde. Nachdem Sie sowohl die öffentliche als auch die private Netzeinheit erstellt haben, klicken Sie auf **OK**.

## Installationsmedien anfordern
{: #obtaining-installation-media}

Nachdem die virtuellen Netzeinheiten erstellt wurden, müssen Sie jetzt die Installationsmedien anfordern. Im vorliegenden Beispiel wird als Installationsmedium eine Datei mit einem CD/DVD-Image verwendet. IBM Cloud unterstützt die folgenden Betriebssysteme für Hyper-V:
* Windows 2008
* Windows 2003
* CentOS
* Fedora
* Ubuntu.
**Hinweis:** Wenn Sie die Installationsmedien bereits erhalten haben, dann rufen Sie **Virtuelle Maschine erstellen** auf.

Die Installationsmedien für [CentOS ![Symbol für externenLink](../../icons/launch-glyph.svg "Symbol für externen Link")](http://centos.org){: new_window}, [Fedora ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](http://fedoraproject.org/){: new_window} und [Ubuntu ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](http://www.ubuntu.com/){: new_window} stehen auf den entsprechenden Websites zur Verfügung. Alle drei Betriebssysteme können frei lizenziert werden. Der Benutzer muss keine weiteren Aktionen ausführen.


## Virtuelle Maschine erstellen
{: #creating-a-virtual-machine}

Nachdem Sie die Installationsmedien erhalten haben, können Sie mithilfe des Hyper-V Manager eine virtuelle Maschine erstellen. Klicken Sie auf **Neu > Virtuelle Maschine**, um den **Assistenten für neue virtuelle Maschinen** zu starten.

Klicken Sie in der ersten Anzeige auf **Weiter** und geben Sie einen Namen für die neue virtuelle Maschine ein. Dieser Name kann zu einem späteren Zeitpunkt geändert werden. Sie können die Position der virtuellen Festplatte ändern. Das System verwendet den zuvor von Ihnen festgelegten Standardwert, sofern Sie keine andere Position angeben. Nachdem Sie die Informationen eingegeben haben, klicken Sie auf **Weiter**.

Nun müssen Sie dem Server Speicherplatz zuordnen. Im Allgemeinen ordnen Sie zumindest die empfohlene Mindestmenge an Speicherplatz zu, die für das verwendete Betriebssystem angegeben ist. Nachdem Sie die Speichermenge zugeordnet haben, klicken Sie auf **Weiter**.

Als Nächstes legen Sie den ersten Netzadapter fest. Wenn ein Linux-Betriebssystem auf der virtuellen Maschine installiert wird, wählen Sie **Nicht verbunden** aus. Wenn Windows installiert wird, wählen Sie **Privat** aus und klicken Sie dann auf **Weiter**.

Sie können die folgenden Einstellungen für die virtuelle Platte ändern: Dateiname, Speicherposition und Größe. Führen Sie alle erforderlichen Änderungen durch und klicken Sie dann auf **Weiter**.

Die Installationsmedien müssen festgelegt werden. Wählen Sie **Betriebssystem von startfähiger CD/DVD-ROM installieren > Abbilddatei (.iso)** aus, um die Position festzulegen, an der sich die CD/DVD-Imagedatei Ihrer Installationsmedien befindet. Klicken Sie auf **Weiter**, zeigen Sie die Zusammenfassung an und klicken Sie dann auf **Fertigstellen**, um das Fenster zu schließen.

Zuletzt müssen Sie die Adapter des virtuellen Netzes erstellen. Die neue virtuelle Maschine wird nun im Abschnitt **Virtuelle Maschinen** des Hyper-V Managers aufgelistet. Klicken Sie mit der rechten Maustaste auf die neue virtuelle Maschine und dann auf **Einstellungen**.

### Adapter des virtuellen Netzes für Windows-Betriebssystem konfigurieren
{: #configuring-virtual-network-adapter-for-windows-operating-system}

Für die virtuelle Maschine steht nun eine Liste mit Hardware zur Verfügung. In der Liste finden Sie die Einträge für **Netzadapter** und **Privat**. Wird **Nicht verbunden** angezeigt, dann klicken Sie auf den Netzadapter, wählen Sie **Privat** aus und klicken Sie dann auf **Anwenden**.

Klicken Sie als Nächstes auf **Hardware hinzufügen > Netzadapter > Hinzufügen**, um den Netzadapter zur Hardware hinzuzufügen.

Wählen Sie im rechten Teilfenster im Dropdown-Menü **Öffentlich** aus und klicken Sie dann auf **OK**.

### Adapter des virtuellen Netzes für Linux-Betriebssystem konfigurieren
{: #configuring-virtual-network-adapter-for-linux-operating-system}

Für die virtuelle Maschine steht nun eine Liste mit Hardware zur Verfügung. Klicken Sie auf **Netzadapter > Entfernen** und wählen Sie dann **Hardware hinzufügen** aus. Die Liste auf der rechten Seite wird geändert. Wählen Sie in der Liste **Älterer Netzadapter** aus. **Hinweis:** Diese Option unterscheidet sich von dem Netzadapter, der auf einer virtuellen Maschine unter Linux nicht verwendet wird. Klicken Sie auf **Hinzufügen**, um diesen traditionellen Netzadapter zur Hardwareliste hinzuzufügen. Der neue traditionelle Netzadapter wird automatisch ausgewählt. Wählen Sie in der Dropdown-Liste **Privat** aus und klicken Sie auf **Anwenden**. Wiederholen Sie die vorherigen Schritte, um den **Traditionellen Netzadapter** hinzuzufügen. Wählen Sie jedoch dann **Öffentlich** aus. Nachdem das neue traditionelle Netz hinzugefügt wurde, klicken Sie auf **OK**.

## Gastbetriebssystem installieren
{: #installing-a-guest-operating-system}

Die virtuelle Maschine kann jetzt gestartet werden. Klicken Sie mit der rechten Maustaste auf die virtuelle Maschine und wählen Sie dann **Verbinden** aus. Die Konsole der virtuellen Maschine wird geöffnet. Wählen Sie im Menü **Aktion** die Option **Starten** aus. Die neue virtuelle Maschine wird mit den von Ihnen ausgewählten Installationsmedien gestartet. Nun wird die Installation des Betriebssystems durchlaufen. Während des Installationsprozesses für das Betriebssystem sollten Sie die Einstellungen für das öffentliche Netz konfigurieren. Das öffentliche Netz ist die zweite Netzschnittstelle. Wenn die Betriebssysteminstallation abgeschlossen ist, verfügen Sie über eine funktionierende virtuelle Maschine. Wenn ein öffentliches Netz konfiguriert wird, können Sie über Remotezugriff auf die virtuelle Maschine zugreifen.

Der letzte Schritt im Installationsprozess ist die Konfiguration des privaten Netzes. Wenn die virtuelle Maschine nicht mit dem privaten Netz verbunden ist, ist der Installationsprozess abgeschlossen. Weitere Informationen zum Konfigurieren des privaten Netzes finden Sie im Abschnitt zum [Einrichten eines Netzes für virtuelle Maschinen](/docs/infrastructure/virtualization?topic=Virtualization-setting-up-a-virtual-machine-network).
