# Riassunto Trascrizione - Ansaldo Customer Portal Onboarding

## Contesto generale
La riunione allinea i team su obiettivi, perimetro e roadmap del progetto Ansaldo Customer Portal. Inizialmente il focus e su 2 BU (New Units e Service), con obiettivo di estensione successiva a tutte e 4 le BU.

## Macro-stream di progetto
Sono stati confermati 3 filoni principali:

1. Customer Portal (Experience Cloud): area cliente per consultazione progetti e scambio documentazione.
2. IPS Ticketing System (Service Cloud): gestione ticket da cliente e da segnali automatici (early warning).
3. Document Management: integrazione documentale tra Team Center e Salesforce/portale.

Nota: i tre stream hanno rilasci separati; ticketing/area cliente va live prima del document management.

## Integrazioni e architettura
- Team Center e il sistema PLM on-premise di riferimento.
- Salesforce e il backend applicativo; il portale e uno specchio dei dati presenti in Salesforce.
- Mulesoft media parte dei flussi tra Team Center e Salesforce.
- Azure API entra in gioco soprattutto per il recupero file/documenti e per alcune chiamate applicative.
- E confermato l uso di un server proxy on-prem per le chiamate verso Team Center (metadati e file), gia presente in altre integrazioni e riutilizzato anche qui.

## Chiarimenti funzionali emersi
- L integrazione Team Center riguarda principalmente il perimetro documentale.
- Per il ticketing non e prevista, allo stato attuale, una dipendenza diretta da Azure API o SharePoint, salvo nuove evidenze in analisi.
- L entita principale di aggancio dei documenti in Salesforce sembra essere il progetto.
- I commenti documento prevedono integrazione bidirezionale Team Center <-> Salesforce.

## P-Bus Hierarchy
Viene richiamata la gerarchia impiantistica (Plant -> Block -> Unit -> System ...), ereditata da Team Center e usata come base dati strutturale per i processi applicativi.

## Roadmap condivisa (alto livello)
- Fase iniziale: analisi anagrafiche, interfacce e disegno flussi integrativi.
- Analisi/design paralleli:
  - Ticketing IPS
  - Document management
- Fine settembre: primo prototipo ticketing.
- Tra fine settembre e inizio ottobre: test integrazione (con dipendenza da disponibilita interfacce Team Center).
- Novembre (seconda settimana): primo go-live ticketing.
- Fine gennaio: go-live document management (gia shiftato di alcune settimane).
- Previsti deploy tecnici del data model prima dei go-live, per supportare migrazioni e preload dati.

## Aspetti organizzativi e operativi
- Team Team Center lato system integrator: citato il gruppo Easy Plus.
- Richiesta raccolta foto team per presentazione completa.
- Richiesta account dominio Ansaldo per abilitazioni (es. permessi area Azure).
- Kick-off confermato per 8 luglio (11:00-12:30), con verifica distribuzione inviti.

## Rischi/attenzioni emerse
- Necessita di dettagliare ulteriormente i diagrammi architetturali e i flussi.
- Dipendenza critica dalla disponibilita interfacce Team Center per rispettare test e milestone.
- Necessario validare capacita operativa durante il periodo ferie natalizie sulla coda finale del piano.

## Prossimi passi suggeriti
- Consolidare i flussi integrativi con livello di dettaglio tecnico maggiore.
- Confermare scenario early warning -> ticket e relativo pattern API definitivo.
- Definire con precisione modello dati documentale (metadati + entita correlate).
- Pianificare sessioni di analisi tematiche prima del kick-off per arrivare con agenda e output attesi gia definiti.
