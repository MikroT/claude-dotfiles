Voglio adottare in questo repo il mio standard personale di documentazione — un piccolo insieme di pattern complementari (in primis ADR e runbook, più altri usati solo quando serve davvero) così le decisioni e le procedure operative sopravvivono indipendentemente da questa conversazione.

## 1. Setup delle cartelle

Crea, se non esistono già:
- `docs/decisions/` — per gli Architecture Decision Record (ADR): il *perché* di una scelta
- `docs/runbooks/` — per le procedure operative: il *come* rifare/gestire qualcosa

## 2. Pattern ADR (`docs/decisions/`)

Un ADR documenta una decisione architetturale importante — non ogni piccola modifica, solo scelte che cambiano la struttura del sistema o scartano un'alternativa reale dopo discussione.

File: `docs/decisions/NNNN-titolo-breve.md`, numero progressivo a 4 cifre (0001, 0002, ...), in ordine cronologico di quando la decisione è stata presa (non di quando la scrivi).

Formato:

```markdown
# NNNN — Titolo della decisione

Stato: proposta | in discussione | approvata | superata da NNNN
Data: YYYY-MM-DD

## Contesto
Perché questa decisione serve, qual è il problema che risolve.

## Decisione
Cosa è stato deciso, con dettaglio tecnico sufficiente a essere utile in futuro.

## Alternative scartate
Cosa altro era sul tavolo e perché non è stato scelto.

## Domande ancora aperte
Se la decisione lascia punti non risolti, elencali esplicitamente.

## Riferimenti
Link a fonti esterne, PR collegate, altri ADR collegati.
```

Regola chiave: **non aggiornare mai un ADR passato per riflettere un cambio idea** — crea un nuovo ADR e marca il vecchio "Stato: superata da NNNN". Un ADR è un registro storico immutabile, non riflette lo stato attuale.

## 3. Pattern Runbook (`docs/runbooks/`)

Un runbook documenta una procedura operativa — setup infrastrutturale, disaster recovery, task ricorrenti di manutenzione. Complementare all'ADR: l'ADR dice perché avete scelto una certa architettura o strumento, il runbook dice esattamente come ricrearlo o come recuperarlo se si rompe.

File: `docs/runbooks/nome-descrittivo.md` (niente numerazione, il nome stesso deve essere ricercabile — es. `disaster-recovery-database.md`, `deploy-produzione.md`).

Formato:

```markdown
# Nome procedura

Ultimo aggiornamento: YYYY-MM-DD

## Quando usarlo
In che situazione questa procedura si applica.

## Prerequisiti
Accessi, strumenti, permessi necessari prima di iniziare.

## Procedura
Passi numerati, comandi esatti dove applicabile. Mai credenziali/segreti reali — usa placeholder (`<password>`, `<api-key>`) o rimanda a dove sono conservati (es. "vedi password manager, voce X").

## Verifica
Come confermare che la procedura è andata a buon fine.

## Troubleshooting
Problemi noti incontrati durante l'esecuzione e come risolverli, se rilevanti.
```

Regola chiave: a differenza degli ADR, **un runbook si aggiorna in-place** quando la procedura reale cambia (nuovi comandi, path diversi) — deve sempre riflettere lo stato attuale, non uno storico di versioni.

## 4. Conoscenza di sessione che va scritta, non solo ricordata

Se durante il lavoro emerge qualcosa che varrebbe la pena ricordare — una decisione presa, un vincolo scoperto, un workaround adottato — e non è già ovvio dal codice o dalla git history, trattalo come candidato per un ADR o un runbook, non solo per la tua memoria di sessione. La memoria aiuta te in questa conversazione; la conoscenza che deve sopravvivere e viaggiare con il progetto va scritta qui.

## 5. Altri pattern di documentazione (da usare solo quando serve davvero)

Oltre ad ADR e runbook, questi pattern emergono spesso — ma vanno creati solo quando scatta il loro trigger concreto, mai vuoti o per precauzione:

- `docs/incidents/` — post-mortem scritto *dopo* un problema in produzione (cosa è andato storto, come risolto, cosa cambia per prevenirlo). Trigger: primo incidente reale in produzione su questo progetto.
- `docs/glossary.md` — definizioni brevi di termini di dominio specifici del progetto. Trigger: un termine usato in modo ambiguo/incoerente genera confusione reale.
- `docs/architecture/` (diagrammi, overview di sistema). Trigger: il sistema è cresciuto abbastanza da non stare più in testa a una persona sola, o serve onboarding di qualcuno di nuovo.
- `CONTRIBUTING.md`. Trigger: qualcuno oltre all'utente e a Claude inizia a contribuire codice al progetto.
- `CHANGELOG.md`. Trigger: il progetto ha utenti/clienti esterni che devono sapere cosa cambia release dopo release.

## 6. Ricostruzione retroattiva

Ora ricostruisci lo storico di questo progetto in ADR e runbook, usando queste fonti in ordine di affidabilità. Se durante la ricerca noti che è già scattato uno dei trigger del §5 (es. un vecchio incidente di produzione mai documentato, o un termine di dominio storicamente ambiguo), segnalamelo a parte — non crearlo automaticamente, proponilo insieme al resto.
1. Questa conversazione, se contiene decisioni o procedure già discusse
2. `git log` e messaggi di commit — cerca commit che segnalano scelte strutturali (refactoring grossi, cambi di schema, scelte di libreria) per gli ADR; cerca script/config che rivelano una procedura ripetuta per i runbook
3. Commenti nel codice che spiegano un "perché" (per ADR) o descrivono passaggi operativi (per runbook)
4. README o altri file .md esistenti che già descrivono scelte o procedure

Regole per la ricostruzione:
- Non inventare contesto o motivazioni che non trovi in queste fonti. Se sai CHE una decisione è stata presa ma non trovi il PERCHÉ, scrivilo esplicitamente come "motivazione non documentata, verificare con il team" invece di inventare una giustificazione plausibile.
- Non serve un ADR per ogni scelta ovvia dal codice — solo quelle con alternative reali sul tavolo.
- Non serve un runbook per operazioni banali/standard (es. "npm install") — solo procedure specifiche di questo progetto che richiederebbero altrimenti tribal knowledge.
- Numera gli ADR in ordine cronologico reale, non di scrittura.
- Alla fine, dammi un elenco separato per ADR e runbook di cosa hai documentato, e cosa sospetti manchi ma non hai trovato abbastanza informazione per scrivere con sicurezza.

## 7. Regola permanente per il futuro

Da qui in avanti, per ogni sessione futura su questo repo:
- Prima di lavorare su un task architetturale, leggi `docs/decisions/` — non ripetere discussioni già chiuse.
- Prima di eseguire una procedura operativa nota (deploy, backup, disaster recovery), controlla se esiste già un runbook in `docs/runbooks/` — seguilo invece di reinventare il processo.
- Quando chiudiamo una nuova decisione architetturale importante, proponi di creare l'ADR corrispondente.
- Quando completiamo un setup infrastrutturale rilevante o definiamo una procedura ricorrente, proponi di scrivere o aggiornare il runbook corrispondente.
- Riconosci anche i trigger degli altri pattern (§5) — proponili solo quando scattano davvero, non prima.
- Fammi sempre confermare/rivedere il contenuto prima di committare, in entrambi i casi.
