---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Netz für virtuelle Maschinen einrichten
{: #setting-up-a-virtual-machine-network}

Die Netzkonfiguration auf der neuen virtuellen Maschine wird in wenigen Schritten ausgeführt. Sie benötigen einen separaten portierbaren IP-Block sowohl für das öffentliche als auch für das private Netz. Es wird vorausgesetzt, dass Sie ein Virtualisierungsangebot verwenden, für das eine Broadcast-Domäne (Sekundär im VLAN) und ein Teilnetz ohne Routing ans VLAN (ohne Netz-ID/Gateway/Broadcast) erforderlich ist. Wenn auf der virtuellen Maschine das private Netz nicht verwendet werden soll, dann benötigen Sie lediglich das öffentliche Teilnetz. Portierbare IP-Blöcke können unter [Verkäufe->IP-Adressen hinzufügen ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://manage.softlayer.com/Sales/orderAdditionalServices/subnet){: new_window} oder über den [IP-Manager für das öffentliche Netz ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://manage.softlayer.com/PublicNetwork/ipManager){: new_window} direkt über das Portal bestellt werden.

Nachdem Sie Ihre IP-Blöcke erhalten haben, können Sie den Netzbetrieb auf der virtuellen Maschine konfigurieren. Wenn die virtuelle Maschine nicht installiert ist, können Sie den Block für das öffentliche Netz über den Installationsprozess des Betriebssystems konfigurieren. Dieser Prozess ist die schnellste Methode, um Ihr öffentliches Netz zu konfigurieren. Bei allen Netzkonfigurationsschritten wird davon ausgegangen, dass die erste Netzschnittstelle das private Netz und die zweite Netzschnittstelle das öffentliche Netz ist.

Die Konfiguration Ihres Netzes ist für jedes Betriebssystem unterschiedlich. Detaillierte Informationen zum Konfigurieren Ihres Netzes für die folgenden Betriebssysteme finden Sie hier. **Hinweis:** Betriebssysteme in einer Gruppe verfügen über die gleiche Netzkonfiguration.

* Windows 2008 Server Core
* Windows 2003 Standard und Enterprise
* Windows 2008 Web, Standard, Enterprise und Datacenter
* RedHat, Fedora und CentOS
* Ubuntu und Debian

Für die Netzkonfiguration sind die folgenden Informationen sowohl für öffentliche als auch private IP-Blöcke erforderlich. Die Informationen finden Sie im Portal für öffentliche IP-Blöcke unter [Öffentliches Netz -> IP-Manager ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://manage.softlayer.com/PublicNetwork/ipManager){: new_window} und für private IP-Blöcke unter [Privates Netz -> IP-Manager ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://manage.softlayer.com/PrivateNetwork/ipManager){: new_window}.

    -IP Address
    -Gateway
    -Subnet Mask
    --Subnet conversion
    ---/29 - 255.255.255.248
    ---/28 - 255.255.255.240
    ---/27 - 255.255.255.224
    ---/26 - 255.255.255.192
    ---/25 - 255.255.255.128
    ---/24 - 255.255.255.0

## Windows 2008 Server Core
{: #windows-2008-server-core}

Windows 2008 Server Core Edition stellt keine grafische Oberfläche bereit, um das Systemnetz zu konfigurieren. Die Konfiguration muss manuell über die Eingabeaufforderung ausgeführt werden. Sie müssen die folgenden Befehle ausführen. Die IP-Adressen, die in diesem Beispiel verwendet werden, müssen durch die IP-Adressen aus Ihren IP-Blöcken ersetzt werden. Diese Befehle können mit älteren Windows Server-Versionen verwendet werden, die den Befehl [netsh ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](https://support.microsoft.com/kb/242468){: new_window} enthalten.

**Beispiel für öffentlichen IP-Block – 192.0.2.0/29**

* IP-Adresse – 192.0.2.2
* Gateway – 192.0.2.1
* Teilnetzmaske – 255.255.255.248

**Beispiel für privaten IP-Block – 10.0.0.0/29**

* IP-Adresse – 10.0.0.2
* Gateway – 10.0.0.1
* Teilnetzmaske – 255.255.255.248

**Öffentliches Netz**

Mit diesem Befehl wird das öffentliche Netz erstellt, um den Server mit dem Internet zu verbinden.

* *Netsh interface ip set address name=”Local Area Connection 2” static 192.0.2.2 255.255.255.248 192.0.2.1*

Mit diesen Befehlen werden die DNS-Einträge erstellt. Wenn Sie das private Netz nicht verwenden, müssen Sie diese IP-Adressen durch funktionierende DNS-Server ersetzen.

* *Netsh interface ip add dns “Local Area Connection 2” 10.0.80.11*
* *Netsh interface ip add dns “Local Area Connection 2” 10.0.80.12*

**Privates Netz**

Mit den folgenden beiden Befehlen wird das private Netz konfiguriert und die persistente Route für das private Netz erstellt. Die persistente Route stellt Ihrem privaten Netz Zugriff auf das gesamte private Netz zur Verfügung. Der Zugriff umfasst die DNS-Server in der Konfiguration des öffentlichen Netzes. **Hinweis:** Dem privaten Netz ist kein Gateway zugeordnet.

* `Netsh interface IP set address name=”Local Area Connection” static 10.0.0.2 255.255.255.248`
* `Route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 -p`

## Windows 2003 Standard und Enterprise
{: #windows-2003-standard-and-enterprise}

Mit Windows 2003 können Sie den Netzbetrieb entweder über die Befehlszeile oder die grafische Benutzerschnittstelle konfigurieren. Wenn Sie die Befehlszeile verwenden möchten, lesen Sie die Konfigurationsanweisungen für Windows 2008 Server Core. Diese Anweisungen funktionieren auch für Windows 2003. Die Anweisungen zur Verwendung der grafischen Oberfläche finden Sie hier. Die IP-Adressen, die in diesem Beispiel verwendet werden, müssen durch die IP-Adressen aus Ihren IP-Blöcken ersetzt werden.

**Beispiel für privaten IP-Block – 10.0.0.0/29**

* IP-Adresse – 10.0.0.2
* Gateway – 10.0.0.1
* Teilnetzmaske – 255.255.255.248

### Netzkonfigurationen öffnen
{: #opening-your-network-configurations}

1. Rufen Sie **Startmenü > Einstellungen > Systemsteuerung** auf.
2. Öffnen Sie **Netzwerkverbindungen**.
   **Hinweis:** Wenn Sie zwei Netzadapter installiert haben, dann zeigt das System nun 'LAN-Verbindung' und 'LAN-Verbindung 2' an.

**Öffentliches Netz**

Verwenden Sie die folgenden Anweisungen, um das öffentliche Netz über die grafische Oberfläche von Windows zu konfigurieren.

1. Klicken Sie mit der rechten Maustaste auf **LAN-Verbindung > Eigenschaften** und wählen Sie **Internet Protocol (TCP/IP) > Eigenschaften** aus.
2. Rufen Sie **Allgemein > Folgende IP-Adresse verwenden:** auf.
3. Geben Sie die öffentliche IP-Adresse, die Teilnetzmaske und das Gateway ein.
4. Wählen Sie **Folgende DNS-Serveradressen verwenden:** aus. Wenn Sie das private Netz konfigurieren, verwenden Sie die folgenden DNS-Server. Wenn Sie das private Netz nicht konfigurieren, müssen Sie DNS-Server bereitstellen.
    * Bevorzugter DNS-Server: 10.0.80.11
    * Alternativer DNS-Server: 10.0.80.12
5. Klicken Sie auf **OK**.

**Privates Netz**

1. Klicken Sie mit der rechten Maustaste auf **LAN-Verbindung 2 > Eigenschaften** und wählen Sie **Internet Protocol (TCP/IP) > Eigenschaften** aus.
2. Rufen Sie **Allgemein > Folgende IP-Adresse verwenden:** auf und geben Sie die private IP-Adresse und die Teilnetzmaske ein. Geben Sie im Feld für das Gateway nichts ein und klicken Sie dann auf **OK**.
3. Klicken Sie nochmals auf **OK**, um das Fenster für die Netzkonfiguration zu schließen.

Um den Zugriff auf das gesamte private Netz, das die DNS-Server umfasst, bereitzustellen, müssen Sie eine angepasste Route zum Server hinzufügen. Gehen Sie wie folgt vor, um den Zugriff auf das private Netz bereitzustellen:

1. Rufen Sie **Start > Ausführen** auf, geben Sie *cmd* ein und klicken Sie auf **OK**.
2. Führen Sie den folgenden Befehl aus. **Hinweis:** Vergewissern Sie sich, dass die Gateway-Adresse (10.0.0.1) durch das Gateway für Ihren privaten IP-Block ersetzt wird.<br/>
*“route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 –p”*

## Windows 2008 Web, Standard, Enterprise und Datacenter
{: #windows-2008-web-standard-enterprise-and-datacenter}

Wenn Sie Windows 2008 verwenden, konfigurieren Sie den Netzbetrieb entweder über die Eingabeaufforderung oder die grafische Benutzerschnittstelle. Wenn Sie die Eingabeaufforderung verwenden möchten, lesen Sie die Konfigurationsanweisungen für Windows 2008 Server Core. Diese Anweisungen funktionieren für alle 2008-Server. Die Anweisungen zur Verwendung der grafischen Oberfläche finden Sie hier. Die IP-Adressen, die in diesem Beispiel verwendet werden, müssen durch die IP-Adressen aus Ihren IP-Blöcken ersetzt werden.

**Beispiel für privaten IP-Block – 10.0.0.0/29**

* IP-Adresse – 10.0.0.2
* Gateway – 10.0.0.1
* Teilnetzmaske – 255.255.255.248

**Öffnen Sie die Netzkonfigurationen**

1. Rufen Sie **Startmenü > Einstellungen > Systemsteuerung** auf.
2. Öffnen Sie **Netzwerk- und Freigabecenter** und klicken Sie dann auf **Netzwerkverbindungen verwalten**.
* Wenn Sie zwei Netzadapter installiert haben, dann zeigt das System nun 'LAN-Verbindung' und 'LAN-Verbindung 2' an.

**Öffentliches Netz**

In den folgenden Anweisungen finden Sie die Arbeitsschritte, die zum Konfigurieren des öffentlichen Netzes über die grafische Oberfläche von Windows ausgeführt werden müssen.

1. Klicken Sie mit der rechten Maustaste auf **LAN-Verbindung > Eigenschaften**.
2. Wählen Sie **Internet Protocol Version 4 (TCP/IPv4) - Eigenschaften** aus.
3. Rufen Sie **Allgemein > Folgende IP-Adresse verwenden:** auf und geben Sie die öffentliche IP-Adresse, die Teilnetzmaske und das Gateway ein.
4. Wählen Sie **Folgende DNS-Serveradressen verwenden:** aus. Wenn Sie das private Netz konfigurieren, verwenden Sie die folgenden DNS-Server. Wenn Sie das private Netz nicht konfigurieren, müssen Sie DNS-Server bereitstellen.
    * Bevorzugter DNS-Server: 10.0.80.11
    * Alternativer DNS-Server: 10.0.80.12
5. Klicken Sie auf **OK**.

**Privates Netz**

1. Klicken Sie mit der rechten Maustaste auf **LAN-Verbindung 2 > Eigenschaften** und wählen Sie **Internet Protocol Version 4 (TCP/IPv4) > Eigenschaften** aus.
2. Rufen Sie **Allgemein > Folgende IP-Adresse verwenden:** auf, geben Sie die private IP-Adresse und die Teilnetzmaske ein und klicken Sie dann auf **OK**.
3. Klicken Sie nochmals auf **OK**, um das Fenster für die Netzkonfiguration zu schließen.

Um den Zugriff auf das gesamte private Netz, das die DNS-Server umfasst, bereitzustellen, müssen Sie eine angepasste Route zum Server hinzufügen.

1. Rufen Sie **Start > Ausführen** auf, geben Sie 'cmd' ein und klicken Sie auf **OK**.
2. Führen Sie den folgenden Befehl aus. **Hinweis:** Ersetzen Sie die Gateway-Adresse (_10.0.0.1_) durch das Gateway für Ihren privaten IP-Block.
  * `route add 10.0.0.0 mask 255.0.0.0 10.0.0.1 –p`

## RedHat, Fedora und CentOS
{: #redhat-fedora-and-centos}

Die Konfiguration des Netzes unter RedHat, Fedora und CentOS erfolgt durch manuelles Bearbeiten von Konfigurationsdateien. Wenn Sie die Dateien manuell bearbeiten möchten, müssen Sie sich bei der virtuellen Maschine anmelden.

**Beispiel für öffentlichen IP-Block – 192.0.2.0/29**

* IP-Adresse – 192.0.2.2
* Gateway – 192.0.2.1
* Teilnetzmaske – 255.255.255.248

**Beispiel für privaten IP-Block – 10.0.0.0/29**

* IP-Adresse – 10.0.0.2
* Gateway – 10.0.0.1
* Teilnetzmaske – 255.255.255.248

**Öffentliches Netz**

Die Einstellungen des öffentlichen Netzes sind in der folgenden Datei enthalten. Sie müssen diese Datei mit den Informationen bearbeiten, die bereitgestellt werden. Ersetzen Sie die Beispiel-IP-Adressen durch die IP-Adressen aus Ihrem öffentlichen IP-Block. Wenn die Datei nicht vorhanden ist, erstellen Sie sie. Ist die Datei vorhanden, ersetzen Sie alle Daten, die in der Datei enthalten sind, durch die folgenden Informationen:
* /etc/sysconfig/network-scripts/ ifcfg-eth1
      DEVICE=eth1
      BOOTPROTO=static
      BROADCAST=192.0.2.7
      IPADDR=192.0.2.2
      NETMASK=255.255.255.248
      NETWORK=192.0.2.0

      GATEWAY=192.0.2.1
      ONBOOT=yes

**Privates Netz**

Die Einstellungen des privaten Netzes sind in der folgenden Datei enthalten. Sie müssen diese Datei mit den Informationen bearbeiten, die bereitgestellt werden. Ersetzen Sie die Beispiel-IP-Adressen durch die korrekten IP-Adressen aus Ihrem privaten IP-Block. Wenn die Datei nicht vorhanden ist, erstellen Sie sie. Ist die Datei vorhanden, ersetzen Sie alle Daten, die in der Datei enthalten sind, durch die folgenden Informationen. **Hinweis:** Das private Netz hat *KEINE* Standardroute. Deshalb ist das *GATEWAY* nicht definiert.

* /etc/sysconfig/network-scripts/ ifcfg-eth0
      DEVICE=eth0
      BOOTPROTO=static
      IPADDR=10.0.0.2
      NETMASK=255.255.255.248
      ONBOOT=yes

Abschließend ist eine neue Route erforderlich, um privaten Zugriff auf das gesamte private Netz bereitzustellen, damit der DNS-Server eingebunden werden kann. Dazu wird die folgende Datei bearbeitet. Diese Datei ist nicht vorhanden und muss von Ihnen erstellt werden. **Hinweis:** Ersetzen Sie '10.0.0.1' im Beispiel durch das IP-Gateway Ihres privaten Teilnetzes.

* /etc/sysconfig/network-scripts/route-eth0
      10.0.0.0/8 via 10.0.0.1

**DNS-Konfiguration**

Die primären und sekundären DNS-Konfigurationen sind in einer separaten Datei enthalten. Sie müssen die Datei mit den folgenden Informationen bearbeiten. Wenn diese virtuelle Maschine keinen Zugriff auf das private Netz hat, dann müssen Sie die Server-IPs durch die IP-Adressen der DNS-Server ersetzen, die Sie verwenden möchten.

* /etc/resolv.conf
      nameserver 10.0.80.11
      nameserver 10.0.80.12

Damit diese Änderungen wirksam werden, müssen Sie den Netzbetrieb auf dem Server erneut starten. Sie können den Netzbetrieb erneut starten, indem Sie den folgenden Befehl ausführen:

* `service network restart`

## Ubuntu und Debian
{: #ubuntu-and-debian}

Sie konfigurieren Ubuntu- und Debian-Netze über eine einzige Konfigurationsdatei. Sie müssen diese Konfigurationsdatei mit den folgenden Informationen bearbeiten. Um diese Datei bearbeiten zu können, müssen Sie über Rootzugriff auf den Server verfügen. In der Basisinstallation von Ubuntu steht keine Möglichkeit für die Rootanmeldung zur Verfügung. Der Benutzer, der im Installationsprozess erstellt wurde, kann jedoch auf sudo zugreifen. Sie müssen den Befehl sudo verwenden, um die Datei zu bearbeiten, wenn Sie mit Ubuntu arbeiten.

**Beispiel für öffentlichen IP-Block – 192.0.2.0/29**

* IP-Adresse – 192.0.2.2
* Gateway – 192.0.2.1
* Teilnetzmaske – 255.255.255.248

**Beispiel für privaten IP-Block – 10.0.0.0/29**

·IP-Adresse – 10.0.0.2
·Gateway – 10.0.0.1
·Teilnetzmaske – 255.255.255.248

**Öffentliches und privates Netz**

Bearbeiten Sie die folgende Datei mit einem beliebigen Texteditor und ersetzen Sie die Beispiel-IP-Adressen durch die IPs, die sich in Ihren öffentlichen und privaten IP-Blöcken befinden.

* /etc/network/interfaces
      auto lo
      iface lo inet loopback

      auto eth1
      iface eth1 inet static
      address 192.0.2.2
      netmask 255.255.255.248
      gateway 192.0.2.1

      auto eth0
      iface eth0 inet static
      address 10.0.0.2
      netmask 255.255.255.248

      #Static route for backend service network
      up route add -net 10.0.0.0/8 gw 10.0.0.1

**DNS-Konfiguration**

Die primäre und die sekundäre DNS-Konfiguration sind in einer separaten Datei enthalten. Sie müssen diese Konfigurationsdatei mit den folgenden Informationen bearbeiten. Wenn diese virtuelle Maschine keinen Zugriff auf das private Netz hat, dann müssen Sie die Server-IPs durch die IP-Adressen der DNS-Server ersetzen, die Sie verwenden möchten.

* /etc/resolv.conf
      nameserver 10.0.80.11
      nameserver 10.0.80.12

Damit diese Änderungen wirksam werden, müssen Sie den Netzbetrieb auf dem Server erneut starten. Sie können das Netz erneut starten, indem Sie den folgenden Befehl ausführen:

* */etc/init.d/network restart*

**Hinweis:** '192.0.2.0/24' wird als öffentliche IP-Dokumentation gemäß RFC1166 und RFC5737 verwendet. Sie verwenden diese IP-Adressen nicht auf Ihren Servern.
