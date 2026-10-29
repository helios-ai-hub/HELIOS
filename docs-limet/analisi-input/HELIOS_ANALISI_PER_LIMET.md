# HELIOS — Analisi e piano di implementazione (input per LIMET)

> Documento di **input** da passare a LIMET per avviare lo sviluppo di HELIOS. Non è un insieme di documenti LIMET di progetto: da questo LIMET deriva PLAN, TASK_LIST, TEST_PLAN, E2E_VERIFICATION, ecc.
> Pacchetto autosufficiente: in questa cartella (`limet-docs\`) ci sono copie di `00`–`06`, `REGISTRO_SKILLS_AGENTI_ECC.md`, `FEASIBILITY_O1_CLAUDE_CODE.html` (FO1) e `ANALISI_limet-agents_con_workflow_claude.md` (ALA). I rimandi `..\NN`, `..\appendici\REGISTRO…` e `..\0X_*.md` nel testo vanno letti come file **nella stessa cartella** (`NN_*.md`, `REGISTRO_SKILLS_AGENTI_ECC.md`). Le copie sono di sola lettura per LIMET; gli originali dello studio restano in `..\`.
> Etichette: **[F]** fatto verificato · **[I]** ipotesi · **[P]** proposta · **[N]** non verificato. Le fonti sono indicate per file e sezione; si rimanda a `..\01`–`..\06` senza ripeterli.
> Stato decisioni: tutte le criticità C01–C28 sono **decise dal SUPERVISORE (2026-10-28)** in `..\05_roadmap_e_rischi.md` §2–3 e sono **vincolanti**; le questioni Q1–Q9 (§8) sono chiuse.
> Documenti vincolanti non modificabili: `FEASIBILITY_O1_CLAUDE_CODE.html` (FO1), `ANALISI_limet-agents_con_workflow_claude.md` (ALA).

---

## 0. Leggimi prima: HELIOS in parole semplici

**Cosa è HELIOS.** Un software che orchestra una catena di agenti (analisti, sviluppatori, tester, revisori, validatori) guidata dal metodo LIMET, per portare una pratica dal piano al codice verificato, con **pause obbligatorie** in cui decide il SUPERVISORE. Riusa il più possibile le skill e gli agenti del progetto open source ECC (MIT), ma **senza** i suoi hook, la sua memoria e il suo flusso di pianificazione. Include una dashboard web unica per HELIOS, LIMET e CEREBRO. Non è «autonomo senza limiti»: si ferma e chiede in caso di dubbio, errori ripetuti o costo oltre soglia.

**Come si sviluppa HELIOS (importante).** HELIOS è sviluppato con **LIMET «puro»**, cioè con il solo metodo LIMET e con l'LLM che esegue i task: **gli agenti di HELIOS non esistono ancora** e non possono essere usati per costruirlo. Le regole che servono durante lo sviluppo (per esempio la valutazione dei rischi dopo ogni task, §2.3 n. 7) valgono quindi come **regole per l'LLM che sviluppa**. Solo quando la catena è costruita (fasi 4–7) il prodotto le applica da sé (H-612).

**Cosa deve fare LIMET con questo file.** Leggerlo per intero insieme ai documenti dell'elenco in §9, poi derivare i propri documenti di progetto (PLAN, TASK_LIST, TEST_PLAN, E2E_VERIFICATION). Questo file non li sostituisce: li alimenta. Ogni task `H-xxx` di §5 è già atomico, con file, test (`T-xxx` di §6) e criterio di completamento (*Done*).

**Come leggerlo.** §1 cosa si costruisce e le decisioni · §2 fonti e regole non negoziabili · §3 architettura · §4 fasi · §5 task · §6 test · §7 governance · §8 questioni chiuse e punti non verificati · §9 uso con LIMET. Dove una frase rimanda a `..\NN`, vale il file `NN_*.md` in questa cartella.

**Glossario.**

| Termine | Significato |
|---|---|
| SUPERVISORE | L'utente umano: unico che approva i gate, decide le questioni, esegue build/test, commit e push |
| LIMET «puro» | Il metodo LIMET senza agenti HELIOS: l'LLM esegue i task, il SUPERVISORE controlla |
| ECC | Progetto open source «Everything Claude Code» (agenti, skill, comandi, hook), fissato al commit `25278e7` |
| Asset ECC | Un agente o una skill di ECC portati in HELIOS (testo, senza hook) |
| Ledger | `ecc-ledger.json`: per ogni asset portato, percorso, SHA-256, licenza, origine e esito dei test |
| Overlay | Breve testo HELIOS aggiunto a un asset ECC; il corpo originale resta invariato |
| Ruolo | Funzione della catena (analyst, developer, tester, integrator, reviewer, validator…), §3.5 |
| Runner | Esecutore deterministico (script, non LLM) che lancia build/test per conto dell'utente, con log e hash |
| Worktree | Copia di lavoro git separata, una per task |
| Gate | Punto di arresto in cui procede solo chi ha l'autorità (SUPERVISORE) |
| «Fermati e chiedi» | Stato in cui la catena si blocca e attende una decisione |
| Plugin | Pacchetto Claude Code `limet-agents` che porta agenti, workflow e skill (§3.6) |
| PoC | Prova di fattibilità verticale (Fase 4) con criteri go/no-go misurabili |
| C01–C28 | Criticità decise (`..\05` §2–3) |
| Q1–Q9 | Questioni aperte, ora tutte decise (§8) |
| Base di conoscenza | Collection Qdrant interrogate dagli agenti in sola lettura: `CRB_<slug>` (documenti del progetto) e `HLS_*` (asset ECC e matrice catena), §3.8 |
| H-xxx / T-xxx | Task di backlog (§5) / test (§6) |
| [F] [I] [P] [N] | Fatto verificato / ipotesi / proposta / non verificato |

---

## 1. Sintesi e perimetro

### 1.1 Cosa si costruisce **[P]**
HELIOS è un **nuovo progetto autonomo** (C01) in un **unico repository a moduli** (C21, C22) che contiene:
- `limet/` — copia interna del metodo LIMET (manuali, template, script);
- `cerebro/` — copia interna del RAG (ingest, query Qdrant, MCP `limet_search`);
- `agents/` — core agentico: plugin Claude Code `limet-agents`, workflow `limet-change.js`, ruoli/agenti, **runner**, asset ECC portati e ledger;
- `ui/` + `api/` — **dashboard web React** unica per HELIOS, LIMET e CEREBRO;
- `compat/` — shim dei vecchi entry-point (parità di comportamento).

Formula: «un ECC con agenti, guidato da LIMET». LIMET è l'unico orchestratore, il runner l'unico esecutore, CEREBRO l'unico provider di contesto (`..\04` §1–3).

**Principio guida (SUPERVISORE, 2026-10-28) [P]:** HELIOS porta al proprio interno **il massimo possibile di skill e agenti ECC** (obiettivo: tutti o quasi, tracciati in `REGISTRO_SKILLS_AGENTI_ECC.md`) **mantenendo intatta l'infrastruttura LIMET e CEREBRO** (metodo, gate, stati, RAG). Le esclusioni sono solo quelle motivate da sicurezza, licenza o incompatibilità con LIMET (§1.2, C03–C07); ogni asset escluso ha un motivo nel registro. Tutto vive nel repository HELIOS, plugin `limet-agents` compreso (Q7).

### 1.2 Cosa è escluso
Hook ECC non installati né eseguiti (C04): vanno però **analizzati** (cosa fanno, a che serve ciascuno) e, se utili, **replicati come hook HELIOS propri** in modo sicuro (H-800, H-800b), memoria ECC (`unified-memory`, `continuous-learning*`, `observe`), flusso di pianificazione ECC come **autore del piano** (`plan*`, `prp-*`, `orch-*`, `blueprint`, `epic-*`, agente `planner`: ammessi solo come consulenti dell'analyst, §3.5), Plan Canvas, Control Pane, GateGuard, GitHub App ecc.tools, installazione globale in `~/.claude`, `--opus` di AgentShield, 4 rules `common/` in conflitto. Asset non MIT-verificati restano fuori finché non approvati (C07).

### 1.3 Installazioni esistenti **[F]**
I LIMET e CEREBRO in uso per COSMO e mudeopen (`C:\Progetti\VSC\LIMET`, `C:\software\cerebro`) **non vengono toccati** (C19, decisione del SUPERVISORE). HELIOS usa copie interne; nessun progetto consumatore va modificato.

### 1.4 Decisioni vincolanti e conseguenze implementative

| ID | Scelta | Conseguenza implementativa |
|---|---|---|
| C01 | HELIOS progetto autonomo con proprie regole | Redigere ADR fondativo + «costituzione HELIOS» che eredita i non-negoziabili (§2.3) |
| C02 | Frontmatter `tools:` senza Bash + permessi agenti configurabili da dashboard | Pavimento hard-deny non modificabile dalla UI; policy versionata; audit e rollback; test statico |
| C03 | Non installare 4 rules `common/` (`testing`, `development-workflow`, `agents`, `git-workflow`) | Rules caricate per ruolo, non globali |
| C04 | Nessun hook ECC | Nessun `hooks.json`; garanzie dai gate meccanici LIMET |
| C05 | Nessun invio di contenuto a LLM salvo automazione approvata | Registro delle chiamate LLM aggiuntive, visibile in dashboard, con test CI |
| C06 | Prefisso `ecc-`; pianificazione ECC solo consultiva (proposte, mai file di piano) | Test: nessun file di piano fuori dai documenti LIMET; nessuna collisione di nomi |
| C07 | Ledger per file + allowlist MIT verificata + NOTICE | `ecc-ledger.json`; parere legale prima del rilascio |
| C08 | CEREBRO unica memoria | Nessuna memoria ECC |
| C09 | Plan Canvas/Control Pane non portati | Revisione PLAN nella dashboard HELIOS |
| C10 | AgentShield: versione pinnata + hash + SBOM, nel runner senza rete e senza `--opus` | Wrapper nel runner; verifica hash |
| C11 | GitHub App mai installata | Policy di blocco a livello organizzazione (azione del SUPERVISORE) |
| C12 | Fork/vendor pinned a commit `25278e7` | Nessun aggiornamento automatico; upgrade = decisione + non regressione |
| C13 | Core in runtime portabile, CI Windows/Linux/macOS | Niente tmux/bash/PowerShell nel core; suite identica sui 3 OS |
| C14 | PoC verticale (3 ruoli + runner + 1 gate) prima del porting ECC | Fase 4 = go/no-go |
| C15 | Inventario scriptato al 100% di ECC | Script + tabella per skill/agente |
| C16 | Esperimento A/B con soglie go/no-go | Asset senza beneficio misurato non entra |
| C17 | Test statico «LIMET-agnostico» in CI | Asset = solo prompt (no hook/tool/memoria/`.claude/plans`) |
| C18 | Runner con allowlist, senza rete, log con hash, mai commit/push | Contratto agente→runner dichiarativo |
| C19 | Shim di compatibilità, installazioni attuali intoccate | `compat/` con suite di parità |
| C20 | Segreti/config fuori dal repo | Solo `projects.example.json`; Qdrant e `cache/` non versionati |
| C21 | Nuovo repo con import storia in sottocartelle | Import eseguito dal SUPERVISORE (commit/push a suo carico) |
| C22 | Monorepo a moduli con confini imposti + CI a matrice di supporto | Test che vietano riferimenti fuori dai contratti |
| C23 | Backend solo loopback, token, allowlist Host/Origin, CSRF, CSP | Test di sicurezza automatici |
| C24 | Gate approvati solo da sessione umana; la UI non esegue build/test/git | Token non esposto agli agenti |
| C25 | Lockfile, licenze permissive, audit+SBOM in CI, pin | Aggiornamenti solo via PR |
| C26 | Grafica SVG/Canvas con librerie permissive; WebGL opzionale con fallback | Budget prestazioni e test WCAG 2.1 AA; modalità «leggera» **non** scelta |
| C27 | Read model derivato, nessun DB di stato proprio | Cache rigenerabile |
| C28 | Valutazione rischi a fine di ogni task: **regola per l'LLM che sviluppa** (HELIOS è sviluppato con LIMET puro, §2.3 n. 7); nel prodotto HELIOS diventa il ruolo `risk-assessor` (H-612). Segnala al SUPERVISORE con opzioni risolutive, non corregge da solo | Evidenza obbligatoria; giudizio del modello, non prova (T-611 solo per il ruolo) |

---

## 2. Analisi dei documenti attuali

### 2.1 Cosa dicono (mappa fonti → uso)

| Fonte | Contenuto rilevante per l'implementazione | Rif. |
|---|---|---|
| FO1 | Verdetto «fattibile»; gate meccanici (§2.4), stati (§2.2), scheduler (§2.3), scheletro workflow (§2.5), protocollo «fermati e chiedi» (§3), costi 20–45 USD / 6–10 h (§6) **[F]** | `..\02` §2 |
| ALA | Requisiti R1–R27 (R13 gate, R17 stati, R19 deny `./mvnw*`/`git push*`/`git merge*`, R23 `limet-change.js`, R24 retrocompatibilità, R25–R27 metriche), 9+ ruoli (analyst, developer, tester, integrator, test-reader, reviewer, validator, e2e-*, metrics-observer), §13 skill ammesse se LIMET-agnostiche **[F]** | `..\02` §2, §5 |
| `..\01` | ECC @ `25278e7` (v2.2.3, MIT): 71 agenti (55 con Bash, 28 con Write/Edit), 311 skill, 97 comandi, 27 hook, 122 rules; nessuno spec-kit dedicato; GateGuard ≈ +45% turni **[F]** | `..\01` |
| `..\03` | Matrici sovrapposizioni/gap/conflitti | `..\03` |
| `..\04` | Architettura, layout monorepo, contratti (§7), dashboard (§8), workflow (§9) | `..\04` |
| `..\05` | Catalogo C01–C28, roadmap 12 fasi (+8b), go/no-go PoC, licenze | `..\05` |
| Manuale LIMET | Ciclo fasi 0–7; §1.3: l'agente non esegue build/test né commit/push senza autorizzazione **[F]** | `..\02` §1 |

### 2.2 Stato di fatto **[F]**
- `C:\software\limet-agents` **non esiste**: ALA è un design. La fattibilità è su carta **[I]** fino al PoC (C14).
- `CLAUDE.md` di LIMET punta a `C:\Progetti\CEREBRO` (errato: CEREBRO è in `C:\software\cerebro`). Da correggere **nella copia interna**, non nell'originale.
- CEREBRO ha CI pytest su `windows-latest`; LIMET non ha CI.
- Porte esistenti: launcher :8799, CEREBRO :8789 e :8777, Qdrant :6333, Ollama :11434.

### 2.3 Non-negoziabili (ereditati dalla costituzione HELIOS) **[F]**
1. Nessuna build/test/commit/push/merge autonomi: li esegue solo il runner (build/test, su allowlist) o l'utente (commit/push).
2. Gate umani su PLAN e validazione finale; approvabili solo da sessione umana (C24).
3. Funzionamento su Windows **e** Unix con la stessa suite (C13).
4. Nessun invio di contenuto a LLM salvo automazione approvata e registrata (C05).
5. Nessun segreto nel repository; nessuna scrittura fuori dal workspace; nessuna modifica a `~/.claude`.
6. Nessuna modifica a file preesistenti dei progetti esterni; nessuna sincronizzazione su COSMO_CEREBRO_DOCS.
7. **Valutazione rischi a fine di ogni task (regola di sviluppo, SUPERVISORE, C28) [P]:** HELIOS è sviluppato con **LIMET puro** (sessione LLM + documenti, senza il workflow multi-agente, che ancora non esiste). Perciò, **dopo aver sviluppato ogni singolo task**, l'LLM che lo ha sviluppato deve, **prima di dichiararlo concluso**, fare una valutazione esplicita di rischi, errori e possibili malfunzionamenti **dovuti a quel task** (regressioni su altri moduli/file, casi limite non coperti dai test, sicurezza e dati, prestazioni, compatibilità Windows/Unix, impatto sui task successivi, violazioni dei non-negoziabili 1–6). Se ne trova: li **segnala al SUPERVISORE** (con evidenza: file/riga/test) e propone **almeno due opzioni risolutive** con pro/contro e una raccomandazione; **non decide e non corregge da solo**, e attende la scelta. Se non ne trova, elenca i controlli eseguiti. L'esito va nella voce del task («Esito implementazione» / note del task) e, se ci sono rischi aperti, nelle domande contestuali. È un giudizio dell'LLM, non una prova: non sostituisce i test, che esegue l'utente.

### 2.4 Lacune da colmare **[I]**
Nessuna implementazione di runner/workflow; nessun catalogo di skill per linguaggio; reviewer specializzati assenti. **[F]** (verifica in sola lettura 2026-10-28): Qdrant/Ollama attivi, `CRB_limet` assente (vedi §8); CEREBRO `C:\software\cerebro` è un repo git (17 commit, remoto `https://github.com/odyssey2073/cerebro.git`) e LIMET `C:\Progetti\VSC\LIMET` è un repo git (20 commit, remoto `https://github.com/odyssey2073/limet.git`). **[P]** Per C21 entrambi sono importati con la storia come moduli `cerebro/` e `limet/` del repo HELIOS (decisione SUPERVISORE); import a cura dell'utente; i repo originali restano invariati.

---

## 3. Architettura target

### 3.1 Moduli e confini **[P]** (`..\04` §6, C22)

```
HELIOS/            (radice del repository, nessuna cartella «helios» annidata)
├─ limet/    metodo, manuali IT/EN, template, scripts
├─ cerebro/  RAG: ingest, query_qdrant, MCP limet_search, hook limet_rag.py
├─ agents/   plugin Claude Code limet-agents (agents/plugin/: .claude-plugin/plugin.json, workflows/limet-change.js, agents, skills ecc-*, installer), runner, asset ECC + ecc-ledger.json
├─ ui/       React + TypeScript
├─ api/      backend sottile su loopback
├─ compat/   shim vecchi entry-point
└─ .github/  CI matrice windows/ubuntu/macos, scansione segreti
```

Regole di dipendenza: `agents/` usa `cerebro/` solo via MCP/CLI pubblici; `cerebro/` non conosce `agents/`; `limet/` non dipende da codice; `ui/` parla solo con `api/`; `api/` non scrive stato proprio.

### 3.2 Runtime **[P]**
Core, runner e api in **Python ≥ 3.11** (già nello stack CEREBRO, test con pytest), UI in TypeScript/React. Motivazione: unico runtime portabile (C13) per tutto il core. **Eccezione deliberata (Q8):** JavaScript (Node ≥ 18, test `node --test`) solo per scheduler e workflow `limet-change.js`, perché il plugin Claude Code e ALA R4/R23 lo richiedono. Il confine tra i due linguaggi è il contratto JSON (§3.3). **Deciso dal SUPERVISORE** (§8, Q4 e Q8).

### 3.3 API pubbliche **[P]** (`..\04` §7)
- **Manifest asset** `ecc-ledger.json`: path, SHA-256, licenza, origine, commit `25278e7`, ruolo destinatario, esito test agnosticità.
- **Agente→runner:** richiesta `{operazione ∈ allowlist, workspace, parametri}`; risposta `{esito, hash_log, artefatti}`.
- **Contratto ruolo:** frontmatter con `tools:` minimi (mai Bash), modello, file di output.
- **Policy permessi** (C02): file versionato con pavimento hard-deny (`./mvnw*`, build/test, `git commit/push/merge`, segreti, rete non autorizzata) che la UI può solo restringere.
- **Portabilità:** percorsi relativi, separatori normalizzati, fine riga LF, nessuna shell nel core.

### 3.4 Flusso dati (C27) **[P]**
Sorgenti: artefatti LIMET su file, stato del workflow, API CEREBRO → `api/` costruisce un **read model derivato** (cache rigenerabile) → `ui/`. Le azioni risalgono come richieste al workflow LIMET, non scrivono stato.

### 3.5 Workflow **[P]** (`..\04` §9)
In sintesi: i documenti LIMET approvati dal SUPERVISORE (PLAN, SPEC, TASK_LIST, TEST_PLAN) entrano nel workflow → l'analyst ne ricava il grafo dei task (`plan.json`), che passa dal **gate umano** → per ogni task developer e tester lavorano in parallelo senza eseguire nulla → il **runner** esegue build/test → reviewer e validator controllano → valutazione rischi → **gate finale**. Commit/push solo su autorizzazione umana. Retry limitato a N, poi «fermati e chiedi» (FO1 §3). Il piano resta di LIMET e del SUPERVISORE: l'analyst non lo riscrive. Il dettaglio per task è qui sotto.

**Flusso per task (FO1, passi 0–10; vincolante)**: `T: developer + tester (in parallelo, worktree separati, il tester non vede il diff) → integrator (task/NNNN-Tn + test/NNNN-Tnt → integration/NNNN) → runner (script, stesso SHA) → [FAIL: test-reader → task di correzione Tn-fixK al developer, o al tester se difetto del test] → [PASS: reviewer ∥ validator] → VERIFIED → valutazione rischi post-task (C28, H-612)`. Finale: runner completo → validator → `REPORT_CONFORMITA.md` → `REPORT_FINALE.md` → il SUPERVISORE decide il merge. Bug-fix: il tester scrive prima la regressione, il runner la vede fallire, poi fix minimale (R15). E2E: e2e-designer → runner → e2e-reader.

| Ruolo | Modello | Input | Output | Non fa mai | Task |
|---|---|---|---|---|---|
| Orchestratore (workflow) | — | PLAN/SPEC/TASK_LIST/TEST_PLAN, `limits.json` | `state.json`, `events.jsonl`, report | scrivere codice, modificare documenti LIMET, merge su main | H-701, H-704–H-709 |
| analyst | opus | documenti LIMET, Graphify, CEREBRO | `plan.json` (grafo, split) | inventare requisiti; procedere con documenti incompleti | H-602 |
| developer ×N | sonnet | task + SPEC, worktree; CEREBRO e Graphify (sola lettura, §3.8) | commit su `task/NNNN-Tn`, elenco file | build/test, file fuori perimetro, push | H-603 |
| tester ×M | sonnet | TEST_PLAN + SPEC (senza diff); CEREBRO solo documenti, **mai** Graphify (§3.8) | test su `test/NNNN-Tnt`, autorevisione §5.1 | eseguire test, adattarli all'implementazione | H-604 |
| integrator | sonnet | i due rami del task; Graphify (sola lettura) | `integration/NNNN`, conflitti registrati | conflitti semantici, merge su main | H-605 |
| runner | script | worktree + SHA | `result.json` (+ log su disco) | interpretare, riassumere | H-409 |
| test-reader | haiku | `result.json` + righe di log | `reader-<run>.json` (classificazione, estratti reali) | riassumere a memoria; dichiarare passato un test non eseguito | H-606 |
| reviewer | opus (≠ developer) | SPEC + diff; CEREBRO e Graphify (sola lettura, §3.8) | `findings` (severità, file, riga) | correggere; approvare «a sensazione» | H-607 |
| validator | opus | report runner/reader; CEREBRO (sola lettura, §3.8) | verdetto per requisito con `evidence[]` | «conforme» senza prove | H-608 |
| risk-assessor (C28) | opus (≠ developer) | task VERIFIED: diff, SPEC, report runner/validator, grafo delle dipendenze, CEREBRO/Graphify | `ecosystem/risks/RISK-<task>.json` + voci in `QUESTIONS.md`: rischi/errori/malfunzionamenti con severità, evidenza e ≥ 2 opzioni risolutive (pro/contro, raccomandazione) | correggere da solo; dichiarare «nessun rischio» senza elencare i controlli fatti; inventare rischi senza evidenza | H-612 |
| e2e-designer / e2e-reader | sonnet / haiku | E2E_VERIFICATION, trace | script Playwright / classificazione | eseguire in proprio; inventare passi | H-610 |
| metrics-observer | — | metriche aggregate | interpretazione, `REPORT_METRICHE.html` | aggregare in proprio | H-609 |
| SUPERVISORE | umano | report, domande | gate grafo, risposte, merge, chiusura change | — | H-703, H-8b05 |

**Obbligo di progettazione della catena (SUPERVISORE) [P].** LIMET deve **progettare** (nel PLAN e nella TASK_LIST, non lasciare implicito) la catena multi-agente combinando **tre fonti**:
1. **Ruoli LIMET/FO1** della tabella sopra (analyst, developer, tester, integrator, test-reader, reviewer, validator, e2e-*, metrics-observer): sono lo **scheletro** della catena e non vengono sostituiti.
2. **Agenti ECC** (71, da `REGISTRO_SKILLS_AGENTI_ECC.md`): ognuno è **assegnato a uno step della catena** come specializzazione di un ruolo (es. reviewer di linguaggio/sicurezza → `reviewer`; build-resolver, tdd-guide, refactor-cleaner → `developer`/`tester`; doc-updater → fase di chiusura) oppure escluso con motivo. Gli agenti/skill di pianificazione ECC (planner, architect e simili) entrano **solo come consulenti dell'analyst**, in sola lettura: producono **proposte** (rischi, alternative, split) in `ecosystem/proposals/`, che l'analyst integra nel `plan.json` soggetto al gate umano. **Regola (deciso dal SUPERVISORE):** una proposta puramente tecnica (split, ordine, rischi) l'analyst la integra; una proposta che **aggiunge, modifica o elimina un requisito** non va integrata: l'analyst si ferma («fermati e chiedi», R8), la registra in `QUESTIONS.md` con opzioni e motivazioni e procede solo dopo la decisione del SUPERVISORE (coerente con ALA R2). Il piano resta di LIMET (PLAN/SPEC/TASK_LIST/TEST_PLAN approvati dal SUPERVISORE); nessun file di piano ECC e nessuna scrittura in `.claude/plans` (§1.2, C06). Gli agenti ECC si **riusano, non si riscrivono**: il corpo del testo resta invariato (hash e origine nel ledger); cambiano solo il frontmatter (prefisso `ecc-`, `tools` senza `Bash` libero, modello) e un breve overlay HELIOS per il ruolo (§2.3: nessun hook, nessuna memoria/`.claude/plans`). Il testo si modifica solo se l'A/B (C16) o la revisione lo richiedono, con motivo registrato.
3. **Skill ECC** (311, dal registro): ogni skill è **associata ai ruoli** che la caricano (campo `skills:` / rules per ruolo, C-rules per ruolo) o esclusa con motivo; nessuna skill è caricata globalmente.

Deliverable richiesto a LIMET: una **matrice catena** `docs/chain-matrix.md` (generata e verificata da H-204) con colonne *step · ruolo LIMET · agenti ECC assegnati · skill ECC assegnate · tools · modello · input · output · gate*, in cui **ogni** agente e skill del registro compare con decisione *porta / porta con adattamento / escludi + motivo*. Nessuna riga «da classificare» è ammessa a fine Fase 2. **Skill a uso su richiesta (SUPERVISORE):** non si verificano in anticipo tutte le 311 licenze; una skill è *disponibile* nel registro e viene *attivata* solo quando serve a un ruolo. Al momento dell'attivazione si verifica (e si registra nel ledger) la licenza, si rivedono i segnali di rischio e si assegna il ruolo; senza questo passo la skill non entra in nessun prompt (C05, C07). Il workflow `limet-change.js` legge la matrice per comporre i prompt dei ruoli; l'A/B (C16) misura il beneficio degli agenti/skill ECC rispetto al ruolo base.

Regola di tool: i ruoli non-runner non hanno `Bash` libero; solo il developer (e l'integrator) usano `Bash(git add|commit|status|diff *)` con pattern ristretti (FO1 §developer.md), mai `git push*` / `git merge*` su main. I reviewer, analyst, test-reader, validator e risk-assessor non hanno `Bash`.

### 3.6 Forma di consegna: plugin Claude Code `limet-agents` **[F/P]**
**[F]** HELIOS è l'evoluzione di `limet-agents`, che ALA definisce un **plugin Claude Code** (`ALA` §2, R22, R23, R24: `.claude-plugin/plugin.json`, `agents/`, `workflows/limet-change.js` come Dynamic Workflow nativo, `skills/`, `settings.example.json`, installer `limet-agents.ps1/.sh` con `claude plugin marketplace add` + `claude plugin install`; facoltativo e retrocompatibile con LIMET classico). **[P]** Il plugin è la **forma di distribuzione del core agentico** di HELIOS, non un'alternativa: i sorgenti stanno nel monorepo in `agents/plugin/` (manifest, agenti/ruoli, workflow, skill `ecc-*` allowlist, installer) e il plugin installato è ciò che Claude Code carica. Runner Python, `api/` e `ui/` restano componenti esterni al plugin (il workflow chiama il runner via contratto §3.3); la dashboard HELIOS estende `REPORT_METRICHE.html` (R26). R24: nessun obbligo di installare il plugin per usare LIMET classico. **[N]** Sintassi effettiva, percorso e invocazione dei Dynamic Workflow nella versione installata (2.1.311): prova in H-407 (ALA §Verifica 4). **[P]** Repository: monorepo HELIOS (ALA prevedeva un repo proprio; deciso in §8 Q7).

### 3.7 Dashboard **[P]** (`..\04` §8)
Viste: Portfolio, LIMET, HELIOS (DAG workflow, gate, log runner, metriche R25–R27, ledger asset), CEREBRO (collection, stato Qdrant/Ollama, ricerca read-only), Criticità, Permessi agenti, Registro chiamate LLM (C05). Sicurezza C23–C25, grafica C26.

### 3.8 Base di conoscenza per gli agenti (Q9) **[P]**
**Decisione del SUPERVISORE (2026-10-29):** durante l'esecuzione gli agenti consultano una base di conoscenza in Qdrant, tramite il modulo `cerebro/` (MCP `limet_search` o CLI, §3.1), mai direttamente. Due livelli:

| Livello | Collection | Contenuto | Chi la crea |
|---|---|---|---|
| Progetto | `CRB_<slug>` | documenti LIMET del progetto in lavorazione (`docs-limet/`: SPEC, PLAN, convenzioni, decisioni) | l'utente, con `limet-index` come oggi |
| HELIOS | `HLS_*` (nome distinto) | asset ECC portati e matrice catena (§3.5), per recuperare la skill giusta per ruolo senza caricarle tutte nel prompt (C05) | solo in fase di setup (H-803), con autorizzazione esplicita del SUPERVISORE |

Chi usa cosa: analyst, validator e risk-assessor, developer e reviewer usano CEREBRO e Graphify; il **tester** usa CEREBRO solo per documenti (SPEC, convenzioni) e **mai** Graphify né codice del developer (R5: indipendenza); l'integrator usa solo Graphify; test-reader e runner nessuno dei due.

Regole (valgono per tutti i ruoli, in `_common`, H-611): (1) **sola lettura**: nessun agente scrive, indicizza o cancella; l'indicizzazione è un'azione dell'utente o dello script di setup; (2) le collection esistenti non si toccano; (3) nelle collection consultate dal tester non c'è codice del developer; (4) al ruolo tornano pochi estratti sintetici con fonte, per contenere i token (tetto 100 USD); (5) se Qdrant o Ollama non rispondono il ruolo prosegue **senza** contesto e lo segnala nel report, senza fermare il workflow; (6) ogni consultazione è registrata in `events.jsonl` (collection, query, esito).

---

## 4. Piano di implementazione per fasi

Effort **[I]** in persona-giorni, da `..\05` §4. Percorso critico: 1→3→4→5→9→10→12.

| Fase | Obiettivo | Deliverable | Dipende da | Criteri di accettazione | Rischi residui |
|---|---|---|---|---|---|
| 1 Discovery (2–3) | Congelare requisiti | ADR fondativo, costituzione HELIOS, scelta runtime | — | ADR approvato dal SUPERVISORE; Q1–Q9 (§8) chiuse | titolare eccezione di scope non definito |
| 2 Inventario ECC (4–6) | Leggere il 100% degli asset | script inventario, tabella per-asset, `ecc-ledger.json` (C07, C15) | 1 | 0 asset in allowlist non letti; `chain-matrix.md` (H-204) completa per 71 agenti + 311 skill | licenze dubbie (§7.1) |
| 3 Contratti e consolidamento (11–16) | Monorepo, shim, CI | layout §3.1, schemi artefatti, `compat/`, suite di parità, CI a matrice | 1 | parità verde su Windows e Unix; installazioni originali invariate | remoto CEREBRO **[N]**; segreti in `projects.json` |
| 4 **PoC go/no-go** (8–12) | Verificare la fattibilità | 3 ruoli + runner + 1 gate + 1 asset ECC, **consegnati come plugin Claude Code** (H-407) | 1, 3 | criteri `..\05` §5 (1–10) | piano B: wrapper senza copia (`..\04` §5) |
| 5 Porting skill/rules (5–8) | Portare asset in allowlist | asset `ecc-*` + test agnosticità + A/B | 2, 3, 4 | CI verde; metrica ≥ baseline (C16) | beneficio non dimostrato → asset escluso |
| 6 Catena agenti (10–15) | Tutti i ruoli ALA §3 come definizioni agente | analyst, developer×N, tester×M, integrator, test-reader, reviewer, validator, metrics-observer, e2e-designer/reader, risk-assessor, `_common` (H-601–H-612; agenti e skill ECC assegnati ai ruoli secondo `chain-matrix.md`) | 4, 5 | test statico: nessun `Bash` libero (reviewer/analyst/test-reader/validator/risk-assessor senza `Bash`; git ristretto per developer/integrator); ogni ruolo ha input/output/«non fa mai» da ALA §3 | benefici dei ruoli aggiuntivi da dimostrare con A/B |
| 7 Workflow completo (8–12) | Flusso di controllo `limet-change.js` + scheduler, stato, gate, integrazione | H-701, H-703–H-712 | 4, 6 | tutti gli scenari ALA V1–V14 verdi in sandbox; 0 collisioni con file di piano ECC | formato Dynamic Workflow non verificato **[N]** (H-407) |
| 8 Hook e comandi (2–4) | Analizzare gli hook ECC e replicare in modo sicuro solo quelli utili | `ecc-hooks-analysis.md`, hook HELIOS propri (o nessuno) | 4 | ogni hook ECC ha una decisione motivata; 0 scritture fuori workspace; nessun codice hook ECC eseguito | — |
| 8b Dashboard (15–25) | UI + API | `api/`, `ui/`, test di contratto e sicurezza | 3, 4 | viste in sola lettura complete; azioni abilitate dopo test C23–C24; budget C26 | dipendenze npm (C25) |
| 9 E2E (6–10) | Scenari ALA V1–V24 su 3 OS | suite E2E, `E2E_VERIFICATION.md` | 5–8 | scenari passati su ogni OS | costo token |
| 10 Hardening (4–6) | Sicurezza e audit | log immutabili, sandbox, SBOM, AgentShield pinnato | 9 | audit completato | — |
| 11 Documentazione (3–4) | Manuali e NOTICE | manuali, NOTICE, runbook | 9 | revisione approvata | — |
| 12 Rilascio (3–5) | Rollout progressivo | pilota, rollback | 10, 11 | pilota con metriche; parere legale ottenuto | progetto pilota non scelto |

Totale **[I]** 69–107 persona-giorni.

---

## 5. Backlog di task atomici

Convenzione: percorsi relativi alla radice del nuovo repo HELIOS **[P]**. Ogni task ha test collegati (§6) e criterio di done. **Ogni task, oltre al suo criterio di done, richiede a fine sviluppo la valutazione dei rischi di §2.3 n. 7 (C28): senza di essa il task non è concluso.** Le fasi 4–5 non iniziano senza esito positivo della precedente.

### Fase 1
- **H-101** ADR fondativo: owner, perimetro ECC, pin `25278e7`, revoca. File: `docs/adr/0001-helios.md`. Done: approvato dal SUPERVISORE.
- **H-102** Costituzione HELIOS con i non-negoziabili §2.3. File: `docs/CONSTITUTION.md`. Done: approvata; richiamata da ogni ruolo; include la regola di valutazione rischi a fine task (§2.3 n. 7, C28), valida per chi sviluppa HELIOS con LIMET puro.
- **H-103** Conferma runtime (Q4) e struttura dei test. File: `docs/adr/0002-runtime.md`. Done: decisione scritta.

### Fase 2
- **H-201** Script di inventario ECC (legge il 100% dei file, conteggi, frontmatter). File: `agents/tools/inventory_ecc.py`. Test: T-201. Done: tabella per-asset generata (punto di partenza: `appendici\REGISTRO_SKILLS_AGENTI_ECC.md`; si compila la colonna *Decisione* per ogni skill/agente: porta / porta con adattamento / escludi + motivo), conteggi riconciliati con quelli dichiarati.
- **H-202** Ledger licenze + allowlist MIT. File: `agents/assets/ecc-ledger.json`, `NOTICE`. Test: T-202. Done: ogni asset ha hash/licenza/origine; asset non MIT esclusi.
- **H-203** Revisione manuale degli asset in allowlist (checklist). File: `docs/ecc-review.md`. Done: tutti firmati.
- **H-204** Matrice catena `docs/chain-matrix.md` (§3.5): per **tutti i 71 agenti e le 311 skill** ECC assegnazione a step/ruolo della catena (insieme ai ruoli LIMET) o esclusione motivata; compila la colonna *Decisione* del registro. File: `docs/chain-matrix.md`, `agents/assets/chain-matrix.json`. Test: T-204. Done: 0 righe «da classificare» (le skill non necessarie restano *disponibili, non attivate*); ogni agente/skill **attivato** ha ruolo, modello, tools e licenza nel ledger, verificata al momento dell'attivazione; approvata dal SUPERVISORE.

### Fase 3
- **H-301** Import di LIMET e CEREBRO in `limet/` e `cerebro/` (storia in sottocartelle). Azione **dell'utente** (commit/push). Done: originali invariati.
- **H-302** Segreti fuori dal repo: `projects.example.json`, `.gitignore` per `cache/` e dati Qdrant, scansione segreti in CI. Test: T-302. Done: 0 segreti.
- **H-303** Definizione dei contratti §3.3 come schemi JSON. File: `agents/contracts/*.schema.json`. Test: T-303.
- **H-304** Shim `compat/` per `cerebro.sh`, `query_qdrant.py`, `ingest_docs.py`, `limet.ps1/.sh`, hook/MCP: stessi argomenti, porte, nomi collection. Test: T-304. Done: parità su Windows e Unix.
- **H-305** Test dei confini tra moduli. File: `tests/test_module_boundaries.py`. Test: T-305.
- **H-306** CI a matrice (windows/ubuntu/macos). File: `.github/workflows/ci.yml`. Test: T-306.
- **H-307** Correzione del percorso CEREBRO nella copia interna di `CLAUDE.md`. Done: nessun riferimento a `C:\Progetti\CEREBRO`.

### Fase 4 (PoC)
- **H-401** Runner con allowlist, senza rete, log con hash, nessun commit/push. File: `agents/runner/`. Test: T-401, T-402.
- **H-402** Ruoli PoC: analyst, developer, reviewer (`tools:` senza Bash). File: `agents/roles/*.md`. Test: T-403.
- **H-403** Gate umano meccanico sul PLAN. File: `agents/workflow/gate.py`. Test: T-404.
- **H-404** Policy permessi con pavimento hard-deny. File: `agents/policy/policy.json`, `agents/policy/floor.json`. Test: T-405.
- **H-405** Un asset ECC portato (`ecc-*`) + A/B vs baseline. Test: T-406.
- **H-406** Rapporto go/no-go sui criteri 1–10 di `..\05` §5. File: `docs/poc-report.md`.
- **H-407** Prova del plugin Claude Code (ALA §Verifica 4, V13): `agents/plugin/.claude-plugin/plugin.json` + `workflows/limet-change.js` minimo; installazione in repo sandbox con `claude plugin marketplace add` / `install`; verificare registrazione e invocazione reale del workflow, 2 agenti in parallelo, gate umano, ripresa; verificare inoltre se Claude Code copia il plugin in una propria cache (comportamento a cartella sorgente rimossa/spostata) e documentare che runner, policy, `cerebro/` e `limet/` restano comunque necessari fuori dal plugin. File: `agents/plugin/`. Test: T-407. Done: workflow riconosciuto e invocato in una sessione Claude Code reale (non basta il controllo sintattico, ALA R23); altrimenti piano B documentato in `docs/poc-report.md`.

### Fase 5–7
**Ordine di esecuzione e dipendenze (le voci sotto non sono in ordine numerico; vale questo schema).** I task di questa sezione dipendono dal PoC (Fase 4 con esito go) e vanno eseguiti in questo ordine:

| Gruppo | Task | Dipende da |
|---|---|---|
| A. Contratti e strumenti di base | H-308, H-409, H-408, H-611 | H-303, H-401 |
| B. Asset ECC | H-501, H-502, H-503 | H-204, H-308 |
| C. Ruoli (ognuno richiama `_common`) | H-602–H-610, H-601 | gruppo A, H-402 |
| D. Workflow | H-701, H-703–H-711, H-702 | gruppi A e C, H-407 |
| E. Valutazione rischi di prodotto | H-612 | H-308 (schema `risk`), H-701, H-706 |
| F. Modifiche documentali a LIMET | H-712 | nessuna: da pianificare con dipendenza esplicita dal plugin (ALA §7) |

Chi sviluppa HELIOS applica comunque la regola di valutazione rischi (§2.3 n. 7) a **ogni** task già dalla Fase 1, anche prima che H-612 esista.

- **H-501** Test di agnosticità statico (no hook/tool/memoria/`.claude/plans`). Test: T-501.
- **H-502** Porting per ruolo di rules e skill in allowlist (prefisso `ecc-`, rules per ruolo). Test: T-502.
- **H-503** Test: nessun file di piano ECC scritto fuori dai documenti LIMET (le proposte vanno in `ecosystem/proposals/`) e nessuna collisione di nomi. Test: T-503.
- **H-601** Reviewer specializzati (java, security, ecc.) ECC riusati con overlay minimo (frontmatter senza Bash libero, corpo invariato), come lenti `ecc-*` (estende H-607). Test: T-403.
- **H-602** Ruolo `analyst` (opus): legge PLAN/SPEC/TASK_LIST/TEST_PLAN, usa Graphify e CEREBRO, produce `plan.json` (task dev/test, file, dipendenze, requisiti, stima, rischio conflitto), propone split oltre il cap (`files ≤ 6`, `diffLines ≤ 300`); non inventa requisiti e si ferma su documenti incompleti (ALA R2). File: `agents/plugin/agents/analyst.md`. Test: T-601. Done: output conforme allo schema `plan.json`.
- **H-603** Ruolo `developer` ×N (sonnet): un task per worktree e ramo `task/NNNN-Tn`, commit sul ramo, elenco file in formato «Registro modifiche»; mai build/test, mai file fuori perimetro, mai push (R5); consulta CEREBRO e Graphify in sola lettura per convenzioni e dipendenze dei file che tocca (§3.8). File: `agents/plugin/agents/developer.md`. Test: T-602.
- **H-604** Ruolo `tester` ×M (sonnet): traduce il TEST_PLAN in codice **senza vedere il diff del developer**, `self_review` §5.1, mai esecuzione (R5); consulta CEREBRO solo per documenti (SPEC, convenzioni di test), **mai** Graphify né codice del developer (§3.8). File: `agents/plugin/agents/tester.md`. Test: T-603.
- **H-605** Ruolo `integrator` (sonnet): integra task+test su `integration/NNNN`; conflitti banali risolti, gli altri rimandati; mai merge su `main` (R6, R21). File: `agents/plugin/agents/integrator.md`. Test: T-604.
- **H-606** Ruolo `test-reader` (haiku): classifica ogni fallimento (codice / test / ambiguità contratto / ambiente) con estratti reali di log in `reader-<run>.json` (R11). File: `agents/plugin/agents/test-reader.md`. Test: T-605.
- **H-607** Ruolo `reviewer` (opus, modello ≠ developer): checklist su SPEC+diff, findings con severità/file/riga, non corregge (R13); consulta CEREBRO e Graphify in sola lettura per convenzioni e impatto del diff (§3.8). File: `agents/plugin/agents/reviewer.md`. Test: T-606.
- **H-608** Ruolo `validator` (opus): per ogni requisito `conforme / non conforme / non determinabile` con `evidence[]`; `UNDETERMINED` di default; segnala requisiti senza test e test senza requisito (R14). File: `agents/plugin/agents/validator.md`. Test: T-607.
- **H-609** Ruolo `metrics-observer` + collector/aggregatore deterministico: l'agente interpreta solo metriche già aggregate (R25–R27, ALA §3). File: `agents/plugin/agents/metrics-observer.md`, `agents/metrics/`. Test: T-608.
- **H-610** Ruoli `e2e-designer` / `e2e-reader` (Playwright, eseguiti dal runner, reader con trace/screenshot, DB in sola lettura) (R16). File: `agents/plugin/agents/e2e-*.md`. Test: T-609.
- **H-612** Valutazione rischi post-task nel **prodotto** HELIOS (C28; la regola per lo sviluppo con LIMET puro è §2.3 n. 7, già valida da subito, e questo task la automatizza): al passaggio di un task a VERIFIED il workflow lancia `risk-assessor` (sola lettura, senza `Bash`, modello ≠ developer). Controlla: regressioni su moduli/file dipendenti (Graphify), casi limite non coperti dai test, sicurezza e dati, prestazioni, compatibilità, impatto sui task successivi. Output `ecosystem/risks/RISK-<task>.json` (schema in H-308). Se trova rischi: (1) li segnala al SUPERVISORE tramite `QUESTIONS.md` e `state.json`, ciascuno con severità, evidenza (file/riga/test) e **almeno due opzioni risolutive** con pro/contro e raccomandazione; (2) **non corregge in autonomia**; (3) un rischio alto o critico blocca **solo i task dipendenti** (come «fermati e chiedi», R8) finché il SUPERVISORE non decide; medio/basso sono registrati e riportati in `REPORT_FINALE.md`. Se non ne trova, elenca i controlli eseguiti. Il costo rientra in `limits.json`. File: `agents/plugin/agents/risk-assessor.md`. Test: T-611. Done: ogni task VERIFIED ha un `RISK-<task>.json`; nessuna segnalazione senza evidenza e senza opzioni.
- **H-611** `_common` (regole condivise dei ruoli, «fermati e chiedi» R8, limiti, formato output, **regole d'uso della base di conoscenza §3.8**: sola lettura, estratti sintetici, degrado senza contesto se Qdrant/Ollama non rispondono) e rules per ruolo. File: `agents/plugin/agents/_common.md`. Test: T-610. Done: ogni ruolo lo richiama; nessun ruolo ha `Bash` libero (solo git con pattern ristretti per developer/integrator); reviewer, analyst, test-reader, validator, risk-assessor senza `Bash`.
- **H-308** Contratti della catena: schemi JSON `plan`, `result`, `findings`, `verdict`, `reading`, `merge`, `risk` (campi: task, severità, descrizione, evidenza, opzioni[≥2] con pro/contro, raccomandazione, stato decisione SUPERVISORE); formato `state.json` e `events.jsonl`; stati ALA R17 e mappatura sui 4 stati LIMET (estende H-303). File: `agents/contracts/`. Test: T-308. Done: ogni output di ruolo validato dallo schema.
- **H-408** Scheduler a funzioni pure (R4): `ready`, onde a file disgiunti, `budget = min(maxAgents, slot CPU − build, costo residuo / costo medio)`, priorità per cammino critico/rischio/stima. È in JavaScript perché chiamato dal workflow (ALA R4: `node --test`); deciso in Q8 (opzione A). File: `agents/plugin/scheduler/limet-scheduler.js`. Test: T-408. Done: test deterministici su onde, dipendenze, budget.
- **H-409** Skill `limet-runner` (`build | test --select | lint | mutation | coverage-diff | e2e | all`): verifica SHA e worktree pulito, log su disco, redazione segreti, `result.json` con `INFRA_ERROR`; nel contesto LLM entra solo il riepilogo (R10). Wrapper sul runner H-401. File: `agents/plugin/skills/limet-runner/`. Test: T-409.
- **H-701** Workflow `limet-change.js`: struttura nativa, lettura e validazione input (rifiuta se PLAN non approvato o mancano SPEC/TASK_LIST/TEST_PLAN, R1), stati, retry N, «fermati e chiedi». Test: T-701.
- **H-703** Gate umano sul grafo (task, onde, stima tempo/costo, split; conferma / senza split / fermati) registrato in `state.json`/`events.jsonl` (R3). Test: T-703.
- **H-704** Orchestrazione per onde con worktree e rami dedicati, parallelismo entro `limits.json` (R4, R5, R20). Test: T-704.
- **H-705** Integrazione su `integration/NNNN` e avanzamento del task solo dopo run del runner sullo stesso SHA; merge su `main` mai automatico (R6, R21). Test: T-705.
- **H-706** Stato ed eventi: `state.json` scritto solo dal workflow con schema e validazione; `events.jsonl` senza prompt integrali né segreti; `STALE` se un merge tocca i file senza ri-test (R17). Test: T-706.
- **H-707** «Fermati e chiedi» non globale (blocca solo dipendenti e file in comune) + `QUESTIONS.md`; `maxAttempts` 3, mai un terzo tentativo cieco (R8, R9, R12). Test: T-707.
- **H-708** Budget, checkpoint e ripresa (`resumeFromRunId`), stop controllato a esaurimento (R20). Test: T-708.
- **H-709** Report `REPORT_NOTTURNO.md`, `REPORT_CONFORMITA.md`, `REPORT_FINALE.md` (con sezione rischi post-task e decisioni del SUPERVISORE, H-612); mai «completato» se non lo è (R18). Test: T-709.
- **H-710** Permessi a livello di tool (`deny` su `./mvnw*`, `git push*`, `git merge*`), hook di log `PreToolUse` HELIOS propri, verifica dell'effettiva applicazione per ruolo/worktree (R19; estende H-404). Test: T-710.
- **H-711** Gate di merge R13 (build, test del task, regressione, statica, copertura differenziale, mutation, invarianti, reviewer, SHA) con `exceptions.json` firmato dall'umano; flusso bug-fix R15 (regressione fallisce prima del fix). Test: T-711.
- **H-712** Modifiche preliminari a LIMET M.1–M.9 (documentali IT/EN) tramite ciclo LIMET e gate; re-index CEREBRO a cura dell'utente (ALA §7). Test: T-712 (verifica documentale).
- **H-702** Installer `limet-agents.ps1/.sh` (`init|update`, idempotente, modello `limet.ps1`): installa il plugin con `claude plugin marketplace add` + `install --scope project`, copia senza sovrascrivere `limits.json`, `runner.config.json`, `.mcp.json` dagli esempi; plugin facoltativo (R24), nessuna modifica a `~/.claude`. File: `agents/plugin/installer/`. Test: T-702. Done: idempotenza verificata su Windows e Unix, LIMET classico invariato senza plugin.

### Fase 8
- **H-800** Analisi degli hook ECC (27 hook, 8 eventi, `..\01` §6): per ciascuno documentare cosa fa, quale rischio comporta e se serve a HELIOS. Output: `docs/ecc-hooks-analysis.md` con decisione per hook (scartare / replicare). Done: ogni hook ha una decisione motivata approvata dal SUPERVISORE. L'analisi si può anticipare in Fase 2 insieme all'inventario.
- **H-800b** Replica sicura degli hook ritenuti utili, come **hook HELIOS propri** (codice nostro, mai codice ECC eseguito): cross-platform (C13), nessuna rete, nessuna scrittura fuori workspace, nessun invio a LLM non registrato (C05), allowlist e log, test dedicati. Dipende da H-800. Test: da definire per ogni hook replicato (stesso schema di §6).
- **H-801** Registro chiamate LLM aggiuntive (C05) + test CI. File: `agents/policy/llm-registry.json`. Test: T-801.
- **H-802** Wrapper AgentShield (pin, hash, SBOM, senza rete, senza `--opus`). Test: T-802.
- **H-803** Setup della base di conoscenza `HLS_*` (Q9, §3.8): script che, solo su richiesta e autorizzazione esplicita del SUPERVISORE, crea la collection `HLS_*` e vi indicizza gli asset ECC portati (da `ecc-ledger.json`) e la matrice catena; verifica che Qdrant/Ollama siano attivi, che il nome non collida con collection esistenti (`CRB_*`, `project_docs`) e non modifica né cancella nulla di esistente; ripetibile e idempotente. File: `agents/tools/setup_hls_kb.py`. Test: T-803. Done: collection creata solo con autorizzazione; query di controllo restituisce l'asset atteso; collection esistenti invariate. Dipende da H-202, H-204.
- **H-804** Accesso degli agenti alla base di conoscenza: il workflow passa ai ruoli ammessi (§3.8) le sole chiamate MCP/CLI in sola lettura, registra ogni consultazione in `events.jsonl` e gestisce l'assenza di Qdrant/Ollama come «senza contesto + segnalazione». Test: T-804. Done: il tester non ottiene Graphify né codice del developer; nessun ruolo ha scritture verso Qdrant. Dipende da H-611, H-701, H-803.

### Fase 8b
- **H-8b01** `api/`: loopback, token per avvio, allowlist Host/Origin, CSRF, CSP, limite body. Test: T-8b01..T-8b04.
- **H-8b02** Read model derivato con cache rigenerabile. Test: T-8b05.
- **H-8b03** `ui/`: viste Portfolio, LIMET, HELIOS, CEREBRO, Criticità. Test: T-8b06.
- **H-8b04** Vista Permessi agenti (conferma, motivazione, audit, rollback). Test: T-405, T-8b07.
- **H-8b05** Gate approvabili solo da sessione umana; la UI non esegue build/test/git. Test: T-8b08.
- **H-8b06** Grafica SVG/Canvas (DAG, timeline, heatmap), accessibilità WCAG 2.1 AA, budget prestazioni. Test: T-8b09.
- **H-8b07** Governance dipendenze npm: lockfile, licenze, audit, SBOM. Test: T-8b10.
- **H-8b08** Sostituzione del launcher :8799 tramite shim. Test: T-304.

### Fase 9–12
- **H-901** Scenari E2E ALA V1–V24 (scaffold E2E, progetto pilota «biblioteca») sulla catena completa, 3 OS. Documento: `E2E_VERIFICATION.md`. Test: V1–V24 di ALA §9 (comandi per scenario in `TEST_PLAN.md`).
- **H-902** README e troubleshooting del plugin (installazione, configurazione, cache, errori comuni). File: `agents/plugin/README.md`.
- **H-1001** Hardening: log immutabili, sandbox, SBOM, audit.
- **H-1101** Manuali, NOTICE, runbook.
- **H-1201** Rollout sul progetto pilota con metriche, rollback, parere legale.

---

## 6. Strategia di test

**Regola:** nessuna esecuzione autonoma di build/test; i comandi sono forniti all'utente, che li esegue e comunica l'esito. Per ogni test sono indicati classe e metodo, con **entrambi** i comandi di riesecuzione.
**Framework [P]** (da confermare in H-103): backend/core con `pytest` (dalla radice del repo, `python -m pytest`), UI con `vitest` (`npm --prefix ui test`). I nomi sotto sono **proposti**: i file non esistono ancora **[N]**.

| ID     | Cosa verifica                                                                                                                                                                   | Classe (file)                                                       | Comando classe                                                               | Comando metodo                                                                                              |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------- | ---------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| T-201  | L'inventario conta il 100% dei file e riconcilia i totali                                                                                                                       | `TestInventory` (`tests/test_inventory.py`)                         | `python -m pytest tests/test_inventory.py::TestInventory`                    | `python -m pytest tests/test_inventory.py::TestInventory::test_conteggi_completi`                           |
| T-202  | Il ledger ha hash/licenza/origine per ogni asset e rifiuta asset non MIT                                                                                                        | `TestLedger` (`tests/test_ledger.py`)                               | `python -m pytest tests/test_ledger.py::TestLedger`                          | `python -m pytest tests/test_ledger.py::TestLedger::test_solo_mit_in_allowlist`                             |
| T-204  | Ogni agente/skill del registro ha decisione e (se portato) ruolo/step nella matrice catena; nessun «da classificare»; nessun agente di piano ECC                                | `TestChainMatrix` (`tests/test_chain_matrix.py`)                    | `python -m pytest tests/test_chain_matrix.py::TestChainMatrix`               | `python -m pytest tests/test_chain_matrix.py::TestChainMatrix::test_nessun_da_classificare`                 |
| T-302  | Nessun segreto nel repo; `cache/` e dati Qdrant ignorati                                                                                                                        | `TestSecrets` (`tests/test_secrets.py`)                             | `python -m pytest tests/test_secrets.py::TestSecrets`                        | `python -m pytest tests/test_secrets.py::TestSecrets::test_nessun_segreto_versionato`                       |
| T-303  | Gli artefatti rispettano gli schemi dei contratti                                                                                                                               | `TestContracts` (`tests/test_contracts.py`)                         | `python -m pytest tests/test_contracts.py::TestContracts`                    | `python -m pytest tests/test_contracts.py::TestContracts::test_richiesta_runner_valida`                     |
| T-304  | Gli shim danno gli stessi esiti dei comandi legacy                                                                                                                              | `TestCompatParity` (`tests/test_compat_parity.py`)                  | `python -m pytest tests/test_compat_parity.py::TestCompatParity`             | `python -m pytest tests/test_compat_parity.py::TestCompatParity::test_query_qdrant_parita`                  |
| T-305  | Nessun riferimento fuori dai contratti tra moduli                                                                                                                               | `TestModuleBoundaries` (`tests/test_module_boundaries.py`)          | `python -m pytest tests/test_module_boundaries.py::TestModuleBoundaries`     | `python -m pytest tests/test_module_boundaries.py::TestModuleBoundaries::test_cerebro_non_importa_helios`   |
| T-306  | La CI definisce la matrice dei 3 OS con la stessa suite                                                                                                                         | `TestCiMatrix` (`tests/test_ci_matrix.py`)                          | `python -m pytest tests/test_ci_matrix.py::TestCiMatrix`                     | `python -m pytest tests/test_ci_matrix.py::TestCiMatrix::test_tre_os_stessa_suite`                          |
| T-401  | Il runner rifiuta operazioni fuori allowlist                                                                                                                                    | `TestRunnerAllowlist` (`tests/test_runner_allowlist.py`)            | `python -m pytest tests/test_runner_allowlist.py::TestRunnerAllowlist`       | `python -m pytest tests/test_runner_allowlist.py::TestRunnerAllowlist::test_rifiuta_operazione_non_ammessa` |
| T-402  | Il log ha hash riproducibile e il runner non fa commit/push né usa la rete                                                                                                      | `TestRunnerLog` (`tests/test_runner_log.py`)                        | `python -m pytest tests/test_runner_log.py::TestRunnerLog`                   | `python -m pytest tests/test_runner_log.py::TestRunnerLog::test_hash_riproducibile`                         |
| T-403  | Nessun ruolo/asset portato ha `Bash` libero (reviewer/analyst/test-reader/validator/risk-assessor senza `Bash`; solo git ristretto per developer/integrator)                                  | `TestRolesNoBash` (`tests/test_roles_no_bash.py`)                   | `python -m pytest tests/test_roles_no_bash.py::TestRolesNoBash`              | `python -m pytest tests/test_roles_no_bash.py::TestRolesNoBash::test_nessun_bash`                           |
| T-404  | Il gate sul PLAN blocca l'avanzamento senza approvazione umana                                                                                                                  | `TestGate` (`tests/test_gate.py`)                                   | `python -m pytest tests/test_gate.py::TestGate`                              | `python -m pytest tests/test_gate.py::TestGate::test_blocca_senza_approvazione`                             |
| T-405  | Il pavimento hard-deny non è rimovibile (neanche dalla UI/API) e la policy è auditata                                                                                           | `TestPolicyFloor` (`tests/test_policy_floor.py`)                    | `python -m pytest tests/test_policy_floor.py::TestPolicyFloor`               | `python -m pytest tests/test_policy_floor.py::TestPolicyFloor::test_deny_mvnw_non_rimovibile`               |
| T-406  | L'asset portato non peggiora le metriche rispetto al baseline                                                                                                                   | `TestAbMetrics` (`tests/test_ab_metrics.py`)                        | `python -m pytest tests/test_ab_metrics.py::TestAbMetrics`                   | `python -m pytest tests/test_ab_metrics.py::TestAbMetrics::test_soglia_go_no_go`                            |
| T-501  | Gli asset sono solo prompt (no hook/tool/memoria/`.claude/plans`)                                                                                                               | `TestAgnostic` (`tests/test_agnostic.py`)                           | `python -m pytest tests/test_agnostic.py::TestAgnostic`                      | `python -m pytest tests/test_agnostic.py::TestAgnostic::test_asset_solo_prompt`                             |
| T-502  | Le rules sono caricate per ruolo, non globali; le 4 `common/` assenti                                                                                                           | `TestRulesPerRole` (`tests/test_rules_per_role.py`)                 | `python -m pytest tests/test_rules_per_role.py::TestRulesPerRole`            | `python -m pytest tests/test_rules_per_role.py::TestRulesPerRole::test_quattro_common_assenti`              |
| T-503  | Nessun file di piano fuori dai documenti LIMET (solo proposte in `ecosystem/proposals/`); prefisso `ecc-` ovunque                                                               | `TestNaming` (`tests/test_naming.py`)                               | `python -m pytest tests/test_naming.py::TestNaming`                          | `python -m pytest tests/test_naming.py::TestNaming::test_nessun_file_di_piano_ecc`                          |
| T-701  | Stati, retry limitato e «fermati e chiedi» nel workflow                                                                                                                         | `TestWorkflowStates` (`tests/test_workflow_states.py`)              | `python -m pytest tests/test_workflow_states.py::TestWorkflowStates`         | `python -m pytest tests/test_workflow_states.py::TestWorkflowStates::test_retry_poi_stop`                   |
| T-407  | Manifest e struttura del plugin valide (`plugin.json`, workflow sotto `workflows/`); registrazione e invocazione reale verificate dall'utente in sessione Claude Code (manuale) | `TestPluginManifest` (`tests/test_plugin_manifest.py`)              | `python -m pytest tests/test_plugin_manifest.py::TestPluginManifest`         | `python -m pytest tests/test_plugin_manifest.py::TestPluginManifest::test_manifest_e_workflow_presenti`     |
| T-308  | Ogni output di ruolo (`plan`, `result`, `findings`, `verdict`, `reading`, `merge`, `state`) è validato dallo schema                                                             | `TestChainContracts` (`tests/test_chain_contracts.py`)              | `python -m pytest tests/test_chain_contracts.py::TestChainContracts`         | `python -m pytest tests/test_chain_contracts.py::TestChainContracts::test_verdict_senza_evidence_invalido`  |
| T-408  | Lo scheduler calcola onde a file disgiunti, rispetta dipendenze e budget                                                                                                        | `SchedulerTest` (`agents/plugin/scheduler/limet-scheduler.test.js`) | `node --test agents/plugin/scheduler/limet-scheduler.test.js`                | `node --test --test-name-pattern="budget" agents/plugin/scheduler/limet-scheduler.test.js`                  |
| T-409  | La skill runner verifica SHA/worktree, redige segreti, produce `INFRA_ERROR` e riepilogo piccolo                                                                                | `TestRunnerSkill` (`tests/test_runner_skill.py`)                    | `python -m pytest tests/test_runner_skill.py::TestRunnerSkill`               | `python -m pytest tests/test_runner_skill.py::TestRunnerSkill::test_sha_diverso_rifiutato`                  |
| T-601  | analyst: output conforme a `plan.json`, split oltre cap, stop su documenti incompleti                                                                                           | `TestAnalystRole` (`tests/test_role_analyst.py`)                    | `python -m pytest tests/test_role_analyst.py::TestAnalystRole`               | `python -m pytest tests/test_role_analyst.py::TestAnalystRole::test_split_oltre_cap`                        |
| T-602  | developer: perimetro file, nessun build/test/push, formato «Registro modifiche»                                                                                                 | `TestDeveloperRole` (`tests/test_role_developer.py`)                | `python -m pytest tests/test_role_developer.py::TestDeveloperRole`           | `python -m pytest tests/test_role_developer.py::TestDeveloperRole::test_git_solo_pattern_ammessi`           |
| T-603  | tester: input senza diff del developer; nessuna esecuzione                                                                                                                      | `TestTesterRole` (`tests/test_role_tester.py`)                      | `python -m pytest tests/test_role_tester.py::TestTesterRole`                 | `python -m pytest tests/test_role_tester.py::TestTesterRole::test_input_senza_diff`                         |
| T-604  | integrator: conflitti banali risolti, semantici rimandati, mai merge su main                                                                                                    | `TestIntegratorRole` (`tests/test_role_integrator.py`)              | `python -m pytest tests/test_role_integrator.py::TestIntegratorRole`         | `python -m pytest tests/test_role_integrator.py::TestIntegratorRole::test_conflitto_semantico_rimandato`    |
| T-605  | test-reader: classifica 4 categorie con estratti reali di log                                                                                                                   | `TestReaderRole` (`tests/test_role_reader.py`)                      | `python -m pytest tests/test_role_reader.py::TestReaderRole`                 | `python -m pytest tests/test_role_reader.py::TestReaderRole::test_estratto_reale_non_riassunto`             |
| T-606  | reviewer: modello ≠ developer, findings con severità/file/riga, non modifica                                                                                                    | `TestReviewerRole` (`tests/test_role_reviewer.py`)                  | `python -m pytest tests/test_role_reviewer.py::TestReviewerRole`             | `python -m pytest tests/test_role_reviewer.py::TestReviewerRole::test_modello_diverso_da_developer`         |
| T-607  | validator: `UNDETERMINED` di default, «conforme» solo con evidenze (test, run, SHA)                                                                                             | `TestValidatorRole` (`tests/test_role_validator.py`)                | `python -m pytest tests/test_role_validator.py::TestValidatorRole`           | `python -m pytest tests/test_role_validator.py::TestValidatorRole::test_conforme_richiede_evidenze`         |
| T-608  | metrics-observer: usa solo metriche aggregate; il collector è deterministico                                                                                                    | `TestMetricsObserver` (`tests/test_metrics_observer.py`)            | `python -m pytest tests/test_metrics_observer.py::TestMetricsObserver`       | `python -m pytest tests/test_metrics_observer.py::TestMetricsObserver::test_aggregazione_deterministica`    |
| T-609  | e2e-designer/reader: passi solo da E2E_VERIFICATION, esecuzione solo dal runner                                                                                                 | `TestE2eRoles` (`tests/test_role_e2e.py`)                           | `python -m pytest tests/test_role_e2e.py::TestE2eRoles`                      | `python -m pytest tests/test_role_e2e.py::TestE2eRoles::test_nessun_passo_inventato`                        |
| T-610  | `_common` richiamato da ogni ruolo; frontmatter (modello, tools) coerente con la tabella §3.5                                                                                   | `TestCommonAndFrontmatter` (`tests/test_roles_frontmatter.py`)      | `python -m pytest tests/test_roles_frontmatter.py::TestCommonAndFrontmatter` | `python -m pytest tests/test_roles_frontmatter.py::TestCommonAndFrontmatter::test_modelli_come_da_tabella`  |
| T-611  | risk-assessor: un `RISK-<task>.json` per ogni task VERIFIED; ogni rischio ha evidenza e ≥ 2 opzioni; rischio alto blocca solo i dipendenti; nessuna correzione autonoma; «nessun rischio» elenca i controlli | `TestRiskAssessor` (`tests/test_role_risk_assessor.py`) | `python -m pytest tests/test_role_risk_assessor.py::TestRiskAssessor` | `python -m pytest tests/test_role_risk_assessor.py::TestRiskAssessor::test_rischio_senza_opzioni_invalido` |
| T-703  | Gate sul grafo: nessun lancio senza conferma, scelta registrata                                                                                                                 | `TestGraphGate` (`tests/test_graph_gate.py`)                        | `python -m pytest tests/test_graph_gate.py::TestGraphGate`                   | `python -m pytest tests/test_graph_gate.py::TestGraphGate::test_nessun_lancio_senza_conferma`               |
| T-704  | Onde con worktree/rami dedicati entro `limits.json`                                                                                                                             | `TestWaves` (`tests/test_waves.py`)                                 | `python -m pytest tests/test_waves.py::TestWaves`                            | `python -m pytest tests/test_waves.py::TestWaves::test_parallelismo_entro_limiti`                           |
| T-705  | Il task avanza solo dopo runner sullo stesso SHA; nessun merge su main                                                                                                          | `TestIntegrationFlow` (`tests/test_integration_flow.py`)            | `python -m pytest tests/test_integration_flow.py::TestIntegrationFlow`       | `python -m pytest tests/test_integration_flow.py::TestIntegrationFlow::test_avanza_solo_con_sha_verde`      |
| T-706  | `state.json` solo dal workflow; `events.jsonl` senza prompt/segreti; `STALE` dopo merge sui file                                                                                | `TestStateEvents` (`tests/test_state_events.py`)                    | `python -m pytest tests/test_state_events.py::TestStateEvents`               | `python -m pytest tests/test_state_events.py::TestStateEvents::test_stale_dopo_merge`                       |
| T-707  | «Fermati e chiedi» blocca solo i dipendenti; `QUESTIONS.md`; maxAttempts 3                                                                                                      | `TestAskAndStop` (`tests/test_ask_and_stop.py`)                     | `python -m pytest tests/test_ask_and_stop.py::TestAskAndStop`                | `python -m pytest tests/test_ask_and_stop.py::TestAskAndStop::test_blocco_non_globale`                      |
| T-708  | Budget esaurito → stop controllato; ripresa da checkpoint                                                                                                                       | `TestBudgetResume` (`tests/test_budget_resume.py`)                  | `python -m pytest tests/test_budget_resume.py::TestBudgetResume`             | `python -m pytest tests/test_budget_resume.py::TestBudgetResume::test_ripresa_da_checkpoint`                |
| T-709  | I report non dicono mai «completato» se incompleto                                                                                                                              | `TestReports` (`tests/test_reports.py`)                             | `python -m pytest tests/test_reports.py::TestReports`                        | `python -m pytest tests/test_reports.py::TestReports::test_incompleto_mai_completato`                       |
| T-710  | `deny` su `./mvnw*`, `git push*`, `git merge*` effettivi per ruolo/worktree                                                                                                     | `TestPermissionsApplied` (`tests/test_permissions_applied.py`)      | `python -m pytest tests/test_permissions_applied.py::TestPermissionsApplied` | `python -m pytest tests/test_permissions_applied.py::TestPermissionsApplied::test_deny_push_effettivo`      |
| T-711  | Gate di merge R13 completi; bug-fix: regressione fallisce prima del fix                                                                                                         | `TestMergeGates` (`tests/test_merge_gates.py`)                      | `python -m pytest tests/test_merge_gates.py::TestMergeGates`                 | `python -m pytest tests/test_merge_gates.py::TestMergeGates::test_regressione_fallisce_prima_del_fix`       |
| T-712  | Modifiche LIMET M.1–M.9 presenti in IT/EN e coerenti (verifica documentale)                                                                                                     | `TestLimetDocChanges` (`tests/test_limet_doc_changes.py`)           | `python -m pytest tests/test_limet_doc_changes.py::TestLimetDocChanges`      | `python -m pytest tests/test_limet_doc_changes.py::TestLimetDocChanges::test_m1_m9_it_en`                   |
| T-702  | Installer idempotente, non sovrascrive config di progetto, plugin facoltativo                                                                                                   | `TestInstaller` (`tests/test_installer.py`)                         | `python -m pytest tests/test_installer.py::TestInstaller`                    | `python -m pytest tests/test_installer.py::TestInstaller::test_idempotente_senza_sovrascrivere`             |
| T-801  | Nessuna chiamata LLM fuori dal registro approvato                                                                                                                               | `TestLlmRegistry` (`tests/test_llm_registry.py`)                    | `python -m pytest tests/test_llm_registry.py::TestLlmRegistry`               | `python -m pytest tests/test_llm_registry.py::TestLlmRegistry::test_chiamata_non_registrata_fallisce`       |
| T-802  | AgentShield: versione/hash corrispondono al pin, nessuna rete, nessun `--opus`                                                                                                  | `TestAgentShieldWrapper` (`tests/test_agentshield_wrapper.py`)      | `python -m pytest tests/test_agentshield_wrapper.py::TestAgentShieldWrapper` | `python -m pytest tests/test_agentshield_wrapper.py::TestAgentShieldWrapper::test_senza_opus_e_senza_rete`  |
| T-803  | Il setup `HLS_*` rifiuta senza autorizzazione, non tocca collection esistenti e indicizza gli asset del ledger | `TestSetupHlsKb` (`tests/test_setup_hls_kb.py`) | `python -m pytest tests/test_setup_hls_kb.py::TestSetupHlsKb` | `python -m pytest tests/test_setup_hls_kb.py::TestSetupHlsKb::test_non_tocca_collection_esistenti` |
| T-804  | Gli agenti accedono alla base di conoscenza solo in lettura; il tester non ottiene Graphify né codice del developer; senza Qdrant il ruolo prosegue e segnala | `TestKnowledgeAccess` (`tests/test_knowledge_access.py`) | `python -m pytest tests/test_knowledge_access.py::TestKnowledgeAccess` | `python -m pytest tests/test_knowledge_access.py::TestKnowledgeAccess::test_tester_senza_graphify` |
| T-8b01 | L'API ascolta solo su loopback                                                                                                                                                  | `TestApiLoopback` (`tests/api/test_loopback.py`)                    | `python -m pytest tests/api/test_loopback.py::TestApiLoopback`               | `python -m pytest tests/api/test_loopback.py::TestApiLoopback::test_bind_solo_127_0_0_1`                    |
| T-8b02 | Richieste senza token o con Origin/Host esterno sono rifiutate                                                                                                                  | `TestApiAuth` (`tests/api/test_auth.py`)                            | `python -m pytest tests/api/test_auth.py::TestApiAuth`                       | `python -m pytest tests/api/test_auth.py::TestApiAuth::test_origin_esterno_rifiutato`                       |
| T-8b03 | CSRF e CSP presenti; body limitato                                                                                                                                              | `TestApiHardening` (`tests/api/test_hardening.py`)                  | `python -m pytest tests/api/test_hardening.py::TestApiHardening`             | `python -m pytest tests/api/test_hardening.py::TestApiHardening::test_csp_restrittiva`                      |
| T-8b04 | Payload fuori allowlist rifiutati; nessuna azione shell/build/test/git                                                                                                          | `TestApiAllowlist` (`tests/api/test_allowlist.py`)                  | `python -m pytest tests/api/test_allowlist.py::TestApiAllowlist`             | `python -m pytest tests/api/test_allowlist.py::TestApiAllowlist::test_nessuna_azione_git`                   |
| T-8b05 | Il read model è derivato e rigenerabile; l'API non scrive stato proprio                                                                                                         | `TestReadModel` (`tests/api/test_read_model.py`)                    | `python -m pytest tests/api/test_read_model.py::TestReadModel`               | `python -m pytest tests/api/test_read_model.py::TestReadModel::test_cache_rigenerabile`                     |
| T-8b06 | Le viste si renderizzano dai dati del read model                                                                                                                                | `ViewsRender` (`ui/src/__tests__/views.test.tsx`)                   | `npm --prefix ui test -- views.test.tsx`                                     | `npm --prefix ui test -- views.test.tsx -t "Portfolio"`                                                     |
| T-8b07 | Ogni modifica ai permessi richiede conferma e produce audit                                                                                                                     | `PermissionsView` (`ui/src/__tests__/permissions.test.tsx`)         | `npm --prefix ui test -- permissions.test.tsx`                               | `npm --prefix ui test -- permissions.test.tsx -t "richiede conferma"`                                       |
| T-8b08 | La sessione di un agente non può approvare un gate                                                                                                                              | `TestGateHumanOnly` (`tests/api/test_gate_human_only.py`)           | `python -m pytest tests/api/test_gate_human_only.py::TestGateHumanOnly`      | `python -m pytest tests/api/test_gate_human_only.py::TestGateHumanOnly::test_agente_non_approva`            |
| T-8b09 | Accessibilità (WCAG 2.1 AA) e budget prestazioni                                                                                                                                | `A11yAndPerf` (`ui/src/__tests__/a11y.test.tsx`)                    | `npm --prefix ui test -- a11y.test.tsx`                                      | `npm --prefix ui test -- a11y.test.tsx -t "contrasto"`                                                      |
| T-8b10 | Lockfile presente, licenze ammesse, nessun audit critico                                                                                                                        | `TestFrontendDeps` (`tests/test_frontend_deps.py`)                  | `python -m pytest tests/test_frontend_deps.py::TestFrontendDeps`             | `python -m pytest tests/test_frontend_deps.py::TestFrontendDeps::test_licenze_in_allowlist`                 |

**E2E:** scenari ALA V1–V24 sul PoC, uno per OS, descritti in `E2E_VERIFICATION.md` (a cura di LIMET alla chiusura della fase 9). **Test su 3 OS:** la CI a matrice li esegue; in locale l'utente li lancia con gli stessi comandi.

---

## 7. Governance

### 7.1 Licenze (C07) **[F]**
Allowlist solo MIT verificata. Esclusi finché non approvati: `osint-investigation` (CC-BY-SA-4.0), 8 skill Apache-2.0 senza NOTICE, `prp-*`/`code-review` (Wirasm, licenza non dichiarata), `taste-application`, asset con marchi/branding; licenza di `ecc-agentshield`: **[F]** MIT (npm `ecc-agentshield` 1.6.0 e GitHub `affaan-m/agentshield`, SPDX MIT, verificati 2026-10-28); le sue **dipendenze** restano **[N]** (da coprire con SBOM e ledger, H-802/C25); termini ECC Pro: **[F]** Pro è l'offerta della GitHub App ecc.tools (repo privati, audit su PR, fatturazione) «sopra» l'OSS; ecc.tools/pricing dichiara che «the ECC OSS repository is MIT-licensed» e che gli strumenti open source sono disponibili indipendentemente dai piani (letto 2026-10-28); `LICENSE` al commit `25278e7` = MIT, © Affaan Mustafa. **[I]** Il codice MIT portato non è soggetto a vincoli Pro; i termini di servizio della App non sono stati letti per intero, ma HELIOS non usa la App (C11). Parere legale prima del rilascio (`..\05` §6): non c'è un parere definitivo.

### 7.2 Pin ECC (C12) e AgentShield (C10)
Commit `25278e7`, nessun aggiornamento automatico; upgrade = decisione con test di non regressione. AgentShield: versione pinnata, hash, SBOM, nel runner senza rete e senza `--opus`.

### 7.3 Sicurezza UI (C23–C25)
Loopback, token per avvio, allowlist Host/Origin, CSRF, CSP, body limitato; gate approvabili solo da sessione umana; lockfile, licenze permissive, `npm audit`, SBOM, pin, aggiornamenti via PR. La UI non esegue build/test/git; commit/push restano manuali.

### 7.4 CI (C13, C22)
Matrice windows-latest / ubuntu-latest / macos-latest con suite identica (percorsi, fine riga, permessi coperti da test); job per modulo con gate di non regressione; scansione segreti.

### 7.5 Regole operative per chi implementa
- Nessuna build/test/commit/push autonomi: i comandi si comunicano all'utente e si attende l'esito.
- Documenti del progetto sempre allineati (PLAN, TASK_LIST, TEST_PLAN, E2E_VERIFICATION, TEST_EXECUTION).
- Non toccare le installazioni LIMET/CEREBRO in uso; non creare file/scaffolding esterni al repo senza autorizzazione.

---

## 8. Questioni aperte e punti [N]

**Questioni aperte (decisioni del SUPERVISORE)**
- Q1 ~~Progetto pilota~~ **DECISO (SUPERVISORE, 2026-10-28): il progetto «biblioteca»** (opzione A: progetto piccolo e non produttivo; riferimento `GUIDA_ECC_BIBLIOTECA.md` e `ANALISI_PROGETTO_BIBLIOTECA*.md`, utili anche come disegno A/B, `..\02` §1).
- Q2 ~~Tetto di costo~~ **DECISO (SUPERVISORE, 2026-10-28): opzione A con tetto rigido di 100 USD per workflow**, con interruzione automatica e stato «fermati e chiedi» alla soglia (riferimento FO1 §6: 20–45 USD).
- Q3 ~~Titolare eccezione di scope / owner ADR~~ **DECISO (SUPERVISORE, 2026-10-28): opzione A, il SUPERVISORE è owner unico.**
- Q4 ~~Runtime del core~~ **DECISO (SUPERVISORE, 2026-10-28): opzione A, Python ≥ 3.11 per core, runner e API; UI in TypeScript/React.** H-103 diventa la sola formalizzazione in ADR.
- Q5 ~~Nome e posizione del nuovo repository~~ **DECISO (SUPERVISORE, 2026-10-28): opzione A con nome solo «HELIOS»:** cartella locale `C:\Progetti\VSC\HELIOS`, repository privato `HELIOS` nell'account GitHub `odyssey2073` (nessun suffisso `_APP`). La cartella di studio `C:\Progetti\VSC\LIMET\HELIOS` resta separata. Import e commit/push a cura dell'utente.
- Q6 **DECISO (SUPERVISORE): opzione A.** Modalità grafica «leggera» (C26-C) non inclusa ora; da riconsiderare se la dashboard risulta pesante nel PoC o nei budget di prestazioni.
- Q7 **DECISO (SUPERVISORE, 2026-10-28): opzione A, monorepo.** HELIOS contiene tutto al proprio interno (plugin `limet-agents` in `agents/plugin/`, skill/agenti ECC portati, infrastruttura LIMET e CEREBRO); nessun repo separato `C:\Software\limet-agents`. Il plugin è installabile dalla cartella `agents/plugin/` tramite `claude plugin marketplace add`.
- Q8 **DECISO (SUPERVISORE, 2026-10-28): opzione A.** JavaScript solo per scheduler e workflow (richiesti dal plugin, ALA R4/R23, test `node --test`); Python per runner, API, contratti, inventario e test. Il confine è il contratto JSON (§3.3). H-103 lo registra in `docs/adr/0002-runtime.md`.
- Q9 **DECISO (SUPERVISORE, 2026-10-29): base di conoscenza in Qdrant usata dagli agenti in esecuzione.** Due livelli: `CRB_<slug>` (documenti del progetto, indicizzati dall'utente) e `HLS_*` (asset ECC e matrice catena, creata solo in setup con autorizzazione, H-803). Accesso in sola lettura via `cerebro/`, regole e ruoli ammessi in §3.8, task H-803 e H-804. Resta valido che nessun agente indicizza né modifica collection esistenti.

**Punti non verificati [N]**
- ~~Stato di Qdrant :6333 e Ollama :11434; collection `CRB_limet`~~ **CHIUSO (SUPERVISORE, 2026-10-28).** **[F]** Verificato in sola lettura: Qdrant e Ollama (`nomic-embed-text`) attivi; collection presenti `CRB_csi_cosmo`, `CRB_mudeopenbtcet`, `CRB_mudeopensrvet`, `project_docs`; `CRB_limet` assente. **Decisione:** le collection esistenti restano intatte, perché il SUPERVISORE continua a gestire i progetti con LIMET e CEREBRO «puri». HELIOS non crea, modifica o cancella collection esistenti e **nessun agente** indicizza nulla in autonomia; la collection propria di HELIOS (`HLS_*`, nome distinto, Q9/§3.8) è creata solo dallo script di setup H-803, con autorizzazione esplicita.
- Remoto e storia git di CEREBRO.
- ~~Versione effettiva di Claude Code (≥ 2.1 richiesta)~~ **CHIUSO.** **[F]** `claude --version` sulla macchina del SUPERVISORE (2026-10-28): 2.1.311, `C:\Users\mscibetta\.local\bin\claude.exe`. Requisito ≥ 2.1 soddisfatto; da ricontrollare nel PoC (H-1xx) se la versione cambia.
- ~~Licenza di `ecc-agentshield`~~ **CHIUSO [F]:** MIT (npm 1.6.0 e repo GitHub, 2026-10-28). Restano **[N]** le licenze delle sue dipendenze (da SBOM/ledger in H-802).
- ~~Termini ECC Pro~~ **CHIUSO [F]/[I]:** Pro riguarda solo la GitHub App; il repo OSS è MIT (`LICENSE` a `25278e7`, pagina pricing 2026-10-28). HELIOS non usa la App (C11): voce fuori perimetro, salvo parere legale.
- ~~Letture parziali di ECC (skill/agenti)~~ **CHIUSO (scelta B del SUPERVISORE, 2026-10-28) con inventario scriptato [F]** su clone temporaneo al commit `25278e7` (poi eliminato): letti il 100% dei file di `skills/` (311 skill, 614 file, tutte con `SKILL.md`) e di `agents/` (71). Risultati: 29 skill con script/codice non-Markdown; licenza dichiarata nel frontmatter solo per 19 skill (10 MIT, 8 Apache-2.0, 1 CC-BY-SA-4.0), **292 senza licenza dichiarata** → ereditano la MIT del repo **[I]**, da confermare con il ledger (C07/H-201); segnali di rischio per skill (euristica a regex, **non** verifica semantica): rete 112, build/test 49, git-write 14, distruttivi 14, invio a LLM 25, flusso di pianificazione 21, hook/memoria 21; agenti: 55 con Bash, 28 con Write/Edit (il «26» dichiarato in precedenza escludeva Edit **[I]**), rete 13, git-write 7, build/test 21. Dettaglio per-asset (tutte le 311 skill e i 71 agenti, con colonna *Decisione* «da classificare») in `..\appendici\REGISTRO_SKILLS_AGENTI_ECC.md` (nella SPA). **[P]** Indirizzo del SUPERVISORE: usarne tutti o quasi, escludendo solo ciò che non è sicuro o compatibile (C03–C07). **Resta [N]:** la classificazione semantica skill per skill (porta / non porta) → H-201 in Fase 2, che usa questo inventario come base e non parte da zero.
- Nomi di file/classi/metodi di test di §6: proposti, da fissare in implementazione.
- Stime di effort: tutte **[I]**.

**Ipotesi [I] da validare nel PoC**
- Fattibilità di `limet-agents` (esiste solo il design).
- Beneficio degli asset ECC rispetto al baseline LIMET (C16).
- Compatibilità di Windows nativo con il runner (ECC cita limitazioni, issue #2489, #2626, #2674).

---

## 9. Come usare questo documento con LIMET
0. **Lettura obbligatoria prima di derivare qualsiasi documento** (questo file è volutamente sintetico e rimanda agli altri; non leggerli porta a PLAN incompleti). Leggere per intero, nell'ordine, e dichiarare in chat di averlo fatto:

| File (in questa cartella) | Cosa ne ricavi |
|---|---|
| `00_README.md`, `06_sintesi_non_tecnica.md` | verdetto, obiettivo, linguaggio per il SUPERVISORE |
| `01_analisi_ECC.md` | struttura ECC, hook, installer, licenze, dipendenze da Claude Code |
| `02_analisi_limet-agents.md` | requisiti R1–R27, ruoli, stati, gate |
| `03_matrice_confronto.md` | sovrapposizioni, gap, conflitti ECC↔LIMET↔CEREBRO |
| `04_architettura_HELIOS.md` | principi, architettura, contratti, dashboard, workflow |
| `05_roadmap_e_rischi.md` | C01–C28 decise, roadmap, criteri go/no-go PoC, licenze |
| `REGISTRO_SKILLS_AGENTI_ECC.md` | 311 skill e 71 agenti da classificare (H-201) |
| `ANALISI_limet-agents_con_workflow_claude.md` (ALA) | requisiti vincolanti del plugin `limet-agents` (R1–R27, M.1–M.9, V1–V24) |
| `FEASIBILITY_O1_CLAUDE_CODE.html` (FO1) | fattibilità, gate meccanici, scheduler, costi |

In caso di conflitto tra questo file e un documento vincolante (ALA, FO1) o le decisioni C01–C28, prevalgono questi ultimi: segnalare il conflitto al SUPERVISORE.
1. Passare il file a LIMET come input della fase 0/1 di un nuovo progetto «HELIOS».
2. LIMET deriva `PLAN.md` (da `PLAN_TEMPLATE.md`), poi `TASK_LIST.md` (§5), `TEST_PLAN.md` (§6) ed `E2E_VERIFICATION.md` (§6, fase 9), senza chiedere conferma di generazione.
3. Ogni task `H-xxx` si implementa comunicando prima cosa si fa, spiegandone il contenuto e, alla fine, elencando modifiche e file toccati.
4. A fine fase, archiviare TEST_PLAN, E2E_VERIFICATION e TEST_EXECUTION in `limet/archive/<data-slug>/`.
