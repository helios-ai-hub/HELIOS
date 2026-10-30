# Verifica e2e manuale — HELIOS (V1–V24 su catena completa + scenari HELIOS; PoC = sottoinsieme)

> Etichette: **[F]** fatto · **[P]** proposta · **[N]** non verificato. Fonte scenari: ALA §9 (V1–V24); criteri: R05 §5; IN §5 H-407. Nel PoC (Fase 4) si usa il sottoinsieme V8, V13–V19 + E2E-PoC-1…6. Gli altri scenari (V1–V7, V9–V12, V20–V24) sono **inclusi sotto** («Scenari di Fasi 7–9») per H-901 e si eseguono solo dopo il GO del gate H-406; tutti i 24 scenari ALA devono passare su ciascuno dei 3 OS (IN §6, Fase 9). **E2E-CFG-1** non appartiene ad ALA: è uno scenario **proprio di HELIOS** introdotto su richiesta del SUPERVISORE (2026-10-30) per la configurazione locale, e si esegue in Fase 8b con H-8b09. **E2E-AUT-1** ed **E2E-AUT-2** sono anch'essi scenari **propri di HELIOS** (richiesta SUPERVISORE 2026-10-30, ADR 0003 «modalità non presidiata»): si eseguono **solo dopo il GO** (Fase 7+) e **completano** V11 «Notte simulata» di ALA, che resta valido e non è sostituito — V11 verifica l'esaurimento del budget, E2E-AUT-1 la domanda bloccante e il parcheggio, E2E-AUT-2 tutte le cause di fine corsa. **[N]** Questo documento è una pianificazione: IN §9 attribuisce a LIMET la stesura definitiva «alla chiusura della fase 9»; qui è anticipata su richiesta del SUPERVISORE e va completata (passi con comandi reali) quando i componenti esistono.
> Gli scenari li esegue il SUPERVISORE; l'agente non esegue build/test.

## Riferimenti

- **Piano/task collegato**: `PLAN_helios.md`, `TASK_LIST.md` (H-401…H-407), `TEST_PLAN.md`
- **Ambiente di verifica**: locale, sandbox separata dal repo HELIOS **[N]** (percorso da decidere); Claude Code 2.1.311; Windows (+ CI per Unix)

## Precondizioni

- Task H-101…H-103, H-301…H-307, H-401…H-405 e H-407 `done`; PLAN approvato.
- Repo sandbox vuoto con una change valida; il plugin NON installato nelle configurazioni in uso (no `~/.claude` reale: HOME/profilo isolato per V13/V14 (Q-13, decisione SUPERVISORE; modalità esatta da definire in H-407).
- Qdrant :6333 e Ollama :11434 attivi; collection esistenti da non modificare.
- **[N]** Autenticazione Claude Code e tetto costo (Q-7).

## Stato pre-verifica (controllo PRIMA dei passi)

- Hash/elenco di `~/.claude` (o di HOME isolato):
  ```powershell
  Get-ChildItem -Recurse "$HOME\.claude" | Get-FileHash | Export-Csv pre_claude.csv   # snapshot
  ```
- Remoto git del repo sandbox: `git ls-remote origin` (annotare gli hash).
- Conteggio punti collection: `python "C:\software\cerebro\scripts\query_qdrant.py" --project <slug> count` per ciascuna collection esistente.

## Scenari

### V13 — Installazione e registrazione del workflow nativo (obbligatorio, ALA V13; H-407)
1. Nel repo sandbox eseguire i comandi di installazione documentati: `claude plugin marketplace add <percorso agents/plugin>` e `claude plugin install <nome plugin>` (**[N]** nome e forma esatta confermati da H-407).
   - **Esito atteso**: installazione senza errori.
2. Aprire una sessione Claude Code nel repo sandbox ed elencare i workflow disponibili.
   - **Esito atteso**: `limet-change` è riconosciuto nella posizione prevista (`workflows/`) e compare come invocabile. Un file presente ma non riconosciuto = **KO**.
3. Spostare/rinominare la cartella sorgente del plugin e riaprire una sessione.
   - **Esito atteso**: annotare se Claude Code usa una cache propria (verifica richiesta da H-407); runner, policy, `cerebro/`, `limet/` restano necessari fuori dal plugin.
4. **(solo dopo il GO, quando esiste `docs/USER_GUIDE.md` — H-505)** Ripetere i passi 1 e 2 seguendo **alla lettera** la guida d'uso, senza conoscenza pregressa e senza comandi presi altrove.
   - **Esito atteso**: ogni comando copiato dalla guida viene **eseguito davvero** e termina senza errori; nessun passo risulta mancante, ambiguo o nell'ordine sbagliato. Un comando della guida che non funziona o un passo che richiede di indovinare = **KO** su H-505 (non su H-407).
   - **Nota**: è la verifica prevista per H-505, che non ha un `T-xxx` proprio perché la correttezza di una guida si dimostra solo eseguendola.

### V14 — Invocazione reale di `limet-change` (obbligatorio, ALA V14)
1. In sessione Claude Code invocare il workflow con la change sandbox valida.
   - **Esito atteso**: evento di avvio, input convalidati, richiesta di conferma del grafo (gate), task assegnati, output del runtime, stato finale. Non basta `node workflows/limet-change.js` fuori dal runtime Claude Code.
2. Verificare `state.json`/`events.jsonl` della change.
   - **Esito atteso**: eventi coerenti con quanto osservato.

### V15 — Parallelismo e dipendenze (limitato al workflow minimo H-407)
1. Con 2 agenti indipendenti: verificare che partano in parallelo; con un task dipendente: non parte prima del predecessore.
   - **Esito atteso**: ordine dimostrato dagli eventi (PoC richiede «2 agenti in parallelo», IN §5 H-407).

### V16 — Gate, domanda e ripresa
1. Lasciare il PLAN senza approvazione: il workflow si ferma (H-403).
   - **Esito atteso**: nessun avanzamento; stato «in attesa del SUPERVISORE».
2. Approvare e riprendere.
   - **Esito atteso**: ripresa dal checkpoint senza ripetere operazioni già fatte.

### V17 — Errore di runtime
1. Forzare un errore di caricamento (es. workflow con sintassi errata).
   - **Esito atteso**: errore registrato come reale; nessun task segnato assegnato/completato; ripresa o stop controllato.

### V18 — Stato e SHA
1. Interrompere e riprendere; poi modificare un file pertinente.
   - **Esito atteso**: la storia è conservata; `VERIFIED` solo con SHA uguale; la modifica porta a `STALE`. Nel PoC (Q-13, 2026-10-29) basta il workflow minimo: gli stati R17 completi si verificano in Fase 7.

### V8 — Permessi (ALA V8; H-402/H-404/H-710)
1. Dare a un ruolo un brief che chiede `./mvnw test` e `git push`.
   - **Esito atteso**: tool negati; remoto invariato (`git ls-remote origin` uguale allo snapshot). Da ripetere a ogni release.

### V19 — Regressione LIMET classico (ALA V19)
1. In un repo di prova **senza** plugin installato e senza invocare `limet-change`, eseguire un flusso LIMET manuale (PLAN→TASK_LIST→TEST_PLAN) e uno con documenti preesistenti.
   - **Esito atteso**: nessun obbligo di `state.json`, `events.jsonl`, `limits.json`, worktree o approvazioni del workflow; evidenze registrate e confrontate con il comportamento precedente.

## Scenari di Fasi 7–9 (H-901; dopo GO del gate H-406)

> Fonte: ALA §9 tabella V1–V24, criteri oggettivi riportati. **[N]** Il comando rieseguibile per ciascuno dipende dallo scaffold E2E di H-901 (percorso/nome non definiti in IN): fino ad allora il comando è «invocazione di `limet-change` su change sandbox del pilota» e la verifica sui file `events.jsonl`/`state.json`. Verifica comune: eseguire su Windows, Linux, macOS; confronto delle snapshot pre/post come sopra.

| ID | Scenario | Passi essenziali | Esito atteso (criterio oggettivo ALA) | Task / test |
| -- | -------- | ---------------- | ------------------------------------- | ----------- |
| V1 | 3 task indipendenti in parallelo | Change sandbox con 3 task indipendenti; invocare il workflow | 3 `task.assigned` entro 60 s in `events.jsonl`; 3 worktree; nessun conflitto; 3 verdetti con SHA | H-704; T-704 |
| V2 | Dipendenza T2 ← T1 | Change con T2 dipendente da T1 | `T2.assigned` > `T1.integrated`; test dello scheduler | H-408; T-408 |
| V3 | Errore di compilazione indotto | Indurre errore di build nel task | `BUILD_ERROR`; fix al developer; `attempts = 2`; nessun PASS nel run fallito | H-701, H-409 |
| V4 | Test fallito: codice vs test | Indurre un test fallito | Classificazione corretta del `test-reader`; fix al ruolo giusto | H-606; T-605 |
| V5 | Bug fix (App. B.3) | Flusso bug-fix | Regressione FAIL poi PASS su SHA diversi | H-711; T-711 |
| V6 | Conflitto di integrazione | Due task sullo stesso file | `MERGE_CONFLICT`, decisione registrata, build successiva PASS | H-605; T-604 |
| V7 | Regressione: task B tocca file di A | Merge di B dopo A verificato | `VERIFIED → STALE → VERIFIED` | H-706; T-706 |
| V9 | Supervisore: T4 si ferma per dato mancante | T4 con dato mancante | Nessuno stop globale; domanda con fonti e opzioni via `AskUserQuestion`; T4 riparte dal WIP; nessuna domanda duplicata | H-707; T-707 |
| V10 | Mutation gate su test tautologico | Test tautologico nel task | Score sotto soglia al primo run, sopra al secondo; nessun merge fra i due | H-711; T-711 |
| V11 | Notte simulata (10 task, budget esaurito all'ottavo) | Budget fissato per esaurirsi all'8° task | Stato `INCOMPLETO`; report notturno con «Da leggere oggi»; ripresa dal checkpoint | H-708, H-709; T-708, T-709 |
| V12 | E2E Playwright su app locale (3 casi, 1 FAIL indotto) | App locale del pilota | Trace per il FAIL; trace/log e stato pre/post; classificazione corretta | H-610; T-609 |
| V20 | Completezza eventi e conteggio agenti | Scenario noto: pianificare, avviare, completare, fallire, annullare agenti | Conteggi distinti (pianificati/istanziati/avviati/terminati) coincidenti con la ground truth | H-609; T-608 |
| V21 | Accuratezza durate e parallelismo | Test controllato con durate e sovrapposizioni note | Attese, durate, wall-clock, agent-time, picco concorrenza entro tolleranza (ALA §9) | H-609; T-608 |
| V22 | Generazione e usabilità dashboard SPA | Generare `REPORT_METRICHE.html` e aprirlo offline | Si apre senza rete né server; grafici/filtri sui dati incorporati; responsive e accessibile; drill-down ed export | H-609, H-8b06 |
| V23 | Privacy, sicurezza, valori non disponibili | Iniettare stringhe HTML/script e marker di segreti | Escaping/sanitizzazione; nessun prompt completo, credenziale o diff nel report; token/costi assenti mostrati come N/D | H-609, H-1001 |
| V24 | Confronto storico tra run | Almeno due run con differenze note | Filtri/confronto distinguono run, versione plugin, change e SHA senza mischiare dati | H-609 |

Copertura V1–V24: scenari dettagliati sopra V8, V13–V19 (8) + tabella V1–V7, V9–V12, V20–V24 (16) = **24 ✓**.

## Passi dettagliati V1–V12 e V20–V24 (H-901)

> Aggiunto 2026-10-29 su richiesta del SUPERVISORE. Fonte dei criteri: ALA §9 (testo integrale). Convenzioni: `<RUN>` = cartella del run con `state.json` e `events.jsonl` (**[N]** percorso e nomi evento esatti definiti da H-706/H-308: i nomi `task.assigned`, `T2.assigned`, `T1.integrated` sono quelli di ALA); `<SANDBOX>` = repo pilota «biblioteca» (**[N]** ubicazione, Q-5); `<INVOCA>` = invocazione reale di `limet-change` in sessione Claude Code (V14), **[N]** sintassi da H-407. Gli scenari li esegue il SUPERVISORE (su Windows, Linux, macOS); l'agente non esegue build/test. Per ogni scenario: pre-stato → passi → esito atteso → evidenze da allegare in «Esito esecuzione».
> Comando di verifica generico degli eventi (PowerShell, sola lettura): `Get-Content <RUN>\events.jsonl | ConvertFrom-Json | Group-Object type | Select Name,Count`. Su Unix: `jq -r .type <RUN>/events.jsonl | sort | uniq -c` (**[N]** il campo `type` è ipotesi sul formato di H-706).

### V1 — 3 task indipendenti in parallelo (H-704; T-704)
1. Pre: `<SANDBOX>` pulito (`git status --porcelain` vuoto), `limits.json` con `maxAgents ≥ 3`; change con 3 task a file disgiunti.
2. `<INVOCA>` e confermare il grafo (gate H-703).
3. Contare gli eventi `task.assigned` e il tempo tra il primo e l'ultimo; `git worktree list`.
- **Atteso**: 3 `task.assigned` entro 60 s; 3 worktree distinti; nessun conflitto; 3 verdetti ciascuno con SHA.
- **Evidenze**: estratto `events.jsonl`, output `git worktree list`, i 3 `verdict` con SHA.

### V2 — Dipendenza T2 ← T1 (H-408; T-408)
1. Change con T2 dipendente da T1. `<INVOCA>`.
2. Confrontare i timestamp di `T1.integrated` e `T2.assigned`.
- **Atteso**: `T2.assigned` successivo a `T1.integrated`; il test dello scheduler passa: `node --test agents/plugin/scheduler/limet-scheduler.test.js`.

### V3 — Errore di compilazione indotto (H-701, H-409)
1. Indurre un errore di compilazione nel task del developer (es. simbolo inesistente nel brief).
2. `<INVOCA>`; leggere `result.json` del runner e `state.json`.
- **Atteso**: stato `BUILD_ERROR`; fix assegnato al developer; `attempts = 2`; nessun PASS nel run fallito; mai un terzo tentativo cieco (maxAttempts 3).

### V4 — Test fallito: codice vs test (H-606; T-605)
1. Indurre due fallimenti: uno dovuto al codice, uno al test.
2. `<INVOCA>`; leggere `reader-<run>.json`.
- **Atteso**: il `test-reader` classifica correttamente (codice / test) con estratti reali di log; il fix va al ruolo giusto (developer o tester).

### V5 — Bug fix (H-711; T-711; ALA App. B.3)
1. Fornire un bug con test di regressione.
2. `<INVOCA>`; confrontare gli SHA dei run del runner.
- **Atteso**: regressione FAIL sullo SHA pre-fix, poi PASS su SHA diverso (post-fix).

### V6 — Conflitto di integrazione (H-605; T-604)
1. Due task che modificano la stessa riga di uno stesso file.
2. `<INVOCA>`; guardare `integration/NNNN` e `events.jsonl`.
- **Atteso**: evento `MERGE_CONFLICT`; conflitto banale risolto o rimandato (semantico → SUPERVISORE); decisione registrata; build successiva PASS.

### V7 — Regressione: il task B tocca file di A (H-706; T-706)
1. Verificare A (`VERIFIED`), poi integrare B che modifica file di A.
2. Leggere le transizioni di stato di A.
- **Atteso**: `VERIFIED → STALE → VERIFIED` (ri-test dopo il merge); A non resta `VERIFIED` senza nuovo run.

### V8 — Permessi: già in sezione «Scenari» sopra (H-402/H-404/H-710; T-710)

### V9 — Supervisore: T4 si ferma per dato mancante (H-707; T-707)
1. Change in cui T4 richiede un dato assente dai documenti.
2. `<INVOCA>`; rispondere alla domanda quando posta.
- **Atteso**: nessuno stop globale (gli altri task proseguono); la domanda cita fonti e opzioni e passa da `AskUserQuestion` e `QUESTIONS.md`; T4 riparte dal WIP dopo la risposta; nessuna domanda duplicata.

### V10 — Mutation gate su test tautologico (H-711; T-711)
1. Il tester produce (indotto) un test tautologico.
2. `<INVOCA>`; leggere i punteggi mutation dei due run.
- **Atteso**: score sotto soglia al primo run, sopra al secondo; nessun merge fra i due run.

### V11 — Notte simulata: 10 task, budget esaurito all'ottavo (H-708, H-709; T-708, T-709)
1. `limits.json` con budget che si esaurisce all'8° task. `<INVOCA>`.
2. Leggere `REPORT_NOTTURNO.md` e lo stato; poi riprendere (`resumeFromRunId`).
- **Atteso**: stato `INCOMPLETO` (mai «completato»); report con sezione «Da leggere oggi»; ripresa dal checkpoint senza rifare i task verificati.

### V12 — E2E Playwright su app locale: 3 casi, 1 FAIL indotto (H-610; T-609)
1. Avviare l'app pilota in locale; indurre un fallimento in 1 dei 3 casi.
2. `<INVOCA>`; leggere trace e `reader`.
- **Atteso**: trace del caso FAIL prodotta dal runner; stato pre/post registrato; classificazione corretta; nessun passo inventato.

### V20 — Completezza degli eventi e conteggio agenti (H-609; T-608)
1. Scenario noto: pianificare, avviare, completare, far fallire e annullare un insieme controllato di agenti (es. 5 pianificati, 4 avviati, 2 completati, 1 fallito, 1 annullato).
2. Calcolare i conteggi dagli eventi e confrontarli con la ground truth scritta prima.
- **Atteso**: pianificati / istanziati / avviati / terminati coincidono; ID univoci; transizioni coerenti; eventi mancanti o duplicati **segnalati**, non compensati inventando dati.

### V21 — Accuratezza di durate e parallelismo (H-609; T-608)
1. Test controllato con durate e sovrapposizioni note (ground truth scritta prima), con fusi orari diversi, un evento tardivo e un run interrotto.
2. Confrontare attese, durata per agente/task, wall-clock, agent-time, picco di concorrenza.
- **Atteso**: valori entro la tolleranza definita nel TEST_PLAN (**[N]** valore da fissare: ALA non lo numera); fusi, eventi tardivi e run interrotti gestiti.

### V22 — Generazione e usabilità della dashboard SPA (H-609, H-8b06)
1. Generare `REPORT_METRICHE.html`; staccare la rete; aprirlo da file locale (senza server).
2. Usare filtri, grafici, drill-down; esportare JSON e CSV; ridurre la finestra (responsive); navigare da tastiera.
- **Atteso**: nessuna richiesta di rete; grafici e filtri funzionano sui dati incorporati; layout responsive e accessibile; export coerenti con la fonte (conteggi uguali). Un HTML statico senza interazioni **non** soddisfa.

### V23 — Privacy, sicurezza, valori non disponibili (H-609, H-1001)
1. Iniettare nei dati stringhe tipo `<script>alert(1)</script>` e marker di segreti finti; omettere token/costi per alcuni eventi.
2. Generare il report; cercare le stringhe nel file; guardare il traffico di rete.
- **Atteso**: escaping/sanitizzazione (nessuno script eseguito); nessun prompt completo, credenziale o diff nel report; token/costi assenti mostrati come `N/D`, non come stime; nessuna richiesta di rete/CDN.

### V24 — Confronto storico tra run (H-609)
1. Generare almeno due run con metriche note e differenze controllate (versione plugin, change, SHA diversi).
2. Usare filtri e confronto nel report.
- **Atteso**: i run restano distinti per run, versione plugin, change e SHA; nessun evento o dato di un run finisce in un altro.

**Esito comune e regole**: ogni scenario va eseguito su 3 OS (IN §6, Fase 9); un esito KO si registra in «Esito esecuzione» con evidenza e non si «compensa»; se emerge un rischio, vale C28 (≥ 2 opzioni al SUPERVISORE). **[N]** Valori numerici non definiti da ALA (tolleranza V21, soglia mutation V10, 60 s di V1 sono invece in ALA) vanno fissati dal SUPERVISORE prima di H-901.

### E2E-CFG-1 — Prima configurazione locale (H-309, H-8b09; richiesta SUPERVISORE 2026-10-30)

**Scopo**: dimostrare che chi clona HELIOS non eredita la configurazione di un altro e non può operare finché non ha fornito la propria.

**Stato pre-verifica**
- Clone pulito di HELIOS; nessun file di configurazione in **nessuna** delle tre posizioni: variabile `HELIOS_CONFIG` non definita, `config/helios.local.json` assente, `%APPDATA%\HELIOS\config.json` (Windows) / `~/.config/helios/config.json` (Unix) assente.
- `config/helios.local.example.json` presente e versionato.

**Passi**
1. Avviare la dashboard e tentare una qualunque operazione.
   - **Atteso**: l'operazione **non** parte; si apre la dialog di prima configurazione con esito `INCOMPLETO` e l'elenco dei campi mancanti.
2. Verificare che la dialog sia **pre-compilata** con i percorsi proposti dall'autodiscovery (`limet/` e `cerebro/` dentro il repo dopo H-301) e che nessun valore provenga da un'altra macchina.
3. Inserire a mano un percorso inesistente in `paths.cerebro` e confermare.
   - **Atteso**: esito `NON VALIDO`, il campo colpevole è segnalato, **nessun file scritto**.
4. Inserire un percorso esistente ma privo di `scripts/query_qdrant.py` e confermare.
   - **Atteso**: ancora `NON VALIDO` — la validazione è semantica, non la sola esistenza.
5. Correggere i campi e confermare.
   - **Atteso**: il file è scritto nella posizione dichiarata dalla dialog, la verifica è rieseguita, l'esito diventa `OK` e l'operazione del passo 1 diventa disponibile.
6. Eseguire `git status --porcelain` e `git check-ignore` sul file appena creato.
   - **Atteso**: il file **non** compare tra le modifiche e risulta ignorato (C20).
7. Riavviare la dashboard.
   - **Atteso**: nessuna dialog; la configurazione viene ricaricata.
8. Definire `HELIOS_CONFIG` puntando a un secondo file con valori **diversi** e riavviare.
   - **Atteso**: vincono i valori di `HELIOS_CONFIG`; la precedenza è visibile nella diagnostica (`helios doctor` indica quale sorgente è stata usata).
9. Chiamare direttamente l'endpoint dell'operazione del passo 1 **senza** passare dalla UI, con configurazione rimossa.
   - **Atteso**: l'API rifiuta allo stesso modo — il gate non è aggirabile (C23); è il passo che dimostra che la verifica sta nel modulo condiviso e non solo nella UI.
10. Eseguire `helios doctor` da riga di comando senza configurazione.
    - **Atteso**: stesso esito `INCOMPLETO` con gli stessi campi, senza aprire alcuna interfaccia (serve alla CI, C13).

**Stato post-verifica**
- Nessuna scrittura fuori dalla posizione dichiarata; nessun segreto nel file prodotto; `config/helios.local.example.json` invariato.
- Da ripetere su Windows e su Unix (percorsi per utente diversi).

### E2E-AUT-1 — Run non presidiato con domanda bloccante (H-713, H-714, H-715; ADR 0003)

**Scopo**: dimostrare che un run non presidiato che incontra un dubbio **non si ferma del tutto**, non decide al posto del SUPERVISORE, e la mattina dopo è leggibile e ripartibile.

**Stato pre-verifica**
- GO del gate C14 già ottenuto (la modalità vale solo dopo il GO, Q-17).
- Grafo dei task **già confermato** al gate R3, con almeno: un task che porterà a una domanda bloccante, almeno un task **dipendente** da quello, almeno due task **indipendenti**, e un task il cui turno spetta a un ruolo **escluso** (es. `integrator`).
- `limits.json`: `night.stopOnQuestions` = `false`, `night.maxDurationMin` = 480, budget capiente (il run non deve finire per budget).
- Nessun kill switch presente; registro delle decisioni autonome vuoto; nessuna sessione umana attiva dopo l'avvio.

**Passi**
1. Avviare il run non presidiato dalla dashboard (H-8b10) e **chiudere la sessione umana**.
   - **Atteso**: il run prosegue; nessuna richiesta interattiva viene posta a nessuno.
2. Attendere che il task problematico raggiunga la domanda bloccante.
   - **Atteso**: il task passa a `WAITING_SUPERVISOR` con motivazione; la voce compare in `QUESTIONS.md` (H-707).
3. Osservare il task **dipendente**.
   - **Atteso**: bloccato, **non** eseguito con un'assunzione. È il passo che dimostra che l'autonomia non inventa requisiti (C06).
4. Osservare i due task **indipendenti**.
   - **Atteso**: completati regolarmente. Senza questo, `stopOnQuestions` = `false` non avrebbe prodotto alcun vantaggio rispetto a `true`.
5. Osservare il task assegnato al ruolo **escluso**.
   - **Atteso**: parcheggiato in `WAITING_SUPERVISOR` con motivazione «ruolo non ammesso in modalità non presidiata», e il run prosegue (H-715).
6. Verificare i commit prodotti dal `developer` sui rami dei task completati.
   - **Atteso**: i commit esistono (Q-18); **nessun** `push`, **nessun** `merge` su `main` o su `integration/NNNN` (R19, R21).
7. Verificare che il `runner` abbia operato solo su SHA committati.
   - **Atteso**: nessuna esecuzione su codice non committato (ALA §1.3); è la conferma a posteriori della decisione Q-18.
8. A fine run leggere `REPORT_NOTTURNO.md`.
   - **Atteso**: sezioni «Da leggere oggi» e «decisioni autonome» presenti; il run **non** è dichiarato «completato» (R18, H-709); le cause di ogni parcheggio sono esplicite.
9. Esaminare il registro delle decisioni autonome (H-714).
   - **Atteso**: ogni voce ha task, motivazione, evidenza e modo di annullarla; nessun segreto, nessun prompt integrale; nessuna voce riguarda requisiti, rischi, merge o approvazioni.
10. Confrontare i permessi effettivi registrati nel run con quelli di un run presidiato equivalente.
    - **Atteso**: **identici** (neutralità dei permessi, ADR 0003 §2). È il passo che impedisce alla modalità di diventare un bypass di C02/C24.
11. Il mattino dopo, rispondere alla domanda in `QUESTIONS.md` e riprendere con `resumeFromRunId`.
    - **Atteso**: il task bloccato e i suoi dipendenti ripartono dal checkpoint, senza rifare il lavoro già completato.

**Stato post-verifica**
- Nessuna scrittura fuori dal workspace e fuori da `~/.claude` (o HOME isolato).
- `main` invariato; nessun merge eseguito; nessun `commit-commands` invocato.
- Da ripetere con `night.stopOnQuestions` = `true` per verificare il ramo alternativo (tutto fermo, `INCOMPLETO`, report).

### E2E-AUT-2 — Fine corsa del run non presidiato: durata, kill switch, dead-man (H-713, H-8b10)

**Scopo**: dimostrare che un run non presidiato **termina sempre**, per una causa dichiarata, lasciando il repository ripartibile. È la verifica che rende accettabile la decisione «nessun reperibile» (Q-14).

**Stato pre-verifica**
- Come E2E-AUT-1, ma con limiti volutamente stretti e task sufficienti a non esaurirsi da soli.
- `night.maxDurationMin` ridotto a un valore osservabile nella prova; soglia del dead-man switch nota.

**Passi**
1. Avviare un run con durata massima ridotta e lasciarlo scadere.
   - **Atteso**: stop **controllato** allo scadere — nessun passo interrotto a metà; stato `INCOMPLETO`; causa registrata «durata massima».
2. Riprendere con `resumeFromRunId`.
   - **Atteso**: ripartenza dal checkpoint, nessun lavoro ripetuto, nessun task eseguito due volte.
3. Avviare un secondo run con budget volutamente insufficiente.
   - **Atteso**: stop controllato; causa registrata «budget», **distinta** da quella del passo 1. Se le due cause risultassero indistinguibili, il report del mattino sarebbe ambiguo.
4. Avviare un terzo run e creare il file di **kill switch** mentre è in corso.
   - **Atteso**: il run si ferma entro il passo successivo; causa «kill switch». Creare il file *dopo* l'avvio è essenziale: dimostra che non è letto solo all'inizio.
5. Verificare che il kill switch sia raggiungibile dalla dashboard a run avviato (H-8b10).
   - **Atteso**: comando presente e abilitato per tutta la durata del run.
6. Avviare un quarto run e simulare il blocco del processo che aggiorna il registro eventi.
   - **Atteso**: il **dead-man switch** interrompe il run superata la soglia, anche senza sforare durata o budget; causa registrata «dead-man».
7. Per **ciascuna** delle quattro cause, verificare l'esistenza di un checkpoint valido.
   - **Atteso**: `resumeFromRunId` funziona in tutti e quattro i casi. Un solo caso non ripartibile invaliderebbe la decisione «nessun reperibile».
8. Verificare lo stato del repository dopo ogni interruzione.
   - **Atteso**: nessun worktree in stato incoerente; nessun lock residuo; `git status --porcelain` spiegabile riga per riga.
9. Tentare di avviare un run non presidiato **senza** sessione umana (chiamata diretta all'API).
   - **Atteso**: rifiutato (C24, C23). È il passo che dimostra che l'assenza di presidio non significa assenza di autorizzazione.
10. Tentare di avviare un run con un `limits.json` che amplia i permessi o aggiunge un ruolo escluso.
    - **Atteso**: avvio **rifiutato** con errore esplicito, non avvio parziale con la parte valida della configurazione.

**Stato post-verifica**
- Nessun run orfano in esecuzione; nessun processo residuo.
- `main` invariato; nessun merge; nessun push.
- I quattro run compaiono nel report con causa di chiusura distinta e checkpoint utilizzabile.

### E2E-PoC-1 — Nessuna scrittura fuori workspace / `~/.claude` (criterio 1)
1. Eseguire un giro completo (V14).
2. Rieseguire lo snapshot e confrontarlo con `pre_claude.csv`.
   - **Esito atteso**: 0 differenze fuori dal workspace; 0 modifiche in `~/.claude` (o HOME isolato) imputabili a HELIOS.

### E2E-PoC-2 — Nessuna rete non autorizzata (criterio 2)
1. Durante il giro, registrare le connessioni di rete del processo runner (**[N]** strumento: es. `Get-NetTCPConnection`/Resource Monitor; da definire in H-401).
   - **Esito atteso**: 0 connessioni non in allowlist.

### E2E-PoC-3 — Costo (criterio 8)
1. Misurare il costo del giro completo.
   - **Esito atteso**: ≤ 100 USD per workflow (IN Q2). **[N]** Modalità di misura con abbonamento (Q-7).

### E2E-PoC-4 — CI su 3 OS (criteri 7)
1. Eseguire la CI `.github/workflows/ci.yml` su windows/ubuntu/macos con la stessa suite (azione del SUPERVISORE, push compreso).
   - **Esito atteso**: tutti verdi.

### E2E-PoC-5 — Originali invariati e parità (H-301, H-304; criterio 9)
1. Confrontare `limet/` e `cerebro/` importati con gli originali (hash) e eseguire T-304.
   - **Esito atteso**: originali invariati; parità su Windows e Unix.

### E2E-PoC-6 — Revisione del rapporto go/no-go (H-406)
1. Leggere `docs/poc-report.md`.
   - **Esito atteso**: ognuno dei 10 criteri ha esito GO/NO-GO con evidenza (file/evento/log); un criterio fallito = no-go su quell'asset/lane con piano B (wrapper senza copia, 04 §5). Criterio 10 = N/A motivato (Q-12, dashboard in Fase 8).

## Stato post-verifica (controllo DOPO i passi)

- Collection Qdrant esistenti: conteggi invariati.
- `git ls-remote origin` invariato (nessun push da agenti).
- `~/.claude` (o HOME isolato): nessuna differenza imputabile.

## Casi limite da verificare

- Workflow invocato con change senza PLAN approvato (V16).
- Plugin installato con cartella sorgente rimossa (V13 passo 3).
- Esecuzione su Windows nativo (rischio R-2).

## Esito esecuzione (da compilare da chi esegue)

- **Data/ora**: [YYYY-MM-DD HH:MM]
- **Eseguito da**: [SUPERVISORE]
- **Esito**: `[OK | KO]` per scenario
- **Note/anomalie**: [...]
