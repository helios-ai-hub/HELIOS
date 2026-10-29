# Analisi — Ecosistema multi-agente `limet-agents` (Claude Code + LIMET)

> **Natura del documento.** Analisi d'ingresso da passare a LIMET. **Non** è già un PLAN o una SPEC esecutiva: LIMET deve trasformarla, attraverso il proprio metodo, nei documenti necessari (PLAN, SPEC, TASK_LIST, TEST_PLAN, E2E_VERIFICATION, ecc.) e poi usarli per realizzare e verificare l'infrastruttura descritta, secondo i gate di approvazione LIMET.
>
> **Obiettivo vincolante:** realizzare l'intera infrastruttura `limet-agents`, compreso un **Dynamic Workflow nativo di Claude Code** effettivamente installabile, invocabile, eseguibile e collaudato. Il workflow Claude non deve restare una voce architetturale o un semplice script JavaScript non riconosciuto da Claude Code.
>
> **Fonte:** `FEASIBILITY_O1_CLAUDE_CODE.html` v2.0 (2026-10-27). Verdetto della fonte: **FATTIBILE** con sola LIMET + funzionalità native di Claude Code (2.1.311), previe le modifiche documentali a LIMET del §7. Tutto quanto è descritto come stato attuale è da distinguere dalla proposta: **nessun agente, workflow, skill, build o test è stato eseguito** secondo la fonte. Marcatori: `[FATTO]` osservato · `[IPOTESI]` · `[DA VERIFICARE]`.
>
> **Verifica tecnica obbligatoria durante l'implementazione:** confermare la versione effettivamente installata di Claude Code e la sintassi/API del suo supporto ai Dynamic Workflows. Usare la documentazione ufficiale relativa alla versione disponibile. Non dare per implementato o funzionante il workflow finché non sia stato caricato e invocato realmente in Claude Code e non abbia superato gli scenari di collaudo previsti.

## 0. Istruzioni a LIMET (come usare questa analisi)

1. Trattare la change come **progetto da zero** (App. B.2) più **modifica documentale a LIMET** (App. B.4): vedi §6 (artefatti da realizzare) e §7 (modifiche a LIMET). Proporre due change distinte o una change con due blocchi; motivare la scelta `PLAN` vs `CHANGE_PROPOSAL` + `SPEC`. La soluzione è estesa, introduce una nuova capability e coinvolge più moduli.
2. Generare i documenti LIMET **partendo dai requisiti candidati del §4** (assegnare ID `R<n>` stabili), dalle decisioni del §5 (già prese: non riaprirle, salvo conflitto documentato) e dalle **domande aperte del §10** (da porre all'utente una alla volta, non da assumere).
3. Il primo task della TASK_LIST è il **contract**: struttura del plugin, schemi JSON, formato di `state.json` ed `events.jsonl`, stati, interfacce tra componenti e contratto del runner. Tutti gli altri task dipendono da questo contract quando ne usano gli artefatti.
4. Strategia di test: vedi §9 (scenari V1–V24 candidati per TEST_PLAN/E2E_VERIFICATION), inclusi i collaudi dell’osservabilità e della dashboard metriche. LIMET deve integrarli e completarli con precondizioni, comandi ripetibili, risultati attesi e criteri oggettivi.
5. **Obbligo di realizzazione:** gli artefatti elencati al §6 costituiscono lo scope di implementazione, non una lista di suggerimenti. LIMET deve generare i documenti che li specificano e, dopo l'approvazione prevista dal suo metodo, guidare la loro realizzazione. Il risultato atteso è l'infrastruttura funzionante, non soltanto un piano o una relazione.
6. **Workflow Claude obbligatorio:** creare il workflow nativo di Claude Code `limet-change`, distribuito nel plugin `limet-agents`, con file `workflows/limet-change.js` e scheduler di supporto. Verificare la compatibilità con il meccanismo di Dynamic Workflows e con il formato plugin della versione installata. Il file deve essere riconosciuto e invocabile secondo le convenzioni effettive di Claude Code; deve orchestrare concretamente gli agenti e i controlli previsti da questo documento.
7. Nella **fase iniziale di analisi e formalizzazione LIMET**, non eseguire build/test e non fare commit/push; l'utente cura il re-index di LIMET in CEREBRO. Una volta che la change di implementazione è stata formalizzata e approvata, build e test dell'infrastruttura devono essere eseguiti attraverso il runner deterministico previsto dal progetto, mai direttamente da un LLM. Non confondere quindi la fase di formalizzazione dei documenti con la successiva fase di implementazione e collaudo.
8. Non consentire merge su `main`, push, migrazioni o operazioni distruttive agli agenti. La consegna e l'eventuale merge finale restano soggetti alle autorizzazioni esplicite del §5 e dei requisiti R19–R21.
9. Se una funzionalità nativa di Claude Code non è disponibile nella versione effettivamente installata, documentare la verifica e l'impatto; proporre il fallback più vicino compatibile con le decisioni architetturali. Non presentare un generico script JS come Dynamic Workflow nativo se non è realmente caricato ed eseguibile dal runtime Claude Code. Ogni deviazione dalla soluzione nativa deve essere dichiarata e approvata.
10. **Compatibilità retroattiva vincolante:** le modifiche a LIMET devono aggiungere la modalità multi-agente senza rimuovere né rendere obbligatoria la nuova infrastruttura nei flussi tradizionali. In assenza del plugin `limet-agents` e senza una richiesta esplicita di invocare `limet-change`, l'utente deve poter continuare a usare LIMET secondo il metodo manuale/assistito già esistente. Le regole specifiche di orchestrazione multi-agente si applicano solo a quel percorso; i nuovi campi e template sono obbligatori soltanto quando pertinenti al tipo di change o al workflow selezionato. Non imporre migrazioni retroattive delle change esistenti.

## 1. Contesto e obiettivo

LIMET è un repo di **metodo** (manuale IT/EN, 18 template, `limet.ps1/.sh`, `limet-index`, launcher). Oggi l'esecuzione dei task è manuale/assistita da una sola sessione agente.

**Obiettivo.** Realizzare un ecosistema esterno a LIMET, denominato plugin `limet-agents`, che **esegue una change già documentata e approvata**: sviluppo, test, integrazione, build/test automatici tramite esecutore deterministico, validazione dei requisiti e reportistica. L'ecosistema usa LIMET come fonte di requisiti e metodo, riducendo la lettura umana a evidenze meccaniche e a un campione di risultati selezionato in base al rischio.

L'infrastruttura finale deve includere tutti gli artefatti del §6 e deve fornire almeno un **Dynamic Workflow nativo di Claude Code** che avvii e coordini il ciclo end-to-end. Questo workflow è l'entry point operativo dell'ecosistema: legge i documenti LIMET approvati, chiede la conferma del grafo quando necessario, orchestra le onde di task, coordina gli agenti, invoca le skill e lo script runner nei punti consentiti, aggiorna lo stato e produce i report previsti.

Casi d'uso coperti (stessi dell'App. B di LIMET):

| Caso | Input documenti (scritti dall'utente) | Comportamento specifico |
| --- | --- | --- |
| Progetto da zero (B.2) | PLAN, SPEC delle prime capability, TASK_LIST (task #0 = contract), TEST_PLAN, E2E_VERIFICATION | Onde di task a partire da repo vuoto/scaffold |
| Nuova feature (B.4) | PLAN o CHANGE_PROPOSAL + SPEC (+ DESIGN), TASK_LIST, TEST_PLAN, E2E_VERIFICATION | Esecuzione task, test, e2e, validazione su SPEC |
| Bug fix (B.3) | BUG_REPORT, TASK_LIST minimale, TEST_PLAN con regressione | Test di regressione **deve fallire** prima del fix → fix minimale → **deve passare** dopo il fix → regressione sui file toccati → validazione sul comportamento atteso |

## 2. Perimetro

**Dentro:**

- Plugin `limet-agents` (agenti, **Dynamic Workflow nativo di Claude Code**, scheduler deterministico, skill runner/report, permessi/hook, MCP, script di installazione e aggiornamento).
- Artefatti per-progetto di configurazione.
- Modifiche documentali a LIMET (§7).
- Schemi e contratti fra workflow, scheduler, agenti e runner.
- Scenari di validazione (§9) e fase pilota.
- Test di unità per scheduler, parser, schemi, transizioni di stato e componenti deterministici.
- Test del workflow nativo: caricamento/registrazione, invocazione, orchestrazione delle onde, gestione degli errori, stato, ripresa e gate.
- Instrumentazione deterministica del ciclo di vita degli agenti e dei task, raccolta/aggregazione delle metriche e generazione di una dashboard HTML SPA interattiva.

**Fuori (esplicito):**

- Agenti che **scrivono o modificano documenti LIMET** (li leggono soltanto; scrivono solo in `limet/changes/NNNN/ecosystem/`).
- Chiusura documentale LIMET (`TEST_EXECUTION`, Esito implementazione, `ARCHIVE_ENTRY`): resta all'utente, usando `REPORT_FINALE.md` come fonte.
- Framework di agenti terzi, altri strumenti AI, repository esterni.
- Merge su ramo principale, push, migrazioni, operazioni distruttive da parte degli agenti, salvo l'eccezione di consegna esplicita e controllata descritta in R21.
- Esecuzione di build/test **da parte di un LLM**: li esegue solo uno script deterministico (`limet-runner`), invocato dal processo autorizzato.

## 3. Attori

| Attore | Ruolo | Non fa mai |
| --- | --- | --- |
| Supervisore (utente) | Scrive/approva i documenti LIMET; risponde alle domande; conferma il grafo; legge il campione a rischio; fa il merge su `main`; chiude la change | Leggere tutto il codice |
| Orchestratore (sessione Claude Code + Dynamic Workflow nativo) | Legge regole da `AGENTS.md` (via `@AGENTS.md` in `CLAUDE.md`); avvia il workflow `limet-change`; lancia/coordina agenti; tiene `state.json` mediante le funzioni autorizzate del workflow; pone domande con `AskUserQuestion`; invoca il runner tramite skill/script nei punti consentiti | Scrivere codice o documenti LIMET; eseguire direttamente build/test; fare merge su `main`; superare i gate umani |
| `analyst` (opus) | Legge PLAN/SPEC/TASK_LIST/TEST_PLAN; stima file (Graphify) e contesto (CEREBRO); collega task↔requisiti↔test; propone split; produce `plan.json` | Inventare requisiti; procedere con documenti incompleti |
| `developer` ×N (sonnet) | Implementa un task in worktree isolato; commit sul ramo del task; elenco file in formato «Registro modifiche» | Build/test; file fuori perimetro; push |
| `tester` ×M (sonnet) | Traduce in codice il TEST_PLAN senza vedere il diff del developer; autorevisione §5.1 (`self_review`) | Eseguire test; adattarli all'implementazione |
| `integrator` (sonnet) | Integra task + test su `integration/NNNN`; conflitti banali risolti, gli altri rimandati | Conflitti semantici; merge su `main` |
| `runner` (**script, non LLM**) | Build, test selezionati, regressione, copertura, mutation, statica, e2e; log su disco; riepilogo JSON per SHA | Interpretare; eseguire su codice non committato |
| `test-reader` (haiku) | Classifica ogni fallimento: codice / test / ambiguità contratto / ambiente; usa estratti reali di log | Riassumere a memoria; dichiarare PASS un test non eseguito |
| `reviewer` (opus, modello ≠ developer) | Checklist su SPEC + diff (conformità, convenzioni, invarianti, sicurezza, complessità); findings con severità/file/riga | Correggere; approvare «a sensazione» |
| `validator` (opus) | Confronta report (test, e2e, copertura, mutation) con i requisiti SPEC: `conforme / non conforme / non determinabile` con evidenze (test, run, SHA) | Dichiarare conforme senza prove |
| `metrics-observer` (modello configurabile, preferibilmente economico per analisi riepilogative) | Ai checkpoint/alla fine di un'onda legge le metriche aggregate aggiornate; a fine run interpreta tempi, concorrenza, utilizzo risorse, costi disponibili, errori e colli di bottiglia e fornisce una sintesi tecnica separata per la dashboard/report | Inventare o correggere metriche; dedurre dati mancanti; usare la memoria della conversazione come fonte; leggere o incorporare prompt completi, segreti o diff non necessari |
| `e2e-designer` / `e2e-reader` | Dal documento E2E_VERIFICATION a script Playwright (Claude in Chrome); classificazione esiti con trace/screenshot | Eseguire e2e in proprio; inventare passi |

Il workflow Claude deve essere responsabile del **flusso di controllo**; lo scheduler deve essere responsabile delle decisioni deterministiche di eleggibilità, dipendenze, budget e dimensione delle onde. L’instrumentazione deve emettere eventi verificabili del ciclo di vita degli agenti e dei task; il collector/aggregatore deterministico calcola le metriche, mentre l’agente `metrics-observer` interpreta soltanto dati già aggregati. Non duplicare le stesse regole in implementazioni concorrenti: definire contratti unici e testati.

## 4. Requisiti candidati (da trasformare in SPEC con scenari Dato/Quando/Allora)

Ogni requisito va completato con valori concreti, casi limite e d'errore.

### Esecuzione e orchestrazione

- **R1** Su richiesta esplicita (es. «usa il workflow `limet-change` per eseguire la change NNNN») l'ecosistema esegue la change leggendo solo i documenti LIMET; rifiuta di partire se PLAN non è approvato (chi + data), o se mancano SPEC/TASK_LIST/TEST_PLAN. L'entry point deve essere il workflow nativo distribuito nel plugin, invocabile secondo la sintassi effettiva di Claude Code. Se il comando non è registrato o il workflow non può essere caricato, l'esecuzione fallisce in modo esplicito senza simulare una partenza valida.
- **R2** L'`analyst` produce `plan.json` (task `dev` e `test`, file, dipendenze, requisiti, stima, rischio di conflitto) e propone split dei task oltre il cap (`files ≤ 6`, `diffLines ≤ 300`, configurabili).
- **R3** Gate umano: il grafo è mostrato al Supervisore (numero di task, onde, stima tempo/costo, split proposti) con scelte conferma / senza split / fermati; la conferma è registrata in `state.json`/`events.jsonl`.
- **R4** Scheduler (funzioni pure, testate con `node --test`): `ready` = task READY con dipendenze VERIFIED/INTEGRATED; onde = insiemi di file disgiunti (conflitto → serializza); `budget = min(maxAgents, slot CPU − build in corso, costo residuo / costo medio task)`; priorità = cammino critico, rischio, stima.
- **R5** Ogni developer/tester lavora in worktree separato e ramo dedicato (`task/NNNN-Tn`, `test/NNNN-Tnt`); il tester non vede il diff del developer.
- **R6** Integrazione su `integration/NNNN`; il task avanza solo dopo run del runner sullo **stesso SHA**.
- **R7** Un task non è mai `done` senza TEST_PLAN compilato o eccezione §5.3 motivata.
- **R23** Il plugin deve contenere un **Dynamic Workflow nativo di Claude Code** per l'esecuzione della change, distribuito in `workflows/limet-change.js` (oppure nel percorso supportato e dichiarato dal manifesto del plugin). Il file deve rispettare il formato previsto dal runtime della versione installata, inclusi metadata obbligatori, schema del modulo e API di orchestrazione supportate. Deve usare il runtime nativo dei workflow e i suoi costrutti documentati (per esempio `agent()`, `parallel()` e/o `pipeline()` quando supportati), delegando allo scheduler le decisioni deterministiche. Il workflow deve essere realmente riconosciuto, invocabile e verificato in una sessione Claude Code; non è sufficiente che il file esista o che passi un controllo sintattico Node isolato. La prova di accettazione deve dimostrare almeno: registrazione/invocazione, orchestrazione di agenti, rispetto delle dipendenze, gate umano, gestione degli esiti e aggiornamento dello stato. Se il plugin può distribuire workflow ma non quello specifico formato, adeguare la struttura al formato supportato e documentare la decisione senza sostituire tacitamente il workflow nativo con un generico script.
- **R24 — Compatibilità con l'uso tradizionale di LIMET:** il plugin `limet-agents` è facoltativo per i progetti e i flussi tradizionali. Le istruzioni operative di R1–R23 e R25–R27 che riguardano l'esecuzione multi-agente, i gate del workflow, `state.json`, `events.jsonl`, worktree, metriche e report dell'ecosistema si applicano quando l'utente invoca esplicitamente `limet-change` o sceglie la modalità multi-agente. Non devono impedire il normale uso manuale/assistito di LIMET, imporre l'installazione del plugin o rendere obbligatori per tutte le change i nuovi artefatti specifici del multi-agente. I documenti e le change preesistenti devono restare utilizzabili senza migrazioni obbligatorie, salvo decisione esplicita dell'utente.

### Osservabilità, metriche e report HTML SPA

- **R25 — Raccolta affidabile delle metriche:** il workflow e gli hook autorizzati devono registrare, con schema versionato e timestamp UTC, gli eventi del ciclo di vita di ogni agente/task (creato/istanziato, avvio effettivo, assegnazione, inizio/fine, successo, fallimento, blocco, annullamento, timeout, retry e motivo). Registrare identificativi run/task/agente, ruolo, modello quando disponibile, wave, dipendenze, worktree/branch quando pertinente, esito, SHA associato e riferimenti agli artefatti. Distinguere l'agente pianificato da quello realmente avviato; i conteggi non devono essere ricavati dalla memoria o dal testo del modello. Un collector/aggregatore deterministico deve derivare da `events.jsonl` e dagli output strutturati le metriche normalizzate e conservarle sotto `limet/changes/NNNN/ecosystem/metrics/`. Usare un clock monotono per le durate quando disponibile e timestamp UTC per correlazione; validare eventi duplicati, mancanti o incoerenti e riportare la qualità/completezza dei dati.
- **R26 — Dashboard finale HTML SPA:** al termine del run, e negli eventuali report notturni, generare `REPORT_METRICHE.html`, una Single Page Application navigabile, responsive e di qualità grafica professionale/accattivante, con grafici interattivi e sintesi leggibili. Deve essere apribile localmente anche senza connessione Internet e non deve richiedere un server o una dipendenza CDN al momento della consultazione. Il report deve mostrare almeno: KPI riepilogativi; agenti istanziati/pianificati/avviati/terminati con suddivisione per ruolo, stato e modello; tempi di attesa, durata e completamento; andamento temporale e sovrapposizione delle esecuzioni (timeline/Gantt e parallelismo effettivo); durate per task/wave/ruolo; throughput; success rate, errori, timeout, retry e rework; durata di build/test; risultati di test e gate; percorso critico e colli di bottiglia quando calcolabili; consumo token e costo soltanto se esposti da fonti attendibili; budget previsto/consumato quando disponibile; utilizzo CPU/RAM/disco e overhead di orchestrazione solo se misurabili in modo affidabile. Prevedere filtri per run, wave, ruolo, stato e intervallo temporale, drill-down sui dettagli rilevanti, confronto tra run, nonché export dei dati aggregati in JSON/CSV. Il report deve indicare versione del plugin, change/run ID, intervallo temporale e copertura dei dati.
- **R27 — Separazione tra misurazione e interpretazione, accuratezza e privacy:** i valori numerici derivano solo da eventi registrati e fonti macchina verificabili; la generazione HTML e i calcoli devono essere deterministici. `metrics-observer` può aggiungere una sintesi narrativa su efficienza, anomalie e possibili miglioramenti, ma non modifica i dati grezzi né attribuisce causalità non dimostrata. Metriche non disponibili (ad esempio token/costi se il runtime non li espone) devono essere indicate come `N/D` con motivazione, mai stimate silenziosamente. Non includere prompt integrali, contenuti sensibili, credenziali, segreti, diff completi o log grezzi nel dashboard; usare riepiloghi e riferimenti agli artefatti protetti. Fare escaping/sanitizzazione dei dati inseriti nell'HTML per prevenire injection; non caricare risorse remote; rispettare permessi e retention configurata. La raccolta va progettata con overhead misurabile e contenuto, da quantificare nel pilota.

### Supervisore e domande

- **R8** Protocollo «fermati e chiedi»: l'agente con informazione mancante/contraddittoria fa commit WIP e restituisce `blocked` con domande (fonti consultate, opzioni, conseguenze, impatto); **non è mai uno stop globale** (bloccano solo i dipendenti e i task con file in comune).
- **R9** Le risposte finiscono in `ecosystem/QUESTIONS.md` (formato tabella «Domande contestuali» LIMET) e nel brief; una domanda equivalente non si ripresenta; se la risposta cambia la SPEC, l'utente è avvisato di aggiornarla con LIMET.

### Esecuzione test (runner)

- **R10** Build/test solo via skill `limet-runner` (`build | test --select | lint | mutation | coverage-diff | e2e | all`): verifica SHA e worktree pulito, log su disco, redazione segreti, `result.json` con distinzione `INFRA_ERROR`; nel contesto LLM entra solo il riepilogo.
- **R11** Il `test-reader` classifica ogni fallimento; difetto del codice → fix al developer, difetto del test → al tester; estratto di log reale salvato in `ecosystem/results/reader-<run>.json` nel formato atteso da TEST_EXECUTION.
- **R12** `maxAttempts` (3) per task prima di fermarsi e chiedere; mai un terzo tentativo cieco sullo stesso test.

### Gate e validazione

- **R13** Gate di merge (tutti obbligatori; eccezioni solo firmate dall'umano in `exceptions.json`): build PASS ∧ test del task PASS (0 NOT_RUN) ∧ regressione mirata PASS ∧ statica 0 errori ∧ copertura differenziale ≥ soglia ∧ mutation score ≥ soglia (classi nuove) ∧ invarianti meccaniche 0 violazioni ∧ reviewer 0 *blocking* ∧ SHA run == SHA candidato.
- **R14** Il `validator` emette per ogni requisito `conforme / non conforme / non determinabile` con `evidence[]` (test, run, SHA); `UNDETERMINED` di default; segnala requisiti senza test e test senza requisito.
- **R15** Bug fix: la regressione deve **fallire prima** del fix (run su SHA diverso) e passare dopo; regressione sui file toccati.
- **R16** E2E: `e2e-designer` = Playwright Test Agents (planner + generator, §13.1) con il contenuto di E2E_VERIFICATION passato come testo (+ Claude in Chrome/`playwright-cli` per esplorare) → script `@playwright/test` → eseguiti dal runner → reader con trace/screenshot e stato pre/post (Postgres MCP Pro, sola lettura). L'`healer` ripara solo fallimenti classificati dal reader come «difetto del test».

### Stato, report, sicurezza

- **R17** `state.json` (scritto solo dal workflow tramite un modulo/funzione con schema e validazione) con stati task: `PLANNED/READY → ASSIGNED → DELIVERED → BUILDING → TESTED_OK|TESTED_KO|BUILD_ERROR|INFRA_ERROR|MERGE_CONFLICT → VALIDATING → VERIFIED|REJECTED|UNDETERMINED`, più `WAITING_SUPERVISOR`, `STALE`, `BLOCKED`; mappati sui quattro stati LIMET §4 (`pending/in_progress/blocked/done` con data + ora). `VERIFIED` richiede SHA run = SHA integrazione; `STALE` se un merge successivo tocca i suoi file senza ri-test. `events.jsonl`: una riga per evento con tipo, timestamp UTC, causa e artefatto; includere eventi di ciclo di vita e timing per alimentare le metriche, senza registrare prompt integrali o segreti.
- **R18** Report: `REPORT_NOTTURNO.md` (stato, task, requisiti, qualità, domande aperte, decisioni autonome, **«Da leggere oggi»** con rischio), `REPORT_CONFORMITA.md`, `REPORT_FINALE.md` (per task: stato, data/ora, righe di Registro modifiche pronte, estratti log, Q&A, costi e riferimento a `REPORT_METRICHE.html` quando generato). Mai «completato» se non lo è: `INCOMPLETO` / `IN ATTESA DEL SUPERVISORE`.
- **R19** Permessi a livello di tool (non solo prompt): `deny` su `Bash(./mvnw*)`, `Bash(git push*)`, `Bash(git merge*)` (salvo integrator su `integration/*`, mai su `main`); hook `PreToolUse` che logga ogni tool call in `events.jsonl`; hook `UserPromptSubmit` con `limet_rag.py`. Verificare che le policy siano effettivamente applicate alla versione Claude Code e ai diversi ruoli/worktree; non trattare il solo prompt come barriera di sicurezza.
- **R20** Limiti da `limits.json` (`maxAgents 3`, `maxBuilds 1`, `maxAttempts 3`, `budgetUsd`, `taskCap`, `models` per ruolo, `night.maxHours`, `night.stopOnQuestions`), sovrascrivibili nel prompt per singola esecuzione nei limiti autorizzati e riportati nello stato. Esaurito il budget → stop controllato con checkpoint e ripresa (`resumeFromRunId`).
- **R21** Merge su 3 livelli: ramo task (agenti, solo commit) → `integration/NNNN` (integrator, solo con gate verdi) → `main` (solo utente, dopo report e campione a rischio con spunta obbligatoria). Prevista una fase finale opt-in «consegna» con `commit-commands` (§13.1), attivata solo su richiesta esplicita dell'utente a fine progetto; il merge su `main` resta dell'utente.

### Installazione e distribuzione

- **R22** `limet-agents.ps1/.sh init|update` (modello `limet.ps1`, idempotente): `claude plugin marketplace add <cartella> --scope project` + `claude plugin install limet-agents@limet-agents --scope project`; copia senza sovrascrivere `limits.json`, `runner.config.json`, `.mcp.json` dagli esempi. Alternative: `claude --plugin-dir`, copia di `.claude/`. `[DA VERIFICARE]` ambito `project` di `install`, modalità di distribuzione dei workflow e comando di invocazione effettivo.

### Requisiti non funzionali (NFR)

- Costo scenario di riferimento 20–45 USD API (modello parametrico, da ricalibrare col pilota); tempo di parete 6–10 h con 3 agenti + 1 build; supervisione umana 2–4 h.
- Risorse (macchina di riferimento 4c/8t, 15,7 GB RAM, 60,8 GB liberi): disco aggiuntivo 4–9 GB, picco RAM ~8–9 GB.
- Determinismo: stesso SHA → stesso esito del runner; riproducibilità via log su disco.
- Idempotenza degli script di installazione; nessuna dipendenza da servizi esterni oltre toolchain di test del progetto e strumenti di contesto (CEREBRO/Qdrant/Ollama/Graphify).
- Il workflow deve rispettare `maxAgents`, `maxBuilds`, `budgetUsd` e i limiti di runtime effettivi di Claude Code; non deve dedurre che il parallelismo configurato sia sempre disponibile se il runtime o la macchina impongono limiti inferiori.
- Le metriche devono distinguere tempo di parete (wall-clock) e somma del tempo di lavoro degli agenti (agent-time); contare separatamente agenti pianificati, istanziati, effettivamente avviati e terminati.
- La disponibilità di token/costi dipende dalle telemetrie realmente esposte da Claude Code/runtime/provider: quando non disponibili, mostrare `N/D` e non inventare stime. Misurare l'overhead introdotto dall’instrumentazione nel pilota; non imporre una soglia non verificata prima di avere una baseline.
- `REPORT_METRICHE.html` deve essere consultabile offline, senza CDN o richieste remote; i dati e il report devono essere versionati e correlabili con il run/SHA che li ha prodotti.

## 5. Decisioni già prese (dalla fonte)

1. Una sola sessione orchestratrice; agenti usa-e-getta in worktree; stato nei file, non in memoria.
2. `runner` è uno **script**, non un agente; build/test mai eseguiti da LLM (coerente con policy LIMET).
3. Plugin Claude Code **esterno a LIMET** (`C:\software\limet-agents`, repo git proprio) con `.claude-plugin/plugin.json`, `agents/`, `workflows/`, `skills/`, `settings.example.json`, `limits.example.json`, `mcp.example.json`; per-progetto solo `limits.json`, `runner.config.json`, `.mcp.json`, `docs-limet/{conventions,invariants}.md`.
4. Registrazione automatica a livello progetto (opzione 3 di §G della fonte), da confermare con una prova d'installazione reale.
5. Modelli per ruolo: analyst/reviewer/validator opus; developer/tester/integrator sonnet; test-reader/e2e-reader e `metrics-observer` haiku o altro modello economico/configurabile per analisi riepilogative. Verificare che gli alias siano disponibili nel piano e nella versione effettivamente utilizzata; in caso contrario configurare un alias/modello compatibile senza alterare il ruolo logico.
6. Reviewer con modello diverso dal developer; tester senza accesso al diff del developer.
7. Identificativi: `NNNN-slug`, `R<n>`, `T<n>`, `T<n>t`, `r-NNNN`; artefatti in `limet/changes/NNNN/ecosystem/`.
8. Scheduler = codice JS puro di supporto al workflow, non LLM. Un run per onda (limite di agenti per run), checkpoint atomico + tag `wave/NNNN-k`.
9. Mutation testing (PIT) solo sulle classi del task; se lento, gate degradato a copertura differenziale con evento esplicito.
10. **Il Dynamic Workflow Claude è un artefatto di runtime, non solo una convenzione progettuale.** Il plugin deve distribuire un workflow nel formato nativo supportato da Claude Code; la sua registrazione, invocazione e orchestrazione reale sono criteri di accettazione della change. La sintassi esatta (metadata, funzioni disponibili, percorso plugin e invocazione slash) va verificata con la versione installata e con la documentazione ufficiale corrente.
11. `limet-change.js` governa il ciclo di controllo, mentre `limet-scheduler.js` calcola in modo deterministico le onde/assegnazioni; evitare logiche discordanti o duplicate fra i due.
12. **Osservabilità affidabile:** il ciclo di vita degli agenti viene rilevato con eventi del workflow/runtime e raccolto da codice deterministico; `metrics-observer` è un ruolo di analisi a posteriori e non la fonte primaria dei conteggi o delle durate. La dashboard viene generata automaticamente alla fine di ogni run completo o arresto controllato, indicando esplicitamente dati parziali e incompleti.

## 6. Artefatti da realizzare (nessuno esiste oggi, salvo dove verificato)

| # | Artefatto | Percorso | Contenuto |
| --- | --- | --- | --- |
| 1 | Definizioni agente | `agents/{analyst,developer,tester,reviewer,integrator,test-reader,validator,e2e-designer,e2e-reader,metrics-observer}.md` + `_common.md` | Frontmatter `name/description/model/tools`; regole del ruolo; «fermati e chiedi»; output JSON |
| 2 | **Dynamic Workflow nativo Claude Code** | `workflows/limet-change.js` | Entry point del ciclo; metadata/formato accettato dalla versione Claude Code; lettura e validazione degli input LIMET; avvio agenti; gate umano; orchestrazione delle onde; invocazione runner; aggiornamento stato/eventi; retry, checkpoint, ripresa e report. Deve poter essere caricato e invocato realmente dal plugin. |
| 3 | Scheduler deterministico | `workflows/limet-scheduler.js` + test `node --test` | Funzioni pure; graph/deps, `ready`, onde, cap, rischio di conflitto, priorità, budget e schemi (`plan`, `result`, `findings`, `verdict`, `reading`, `merge`); nessuna decisione di scheduler affidata a un output LLM non validato |
| 4 | Skill runner | `skills/limet-runner/{SKILL.md,run.ps1,run.sh,runner.py,runner.config.example.json}` | Parsing Surefire/JaCoCo/PIT/SpotBugs/Playwright → `result.json`; build/test esclusivamente tramite script deterministico |
| 5 | Skill report | `skills/limet-report/{SKILL.md,nightly.py,generate-metrics-report.js}` | `REPORT_NOTTURNO` / `REPORT_CONFORMITA`, scoring rischio e generazione deterministica della dashboard metriche |
| 6 | Permessi/hook/MCP/limiti | `settings.example.json`, `limits.example.json`, `mcp.example.json` | Vedi R19–R20; MCP `limet_search` + DB read-only |
| 7 | Installer | `limet-agents.ps1/.sh` | Installazione/aggiornamento idempotente del plugin, dei suoi workflow/agenti/skill e delle configurazioni per-progetto |
| 8 | Doc per progetto | `docs-limet/{conventions.md,invariants.md,ci/}` | Invarianti umani + versione eseguibile per il runner |
| 9 | Scaffold E2E | `e2e/` (Playwright config, page-object, fixtures) | Toolchain test progetto |
| 10 | README del plugin | `README.md` | Installazione, uso, invocazione del workflow, configurazione, gate, ripresa, dashboard metriche, limiti, troubleshooting e sicurezza |
| 11 | Contract e schemi | percorsi condivisi definiti dal task #0 | Schemi validati per `plan.json`, `state.json`, `events.jsonl`, `result.json`, `exceptions.json` e output strutturati degli agenti |
| 12 | Test automatici e prove di accettazione | `tests/` o percorsi definiti nel contract | Unit test per funzioni deterministiche; test degli schemi/transizioni; test d'integrazione del workflow; scenari V1–V24 |
| 13 | Instrumentazione e aggregatore metriche | `metrics/` oppure modulo condiviso definito nel contract (`metrics-schema.json`, `collect-metrics.js`, test) | Eventi versionati; calcolo deterministico di conteggi, durate, concorrenza, throughput, fallimenti/retry e qualità dei dati; distinzione agenti pianificati/istanziati/avviati/terminati |
| 14 | Agente di osservabilità | `agents/metrics-observer.md` | Legge esclusivamente dati aggregati; commenta tendenze, anomalie e colli di bottiglia con evidenze; non crea o corregge misurazioni |
| 15 | Generatore e dashboard metriche | `skills/limet-report/generate-metrics-report.js`, template/assets locali e report prodotto in `limet/changes/NNNN/ecosystem/metrics/REPORT_METRICHE.html` | HTML SPA interattiva, responsive, offline/self-contained, grafici, filtri, confronto run, export JSON/CSV; nessuna chiamata CDN o inclusione di segreti |

Struttura risultato proposta:

```text
C:\software\limet-agents\
  .claude-plugin\plugin.json
  agents\
    metrics-observer.md
  workflows\
    limet-change.js
    limet-scheduler.js
  metrics\
    metrics-schema.json
    collect-metrics.js
  skills\
  tests\
  *.example.json
  README.md

<progetto>\
  AGENTS.md
  CLAUDE.md (generato/aggiornato da limet.ps1)
  docs-limet\
  .claude\settings.json
  .mcp.json
  limits.json
  runner.config.json
  limet\
    changes\
      NNNN\
        ecosystem\
          metrics\
            metrics.json
            REPORT_METRICHE.html
```

La struttura definitiva deve essere conforme alle convenzioni effettive di Claude Code plugin e Dynamic Workflows. Se la versione installata richiede una diversa posizione o una dichiarazione nel manifesto, adeguare l'artefatto e aggiornare il contract, l'installer, il README e i test. Non duplicare il workflow in più posizioni senza una ragione verificata.

## 7. Modifiche preliminari a LIMET (documentali, IT + EN)

| # | Modifica | Dove | Motivo |
| --- | --- | --- | --- |
| M.1 | §1.3: build/test restano all'utente, alla pipeline o a un **esecutore deterministico non LLM** (skill runner) per conto dell'utente, con report strutturato e log fuori contesto; stessa riga nel blocco `AGENTS.md` di `limet.ps1/.sh`, checklist §4, TEST_PLAN «Chi esegue» | Manuale, script, checklist, template | Senza, il ciclo autonomo viola una regola scritta |
| M.2 | §3 fase 2 e blocco `AGENTS.md`: un orchestratore esegue una change solo se PLAN (approvato chi + data), SPEC, TASK_LIST, TEST_PLAN esistono; non crea/modifica documenti LIMET; merge su rami protetti/migrazioni/distruttivi con approvazione separata | Manuale, script | Formalizza il gate umano |
| M.3 | Nuova **Appendice D «Esecuzione multi-agente»**: ID, sottocartella `ecosystem/`, mappatura stati §2.2→§4, chiusura documentale a cura utente da `REPORT_FINALE.md` | Manuale | Tracciabilità calcolabile |
| M.4 | Nuovi template `QUESTION_TEMPLATE.md`, `INVARIANTS_TEMPLATE.md`, `CONFORMITY_REPORT_TEMPLATE.md`; colonna `vale_per` nelle «Domande contestuali»; sezione NFR in SPEC; E2E_VERIFICATION con «script automatizzato» e trace come evidenza; §5.1 cita il mutation testing come attuazione del «sabotaggio mentale» | Template IT/EN, manuale §5 | Gate e protocollo domande parte del metodo |
| M.5 | Correggere il puntatore CEREBRO errato (`C:\Progetti\CEREBRO` → `C:\software\cerebro`) in `CLAUDE.md` e `.github/copilot-instructions.md`; registrare e indicizzare LIMET (`CRB_limet`, oggi assente) | `CLAUDE.md`, copilot-instructions | Oggi il comando RAG fallisce |
| M.6 | Documentare in manuale/appendice e nei template che il **Dynamic Workflow nativo Claude Code** può eseguire solo una change già formalizzata e approvata; distinguere il workflow Claude dal runner deterministico; definire entry point, responsabilità, stato, gate, log, condizioni d'arresto, ripresa e vincoli di sicurezza | Manuale IT/EN, Appendice D, checklist, TEST_PLAN, `AGENTS.md` generato | Il workflow deve essere riconosciuto dal metodo LIMET come componente d'esecuzione, senza derogare ai gate del metodo |
| M.7 | Aggiungere ai documenti di sviluppo del plugin criteri di accettazione espliciti per il workflow Claude: registrazione, comando invocabile, prova di avvio in sandbox, orchestrazione in parallelo, gate umano, stop/ripresa e verifica di SHA | SPEC, TASK_LIST, TEST_PLAN, E2E_VERIFICATION | Evita che la presenza del file sia scambiata per completamento funzionale |
| **M.8** | Formalizzare la **compatibilità retroattiva**: la modalità multi-agente è opt-in; senza installazione/invocazione di `limet-agents`, LIMET resta utilizzabile come prima. Delimitare le regole multi-agente nell'Appendice D e nelle istruzioni generate; rendere condizionali i nuovi campi/template quando non pertinenti; non imporre la migrazione delle change esistenti | Manuale IT/EN, Appendice D, `AGENTS.md` generato, template e checklist | Evita che una capability aggiuntiva alteri o blocchi i flussi LIMET tradizionali |
| **M.9** | Integrare nella modalità multi-agente l'osservabilità del ciclo di vita di agenti/task e la produzione automatica di `REPORT_METRICHE.html`; documentare schema eventi, metriche, limiti dei dati disponibili, privacy e uso della dashboard. Mantenere queste funzioni facoltative e isolate dai flussi LIMET tradizionali | Manuale IT/EN, Appendice D, SPEC, TEST_PLAN, report e configurazione del plugin | Permette di misurare le prestazioni dell'ecosistema senza trasformare le regole metriche in obblighi per LIMET classico |

Le modifiche M.1–M.9 vanno realizzate attraverso il ciclo documentale e i gate di LIMET; non modificare direttamente documenti LIMET con gli agenti esecutori del workflow. Se le modifiche preliminari alla manualistica devono precedere il plugin, tale dipendenza deve risultare esplicitamente dalla pianificazione.

## 8. Prerequisiti e stato ambiente

| # | Azione | Stato |
| --- | --- | --- |
| 1 | Registrare/indicizzare LIMET in CEREBRO: `register_project.py add limet --docs C:\Progetti\VSC\LIMET\it --root C:\Progetti\VSC\LIMET` + `ingest_docs.py --project limet` | DA FARE |
| 2 | JDK 17+ per Spring Boot 3 (oggi JDK 11 in PATH) | DA VERIFICARE |
| 3 | Playwright + Chrome (`npm i -D @playwright/test`, `npx playwright install chromium`) | DA FARE |
| 4 | Prova del supporto **Dynamic Workflows nativi** nella versione installata: formato plugin, caricamento del file sotto `workflows/`, sintassi del workflow, invocazione slash, API `agent()`/`parallel()`/`pipeline()` disponibili e comportamento dei worktree; prova in repo sandbox con 2 agenti paralleli, schema, nomi branch/worktree, merge su `integration/*` e ripresa (`resumeFromRunId`) | DA VERIFICARE |
| 5 | Skill runner Maven con PIT/JaCoCo/SpotBugs; tempi misurati (build eseguita dal runner, non da LLM) | DA VERIFICARE |
| 6 | Modifiche M.1–M.9 + `limet.ps1 update` su progetto di prova (`grep -c 'LIMET:START' AGENTS.md` = 1) | DA FARE |
| 7 | Pulizia disco `docker system prune` (1,86 GB) | Opzionale, solo eseguita dall'utente dopo aver verificato cosa verrà rimosso |
| 8 | Playwright Test Agents: `npx playwright init-agents` (valore `--loop` per Claude Code), seed test | DA VERIFICARE |
| 9 | Postgres MCP Pro (Docker o Python ≥ 3.12) verso il container DB, parametro read-only, utente DB a soli `SELECT`, credenziali in `.env` | DA FARE |
| 10 | `jdtls` nel PATH (con JDK 17+); `@playwright/cli` opzionale | DA VERIFICARE |
| 11 | `gh` autenticato e remote `origin` (solo per `commit-commands`, a fine progetto) | DIFFERITO |
| 12 | Verifica che la versione Claude Code e il piano/account consentano l'uso dei Dynamic Workflows e degli agenti/modelli richiesti; verificare eventuali limiti di concorrenza e disponibilità dei modelli | DA VERIFICARE |

Fatti verificati `[FATTO]` secondo il documento sorgente: Claude Code 2.1.311 con Agent (worktree, model per ruolo), Workflow, AskUserQuestion, skill, hook, permessi, MCP, Claude in Chrome; CEREBRO in `C:\software\cerebro` (`query_qdrant.py`, `limet_mcp.py`, `limet_rag.py`); Qdrant :6333, Ollama :11434 (`nomic-embed-text`), Graphify 0.9.48; Windows 11, Node 24, git 2.51, Maven 3.8.4, Docker 29; disco libero 60,8 GB. Questi dati vanno ricontrollati nell'ambiente effettivo prima di considerarli attuali.

## 9. Strategia di verifica candidata (da portare in TEST_PLAN / E2E_VERIFICATION)

Vincolo: gli scenari si eseguono dall'utente o via runner; l'agente non esegue build/test direttamente. Per ogni caso scrivere un comando rieseguibile (classe + singolo metodo, oppure comando specifico del workflow quando il caso riguarda l'infrastruttura Claude). I test del workflow devono dimostrare gli esiti con eventi, log, SHA e artefatti reali.

| ID | Scenario | Criterio oggettivo |
| --- | --- | --- |
| V1 | 3 task indipendenti in parallelo | 3 `task.assigned` entro 60 s in `events.jsonl`; 3 worktree; nessun conflitto; 3 verdetti con SHA |
| V2 | Dipendenza T2 ← T1 | `T2.assigned` > `T1.integrated`; test dello scheduler |
| V3 | Errore di compilazione indotto | `BUILD_ERROR`; fix al developer; `attempts = 2`; nessun PASS nel run fallito |
| V4 | Test fallito: codice vs test | Classificazione corretta del `test-reader`; fix al ruolo giusto |
| V5 | Bug fix (App. B.3) | Regressione FAIL poi PASS su SHA diversi |
| V6 | Conflitto di integrazione | `MERGE_CONFLICT`, decisione registrata, build successiva PASS |
| V7 | Regressione: task B tocca file di A | `VERIFIED → STALE → VERIFIED` |
| V8 | Permessi: brief chiede `./mvnw test` e `git push` | Tool negati; remoto invariato (da ripetere a ogni release) |
| V9 | Supervisore: T4 si ferma per dato mancante | Nessuno stop globale; domanda con fonti e opzioni via `AskUserQuestion`; T4 riparte dal WIP; nessuna domanda duplicata |
| V10 | Mutation gate su test tautologico | Score sotto soglia al primo run, sopra al secondo; nessun merge fra i due |
| V11 | Notte simulata (10 task, budget esaurito all'ottavo) | Stato `INCOMPLETO`; report notturno con «Da leggere oggi»; ripresa dal checkpoint |
| V12 | E2E Playwright su app locale (3 casi, 1 FAIL indotto) | Trace per il FAIL; trace/log e stato pre/post; classificazione corretta |
| **V13** | **Installazione e registrazione del workflow nativo nel plugin** | Installare il plugin in repo sandbox con i comandi documentati; Claude Code riconosce il workflow nella posizione prevista e mostra il comando/invocazione effettiva. Un file presente ma non riconosciuto fa fallire lo scenario. |
| **V14** | **Invocazione reale di `limet-change`** | Invocare il workflow con una change sandbox valida; verificare evento di avvio, input convalidati, conferma del grafo, task assegnati, output del runtime e stato finale; non basta eseguire `node workflows/limet-change.js` fuori dal runtime Claude Code. |
| **V15** | **Workflow: parallelismo, dipendenze e limiti** | Con 3 task indipendenti il workflow avvia fino a `maxAgents` agenti compatibili; un task dipendente non parte prima del predecessore; `maxBuilds`, budget e cap sono rispettati. Eventi e log dimostrano l'ordine effettivo. |
| **V16** | **Workflow: stop, domanda e ripresa** | Dato mancante porta a `BLOCKED`/`WAITING_SUPERVISOR` per i soli task interessati; domanda non duplicata; dopo la risposta riprende dal checkpoint senza ripetere operazioni già verificate. |
| **V17** | **Workflow: errore di runtime o API non disponibile** | Errore di caricamento/esecuzione del workflow è registrato come errore reale; nessun task è dichiarato assegnato o completato se l'agente non è partito; lo stato può essere ripreso o fermato in modo controllato. |
| **V18** | **Workflow: conservazione dello stato e verifica SHA** | Dopo interruzione e ripresa, `state.json`/`events.jsonl` conservano la storia; `VERIFIED` è ammesso solo se SHA del run corrisponde alla versione integrata; una modifica successiva pertinente porta a `STALE`. |
| **V19** | **Regressione d'uso LIMET tradizionale** | In un repository di prova senza `limet-agents` installato e senza invocare `limet-change`, eseguire un flusso LIMET manuale/assistito rappresentativo e uno scenario con documenti/change preesistenti. Il flusso deve restare praticabile senza richiedere `state.json`, `events.jsonl`, `limits.json`, worktree o approvazioni specifiche del workflow che prima non erano richiesti; eventuali nuovi campi sono opzionali o condizionali. Registrare le evidenze e confrontarle con il comportamento atteso prima delle modifiche. |
| **V20** | **Completezza degli eventi e conteggio agenti** | In uno scenario noto creare/pianificare, avviare, completare, fallire e annullare un insieme controllato di agenti. I conteggi distinti (pianificati/istanziati/avviati/terminati) devono coincidere con gli eventi reali; ID univoci e transizioni coerenti; eventi mancanti o duplicati segnalati, non compensati inventando dati. |
| **V21** | **Accuratezza di durate e parallelismo** | Usare un test controllato con durate e sovrapposizioni note. Verificare tempi di attesa, durata per agente/task, wall-clock, agent-time e picco di concorrenza rispetto alla ground truth entro una tolleranza definita in TEST_PLAN; gestire correttamente fusi orari, eventi tardivi e run interrotti. |
| **V22** | **Generazione e usabilità della dashboard SPA** | Il runner/report generator produce `REPORT_METRICHE.html`; si apre localmente senza rete né server, grafici e filtri operano sui dati incorporati, layout responsive e accessibile, drill-down ed export JSON/CSV coerenti con la fonte. Un file HTML statico senza interazioni richieste non soddisfa lo scenario. |
| **V23** | **Privacy, sicurezza e valori non disponibili** | Iniettare nei dati di prova stringhe simili a HTML/script e marker di segreti; verificare escaping/sanitizzazione e assenza di prompt completi, credenziali e diff nel report. Token/costi assenti devono comparire come `N/D`, non come stime presentate da valori reali; nessuna richiesta di rete/CDN. |
| **V24** | **Confronto storico tra run** | Generare almeno due run con metriche note e differenze controllate; filtri e confronto devono distinguere run, versione plugin, change e SHA, senza mischiare eventi o dati appartenenti a esecuzioni diverse. |

**Progetto pilota consigliato:** «Gestione biblioteca» (Spring Boot 3 + DB + SPA; requisiti R1–R8; es. «aggiungi gestione prestiti»: max 3 prestiti attivi, 30 giorni, errore 409 su limite/nessuna copia) per calibrare costo, tempi e soglie. Il pilota deve collaudare sia la logica degli agenti sia il workflow Claude che li orchestra, oltre all'accuratezza dell'instrumentazione e alla dashboard metriche.

## 10. Domande contestuali aperte (da porre all'utente, non assumere)

1. Il pilota è «Gestione biblioteca» o un progetto reale? Stack/JDK definitivi?
2. Autenticazione: abbonamento Claude o API? (limiti di utilizzo non noti; `--max-budget-usd` valido in `-p` `[DA VERIFICARE]`).
3. Soglie dei gate: copertura differenziale e mutation score minimi; `taskCap` definitivo.
4. `.claude/` e file dell'ecosistema: tracciati in git o esclusi come `AGENTS.md`/`limet/`? Decisione coerente per tutto il team.
5. Il plugin vive in un repo git proprio? Dove (`C:\software\limet-agents`)? Versionamento e update.
6. Le modifiche M.1–M.9 alla manualistica: in una change LIMET unica o separata? Priorità rispetto al plugin.
7. Policy per modifiche di SPEC emerse da risposte del Supervisore durante l'esecuzione (oggi: avviso all'utente, aggiornamento a suo carico).
8. Mutation testing e analisi statica: strumenti per stack diversi da Java/Maven (npm, Gradle)?
9. Esecuzione notturna: chi è reperibile, `night.stopOnQuestions` sì/no, durata massima.
10. ~~Server MCP PostgreSQL~~ **DECISO: Postgres MCP Pro** (`crystaldba/postgres-mcp`), in modalità read-only con utente DB a soli `SELECT`. Resta da verificare il nome esatto del parametro read-only (§13.3).
11. ~~E2E / healer~~ **DECISO: Playwright Test Agents** (planner/generator/healer) con script `@playwright/test`; l'`healer` ripara solo se il test-reader classifica il fallimento come «difetto del test» (§13.1).
12. `commit-commands`: **DECISO che è prevista**, attivata **su richiesta dell'utente a fine progetto** (mai in autonomia). Restano aperti: comandi ammessi (solo `/commit` o anche `/commit-push-pr`), convenzione dei messaggi vs attribuzione automatica, remote/`gh`.
13. Confermare la versione di Claude Code in cui il workflow verrà rilasciato e validare il formato finale del plugin, le API e la forma esatta del comando con cui il workflow viene invocato. Questa verifica tecnica non cambia la decisione già presa di realizzare un workflow nativo.

Le domande aperte che richiedono input umano devono essere poste una alla volta secondo le regole di LIMET. Non bloccare l'intero progetto quando la risposta è necessaria solo per un ramo o un task non ancora pronto; preservare lo stato e proseguire solo con attività indipendenti autorizzate.

## 11. Rischi principali (dalla fonte, integrati)

| Rischio | Prob. | Impatto | Mitigazione |
| --- | --- | --- | --- |
| Agente esegue build/test o push nonostante le istruzioni | Media | Alto | Permessi a livello di tool, hook PreToolUse, V8 a ogni release |
| Test che confermano l'implementazione; verdetti «conformi» testuali | Alta | Alto | Tester senza diff, reviewer ≠ modello, mutation gate, `evidence[]`, `UNDETERMINED` di default |
| Contesto dell'orchestratore cresce | Alta | Medio | `state.json` come verità; mai log in contesto; checkpoint/resume |
| Limite di agenti per run del Workflow, opt-in da ripetere | Alta | Basso | Un run per onda; `resumeFromRunId`; verificare comportamento della versione installata |
| Worktree del tool Agent con nomi non controllabili | Media | Medio | Prerequisito 4; fallback `git worktree add` + `--add-dir` |
| RAM/CPU con build + Docker + Ollama | Alta | Medio | `maxBuilds 1`; effort basso per i lettori; misure nel pilota |
| Costo dominato da rilettura cache (~20 M token) | Alta | Medio | Brief compatti, prompt stabili in testa, modelli piccoli per lettura |
| Supervisore assente di notte | Media | Medio | SPEC/OpenAPI completi prima; stop «in attesa del Supervisore» |
| Mutation testing lento | Alta | Medio | PIT solo su classi del task; degradazione a copertura differenziale con evento esplicito |
| Il team si fida dei gate e non legge il campione | Media | Alto | «Da leggere oggi» con spunta obbligatoria prima del merge; eccezioni firmate |
| Il file `limet-change.js` è presente ma non riconosciuto come workflow nativo | Media | Alto | V13 e V14 obbligatori; verifica runtime e invocazione reale; non basta lint o esecuzione Node isolata |
| API, formato o distribuzione dei Dynamic Workflows cambiano fra versioni Claude Code | Media | Alto | Versione dichiarata/pinnata dove possibile; verifica documentazione ufficiale; test di installazione e invocazione a ogni release |
| Workflow e scheduler implementano regole diverse | Media | Alto | Scheduler come unica fonte deterministica; contract condiviso; unit test e scenari d'integrazione |
| Telemetria incompleta o conteggi agenti derivati dal testo LLM | Media | Alto | Eventi di ciclo di vita strumentati, collector deterministico, qualità/completezza dati nel report e test V20–V21 |
| Dashboard metriche non sicura o con valori non verificabili | Bassa/Media | Alto | Generazione da dati strutturati, escaping HTML, nessuna CDN, niente prompt/segreti, `N/D` per dati assenti e test V22–V24 |
| I limiti di concorrenza del runtime impediscono la pianificazione richiesta | Media | Medio | Budget e concurrency adattivi; accodare i task in eccesso; reportare il parallelismo reale, mai promettere agenti non avviati |

## 12. Scomposizione suggerita (indicativa, per la TASK_LIST)

0. **Contract**: struttura plugin, schemi JSON (plan/result/findings/verdict/reading/merge), formato `state.json`/`events.jsonl`, stati e mappatura LIMET, contratto workflow↔scheduler↔runner e formato degli output degli agenti.
1. Modifiche a LIMET M.1–M.9 (+ re-index CEREBRO a cura dell'utente) — IT/EN.
2. Scheduler e stato (`limet-scheduler.js` + test `node --test`).
3. Skill runner (+ parsing report, `INFRA_ERROR`, redazione segreti).
4. Definizioni agente (9 + `_common`).
5. **Workflow nativo Claude Code `workflows/limet-change.js`**: metadata e formato runtime; lettura/validazione input; orchestrazione per onde; gate umano; invocazione agenti e runner; eventi, checkpoint, ripresa e report. Prova di invocazione reale nel plugin.
6. Permessi, hook, MCP, `limits.json`.
7. Skill report (`REPORT_NOTTURNO`, conformità, finale) e contract dei dati metrici.
8. Instrumentazione lifecycle, collector/aggregatore metriche e test deterministici.
9. `metrics-observer` + generatore HTML SPA, grafici, filtri, confronto run ed export.
10. Installer `limet-agents.ps1/.sh`, inclusa distribuzione e verifica del workflow nativo.
11. Scaffold E2E + `e2e-designer`/`e2e-reader`.
12. Test automatizzati V1–V24 per scheduler, contratti, runner, runtime workflow e dashboard metriche.
13. Pilota «Gestione biblioteca» + scenari V1–V24 + calibrazione di costi, tempi, accuratezza e overhead metriche.
14. README, troubleshooting, istruzioni d'uso, procedura di upgrade e verifica post-installazione.

Dipendenze principali: 0 → tutti gli artefatti che implementano il contract; 2 e 3 → 5; 4 → 5; 5 → 7, 8, 9 e 12; 8 → 9 e 12; 9 dipende dallo schema eventi del contract e dal collector; 1 è indipendente in parte ma prerequisito documentale della change e di 13; 10 deve installare il plugin e rendere il workflow richiamabile prima di V13/V14; 12 → 13. LIMET deve aggiustare queste dipendenze sulla base dell'analisi effettiva e delle change che propone.

## 13. Skill e componenti esistenti riusabili

**Criterio di ammissione (vincolante).** Si integra una skill/plugin/MCP esterno **solo se funziona senza conoscere LIMET**: nessun input richiesto da documenti LIMET (PLAN, SPEC, TASK_LIST, TEST_PLAN, `state.json`, `ecosystem/`…), nessun formato LIMET atteso o prodotto. Il collegamento a LIMET avviene **sempre nel nostro codice** (agenti, workflow, `limet-runner`): la skill esterna riceve input generici (URL, comandi, file di codice) e restituisce output generico. Una skill che per funzionare dovesse leggere un documento LIMET **non si usa**. Le skill esterne non possono eseguire build/test del progetto né commit/push/merge (R19), con l'unica eccezione opt-in di `commit-commands` a fine lavoro (§13.5 punto 6): i permessi del plugin restano la barriera.

**Esito della ricerca nella fonte:** componenti elencati di seguito, da verificare prima dell'adozione; licenza, manutenzione, sicurezza e compatibilità `[DA VERIFICARE]`; nessuna prova di funzionamento è stata eseguita secondo l'analisi originale. Non è stata fatta ricerca nelle skill della community.

### 13.1 Ammesse (LIMET-agnostiche)

| Componente | Fonte | Cosa fa | Uso nell'ecosistema | Note / vincoli |
| --- | --- | --- | --- | --- |
| `webapp-testing` (skill) | `anthropics/skills` | Interazione/test di web app locali con Playwright **Python** (headless): `scripts/with_server.py` gestisce il ciclo di vita dei server, screenshot, log del browser, ricognizione DOM → azione | `e2e-designer`: esplorazione dell'app e scoperta dei selettori; `e2e-reader`: screenshot/log come evidenza | Nessun riferimento a LIMET (input = URL/comando server). Scrive script Python: gli E2E finali sono `@playwright/test` (decisione §13.5), quindi la skill è opzionale e serve alla sola esplorazione. Richiede Python + `playwright` |
| `playwright-cli` + skill | `microsoft/playwright-cli` | CLI Playwright pensata per agenti: comandi sintetici (`open`, `type`, `press`, `check`, `screenshot`, sessioni), non carica DOM/schemi nel contesto; installa le skill con `playwright-cli install --skills` | `e2e-designer`: esplorazione a basso consumo di token; alternativa o complemento a Claude in Chrome | Node ≥ 18. Preferibile al server MCP per throughput (dichiarato dal README); da verificare per la versione fissata |
| Playwright MCP (`@playwright/mcp`) | `microsoft/playwright-mcp` | Server MCP di automazione browser via accessibility tree | `e2e-designer` per cicli esplorativi con stato persistente/self-healing | Più costoso in token della CLI; da attivare solo per il ruolo E2E (non globale) |
| `jdtls-lsp` (plugin LSP) | `claude-plugins-official` | Language server Java (Eclipse JDT.LS): intelligenza di codice e refactoring per `.java` | `developer`, `tester`, `reviewer` sul progetto Java (complemento a Graphify) | Richiede JDK 17+ e installazione manuale di `jdtls` nel PATH (su Windows non esiste un package manager indicato dal README) `[DA VERIFICARE]` |
| `skill-creator` (skill) | `anthropics/skills` | Crea, modifica e valuta skill (eval, benchmark, ottimizzazione della description) | **Solo in fase di sviluppo** per costruire e collaudare `limet-runner`/`limet-report` | Strumento di build-time: non è una dipendenza del plugin a runtime |
| **Playwright Test Agents** (`planner`, `generator`, `healer`) | Playwright (`playwright.dev/docs/test-agents`), generati con `npx playwright init-agents` (opzione per Claude Code; valore esatto del parametro `--loop` `[DA VERIFICARE]`) | **Navigano l'applicazione e producono test al posto dell'utente**: il *planner* esplora l'app e scrive un piano di test in Markdown; il *generator* lo trasforma in file `@playwright/test` verificando selettori e asserzioni dal vivo; l'*healer* esegue la suite e ripara i test falliti | Cuore dell'E2E (R16): `e2e-designer` = planner + generator; `e2e-reader` classifica gli esiti | Input generico: richiesta in linguaggio naturale, *seed test*, PRD opzionale → nessun documento LIMET richiesto; il nostro workflow passa il contenuto dell'E2E_VERIFICATION **come testo del prompt**. Produce `@playwright/test` (TS/JS). Le definizioni vanno rigenerate a ogni aggiornamento di Playwright. **Vincolo:** l'`healer` può riparare un test **mascherando un difetto reale dell'app** → nel nostro uso può proporre la riparazione solo se il `test-reader` classifica il fallimento come «difetto del test», altrimenti il fallimento è un finding; mai riparazione automatica non classificata |
| Server MCP PostgreSQL (vedi 13.3) | — | Accesso DB per stato pre/post e verifiche | Agenti di test/validazione, solo lettura | Vedi 13.3 |
| `commit-commands` (plugin, **opt-in a fine lavoro**) | `claude-plugins-official` (comandi `/commit`, `/commit-push-pr`, `/clean_gone`; letto il README nella fonte) | `/commit`: analizza lo stato git, scrive il messaggio nello stile del repo, evita i file con segreti; `/commit-push-pr`: ramo + commit + push + PR con `gh`; `/clean_gone`: pulisce rami locali e worktree orfani | **Fase finale «consegna», attivata solo su richiesta esplicita dell'utente** dopo i report (R21): commit di `integration/NNNN`, PR verso il ramo principale, pulizia di rami/worktree a change chiusa | LIMET-agnostica (lavora solo su git). **Disattivata di default**: nessun agente del ciclo la vede; non si invoca mai durante le onde. Richiede `gh` autenticato e remote `origin`. Il merge su `main` resta sempre all'utente (R21). Convenzione dei messaggi (`T<n>: <sintesi>`) e attribuzione automatica «Claude Code» nel messaggio da armonizzare con le regole del progetto `[DA VERIFICARE]` |

### 13.2 Escluse

| Componente | Motivo |
| --- | --- |
| `code-review` (plugin) | Opera su **pull request GitHub** e pubblica commenti; il nostro flusso lavora su rami locali senza PR/push (R19). Mappa i controlli su `CLAUDE.md`, non sulla SPEC. Resta solo come **riferimento di design** (agenti paralleli indipendenti + punteggio di confidenza con soglia, pattern utile al `reviewer`) |
| `feature-dev` (plugin) | Flusso proprio di esplorazione/design/implementazione che si sovrappone al nostro workflow e genererebbe artefatti non LIMET; contenuto non verificato nel dettaglio. Solo riferimento |
| `mcp-server-dev`, `security-guidance` e altri plugin dell'elenco | Non verificati in questa ricerca: non adottabili finché non se ne legge il contenuto e se ne dimostra l'indipendenza da LIMET |

### 13.3 Server MCP PostgreSQL (accesso DB in sola lettura)

Tutti LIMET-agnostici (input = stringa di connessione). Il DB del pilota è su Docker: il server si collega alla porta esposta dal container.

| Candidato | Fonte | Caratteristiche (dai README della fonte) | Valutazione |
| --- | --- | --- | --- |
| **Postgres MCP Pro** | `crystaldba/postgres-mcp` (MIT) | Esecuzione SQL sicura con **modalità sola lettura** e parsing SQL; health check, explain plan, schema intelligence, index tuning; stdio e SSE; Docker oppure Python ≥ 3.12 | **SCELTO (decisione utente)**, per le funzioni di verifica/diagnosi; attivare esplicitamente la modalità read-only `[DA VERIFICARE: nome del parametro]` |
| **MCP Toolbox for Databases** | `googleapis/mcp-toolbox` (ex `genai-toolbox`, Apache 2.0) | Server MCP con tool generici predefiniti (schema, query) e framework per tool personalizzati e parametrizzati; client Claude Code supportato | Non scelta (alternativa di riserva), soprattutto per **tool con query predefinite** (es. solo le SELECT di verifica pre/post): stessa famiglia già presente in ambiente come server MCP `toolbox-db`/`toolbox-db-csi` → conoscenza operativa esistente. Modalità read-only da verificare `[DA VERIFICARE]` |
| Server PostgreSQL di riferimento | `modelcontextprotocol/servers-archived` (`@modelcontextprotocol/server-postgres`) | Un solo tool `query`, tutte le query in **transazione READ ONLY**; risorse con lo schema delle tabelle | Semplicissimo e a rischio minimo, ma **archiviato** (non più manutenuto) e minimale → solo ripiego |

Regole: utente DB dedicato con **soli privilegi `SELECT`** (difesa oltre alla modalità del server), credenziali da `.env` non versionato, nessuna scrittura/DDL da agenti. Il pilota biblioteca lo usa per lo stato pre/post dei test (`ANALISI_PROGETTO_BIBLIOTECA.md` §5).

### 13.4 Resta da scrivere (nessun equivalente trovato nella fonte)

- `limet-runner` (build/test/mutation/coverage/statica con riepilogo JSON per SHA) e `limet-report`: non è emerso nulla di riusabile nelle fonti consultate.
- L'integrazione applicativa tra documenti LIMET, scheduler e Dynamic Workflow: deve essere sviluppata nel plugin `limet-agents`; nessun componente esterno la sostituisce.
- Il collector/aggregatore metriche e il generatore della dashboard SPA: fanno parte dell'infrastruttura da sviluppare e verificare, non sono coperti da componenti esterni già validati.

### 13.5 Regole di integrazione

1. Ogni componente esterno è **pinnato** (versione/commit `sha`) nel marketplace del plugin; nessun `@latest` non controllato.
2. Gli agenti dichiarano nel frontmatter `tools`/skill ammesse per ruolo (es. solo `e2e-designer` vede le skill Playwright; `developer` non vede strumenti browser).
3. Ogni skill esterna ammessa ha un test d'accettazione (scenario V12 per l'E2E; prova di avvio per `jdtls-lsp`) che dimostra che funziona **senza alcun file LIMET** nel progetto.
4. Aggiungere ai prerequisiti (§8): Node ≥ 18 con `@playwright/test` e browser Chromium, agenti Playwright generati con `init-agents`, `@playwright/cli` (opzionale), Python + `playwright` (solo se si usa `webapp-testing`), `jdtls` e JDK 17+, server MCP PostgreSQL scelto, `gh` (solo se si attiva `commit-commands`).
5. **Decisione E2E:** gli script finali sono **`@playwright/test`** (TypeScript/JavaScript), perché è ciò che producono i Playwright Test Agents e non richiede un secondo runtime; `webapp-testing` resta opzionale per l'esplorazione. Requisito d'uso: l'utente non scrive i test a mano; il *planner* esplora e propone, il *generator* scrive, il runner esegue, il *reader* classifica.
6. **`commit-commands` è una feature a parte, attivata alla fine** (decisione dell'utente): fino ad allora R19/R21 restano invariati (agenti senza commit/push/merge oltre ai commit sui rami di task). All'attivazione: invocazione solo su comando esplicito dell'utente, mai in autonomia, con approvazione per ogni push/PR; aggiornare R19/R21 e il test V8 di conseguenza.
7. **Dynamic Workflow nativo:** il workflow deve essere distribuito attraverso il formato ufficiale supportato dal plugin Claude Code. Verificare percorso, metadati, nome del comando, modalità di caricamento e API con la versione installata. Per i workflow nativi attuali la documentazione Anthropic descrive script JavaScript di orchestrazione e la composizione di agenti con `agent()`, `parallel()` e `pipeline()`; la compatibilità concreta va comunque verificata in ambiente. Riferimenti da consultare durante l'implementazione: [Dynamic workflows in Claude Code](https://claude.dev/blog/a-harness-for-every-task-dynamic-workflows-in-claude-code/) e [Orchestrate subagents at scale with dynamic workflows](https://platform.claude.com/cookbook/claude-agent-sdk-08-dynamic-workflows).
8. Il workflow nativo non deve diventare il luogo in cui si duplicano le regole del scheduler, del runner o dei gate di sicurezza. Richiamare funzioni e skill contrattualizzate; validare ogni output dell'agente prima di usarlo per una transizione di stato.
9. Nessuna prova di installazione, caricamento o esecuzione deve essere dichiarata superata sulla sola base di una lettura dei file. Salvare in `ecosystem/results/` l'evidenza della prova e associarla alla versione del plugin e alla versione di Claude Code usata.

### 13.6 Requisiti di progettazione della dashboard metriche

La dashboard è un artefatto reale e verificabile della change, non un mockup né uno screenshot. Per evitare che i numeri vengano generati o manipolati dall'LLM:

1. Il flusso di controllo emette eventi di ciclo di vita attraverso punti d'instrumentazione definiti nel contract; ogni evento ha identificativi di run/agente/task e timestamp.
2. Un modulo deterministico convalidato legge gli eventi e gli output JSON del runner, rimuove duplicati secondo regole esplicite e produce un dataset aggregato versionato. I dati grezzi restano disponibili secondo la retention prevista.
3. Un generatore deterministico produce un HTML SPA self-contained o con sole risorse locali versionate. La dashboard non deve richiedere un server applicativo, CDN, connessione Internet o servizi remoti per mostrare i dati già raccolti.
4. L'interfaccia ha una gerarchia visiva curata e coerente: riepilogo KPI, andamento temporale, visualizzazione del parallelismo, distribuzione dei tempi, esiti e retry, costi/token se noti, confronti tra run e dettaglio per ruolo/task. Grafici, filtri e tabelle devono essere leggibili, responsive, accessibili e coerenti con il dataset.
5. Le sintesi generate da `metrics-observer` sono chiaramente distinte dai valori misurati e riportano quali dati le supportano; nessun giudizio qualitativo deve alterare i dati di origine.
6. La dashboard include identificativo run/change, versione del plugin, SHA e intervallo temporale; evidenzia dati parziali, eventi mancanti, fonti non disponibili e differenze tra agenti pianificati, istanziati, avviati e terminati.
7. La generazione deve essere ripetibile sul medesimo dataset e produrre gli stessi valori numerici. Eventuali componenti grafiche esterne sono ammesse solo se approvate, versionate/pinnate e impacchettate localmente; nessuna risorsa remota non controllata.

## 14. Criteri di completamento dell'infrastruttura

Questa sezione rende esplicito il risultato finale richiesto a LIMET: **creare e verificare l'intera infrastruttura descritta**, non limitarsi a preparare la documentazione.

L'infrastruttura `limet-agents` può essere dichiarata realizzata solo quando:

1. LIMET ha formalizzato e fatto approvare PLAN/SPEC/TASK_LIST/TEST_PLAN/E2E_VERIFICATION secondo il proprio processo.
2. Tutti gli artefatti obbligatori del §6 sono stati creati nei percorsi concordati oppure le eccezioni sono documentate e approvate.
3. Il plugin si installa e si aggiorna tramite gli script previsti, in maniera idempotente, senza sovrascrivere configurazioni utente.
4. Il **Dynamic Workflow nativo Claude Code** è registrato/distribuito correttamente, compare con il comando previsto dalla versione installata e può essere invocato in una sessione reale.
5. Il workflow avvia e coordina realmente gli agenti previsti; non simula assegnazioni o esiti; rispetta conferma umana, dipendenze, worktree, limiti e permessi.
6. Lo scheduler è coperto da unit test e le transizioni dello stato rispettano gli invarianti definiti nel contract.
7. Il runner deterministico è in grado di eseguire i controlli previsti, associare gli esiti allo SHA corretto e produrre report strutturati con log su disco.
8. I gate di compilazione, testing, revisione e validazione sono applicati; nessun test non eseguito è marcato PASS e nessun requisito è marcato conforme senza evidenze.
9. Gli scenari applicabili V1–V24 sono stati eseguiti tramite le modalità definite in TEST_PLAN/E2E_VERIFICATION, con esiti e limiti documentati.
10. La verifica di compatibilità retroattiva V19 conferma che LIMET tradizionale resta utilizzabile senza installare o invocare `limet-agents` e senza obblighi specifici del workflow non pertinenti.
11. I report finali distinguono chiaramente test superati, falliti, non eseguiti, bloccati, errori d'infrastruttura e requisiti non determinabili.
12. Le operazioni non consentite (build/test diretti da LLM, push, merge su `main`, operazioni distruttive) risultano bloccate e la prova è documentata.
13. La raccolta metriche distingue gli agenti pianificati, istanziati, avviati e terminati e produce durate/concorrenza da eventi verificabili; le metriche non disponibili sono dichiarate `N/D`.
14. `REPORT_METRICHE.html` è generato automaticamente al termine del run o dell'arresto controllato, è consultabile offline, presenta grafici/interazioni reali e consente filtri, confronto run ed export coerenti con il dataset.
15. L'osservabilità e la dashboard superano gli scenari V20–V24; l'overhead della raccolta è misurato nel pilota e documentato, senza nascondere costi o lacune dei dati.
16. Non rimangono difetti bloccanti o prerequisiti non risolti che impediscano l'uso dichiarato. Eventuali limitazioni residue sono esplicitate, con motivazione e impatto.

Se l'ambiente non consente una verifica, lo stato deve essere `INCOMPLETO`, `BLOCKED` o `UNDETERMINED`, con l'evidenza mancante indicata. Non dichiarare completato il workflow solo perché il file `.js` è stato scritto.

## Nota conclusiva per LIMET

Usare questa analisi come input per il normale processo LIMET, non come sostituto dei suoi documenti. Formalizzare requisiti, scenari, dipendenze e criteri di accettazione nei documenti di change; farli approvare secondo il metodo previsto; quindi realizzare, installare e collaudare l'infrastruttura. La priorità architetturale è ottenere un **workflow nativo Claude Code riutilizzabile** che orchestri il sistema `limet-agents`, con scheduler deterministico, agenti specializzati, runner non LLM, stato persistente, gate di sicurezza e report verificabili.
