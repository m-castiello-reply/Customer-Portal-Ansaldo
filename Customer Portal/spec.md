# Feature Specification: Customer Portal - Integrazione Documentale SF/APIM/SPE/Team Center

**Feature Branch**: `001-customer-portal-document-integration`  
**Created**: 2026-07-02  
**Updated**: 2026-07-08 (kickoff meeting 08/07/2026)  
**Status**: Draft  
**Input**: User description: "Consolidare quanto emerso da trascrizione onboarding, WBS Cluster e kickoff meeting 08/07/2026 per produrre la specifica funzionale dello stream integrazione documentale"

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Trasferimento documento da Team Center a Salesforce/Portale (Priority: P1)

Come utente cliente o interno Ansaldo, voglio che i documenti rilasciati in Team Center diventino disponibili nel Customer Portal (tramite Salesforce), cosi da consultarli nel contesto progetto.

**Why this priority**: E il flusso core del perimetro documentale e abilita il valore principale del portale per consultazione e collaborazione.

**Independent Test**: Si testa end-to-end rilasciando un file in Team Center e verificando disponibilita, metadati e visibilita nel progetto su Salesforce/Portale.

**Acceptance Scenarios**:

1. **Given** un documento rilasciato in Team Center con metadati validi, **When** il flusso di integrazione viene eseguito, **Then** il file viene caricato su SPE e referenziato in Salesforce con identificativo univoco.
2. **Given** un utente autorizzato sul progetto, **When** apre il dettaglio progetto nel portale, **Then** visualizza il documento e i metadati minimi concordati.
3. **Given** errore di upload o trasferimento, **When** il flusso fallisce, **Then** Salesforce riceve esito negativo tracciabile con codice errore e correlazione.

---

### User Story 2 - Gestione CommentSheet bidirezionale (Priority: P1)

Come utente operativo, voglio inviare e ricevere CommentSheet tra Salesforce e Team Center per collaborare sui documenti senza uscire dal processo standard.

**Why this priority**: La trascrizione evidenzia il CommentSheet come flusso bidirezionale chiave e la WBS lo dettaglia in task dedicati.

**Independent Test**: Si testa inviando un CommentSheet da Salesforce verso Team Center e verificando recupero file correlato, inoltro metadata e ricezione esito.

**Acceptance Scenarios**:

1. **Given** un CommentSheet associato a un documento/progetto, **When** l utente avvia invio verso Team Center, **Then** APIM inoltra file e metadata tramite proxy on-prem secondo formato concordato.
2. **Given** un identificativo file valido su SPE, **When** il sistema deve recuperare il file per il CommentSheet, **Then** il recupero avviene con controllo permessi applicativi e tracciamento esito.
3. **Given** formato metadata non conforme, **When** la richiesta arriva al proxy/TC, **Then** il sistema rifiuta la richiesta con errore esplicito e audit log.

---

### User Story 3 - Visualizzazione documento via identificativo (Priority: P2)

Come utente autorizzato, voglio aprire un documento dal portale tramite identificativo restituito dal processo di upload, per consultarlo in modo rapido e coerente con i permessi.

**Why this priority**: E fondamentale per usabilita del portale ma dipende dal completamento del flusso di upload e mapping metadati.

**Independent Test**: Si testa con un documento gia indicizzato, verificando accesso con utente autorizzato e blocco con utente non autorizzato.

**Acceptance Scenarios**:

1. **Given** un documento con identificativo registrato in Salesforce, **When** l utente seleziona view file, **Then** il sistema recupera e mostra il file dal repository SPE.
2. **Given** un utente senza permessi, **When** tenta la visualizzazione, **Then** il sistema nega accesso e registra evento di sicurezza.
3. **Given** un utente autorizzato, **When** richiede la preview da Salesforce/Experience, **Then** il browser accede solo a endpoint proxy e non riceve mai URL diretti SPE/Graph.
4. **Given** un file Office (DOCX/XLSX/PPTX), **When** e richiesta la preview, **Then** il sistema converte server-side in PDF/HTML prima della visualizzazione.

---

### User Story 4 - Governance, test e readiness al go-live (Priority: P2)

Come PM o Tech Lead, voglio avere tracciabilita su assunzioni, esclusioni, dipendenze e risultati test, per ridurre rischio nelle milestone di rilascio.

**Why this priority**: Le dipendenze infrastrutturali e inter-team (TC, rete, ambienti) sono critiche e vanno controllate in anticipo.

**Independent Test**: Si testa verificando checklist di prerequisiti, completamento integration test E2E e supporto UAT con evidenze documentate.

**Acceptance Scenarios**:

1. **Given** una milestone di integrazione imminente, **When** viene eseguita la readiness review, **Then** sono presenti evidenze su ambienti, connettivita, permessi e data set test.
2. **Given** esecuzione test E2E SF-AZURE-PROXY-TC, **When** i test terminano, **Then** esiti, log e anomalie sono tracciati e associati ai flussi impattati.

---

### Edge Cases

- Documento presente in Team Center ma non recuperabile da SPE per mismatch identificativo.
- Transmittal con documenti associati parzialmente non disponibili (alcuni file presenti, altri mancanti).
- Versione documento aggiornata in Team Center senza notifica esplicita a Salesforce (mancata sincronizzazione versione).
- Download tracciato fallisce silenziosamente senza registrazione audit log.
- Utente con ruolo multiplo (es. AEN TPM e Client) con visibilita conflittuale su stesso documento.
- PBUS Hierarchy non ancora importata al momento del primo accesso al portale.
- Timeout o indisponibilita temporanea del proxy on-prem durante inoltro APIM -> TC.
- Permessi Microsoft Graph incompleti (upload consentito ma view negata o viceversa).
- Incongruenza metadata tra Salesforce, SPE e Team Center (campi obbligatori mancanti).
- File non autorizzato o non trovato in fase di recupero CommentSheet.
- Regressione su flussi ticketing dovuta a confusione di perimetro con stream documentale.
- Richiesta preview per formato non supportato (es. ZIP/CAD) con fallback obbligatorio a download controllato.
- Token preview scaduto/alterato o riutilizzato (se single-use) durante apertura file.
- Allegato email (EML/MSG) con contenuto HTML non sanificato in fase di rendering preview.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: System MUST acquisire da Team Center i documenti rilasciati e i relativi metadati minimi per il contesto progetto.
- **FR-002**: System MUST trasferire i file lungo il percorso SF-MS <-> APIM <-> Proxy on-prem <-> Team Center secondo i tracciati concordati.
- **FR-003**: System MUST caricare i file su SharePoint Embedded e restituire a Salesforce un identificativo univoco di riferimento.
- **FR-004**: System MUST consentire la visualizzazione file dal portale tramite identificativo e controlli autorizzativi coerenti.
- **FR-005**: System MUST supportare il flusso CommentSheet da Salesforce verso Team Center con inoltro file+metadata tramite APIM/proxy.
- **FR-006**: System MUST supportare il recupero file legato al CommentSheet da SPE con verifica permessi applicativi.
- **FR-007**: System MUST gestire e propagare esiti positivi/negativi dei trasferimenti con codici errore e correlation id.
- **FR-008**: System MUST produrre log tecnici per ogni hop di integrazione (SF/APIM/Proxy/TC/SPE) ai fini di troubleshooting e audit.
- **FR-009**: System MUST permettere integration test E2E sul percorso SF-AZURE-PROXY-TC prima delle milestone di go-live.
- **FR-010**: System MUST esplicitare assunzioni ed esclusioni operative per ogni componente/fase di integrazione.
- **FR-011**: System MUST mantenere separato il perimetro documentale dal perimetro ticketing IPS, salvo integrazioni esplicitamente approvate in analisi.
- **FR-019**: System MUST supportare la gestione di versioning documenti con metadati strutturati per ogni versione rilasciata da Team Center.
- **FR-020**: System MUST supportare la gestione di Transmittal objects come entita di spedizione documenti verso il portale cliente.
- **FR-021**: System MUST garantire piena tracciabilita di ogni accesso e download di documento (chi, quando, quale versione).
- **FR-022**: System MUST supportare la visualizzazione file (preview) senza obbligo di download, come funzionalita core del portale.
- **FR-023**: System MUST esporre struttura PBUS (Plant > Block > Unit > System) come vista navigabile nel portale.
- **FR-024**: System MUST differenziare la visibilita di oggetti e viste in base al ruolo utente configurato (Client, External Viewer, AEN Document Controller, AEN TPM, AEN Viewer, Plant Administrator).
- **FR-025**: System MUST permettere ai clienti di approvare/rifiutare e commentare documenti, con gestione thread di risposte.
- **FR-026**: System MUST integrare il flusso Early Warning proveniente dal Diagnostic System esterno per la creazione automatica di ticket IPS (fuori perimetro documentale ma da coordinare).
- **FR-012**: System MUST applicare una policy server-side di classificazione file (preview vs download) senza delegare la decisione al client.
- **FR-013**: System MUST consentire preview inline per PDF, immagini e formati testuali semplici tramite proxy.
- **FR-014**: System MUST gestire preview dei file Office (DOCX/XLSX/PPTX) tramite conversione server-side in PDF/HTML.
- **FR-015**: System MUST gestire EML/MSG con preview strutturata (header email + body sanificato + elenco allegati).
- **FR-016**: System MUST gestire ZIP/CAD/formati specialistici come download controllato (senza preview obbligatoria), con eventuale manifest metadati dove previsto.
- **FR-017**: System MUST impedire esposizione di URL diretti SPE/Graph verso browser o client esterni.
- **FR-018**: System MUST supportare endpoint dedicati di preview e download con autorizzazione basata su contesto Salesforce.

### Non-Functional Requirements

- **NFR-001 (Affidabilita)**: I flussi di trasferimento devono essere idempotenti ove applicabile, evitando duplicazioni non volute di file/metadati.
- **NFR-002 (Sicurezza)**: Accesso a file e API deve rispettare permessi applicativi e di rete (APIM policies, proxy allowlist, Graph permissions).
- **NFR-003 (Osservabilita)**: Ogni transazione deve avere identificativo di correlazione disponibile nei log cross-sistema.
- **NFR-004 (Operativita)**: Gli errori devono essere classificati in modo azionabile (autenticazione, autorizzazione, connettivita, formato, disponibilita servizio).
- **NFR-005 (Manutenibilita)**: Le interfacce e i payload devono essere documentati e versionati per ridurre impatti su evolutive successive.
- **NFR-006 (Sicurezza Accesso)**: I token preview/download devono essere firmati server-side (es. HMAC), a TTL breve, associati a user/context e opzionalmente single-use.
- **NFR-007 (HTTP Hardening)**: Le risposte preview/download devono applicare header coerenti (Content-Type, Content-Disposition inline/attachment, Cache-Control no-store, X-Content-Type-Options, supporto range dove necessario).
- **NFR-008 (Data Leakage Prevention)**: Il sistema non deve consentire condivisione pubblica tipo "anyone with link" o accesso diretto utente ai file SPE fuori dal proxy.

### Key Entities *(include if feature involves data)*

- **Project**: entita di aggregazione principale in Salesforce, usata per contestualizzare documenti e visibilita portale.
- **Document**: file tecnico con identificativo univoco, metadati e riferimento a progetto.
- **CommentSheet**: artefatto documentale con file correlato e metadata per collaborazione verso Team Center.
- **Integration Transaction**: record logico di un passaggio tra sistemi con stato, correlation id, timestamp ed errore eventuale.
- **Permission Context**: insieme di autorizzazioni applicative e di rete necessarie a upload, retrieval e view file.
- **Preview Policy**: regole per determinare se un formato e visualizzabile inline, convertibile o solo scaricabile.
- **Access Token**: token temporaneo firmato per accesso a preview/download, con scadenza e vincoli di uso.
- **Transmittal**: oggetto di spedizione che raggruppa uno o piu documenti inviati al portale, con metadati di ricezione e registro.
- **Document Version**: versione specifica di un documento con propri metadati, stato di rilascio e riferimento a Team Center.
- **PBUS Node**: nodo della gerarchia impiantistica (Plant/Block/Unit/System) usato come chiave di navigazione e visibilita documenti.
- **User Role**: profilo applicativo che determina visibilita e azioni disponibili (Client, External Viewer, AEN Document Controller, AEN TPM, AEN Viewer, Plant Administrator).
- **Download Audit Log**: registro immutabile di ogni azione di download con user, timestamp, documento e versione.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Almeno il 95% dei trasferimenti file validi completa con esito positivo nei test E2E pre-go-live.
- **SC-002**: Il 100% delle transazioni E2E produce correlation id tracciabile su tutti gli hop previsti.
- **SC-003**: Il 100% dei casi di accesso non autorizzato alla view file viene bloccato e loggato.
- **SC-004**: Tutti i flussi ATT.2.x della WBS risultano coperti da casi test documentati prima dell UAT.
- **SC-005**: Nessun blocker aperto su prerequisiti infrastrutturali critici (proxy reachability, Graph permissions, ambienti test) alla gate di integration test.
- **SC-006**: Il 100% delle preview erogate in browser avviene tramite proxy senza esposizione di URL diretti SPE/Graph.
- **SC-007**: Per i formati non supportati a preview (ZIP/CAD/altro), il 100% dei casi mostra fallback a download controllato senza errore bloccante lato utente.

## Team e Attori (da kickoff 08/07/2026)

- **Reply Microsoft** (Cluster): Elena Martini (Tech Lead), Matteo Castiello (Analyst), Damiano Leone (Senior Backend Developer) — responsabili integrazione SPE/APIM/proxy/TC.
- **Reply Salesforce**: Chiara Ostuni (PM & Functional Lead), Mattia Diliberto, Andrea Baschieri, Luca Baudo, Francesca Galasso (Mulesoft), Stefano Masaneo (Architect).
- **EasyPlus**: team TeamCenter (Davide Odetti, Adriano Chiavacci, Mattia Fasulo) — responsabili interfacce TC outbound.
- **Ansaldo IT + Business**: SPoC unico per validazioni, UAT feedback giornaliero, account/PBUS hierarchy pre-import.

## Milestone ufficiali (da Gantt kickoff)

- **TC Outbound PBUS Interfaces Delivery**: deadline per disponibilita interfacce EasyPlus per gerarchia impiantistica.
- **TC Outbound DM Interfaces Delivery**: deadline interfacce TC per document management outbound.
- **Data Model Deploy (PBUS)**: deploy data model Salesforce pre-go-live ticketing.
- **DM Data Model Deploy**: deploy data model document management pre-go-live DM.
- **Inbound Interfaces Delivery**: delivery interfacce inbound (da Salesforce verso TC).
- **Go-Live Ticketing IPS**: novembre 2026 (seconda settimana).
- **Go-Live Document Management**: fine gennaio 2027.
- **Prototype IPS**: fine settembre 2026.

## Assunzioni operative (da kickoff)

- SPoC Ansaldo disponibile per tutta la durata del progetto.
- Anagrafiche (Account, PBUS Hierarchy, Projects, Historical Tickets) devono essere preimportate da Ansaldo prima del go-live.
- UAT feedback condiviso da Ansaldo su base giornaliera.
- Le sessioni di analisi sono strutturate per area tematica (Process Drill Down, Technical Integration, Data Access) con output documentati.

## Scope Boundaries (Assunzioni ed Esclusioni consolidate)

### In Scope
- Integrazione documentale SF-MS/APIM/SPE/TC.
- Flussi upload/view/retrieval documenti e CommentSheet.
- Setup componenti APIM/proxy/SPE necessari al perimetro.
- Test integrazione E2E e supporto UAT per stream tecnico integrazione.
- Preview strategy per formati supportati (PDF/Office/email/immagini) e fallback download per formati non supportati.

### Out of Scope
- Migrazione massiva dati/documenti non prevista dai flussi ordinari.
- Ottimizzazioni avanzate di document lifecycle o viewer custom non richiesti.
- Interventi infrastrutturali estesi non esplicitamente approvati (es. remediation rete non pianificate).
- Gestione funzionale/business completa dello stream ticketing IPS.
- Editing collaborativo Office in-browser tramite viewer SharePoint nativo.
- Condivisione pubblica file tramite link diretti SPE/Graph.
