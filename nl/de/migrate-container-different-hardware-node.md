---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Container auf anderen Hardwareknoten migrieren
{: #migrating-a-container-to-a-different-hardware-node}

1. Vergewissern Sie sich, dass beide Hardwareknoten in PIM (Parallels Infrastructure Manager) konfiguriert wurden.
2. Rufen Sie **Infrastruktur** auf und wählen Sie den Hardwareknoten aus, auf dem sich der Container befindet.
3. Wählen Sie den Container aus und klicken Sie auf **Migrieren**.
4. Wählen Sie **Zielknoten** aus.
5. Wählen Sie aus, ob die Migration live ausgeführt werden soll.

**Hinweis:** Abhängig von der Größe des Containers ist eine Livemigration möglicherweise nicht möglich. Die meisten Informationen in den Containern müssen so lange im Arbeitsspeicher gehalten werden, bis die Migration abgeschlossen ist.

6. Unter **Erweitert** können Sie auswählen, ob die Containerdateien, die sich nach der Migration auf dem Quellenknoten befinden, entfernt werden sollen oder nicht. Geben Sie an, dass der Container nicht automatisch auf dem Zielknoten gestartet werden oder die Migration erzwungen werden soll. Das Erzwingen der Migration kann zu Problemen führen, wenn ein weiterer Knoten mit derselben IP vorhanden ist. Ein weiteres Migrationsproblem kann auftreten, wenn der Zielknoten nicht über die korrekten Anwendungs- oder Betriebssystemvorlagen verfügt, die installiert und im Cache zwischengespeichert werden.
7. Sie können die **Details** anzeigen, um den Migrationsprozess zu überwachen.
8. Die Migration ist abgeschlossen. Der Container wird automatisch auf den Zielknoten verschoben, der unter **Infrastruktur** im linken Menüteilfenster aufgeführt wird.
