---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-16"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Che cos'è Virtuozzo?
{: #what-is-virtuozzo-}

Virtuozzo è una soluzione di virtualizzazione basata sui contenitori che consente la condivisione di hardware utilizzando un livello di astrazione.  Virtuozzo crea dei contenitori, noti anche come VE o VPS, che simulano un server. Il contenitore funziona e risponde principalmente come se fosse un server autonomo. Il contenitore è separato dagli altri contenitori che si trovano sullo stesso server fisico nel senso che non può accedere alla memoria, alle risorse IPC o ai file degli altri contenitori. La rete può essere configurata per essere condivisa tra più contenitori oppure isolata.
{:shortdesc}

Virtuozzo non è un hypervisor o un'interfaccia software per un hypervisor. Opera in una combinazione di kernel e spazio utente all'interno di un kernel proprietario. Questa configurazione consente ai contenitori di prendere essenzialmente in prestito le risorse quando sono necessarie, mentre le risorse sono disponibili sul nodo host.

Ulteriori informazioni sono disponibili in [Virtuozzo ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://virtuozzo.com/){: new_window}.
