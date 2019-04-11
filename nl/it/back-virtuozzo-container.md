---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-14"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Backup di un contenitore Virtuozzo
{: #backing-up-a-virtuozzo-container}

1. Assicurati che entrambi i nodi hardware siano configurati in PIM.
2. Vai ai nodi di infrastruttura e hardware su cui si trova il contenitore.
3. Seleziona il contenitore appropriato e fai clic su **Back up**.
4. Aggiungi un commento appropriato che descrive il motivo per cui si esegue il backup di questi dati.
5. Seleziona il tipo di backup:
   * Use Hardware Node Settings
   * Full (per questo esempio)
   * Incremental
   * Differential
6. Seleziona il livello di compressione (Compression Level): **Nota:** una compressione più elevata può risparmiare spazio su disco ma utilizza più risorse CPU.
   * Use Hardware Node Settings
   * None
   * Normal (la selezione di Normal riduce al minimo il carico del server)
   * High
   * Maximum
7. Seleziona il nodo hardware dove desideri che venga conservato il file di backup. Se hai più nodi hardware, ti consigliamo di salvare i tuoi backup su tali nodi invece che sullo stesso server.
  * Altre opzioni sono a discrezione dell'amministratore.
8. Backup. Il backup è completo. Puoi visualizzare i dettagli del backup andando alla scheda **Hardware Node, Container, and Backups** e selezionando il backup.
