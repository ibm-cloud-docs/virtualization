---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Migrazione di un contenitore a un nodo hardware differente
{: #migrating-a-container-to-a-different-hardware-node}

1. Assicurati di avere entrambi i nodi hardware configurati in PIM.
2. Vai a **Infrastructure** e seleziona il nodo hardware in cui si trova il contenitore.
3. Seleziona il contenitore e fai clic su **Migrate**.
4. Seleziona il nodo di destinazione (**Destination Node**).
5. Scegli se desideri che la migrazione venga eseguita live.

**Nota:** a seconda della dimensione del contenitore, una migrazione live potrebbe non essere fattibile. La maggior parte delle informazioni nei contenitori deve essere contenuta nella RAM finché la migrazione non viene completata.

6. In **Advanced**, puoi selezionare se rimuovere o meno i file contenitore che si trovano sul nodo di origine dopo la migrazione. Specifica di non avviare automaticamente il contenitore sul nodo di destinazione o di non forzare la migrazione. Forzare la migrazione può causare dei problemi se hai un altro nodo che utilizza lo stesso IP. Un altro problema di migrazione è che il nodo di destinazione non ha i template di applicazione o di sistema operativo corretti installati e memorizzati in cache.
7. Puoi visualizzare i dettagli (**Details**) per monitorare il processo di migrazione.
8. La migrazione è stata completata. Il contenitore viene automaticamente spostato al nodo di destinazione, che può essere trovato in **Infrastructure** nel riquadro del menu a sinistra.
