---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Panoramica del server Parallels Server 4 Bare Metal
{: #overview-of-parallels-server-4-bare-metal}

Parallels Server Bare Metal è una soluzione di virtualizzazione che fornisce la virtualizzazione hardware insieme alla virtualizzazione software di Virtuozzo, fornendo la tecnologia sia per le macchina virtuali che per i contenitori*.
{:shortdesc}

**Interfaccia riga di comando (CLI)**

Insieme ai consueti comandi Virtuozzo, sono disponibili degli altri comandi: `pctl`, `pmigrate`, `pstat` e `prl_disk_tool`.

**Migrazione**

Puoi ora migrare i contenitori Virtuozzo alle macchine virtuali, i server fisici ai contenitori Virtuozzo e i server fisici alle macchine virtuali per consentire il consolidamento dei servizi. Questa funzionalità consente di eseguire la migrazione di contenitori o VM tra server bare metal, convertire VM create in ambienti virtualizzati differenti (V2V) e modificare i SID Windows quando cloni o distribuisci una VM Windows da un template.

**Utilizzo degli indirizzi IP e VLAN**

Puoi assegnare le VM a un IP dal server fisico utilizzando gli strumenti Parallels presenti nella VM. Inoltre, PSBM consente la creazione di VLAN e switch virtuali all'interno delle VM per proteggere meglio il traffico di rete interno alle VM:

**Esecuzione dei comandi**

Puoi eseguire i comandi raw dal server fisico direttamente nei contenitori Virtuozzo e ora all'interno delle VM (quando gli strumenti Parallels sono installati all'interno della VM), comprese le reimpostazioni di password utente.

**Monitoraggio del numero di processi**

Utilizzi Parallels Server Bare Metal per aumentare o ridurre la priorità delle risorse assegnate (CPU, priorità I/O disco) a una VM rapidamente.

**Backup**

Parallels Server Bare Metal consente di eseguire il backup e il ripristino di VM e contenitori sul server bare metal locale oppure su un server bare metal remoto per includere backup completi e incrementali.

**Monitoraggio della velocità effettiva della rete**

Utilizzi Parallels Server Bare Metal per visualizzare e individuare facilmente VM o contenitori in base alla velocità effettiva di rete corrente e cronologica.

**Rete**

Parallels Server Bare Metal utilizza gli indirizzi IP portatili mentre Virtuozzo utilizza indirizzi IP portatili o statici (a seconda della configurazione).

\* {{site.data.keyword.cloud}} concede in licenza solo macchine virtuali hardware su Parallels Server 4 Bare Metal, se non diversamente indicato nel modulo d'ordine.
_VM_ = virtual machine. _V_ = VPS o contenitore.
