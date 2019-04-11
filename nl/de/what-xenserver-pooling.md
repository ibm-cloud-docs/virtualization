---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# XenServer-Pooling
{: #xenserver-pooling}

Das sog. XenServer-Pooling bietet die Möglichkeit, bis zu 16 Server zu einer Gruppe zusammenzuschließen, die als Cluster arbeitet. Dieser Cluster ermöglicht nicht nur die gemeinsame Nutzung von Ressourcen, sondern auch die Livemigration von virtuellen Maschinen, die mit XenMotion arbeiten. Ein Server fungiert hierbei als Masterknoten, die restlichen Server als Slaves. Wird einer der Server im Pool über XenCenter geöffnet, dann werden auch die Managementinformationen für den gesamten Pool geöffnet. Wenn ein Knoten in den Pool aufgenommen wird, wird sein Managementkennwort so geändert, dass es mit dem Managementkennwort des Masterknotens übereinstimmt. Wenn Sie einen Knoten aus dem Pool entfernen, wird dieses Kennwort nicht geändert.

Alle Systeme im Knoten müssen aus derselben Prozessorfamilie stammen (z. B. Intel oder AMD). Im Idealfall muss die Hardware identisch sein, damit das Pooling erfolgreich ausgeführt werden kann. Kompatibilitätsprobleme treten in Umgebungen mit heterogenem CPU-Pooling nur bedingt auf. Sie müssen Kompatibilitätstests durchführen, um festzustellen, ob Ihre Hardware kompatibel ist. Weitere Informationen finden Sie im Abschnitt mit den [Informationen zum heterogenen CPU-Pooling ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")](http://support.citrix.com/article/CTX127059){: new_window}.

Zum Erstellen eines XenServer-Pools gibt es zwei Methoden. Über die XenCenter-Konsole können Sie die Option 'Neuer Pool' verwenden. Befolgen Sie die einfachen Anweisungsschritte am Bildschirm, um Systeme zu erstellen und zum Pool hinzuzufügen. Die zweite Methode besteht in der Verwendung der Befehlszeile. Auf dem neuen Slaveknoten kann der folgende Befehl ausgeführt werden:

        `xe pool-join master-address= master-username= master-password=`

**Hinweis**: Die IP-Managementadresse ist die IP-Adresse im Format 10.x.x.x des Servers, wenn Sie die Standardinstallation verwendet haben.

Wenn Sie mit gebundenen Netzen arbeiten, dann können die Schnittstellen des neu hinzugefügten Slaves den falschen gebundenen Netzen zugeordnet werden. Zur Behebung dieses Problems müssen Sie die Bindungen des Slaves über die Befehlszeile aufheben und die Bindungen dann mit den richtigen PIFs und Netzen wiederherstellen.

Sie haben zwei Möglichkeiten, um einen Slaveknoten wieder aus dem Pool zu entfernen. Dies ist zum einen mit XenCenter möglich. Rufen Sie hierzu **Pool** auf und wählen Sie dann **Server entfernen** aus. Befolgen Sie die einfachen Anweisungsschritte am Bildschirm, die bereitgestellt werden. Die zweite Methode besteht in der Verwendung der Befehlszeile. Das Entfernen eines Knotens kann über jeden Knoten im Pool ausgeführt werden. Sie müssen die _UUID_ (Universally Unique Identifier) des Knotens lokalisieren, den Sie entfernen möchten, indem Sie den folgenden Befehl ausführen:

        `xe host-list`

Führen Sie dann den folgenden Befehl aus:

        `xe pool-eject uuid=`

**Hinweis**: Wenn ein Knoten entfernt wird, dann wird für ihn in einer bereinigten Installation ein Warmstart durchgeführt. Dieser umfasst die Entfernung der Netzbetriebskonfiguration, die zur Bereitstellung der öffentlichen und privaten Konnektivität verwendet wird. Das Kennwort des Slaveknotens bleibt auch nach seiner Entfernung aus dem Pool weiterhin mit dem des Masterknotens identisch. Sie müssen den Netzbetrieb möglicherweise über IPMI neu konfigurieren, da durch das Entfernen die Einstellungen des Slaves beeinträchtigt werden können.
