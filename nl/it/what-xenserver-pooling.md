---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Pooling di XenServer
{: #xenserver-pooling}

Puoi pensare al pooling XenServer come a un massimo di 16 server che funzionano come un cluster. Questo cluster non solo consente le risorse condivise ma consente la migrazione live di macchine virtuali che utilizzano XenMotion. Un server funge da "nodo master" mentre gli altri sono slave. L'apertura di uno qualsiasi dei server nel pool tramite XenCenter apre le informazioni di gestione per l'intero pool. Quando un nodo si unisce al pool, la sua password di gestione viene modificata in modo che corrisponda alla password di gestione del nodo master. Quando rimuovi un nodo dal pool, questa password non viene modificata.

Tutti i sistemi presenti nel nodo devono essere della stessa famiglia di processori, come in Intel o AMD. Idealmente, l'hardware deve essere identico perché il pooling funzioni correttamente. Non sempre si hanno problemi di compatibilità con un pooling di CPU eterogeneo. Devi eseguire dei test di compatibilità tu stesso per verificare se il tuo hardware è compatibile. Per ulteriori informazioni, vedi [Understanding heterogeneous CPU pooling ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](http://support.citrix.com/article/CTX127059){: new_window}.

Disponi di due metodi per la creazione di un pool XenServer. Uno è mediante la console XenCenter utilizzando l'opzione "New Pool". Segui le semplici descrizioni passo dopo passo riportate a schermo per creare e aggiungere sistemi al pool. L'altro metodo è utilizzando la riga di comando. Sul nuovo nodo slave, è possibile eseguire questo comando:

        `xe pool-join master-address= master-username= master-password=`

**Nota:**: l'indirizzo IP di gestione è l'indirizzo IP 10.x.x.x IP del server se hai utilizzato l'installazione predefinita.

Quando lavori con reti associate, uno slave appena unito può avere le sue interfacce collegate alle reti associate non corrette. Per correggere questo problema, elimina le associazioni sullo slave dalla riga di comando e creale nuovamente con le PIF e le reti appropriate.

Disponi di due metodi per rimuovere un nodo slave dal pool. Il primo è utilizzando XenCenter. Vai a **Pool** e seleziona **Remove Server**. Segui le semplici descrizioni passo dopo passo riportate a schermo che vengono fornite. L'altro metodo è utilizzando la riga di comando. La rimozione di un nodo può essere eseguita da qualsiasi nodo nel pool. Devi individuare l'_uuid_ del nodo che desideri rimuovere eseguendo questo comando:

        `xe host-list`

Esegui quindi questo comando:

        `xe pool-eject uuid=`

**Nota**: quando viene rimosso, un nodo viene riavviato in un'installazione pulita, che include la rimozione della configurazione di rete utilizzata per fornire la connettività pubblica e privata. La password del nodo slave è ancora la stessa del nodo master dopo la rimozione dal pool. È possibile che tu debba riconfigurare la rete mediante IPMI poiché la rimozione può danneggiare le impostazioni degli slave.
