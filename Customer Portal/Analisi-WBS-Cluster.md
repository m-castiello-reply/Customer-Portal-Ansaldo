# Analisi file Excel WBS - componente Cluster

## File analizzato
- Nome file: 27.04.2026 - Customer Portal - SharePoint Embedded e Integrazione Teamcenter  - WBS - componente Cluster.xlsx
- Obiettivo del file: pianificare attivita tecniche e dipendenze dello stream Cluster (integrazione back-end tra Salesforce, Azure APIM, SharePoint Embedded e Team Center).

## Struttura del workbook
Il workbook contiene 4 fogli:
- WBS
- Document process
- CommentSheet process
- Macro piano da kick-off

Osservazione: l'unico foglio con contenuto tabellare testuale è WBS. Gli altri 3 fogli risultano senza righe testuali (probabilmente diagrammi/immagini o elementi grafici).

## Struttura tabella WBS
La tabella principale (A1:J17) ha le colonne:
- Attivita
- Nome Attivita
- Periodo Inizio
- Periodo Fine
- Sotto-attivita
- Nome Sotto-attivita
- Task
- Profilo
- Complessita
- Assunzioni & Esclusioni

### Significato pratico delle colonne
- Attivita / Sotto-attivita: identificano macro-fase e sotto-fase (es. ATT.2.6).
- Task: descrive il flusso tecnico da implementare o validare.
- Profilo: ruolo prevalente richiesto (Tech Lead, Senior Back-end Developer, Project Manager).
- Complessita: stima qualitativa (Bassa, Media, Alta).
- Assunzioni & Esclusioni: confini di responsabilita e prerequisiti operativi.

## Breakdown delle macro-attivita
- ATT.1 Analisi & Design
- ATT.2 Integrazione SF-MS / Azure APIM / SPE / TC
- ATT.3 Documentazione e Test
- ATT.4 Governance

### ATT.1 - Analisi & setup
Include:
- analisi interfacce e fonti (Document Process + CommentSheet Process)
- setup SharePoint Embedded (tenant/subscription/app registration/permessi Graph)
- setup APIM + proxy on-prem verso Team Center

### ATT.2 - Flussi di integrazione implementativi
Copre i passaggi principali:
- richiesta trasferimento file da SF-MS verso APIM
- inoltro APIM verso proxy on-prem Team Center
- trasferimento file Team Center verso API Azure
- upload su SharePoint Embedded
- esito upload con identificativo verso SF-MS
- view file
- invio CommentSheet e recupero file correlato
- invio file + metadata CommentSheet verso Team Center

### ATT.3 - Qualita e messa a terra
- documentazione tecnica integrazione
- integration test E2E SF-AZURE-PROXY-TC
- supporto UAT

### ATT.4 - Governance
- coordinamento avanzamento, rischi e dipendenze progettuali

## Timeline (interpretazione date)
Nel file sono presenti seriali Excel per i periodi. Conversione rilevata:
- 46143 -> 2026-05-01
- 46174 -> 2026-06-01
- 46204 -> 2026-07-01
- 46235 -> 2026-08-01
- 46266 -> 2026-09-01

L andamento indica una sequenza mensile da maggio a settembre 2026 per analisi, build dei flussi, test e UAT support.

## Punti in comune con Riassunto-Trascrizione.md
Confronto con i contenuti del file Riassunto-Trascrizione.md:
- Confermato il ruolo centrale di Team Center nel perimetro documentale.
- Confermato APIM/Azure come layer di transito per recupero e scambio file.
- Confermato uso del proxy on-prem per le chiamate verso Team Center.
- Confermata la presenza del flusso CommentSheet bidirezionale lato documentale.
- Confermata l enfasi su fase di analisi iniziale, integrazione tecnica e test.
- Coerente la necessita di disponibilita interfacce Team Center come dipendenza critica.

## Elementi aggiuntivi che l Excel chiarisce rispetto al riassunto
- Introduce esplicitamente SharePoint Embedded (SPE) come repository/servizio centrale per upload e recupero file.
- Rende operativi i task con livello di dettaglio tecnico endpoint-to-endpoint.
- Esplicita assunzioni/esclusioni per ogni sotto-attivita.
- Assegna profili e complessita per ogni item della WBS.

## Gap o possibili allineamenti da verificare
- Nel riassunto precedente, il focus era Team Center -> Salesforce con passaggi Azure; in WBS emerge in modo forte anche il tratto SF-MS <-> SPE.
- Verificare naming e confini tra SF, SF-MS e portale per evitare ambiguita nei documenti di progetto.
- Verificare dipendenze infrastrutturali citate in assunzioni (network, proxy reachability, permessi Graph) rispetto alla roadmap condivisa.

## Lettura sintetica conclusiva
Questo Excel e una WBS tecnica dello stream integrazione Cluster, orientata a:
- progettare e realizzare i flussi file/comment tra Salesforce, APIM, SPE e Team Center
- chiarire prerequisiti e limiti perimetro tramite assunzioni/esclusioni
- arrivare a test E2E e supporto UAT dopo setup e sviluppo progressivo

In pratica, il file traduce in task implementativi il quadro architetturale emerso nella trascrizione, aggiungendo dettaglio operativo e responsabilita tecniche.
