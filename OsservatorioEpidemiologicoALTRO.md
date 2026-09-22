# Osservatorio Epidemiologico - Knowledge Base Iniziale

## 1. Overview
Questa knowledge base iniziale sintetizza le informazioni contenute nei due file di riferimento:
- **Excel**: "NumerositàDatiOsservatorioEpidemiologico.xlsx", con le numerosità (row counts) delle tabelle del Progetto Osservatorio Epidemiologico sia in area di Staging che in area di Sorgente (produzione).
- **PowerPoint**: "PresentazioneAvvioProgettoRevisioneOsservatorioEpidemiologico.pptx", contenente l'analisi AS IS del sistema, problematiche di performance, esigenze e proposte tecnico-funzionali suddivise in tre fasi progettuali.

Lo scopo è fornire una base per future attività di stima e analisi delle possibili soluzioni.

## 2. Struttura e Volumi Dati (Excel)
### 2.1 Area Staging Produzione (foglio 'AreaStagingProduzione')
- Numero di tabelle enumerate: 86.
- Colonne presenti: NOME TABELLA, N° righe, commenti (Unnamed: 2).
- **Delete tables**: tutte le tabelle T_DELETE_* in area STG sono vuote (NESSUN RECORD) secondo colonna commento. Il campo N° righe è NaN e il commento specifica che nella STG le tabelle di DELETE sono tutte vuote.
- **Tabelle transazionali con record:** (42 tabelle con N° righe non NaN)
  - T_ANAGRAFICA: 15.355.193 righe
  - T_DISPATCHER: 45.997.500 righe
  - T_VERBALE: 47.026.764 righe
  - T_VERBALE_ICD9: 44.764.944 righe
  - T_VISITA: 48.703.491 righe
  - T_SOGGETTO_RICHIESTA: 30.949.764 righe
  - T_INDIRIZZO: 33.902.694 righe
  - T_GIUDIZIO: 9.055.488 righe
  - T_GIUDIZIO_ART4: (dato non disponibile in STG, row count NaN)
  - Altre tabelle di dimensioni minori: T_ICD9: 15.010; T_CML: 469; T_ESPRESSO_DA: 3; T_GRADOINVALIDITA: 28; etc. (Si veda tabella completa di dettaglio allegata internamente). 
  - Tabelle tipologiche (codifiche, dizionari) hanno volumi variabili da poche unità (es. T_GRADOINVALIDITA: 28) a poche decine di record (TBT_* prefix), e T_Provincia: 117, TBT_Comune: 11875.
- **N° righe non forniti** per molte tabelle transazionali (NaN), in particolare le tabelle DELETE e altre con commenti null.

### 2.2 Area Sorgente Produzione (foglio 'AreaSorgenteProduzione')
- Numero di tabelle indicate: 87.
- Colonne presenti: NOME TABELLA, N° righe, data minima inserimento, data massima inserimento.
- **Delete tables**:
  - Alcuni T_DELETE_* presentano numeri di righe in sorgente (es. T_DELETE_DISPATCHER: 7.078 righe; T_DELETE_GIUDIZIO: 23.966; T_DELETE_GIUDIZIO_ART4: 26.311; T_DELETE_INDIRIZZO: 62.181; T_DELETE_SOGGETTO_RICHIESTA: 8.753; T_DELETE_Spedizione: 1.306; T_DELETE_VERBALE: 18.584; T_DELETE_VERBALE_ICD9: 44.727; T_DELETE_VISITA: 2.060) con date min inserimento e max inserimento nell'intervallo ottobre 2025 - settembre 2026.
  - Altre tabelle T_DELETE_* con righe NaN (sorgente non rilevabili / vuote).
- **Tabelle principali (sorgente)** con volumi record e date di inserimento:
  - T_DISPATCHER_STORICO: **347.749.703** righe, date dal 2010-01-09 al 2026-09-15.
  - T_GIUDIZIO: **58.897.354** righe, date dal 2010-01-01 al 9999-12-31 (9999-12-31 indica valore anomalo di maximal date o data ignota/placeholder).
  - T_VISITA: **48.791.582** righe, date dal 2010-01-09 al 2026-09-15.
  - T_SPEDIZIONE (T_Spedizione): **45.222.627** righe, date 2010-07-19 – 2026-09-15.
  - T_INDIRIZZO: **36.059.171** righe, date 2010-01-01 – 2026-09-15.
  - T_VERBALE: **47.026.764** righe (sebbene non duplicate con STG), date 2010-01-01 – 2026-09-15.
  - T_VERBALE_ICD9: **44.764.944** righe, date 2010-01-01 – 2026-09-15.
  - T_SOGGETTO_RICHIESTA: **32.937.094** righe, date 2010-01-09 – 2026-09-15.
  - T_ANAGRAFICA: **16.140.985** righe, date 2010-01-01 – 2026-09-15.
  - T_GIUDIZIO_ART4: **42.249.244** righe, date 2012-12-14 – 2026-09-15.
  - T_DISPATCHER: **48.791.582** righe, etc.
  - Tabelle dimensionali e tipologiche con volumi contenuti (TBT_..., T_CODICE_ART4, T_CML, T_MODALITA_NOTIFICA, T_ORIGINE, T_PROVENIENZA, ... con date relative 2010-01-01 e date massime variabili fino al 2026-...; specificare se occorre). 
- **Date minime e massime**: in generale i dati hanno una profondità storica dal 2010 (data minima di inserimento) fino a settembre 2026 (data massima aggiornata), salvo alcune tabelle (es. T_ICD9: max 2019-06-06; anomalie di valore max = 9999-12-31 per indicatori di data max sconosciuta; alcune tabelle T_DISPATCHER_STORICO, T_ANAGRAFICA, T_DISPATCHER, ecc. includono records fino al 15/09/2026). Il caricamento mensile stava funzionando fino a OTTOBRE 2025, come evidenziato dalle date minima inserimento sui dati di tipo DELETE (es. 2025-10-17), mentre date massime di inserimento su molte sorgenti arrivano fino al 2026-09-15 (quest'ultimo potrebbe riflettere un caricamento tempestivo in area sorgente, ma in area DWH non è più processato a causa delle problematiche di performance).
- **Volume e complessità**: le tabelle T_DISPATCHER_STORICO, T_GIUDIZIO, T_VISITA, T_VERBALE, T_SPEDIZIONE, T_ANAGRAFICA etc. rappresentano alcune decine di milioni fino a centinaia di milioni di record, con forti implicazioni per l'ETL e la performance.

### 2.3 Tabelle oggetto dell'analisi (Slide 10)
- Estrapolazione dei principali gruppi di tabelle soggette ad analisi:
  - **Dati transazionali**: circa 15 tabelle (es. T_ACTIONI, T_EVENTOCIVILE, ecc., dettagli non specificati nel testo, ma classificati come Dati transazionali nel riepilogo). Il slide indica 'Circa 15' tabelle transazionali.
  - **Tipologiche**: circa 25 tabelle di tipologie (codifiche) con righe che vanno da poche unità a qualche migliaia (in STG abbiamo elencato i volumi di tabelle TBT_* come sopra). Nel slide appare 'Circa 25' per le tipologiche e 'Circa 30' per 'Delete tabelle tra tipologiche e business' con riferimento a 10 di esse (o viceversa: 'Delete: 30 tabelle tra tipologiche e business' e numero 10 riportato probabilmente come valore medio).   
  - Il perimetro dello snapshot (Slide 10) include tabelle e viste rilevanti per il modello dimensionale (es. V_GIUDIZIO_DIM), tabelle Stage con spm_CARICA_GIUDIZIO etc.

## 3. AS IS - Situazione Attuale (PowerPoint)
### 3.1 Funzionalità e Contesto Operativo
- Lo **Osservatorio Epidemiologico** fornisce ai Medici dei CML centrali INPS e ai Dirigenti delle direzioni centrali un sistema **BI** per analisi sui dati statistici dell’Invalidità Civile.
- Le analisi si concentrano su **Giudizi Finali** espressi nei verbali delle pratiche di invalidità civile, con approfondimenti su **dati epidemiologici**.
- Dati caricati comprendono l’intero flusso di lavorazione delle pratiche di Invalidità Civile per sistemi **InvCiv2010/VOA/VO/CIC/ITML/CMS/INVER**, includendo:
  - Domande, Visite, Verbali, Giudizi, Patologie, Anagrafiche, Tipologiche.
- **Profondità storica**: dal 2010 al 2025.
- **Aggiornamento prevista**: carico mensile incrementale con gestione della storicizzazione.
- **Profilo utente**: diversi profili con visibilità territoriale.

### 3.2 Problemi di Performance (AS IS)
- Rilasciato in produzione INPS nel 2018, il sistema presenta **importanti problemi di performance** tali da non consentire più il caricamento di dati aggiornati dal 2025, impedendo la fruibilità dello strumento.
- Problemi specifici (Slide 17):
  - **Mancato aggiornamento del dato**: da 2025, l’aggiornamento mensile non risulta operativo per timeout, impedendo la fruizione dei dati recenti; impossibile sia il caricamento incrementale che quello massimo.
  - **Volume e complessità**: STG carica tutte le pratiche (invece che solo quelle modificate/inserite/eliminate nel periodo), con conseguente calcolo RILEVATORE sull’intero db; molte SP, tabelle e viste a supporto della SCD2; requisito di includere i dati della Riforma 2025 aumenta la complessità delle SP ETL.
  - **Obsolescenza**:
    - Web App sviluppata su .Net Framework 4.5 (obsoleto, supporto solo per manutenzione, funziona solo su Windows; il sistema NIC è su .Net CORE 6/8 e cross-platform).
    - Piattaforma BI obsoleta.

### 3.3 Requisiti Funzionali (Slide 5)
- **Integrazione degli elementi della Riforma della Disabilità (INPS) 2025** in sperimentazione:
  - Trasmissione delle pratiche di invalidità con il **Nuovo Certificato Medico**.
  - Nuova modalità di accertamento della disabilità nel verbale: **Unità di valutazione di base (UVB)**.
  - Focus su patologie sperimentali: **Diabete tipo 2**, **Disturbi dello spettro autistico**, **Artrite reumatoide**.
- **Integrazione dati** nell’analisi dell’osservatorio delle pratiche entrate con la riforma e gestite nei sistemi **NIC, UVB, ITML e CMS**.

### 3.4 Requisiti Tecnici (Slide 6)
- **Aggiornare piattaforma BI** usata (ovviamente Analysis Services Tabular, Reporting Services, SSIS, ecc.).
- **Ripristinare caricamento mensile** dei dati dai sistemi dell’invalidità civile, rendere nuovamente fruibile il sistema.
- **Migliorare prestazioni** nel caricamento dei dati verso l’utente finale.
- **Re-implementare la Web App** su versione più aggiornata (sostituire .Net Framework 4.5 con .Net CORE 6/8 o simile).

## 4. Proposta di integrazione con MIC2.0 (Slide 7)
- **MIC 2.0** (Monitoraggio dell’Invalidità Civile 2.0) è stato rilasciato in produzione nel 2023, realizzato su piattaforma BI in cui i dati analizzati riguardano le lavorazioni dei sistemi Invalidità Civile InvCiv2010/VOA/VO/CIC.
- Permette analisi sulle domande e prestazioni; si caricano dati di un **sottoinsieme delle tabelle** analizzate dall’Osservatorio Epidemiologico.
- Profondità storica: dal 2010, aggiornamento mensile e incrementale con storicizzazione, gestione di profili utente.
- **Proposta**: Revisionare il sistema dell’Osservatorio Epidemiologico integrandolo, per quanto possibile, nell’architettura del sistema MIC, che dovrà in futuro acquisire le stesse informazioni introdotte nell’Osservatorio in seguito alla Riforma INPS 2025.

## 5. Approfondimenti tecnici (Slides 11-14)
- Modellazione e flusso ETL consigliati:
  - Detail di SP, tabelle e viste per la dimensione GIUDIZIO:
    - Fase DELTA: es. stored procedure **spm_CARICA_GIUDIZIO** (STG: T_V_GIUDIZIO_DIM in staging, vista V_GIUDIZIO_DIM).
    - Fase DIM: es. tabella sorgente STG: T_V_GIUDIZIO_DIM, T_DELETE_GIUDIZIO con target DWH: DIM_GIUDIZIO.
    - Gestione storicizzazione: tabelle fonte a snapshot con SCD (Slowly Changing Dimensions) e quadrature per gestione per rilevatore (es. codifica Delete fisica/Logica/I/U/SCD2/nessuna variazione).
  - NUMEROSITÀ SP (Stored Procedures) ETL:
    - **17 SP spm_CARICA_***: per calcolare delta per giudizi, fasce d’età, localizzazioni; gestire provenienza e tipologie; preparare strutture intermedie per le dimensioni.
    - **42 SP spm_DIM_***: per popolare/aggiornare dimensioni (anagrafica, domanda, giudizio, verbale, territorio, tipologiche) con storicizzazione e quadrature.
    - **14 SP** (probabilmente per le tabelle numeriche, non raggruppate). (Slide 14 riporta i dettagli numerici sopra).

## 6. Analisi Confronto OE/MIC (Slide 16)
- Livelli di architettura (Sorgente, Snapshot, Staging, WRK, DWH, SSAS) illustrati per Osservatorio OE e Monitoraggio (MIC 2.0) side by side.
- Dettaglio dei database e istanze SQL Server: (es. snapshot in Sqlinps13.servizi.inps, 1989 - InvCiv2010SnapshotMaster; staging & DWH su SQLINPS302.servizi.inps,1433 per OsservatorioInvCivSTG_DWH etc.).

## 7. Soluzioni Proposte (Slides 17-18)
### 7.1 Criticità principali dell'AS IS (Slide 17)
- Problemi determinati da **timeout** e obsolescenza del caricamento dati.
- Caricamento STG esteso a tutte le pratiche -> calcolo rilevatore su intero DB.
- Numerose SP, tabelle, viste a supporto della SCD2.
- Necessità di includere i dati della Riforma 2025 (NIC, UVB, ITML, CMS) richiede aumentare le complessità delle SP ETL esistenti.
- Web App obsoleta (.Net Framework 4.5) e piattaforma BI obsoleta.

### 7.2 Proposte TO BE (Slide 18)
- **CC** (Centro competente) e **BU** (Business Unit) dovranno valutare:
  - se ci sono impatti nell’escludere dal caricamento DWH le informazioni non indispensabili (es. spedizioni, workflow) non essenziali per l’analisi statistica attuale o per estensioni a breve.
  - se la logica complessa di alcune SP ETL attuali può essere spostata in fase STAGGING, come fatto su MIC 2.0.
  - ripetere il meccanismo di **caricamento incrementale con viste e tabella condivisa tra STAGING e DWH** (seen in MIC 2.0).
  - sviluppare **nuova interfaccia** con strumenti aggiornati.
  - analizzare nuovi filtri di ricerca nell’interfaccia (es. sistema pertinenza).
  - valutare possibile svolgimento di alcune attività a livello BI per ripristinare carichi incrementali (revisando logica generale e aggiornando piattaforma BI; integrare con architettura MIC).

### 7.3 Riepilogo proposte per il TO BE (Slide 18 bottom)
- Rivedere la piattaforma BI per adeguarla alle ultime tecnologie;
- Implementare integrate soluzioni architetturali in linea con MIC 2.0;
- Eseguire analisi su spostamento logica ETL a livello di stadio;
- Ripristino carico incrementale e storicizzazione efficiente;
- Realizzare nuova interfaccia web su framework aggiornato.

## 8. Fasi Progettuali (Slide 19)
1. **Fase 1**: Integrare i dati elaborati con quelli della Riforma 2025 dai sistemi (NIC, neuen UVB, ITML, CMS). Mantenere le funzionalità esistenti, revisionare/ottimizzare backend integrando architettura MIC per rendere fruibile e performante; aggiornare la Web App su framework non obsoleto (.Net CORE 6/8).
2. **Fase 2**: Integrare i dati con le informazioni ISTAT per rappresentare statistiche rispetto ai cittadini per territorio; estendere funzionalità secondo nuovi requisiti INPS.
3. **Fase 3**: Adeguare Monitoraggio Invalidità Civile 2.0 con dati della Riforma 2025.

## 9. Riferimenti Tecnici (Slide 20)
| Component | Osservatorio Epidemiologico | Nuovo Monitoraggio Invalidità Civile |
|-----------|-----------------------------|--------------------------------------|
| ETL - SSIS | ET00064_OsservatorioInvCiv_SSIS_001 | ET00201_MonitoraggioInvCiv_SSIS_001 |
|  |  | ET00293_MIC_OHCR_SSIS |
| Cubo Tabulare (AnalysisServices Tabular) | TB0001_OsservatorioInvCiv | TB00077_OsservatorioStatInvCiv (PBIRS contiene proprio motore tabulare) |
| Reporting | SSRS, Power BI Report Server (on-premises) | Power BI Report Server (on-premises) |
| Versione Web App | .Net Framework 4.5 (obsoleto, supporto manutenzione, Windows only; NIC è su .Net CORE 6/8) | .Net Framework 4.5 |

## 10. Considerazioni sui Dati ed eventuali anomalie
- Il record con data massima 9999-12-31 in T_GIUDIZIO (sorgente) indica possibile valore segnaposto per data ignota e va gestito nel caricamento ETL.
- Alcuni campi con data minima e massima identiche al 2010-01-01 o 2010-01-09 indicano possibili tabelle master tipologiche statiche.
- Volume record in T_DISPATCHER_STORICO (347M) potrebbe determinare una delle maggiori criticità di performance nelle fasi STG e WRK, data l’elevatissima cardinalità.

## 11. Metriche Riassuntive (Volume per Tabella - STG)\*
- STG are large tables:
  - **T_VISITA**: ~48,703,491 righe
  - **T_VERBALE**: ~47,026,764 righe
  - **T_VERBALE_ICD9**: ~44,764,944 righe
  - **T_DISPATCHER**: ~45,997,500 righe
  - **T_INDIRIZZO**: ~33,902,694 righe
  - **T_SOGGETTO_RICHIESTA**: ~30,949,764 righe
  - **T_ANAGRAFICA**: ~15,355,193 righe
  - **T_GIUDIZIO**: ~9,055,488 righe
  - Tabelle complementari e di supporto tra 6 e 41M di righe.
- Tipologie (TBT_* tabelle) in ordine di grandezza da 4 a 11875 righe.
- Non ci sono record nelle Tabelle di DELETE in STG; in sorgente quelle di DELETE hanno poche decine di migliaia di righe (modifiche/eliminazioni).

## 12. Considerazioni finali
- Il sistema attuale deve essere completamente rivisitato per ripristinare funzionalità di aggiornamento dei dati e migliorare prestazioni.
- Data la mole di dati (centinaia di milioni di righe in alcune tabelle), le fasi di STAGING e LOAD richiedono una riprogettazione ETL/ELT con eventuale filtraggio, partizionamento, indicizzazione, e possibilmente uso di viste incrementali come già implementato in MIC 2.0.
- La migrazione verso ambienti e strumenti più aggiornati (.Net CORE, Azure/SSAS Tabular tecnologie se previste, piattaforme BI moderne) dovrebbe essere considerata prioritariamente.

*Le stime quantitative di volumi si basano sui valori presenti nei fogli Excel al momento dell’analisi (ultimo aggiornamento rilevato 15/09/2026). Date e conteggi mancanti sono stati considerati NaN ove non presenti.

**Fine del documento knowledge base iniziale.**