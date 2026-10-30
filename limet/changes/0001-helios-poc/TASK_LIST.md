# Elenco task — HELIOS: progetto completo (Fasi 1–12 + 8b), PoC go/no-go in Fase 4

> **Aggiornamento 2026-10-29 (verifica di completezza)**: il documento copre **tutti i 70 task** di IN §5 (prima solo i 17 del PoC). I task oltre il PoC (Fasi 5–12) restano `pending` e **non partono senza esito GO del gate H-406** (IN §4/§5). Indice per fase nella sezione «Indice task per fase».

> Deriva da `PLAN_helios-poc.md` §6 e da IN §5 (`HELIOS_ANALISI_PER_LIMET.md`). Etichette: **[F]** fatto · **[I]** ipotesi · **[P]** proposta · **[N]** non verificato. Fonti: IN = `HELIOS_ANALISI_PER_LIMET.md`; R05 = `05_roadmap_e_rischi.md`; ALA = `ANALISI_limet-agents_con_workflow_claude.md`.
> **Stato documento**: APPROVATO dal SUPERVISORE il 2026-10-29 (vedi PLAN, Metadati). Nessun task è ancora iniziato; ogni task parte solo dopo che l'agente ne ha comunicato il contenuto.

## Regole operative valide per ogni task

1. **C28 (vincolante, IN §2.3 n.7)**: al termine di ogni task chi sviluppa valuta rischi, errori e malfunzionamenti causati dal task. Se ne trova, li riporta al SUPERVISORE con evidenza e ≥ 2 opzioni (pro/contro, raccomandazione) **senza autocorreggere**; se non ne trova, elenca i controlli eseguiti. L'esito va nella colonna Note del task.
2. **Comunicazione** (IN §9 punto 3): prima di ogni task l'agente dice cosa farà; a fine task elenca modifiche (file, cosa, perché).
3. **Mai build/test/commit/push da parte dell'agente**: i comandi sono nel `TEST_PLAN.md`; li esegue il SUPERVISORE e comunica l'esito.
4. Un task passa a `done` solo con verifica eseguita e annotata (`YYYY-MM-DD HH:MM`).
5. Nessuna modifica ai file di input, a LIMET/CEREBRO in uso per COSMO e mudeopen, né alle collection Qdrant esistenti.

## Legenda stato

- `pending` — non ancora iniziato · `in_progress` — in corso · `blocked` — bloccato (motivo in Note) · `done` — completato **e verificato**, con data e ora in Note.

## Tabella task

> Le dipendenze marcate **[P]** sono dedotte da IN §4/§5 (tabella fasi e raggruppamenti A–F) e non scritte esplicitamente per il singolo task; IN dà solo «Fase 4 dipende da Fasi 1 e 3». Le stime di effort sono tutte **[I]** e non sono riportate (IN: «8–12» per l'intera Fase 4, **[I]**).

| # | Titolo | Scopo (non tecnico) | Stato | Dipende da | Dettaglio / fonte | Note |
| - | ------ | ------------------- | ----- | ---------- | ----------------- | ---- |
| H-101 | ADR fondativo | Mettere per iscritto owner, perimetro ECC, pin `25278e7`, revoca | done | — | `docs/adr/0001-helios.md`; Done: approvato dal SUPERVISORE (IN §5 Fase 1) | **done 2026-10-29 20:07**, approvato dal SUPERVISORE. `docs/` non ancora indicizzata in CEREBRO: aggiunta a cura del SUPERVISORE (scelta 1). C28: nessun rischio rilevato; controlli: coerenza con C01/C04/C06/C07/C08/C09/C11/C14/C28, Q3/Q7, nessun file di input toccato. |
| H-102 | Costituzione HELIOS | Raccogliere i non-negoziabili §2.3 compresa la valutazione rischi a fine task (C28) | done | H-101 [P] | `docs/CONSTITUTION.md`; richiamata da ogni ruolo (IN §5 Fase 1) | **done 2026-10-29 20:18**, approvata dal SUPERVISORE. «Richiamata da ogni ruolo»: rinviata alla creazione dei ruoli (H-401/H-402…), non ancora verificabile. C28: nessun rischio rilevato; controlli: 7 regole = IN §2.3 n.1–7, nessun requisito nuovo, nessun file di input toccato. `docs/` non ancora indicizzata (a cura del SUPERVISORE). |
| H-103 | Conferma runtime e struttura test | Formalizzare Python ≥ 3.11 (Q4) e JS solo scheduler/workflow (Q8); confermare nomi/framework dei test | done | H-101 [P] | `docs/adr/0002-runtime.md` (IN §5 Fase 1, §8 Q4/Q8). Q-11 chiusa per i comandi; nomi file definitivi con il codice | **done 2026-10-29 20:24**, approvato dal SUPERVISORE. Restano [N] non bloccanti: framework test UI, gestore pacchetti Python. C28: nessun rischio rilevato; controlli: coerenza con IN §3.1/§8 Q4-Q8 e C13, nessun requisito nuovo, nessun file di input toccato. |
| H-301 | Import LIMET e CEREBRO nel monorepo | Portare `limet/` e `cerebro/` con storia in sottocartelle | pending | H-101 [P] | **Azione del SUPERVISORE** (commit/push). Done: originali invariati (IN §5 Fase 3). **[N]** Q-9 (remote CEREBRO) | |
| H-302 | Segreti fuori dal repo | `projects.example.json`, `.gitignore` per `cache/` e dati Qdrant, scansione segreti in CI | pending | H-301 | Test T-302; Done: 0 segreti (IN §5 Fase 3) | |
| H-303 | Contratti §3.3 come schemi JSON | Definire il linguaggio comune tra ruoli e runner | pending | H-103 | `agents/contracts/*.schema.json`; T-303 | |
| H-304 | Shim `compat/` | Mantenere invariati comandi legacy (`cerebro.sh`, `query_qdrant.py`, `ingest_docs.py`, `limet.ps1/.sh`, hook/MCP): stessi argomenti, porte, nomi collection | pending | H-301 | T-304; Done: parità su Windows e Unix | |
| H-305 | Test dei confini tra moduli | Evitare dipendenze incrociate non previste | pending | H-301 | `tests/test_module_boundaries.py`; T-305 | |
| H-306 | CI a matrice | Stessa suite su windows/ubuntu/macos | pending | H-103 | `.github/workflows/ci.yml`; T-306 | |
| H-307 | Correzione percorso CEREBRO in `CLAUDE.md` | Eliminare il riferimento `C:\Progetti\CEREBRO` nella copia interna | pending | H-301 | Done: nessun riferimento (IN §5 Fase 3) | |
| H-309 | Configurazione locale (`helios.local.json`) | Evitare che chi clona HELIOS erediti i percorsi e le configurazioni di un altro | pending | H-301, H-303 | **Fase 3.** Modulo di bootstrap condiviso (non solo dashboard), catena `HELIOS_CONFIG` → repo → utente, esempio versionato, `.gitignore`, `helios doctor`; T-309 | Richiesta SUPERVISORE 2026-10-30; attua C20 |
| H-401 | Runner con allowlist | Eseguire solo operazioni ammesse, senza rete, con log hashato, senza commit/push | pending | H-303, H-103 [P] | `agents/runner/`; T-401, T-402 | |
| H-404 | Policy permessi con pavimento hard-deny | Impedire che permessi pericolosi siano rimovibili | pending | H-401 [P] | `agents/policy/policy.json`, `floor.json`; T-405 | |
| H-402 | Ruoli PoC | analyst, developer, reviewer con `tools:` senza Bash libero | pending | H-404, H-303 [P] | `agents/roles/*.md`; T-403 | |
| H-403 | Gate umano meccanico sul PLAN | Nessun avanzamento senza approvazione umana | pending | H-303 [P] | `agents/workflow/gate.py`; T-404 | |
| H-405 | Asset ECC portato + A/B | Portare un asset `ecc-*` e confrontarlo con la baseline | pending | H-402, H-401 | T-406. Asset = `code-reviewer` ECC (Q-4 chiusa, 2026-10-29). **[N]** Metrica e baseline da definire (Q-6) | |
| H-407 | Prova del plugin Claude Code | Dimostrare registrazione e invocazione reale del workflow in sessione Claude Code (V13/V14) | pending | H-103, H-303 [P] | `agents/plugin/`; T-407 + E2E; ALA §Verifica 4; Done: workflow riconosciuto e invocato in sessione reale, altrimenti piano B in `poc-report.md` | |
| H-406 | Rapporto go/no-go | Giudicare i 10 criteri di R05 §5 | pending | H-401…H-405, H-407 | `docs/poc-report.md`. **PUNTO DI DECISIONE del SUPERVISORE (GO / NO-GO / piano B), non fine del piano**: Fasi 5–12 dipendono da questo esito | |
| H-201 | Script di inventario ECC | Leggere il 100% dei file ECC e contarli | pending | H-101 [P] | **Fase 2.** `agents/tools/inventory_ecc.py`; T-201; parte da `REGISTRO_SKILLS_AGENTI_ECC.md`, colonna *Decisione* | |
| H-202 | Ledger licenze + allowlist MIT | Ogni asset con hash/licenza/origine; non-MIT esclusi | pending | H-201 [P] | **Fase 2.** `agents/assets/ecc-ledger.json`, `NOTICE`; T-202 | |
| H-203 | Revisione manuale asset in allowlist | Checklist firmata su ogni asset ammesso | pending | H-202 [P] | **Fase 2.** `docs/ecc-review.md`; nessun test (documento) | |
| H-204 | Matrice catena | Assegnare 71 agenti + 311 skill a un ruolo o escluderli con motivo | pending | H-201, H-202 [P] | **Fase 2.** `docs/chain-matrix.md`, `agents/assets/chain-matrix.json`; T-204; approvata dal SUPERVISORE | |
| H-308 | Contratti della catena | Schemi `plan/result/findings/verdict/reading/merge/risk`, `state.json`, `events.jsonl`, stati R17 | pending | H-303, H-401 (gruppo A) | **Fasi 5–7, gruppo A.** T-308. **[P]** fase = 6 (contratti dei ruoli); IN §4 non la assegna esplicitamente | Dopo GO |
| H-409 | Skill `limet-runner` | Wrapper del runner H-401 verso il workflow | pending | H-303, H-401 (gruppo A) | **Gruppo A.** `agents/plugin/skills/limet-runner/`; T-409. **[P]** fase 6 | Dopo GO |
| H-408 | Scheduler a funzioni pure (JS) | Decidere cosa parte e quando (onde, budget) | pending | H-303, H-401 (gruppo A) | **Gruppo A.** `agents/plugin/scheduler/limet-scheduler.js`; T-408 (`node --test`, Q8). **[P]** fase 7 | Dopo GO |
| H-611 | `_common` e rules per ruolo | Regole condivise dei ruoli, «fermati e chiedi», regole KB §3.8 | pending | H-303, H-401 (gruppo A) | **Fase 6, gruppo A.** `agents/plugin/agents/_common.md`; T-610 | Dopo GO |
| H-501 | Test di agnosticità statico | Nessun hook/tool/memoria/`.claude/plans` negli asset | pending | H-204, H-308 | **Fase 5, gruppo B.** T-501 | Dopo GO |
| H-502 | Porting per ruolo di rules e skill | Asset `ecc-*` con rules per ruolo | pending | H-204, H-308 | **Fase 5.** T-502 | Dopo GO |
| H-503 | Nessun file di piano ECC / collisioni | Proposte in `ecosystem/proposals/`, mai in file di piano | pending | H-204, H-308 | **Fase 5.** T-503. Recepisce la regola dei planner ECC (consulenti in sola lettura) | Dopo GO |
| H-602 | Ruolo `analyst` (opus) | Produrre `plan.json`; non inventa requisiti | pending | gruppo A, H-402 | **Fase 6, gruppo C.** `agents/plugin/agents/analyst.md`; T-601 | Dopo GO |
| H-603 | Ruolo `developer` ×N (sonnet) | Un task per worktree/ramo, mai build/test/push | pending | gruppo A, H-402 | **Fase 6.** `developer.md`; T-602 | Dopo GO |
| H-604 | Ruolo `tester` ×M (sonnet) | Test dal TEST_PLAN senza vedere il diff; mai Graphify | pending | gruppo A, H-402 | **Fase 6.** `tester.md`; T-603 | Dopo GO |
| H-605 | Ruolo `integrator` (sonnet) | Integra su `integration/NNNN`; mai merge su `main` | pending | gruppo A, H-402 | **Fase 6.** `integrator.md`; T-604 | Dopo GO |
| H-606 | Ruolo `test-reader` (haiku) | Classifica i fallimenti con estratti reali | pending | gruppo A, H-402 | **Fase 6.** `test-reader.md`; T-605 | Dopo GO |
| H-607 | Ruolo `reviewer` (opus ≠ developer) | Findings con severità/file/riga; non corregge | pending | gruppo A, H-402 | **Fase 6.** `reviewer.md`; T-606 | Dopo GO |
| H-608 | Ruolo `validator` (opus) | conforme/non conforme/non determinabile con evidenze | pending | gruppo A, H-402 | **Fase 6.** `validator.md`; T-607 | Dopo GO |
| H-609 | `metrics-observer` + aggregatore | Interpreta solo metriche già aggregate | pending | gruppo A, H-402 | **Fase 6.** `metrics-observer.md`, `agents/metrics/`; T-608 | Dopo GO |
| H-610 | Ruoli `e2e-designer`/`e2e-reader` | E2E Playwright eseguiti dal runner | pending | gruppo A, H-402 | **Fase 6.** `e2e-*.md`; T-609 | Dopo GO |
| H-601 | Reviewer specializzati ECC | Lenti `ecc-*` riusate con overlay minimo (estende H-607) | pending | gruppo A, H-402, H-607 [P] | **Fase 6.** T-403 | Dopo GO |
| H-612 | Valutazione rischi post-task (prodotto) | `risk-assessor` al passaggio a VERIFIED (C28), `RISK-<task>.json` + `QUESTIONS.md` | pending | H-308, H-701, H-706 | **Fase 6, gruppo E.** T-611 | Dopo GO |
| H-701 | Workflow `limet-change.js` | Struttura, validazione input, stati, retry, stop-e-chiedi | pending | gruppi A e C, H-407 | **Fase 7, gruppo D.** T-701 | Dopo GO |
| H-703 | Gate umano sul grafo | Conferma di task/onde/stima prima del lancio | pending | gruppi A e C, H-407 | **Fase 7.** T-703 | Dopo GO |
| H-704 | Orchestrazione per onde | Worktree e rami dedicati, parallelismo entro limiti | pending | gruppi A e C, H-407 | **Fase 7.** T-704 | Dopo GO |
| H-705 | Integrazione su SHA verde | Avanza solo con run runner sullo stesso SHA | pending | gruppi A e C, H-407 | **Fase 7.** T-705 | Dopo GO |
| H-706 | Stato ed eventi | `state.json`/`events.jsonl`, `STALE` dopo merge | pending | gruppi A e C, H-407 | **Fase 7.** T-706 | Dopo GO |
| H-707 | «Fermati e chiedi» non globale | Blocca solo i dipendenti; `QUESTIONS.md`; maxAttempts 3 | pending | gruppi A e C, H-407 | **Fase 7.** T-707 | Dopo GO |
| H-708 | Budget, checkpoint, ripresa | Stop controllato e `resumeFromRunId` | pending | gruppi A e C, H-407 | **Fase 7.** T-708 | Dopo GO |
| H-709 | Report notturno/conformità/finale | Mai «completato» se incompleto; sezione rischi H-612 | pending | gruppi A e C, H-407 | **Fase 7.** T-709 | Dopo GO |
| H-710 | Permessi a livello di tool | `deny` su mvnw/push/merge, hook di log propri (estende H-404) | pending | gruppi A e C, H-407 | **Fase 7.** T-710 | Dopo GO |
| H-711 | Gate di merge R13 + bug-fix R15 | Merge solo con tutti i gate; regressione fallisce prima del fix | pending | gruppi A e C, H-407 | **Fase 7.** T-711 | Dopo GO |
| H-702 | Installer `limet-agents.ps1/.sh` | `init|update` idempotente, plugin facoltativo | pending | gruppi A e C, H-407 | **Fase 7.** T-702 | Dopo GO |
| H-712 | Modifiche documentali a LIMET M.1–M.9 | Ciclo LIMET IT/EN; re-index a cura dell'utente | pending | nessuna in IN; **[P]** dopo H-407 (ALA §7) | **Fase 7, gruppo F.** T-712 | Dopo GO |
| H-713 | Modalità non presidiata (UNATTENDED) | Sezione `night` in `limits.json`, macchina a stati del run, parcheggio dei dipendenti, kill switch, dead-man switch | pending | H-706, H-707, H-708, H-710 | **Fase 7.** Estende H-707/H-708. Default decisi (Q-14): `stopOnQuestions` = `false`, `maxDurationMin` = 480, nessun reperibile. T-713, E2E-AUT-1, E2E-AUT-2 | ADR 0003; richiesta SUPERVISORE 2026-10-30 |
| H-714 | Registro delle decisioni autonome | Ogni decisione presa in autonomia registrata con evidenza e reversibilità; sezione «decisioni autonome» in `REPORT_NOTTURNO.md` | pending | H-709, H-713 | **Fase 7.** Estende H-709 (ALA R18). T-714, E2E-AUT-1 | ADR 0003 §8 |
| H-715 | Allow-list dei ruoli non presidiati | Solo `developer`, `tester`, `runner`, `test-reader`, `reviewer`, `metrics-observer`; al turno di un ruolo escluso il task si parcheggia con motivazione | pending | H-713 | **Fase 7.** Applicata dal codice, non convenzione (Q-16). T-715, E2E-AUT-1 | ADR 0003 §5 |
| H-800 | Analisi degli hook ECC | Decisione motivata per ognuno dei 27 hook | pending | H-401 [P]; anticipabile in Fase 2 (IN §5) | **Fase 8.** `docs/ecc-hooks-analysis.md`; nessun test | Dopo GO (o anticipo) |
| H-800b | Replica sicura hook utili | Hook HELIOS propri, mai codice ECC eseguito | pending | H-800 | **Fase 8.** Verifica definita (audit A6): 5 criteri per hook — portabilità, nessuna rete, nessuna scrittura fuori workspace, log con hash, nessun file ECC in esecuzione. Nomi dei test dopo H-800 **[N]** | Dopo GO |
| H-801 | Registro chiamate LLM (C05) | Ogni chiamata LLM extra registrata | pending | H-401 [P] | **Fase 8.** `agents/policy/llm-registry.json`; T-801 | Dopo GO |
| H-802 | Wrapper AgentShield | Pin, hash, SBOM, senza rete/`--opus` | pending | H-401 [P] | **Fase 8.** T-802. **[N]** licenze delle dipendenze (Q-10) | Dopo GO |
| H-803 | Setup KB `HLS_*` | Crea `HLS_*` solo con autorizzazione esplicita del SUPERVISORE | pending | H-202, H-204 [P] | **Fase 8.** T-803 (Q9). Mai su collection esistenti | Dopo GO |
| H-804 | Accesso agenti alla KB | Solo MCP/CLI in sola lettura, eventi registrati, degrado senza contesto | pending | H-803, H-611 [P] | **Fase 8.** T-804 | Dopo GO |
| H-8b01 | `api/` loopback e hardening | Token, Host/Origin, CSRF, CSP, limite body | pending | H-303, H-401 [P] | **Fase 8b.** T-8b01…T-8b04 | Dopo GO |
| H-8b02 | Read model derivato | Cache rigenerabile, fonte unica (C27) | pending | H-8b01 | **Fase 8b.** T-8b05 | Dopo GO |
| H-8b03 | `ui/` viste | Portfolio, LIMET, HELIOS, CEREBRO, Criticità | pending | H-8b02 | **Fase 8b.** T-8b06 | Dopo GO |
| H-8b04 | Vista Permessi agenti | Conferma, motivazione, audit, rollback | pending | H-404, H-8b03 | **Fase 8b.** T-405, T-8b07 | Dopo GO |
| H-8b05 | Gate solo da sessione umana | La UI non esegue build/test/git | pending | H-8b01, H-403 | **Fase 8b.** T-8b08 (C23–C24) | Dopo GO |
| H-8b06 | Grafica e accessibilità | SVG/Canvas, WCAG 2.1 AA, budget prestazioni (C26) | pending | H-8b03 | **Fase 8b.** T-8b09 | Dopo GO |
| H-8b07 | Governance dipendenze npm | Lockfile, licenze, audit, SBOM (C25) | pending | H-8b03 | **Fase 8b.** T-8b10 | Dopo GO |
| H-8b08 | Sostituzione launcher :8799 | Tramite shim | pending | H-304, H-8b03 | **Fase 8b.** T-304 | Dopo GO |
| H-8b09 | Gate di configurazione all'avvio della dashboard | Nessuna operazione senza configurazione valida; dialog di prima configurazione | pending | H-309, H-8b01, H-8b03 | **Fase 8b.** Front-end del modulo H-309: stato `OK`/`INCOMPLETO`/`NON VALIDO`, dialog pre-compilata dall'autodiscovery, scrittura del file; T-8b11, E2E-CFG-1 | Richiesta SUPERVISORE 2026-10-30 |
| H-8b10 | Avvio del run non presidiato dalla dashboard | Perimetro e limiti mostrati **prima** dell'avvio; avvio solo da sessione umana (C24); kill switch raggiungibile durante il run | pending | H-713, H-714, H-715, H-8b05 | **Fase 8b.** La UI non amplia i permessi (neutralità dei permessi, ADR 0003 §2). T-8b13, E2E-AUT-2 | ADR 0003 §9 |
| H-901 | Scenari E2E ALA V1–V24 | Catena completa su 3 OS, progetto pilota | pending | Fasi 5–8 | **Fase 9.** `E2E_VERIFICATION.md` (V1–V24); comandi nel TEST_PLAN §E2E | Dopo GO |
| H-902 | README e troubleshooting plugin | Installazione, configurazione, errori | pending | H-901 [P] | **Fase 9.** `agents/plugin/README.md`; nessun test | Dopo GO |
| H-1001 | Hardening | Log immutabili, sandbox, SBOM, audit | pending | H-901 | **Fase 10.** Nessun T-xxx in IN §6 **[N]**: verifica = audit a 5 criteri osservabili (definita dall'audit A6), esito in `docs/audit-hardening.md` | Dopo GO |
| H-1101 | Manuali, NOTICE, runbook | Documentazione finale | pending | H-901 | **Fase 11.** Revisione approvata | Dopo GO |
| H-1201 | Rollout sul pilota | Metriche, rollback, parere legale | pending | H-1001, H-1101 | **Fase 12.** Parere legale **[N]**; progetto pilota non ancora scelto | Dopo GO |

### Indice task per fase (verifica di completezza, IN §4/§5)

| Fase | Task | N. |
| ---- | ---- | -- |
| 1 Discovery | H-101, H-102, H-103 | 3 |
| 2 Inventario ECC | H-201, H-202, H-203, H-204 | 4 |
| 3 Contratti e consolidamento | H-301…H-307, H-309 | 8 |
| 4 PoC go/no-go | H-401…H-407 (H-406 = gate) | 7 |
| 5 Porting skill/rules | H-501, H-502, H-503 | 3 |
| 6 Catena agenti | H-601…H-612, + H-611 gruppo A, H-308, H-409 [P] | 12 + 2 |
| 7 Workflow completo | H-701…H-715, + H-408 [P] | 15 + 1 |
| 8 Hook e comandi | H-800, H-800b, H-801…H-804 | 6 |
| 8b Dashboard | H-8b01…H-8b10 | 10 |
| 9 E2E | H-901, H-902 | 2 |
| 10 Hardening | H-1001 | 1 |
| 11 Documentazione | H-1101 | 1 |
| 12 Rilascio | H-1201 | 1 |
| **Totale** | | **76** (17 PoC/prerequisiti + 59 altri) |

### Mappa dei 10 criteri go/no-go (R05 §5) sui task

| Criterio | Task / verifica |
| -------- | --------------- |
| 1 · 0 scritture fuori workspace, 0 modifiche a `~/.claude` | H-401, H-407 · E2E-PoC-1 (E2E_VERIFICATION) |
| 2 · 0 connessioni di rete non autorizzate | H-401 · T-402, E2E-PoC-2 |
| 3 · 0 asset portati con Bash/hook/memory | H-402, H-405 · T-403 |
| 4 · runner riproducibile | H-401 · T-402 (hash) |
| 5 · ogni chiamata agente→runner tracciata | H-401 · T-402 |
| 6 · almeno una metrica meglio della baseline | H-405 · T-406 (**[N]** Q-6) |
| 7 · stessa suite verde su Windows e Unix | H-306 · T-306 + CI |
| 8 · costo ≤ 100 USD per workflow | H-407 · E2E-PoC-3 (stima equivalente API da token, Q-7 **[P]**) |
| 9 · suite di parità e 0 segreti | H-304, H-302 · T-304, T-302 |
| 10 · test di sicurezza dashboard C23–C24 | H-406 · **N/A motivato** nel rapporto (dashboard in Fase 8): decisione del SUPERVISORE, Q-12, 2026-10-29 |
> Q-12 **chiusa** (2026-10-29, SUPERVISORE): criterio 10 = N/A motivato nel PoC, da valutare in Fase 8.

---

## Schede di dettaglio per task (aggiunte 2026-10-29)

> Per ogni task: output, criterio di done (da IN §5, riportato in sintesi; dove IN è più lungo si rimanda al testo), verifica e controlli C28 specifici da eseguire a fine task (oltre a quelli generici della regola 1). Comandi di verifica: vedi `TEST_PLAN.md` (T-xxx) ed `E2E_VERIFICATION.md`. Ogni task parte dopo che l'agente ne ha comunicato il contenuto, e finisce con l'elenco di file e modifiche (IN §9 punto 3).

| Task | Output / file | Criterio di done | Verifica | Controlli C28 specifici |
| ---- | ------------- | ---------------- | -------- | ----------------------- |
| H-101 | `docs/adr/0001-helios.md` | Approvato dal SUPERVISORE | Revisione | Coerenza con C01–C28 |
| H-102 | `docs/CONSTITUTION.md` | Approvata; richiamata da ogni ruolo; include regola C28 | Revisione + `Select-String` su «valutazione» e «C28» | Nessun non-negoziabile di IN §2.3 omesso |
| H-103 | `docs/adr/0002-runtime.md` | Decisione scritta (Python ≥ 3.11; JS solo scheduler/workflow; nomi/framework test) | Revisione | Coerenza con Q4/Q8; chiude Q-11 |
| H-201 | `agents/tools/inventory_ecc.py` | Tabella per-asset generata; legge il 100% dei file; colonna *Decisione* compilata | T-201 | Conteggi 71 agenti/311 skill/614 file; nessuna scrittura su clone ECC |
| H-202 | `agents/assets/ecc-ledger.json`, `NOTICE` | Ogni asset con hash/licenza/origine; non-MIT esclusi | T-202 | 292 skill senza licenza: trattate come MIT ereditata **[I]**, confermate dal ledger |
| H-203 | `docs/ecc-review.md` | Tutti gli asset in allowlist firmati | Firma SUPERVISORE | Nessun asset ammesso senza revisione |
| H-204 | `docs/chain-matrix.md`, `agents/assets/chain-matrix.json` | 0 righe «da classificare»; 71 agenti + 311 skill assegnati o esclusi con motivo; approvata | T-204 | Planner/architect ECC solo come consulenti dell'analyst |
| H-301 | `limet/`, `cerebro/` nel monorepo | Originali invariati | E2E-PoC-5 | Azione del SUPERVISORE; nessuna modifica a LIMET/CEREBRO in uso |
| H-302 | `projects.example.json`, `.gitignore`, scansione segreti CI | 0 segreti | T-302 | Nessun `projects.json` reale versionato |
| H-303 | `agents/contracts/*.schema.json` | Schemi §3.3 validi, con casi invalidi | T-303 | Campi obbligatori e tipi; nessun campo libero |
| H-304 | `compat/` | Parità su Windows e Unix (stessi argomenti, porte, nomi collection) | T-304, E2E-PoC-5 | Collection esistenti con conteggio punti invariato |
| H-305 | `tests/test_module_boundaries.py` | Nessuna dipendenza incrociata non prevista | T-305 | Insieme completo dei moduli |
| H-306 | `.github/workflows/ci.yml` | Stessa suite su windows/ubuntu/macos | T-306, E2E-PoC-4 | Nessun segreto nella CI |
| H-307 | `CLAUDE.md` (copia interna) | Nessun riferimento a `C:\Progetti\CEREBRO` | `Select-String` | Nessun altro percorso assoluto residuo |
| H-309 | `config/helios.local.example.json`, `config/helios.config.schema.json`, modulo di bootstrap `helios.config` (load/validate/discover/write), comando `helios doctor`, righe `.gitignore` | Catena di precedenza `HELIOS_CONFIG` → `config/helios.local.json` → `%APPDATA%\HELIOS\config.json` / `~/.config/helios/config.json`; validazione **semantica** (i percorsi esistono e contengono ciò che dichiarano) con esito `OK` / `INCOMPLETO` / `NON VALIDO` ed elenco dei campi colpevoli; autodiscovery dalla radice del repo; esempio versionato, file reale **mai** versionato; nessun segreto nel file; `configVersion` con errore esplicito su versione ignota | T-309 | Nessun percorso locale né segreto nell'esempio versionato; `git check-ignore` positivo su tutti i percorsi del file reale; il modulo è **unico** per dashboard, runner, CLI, installer e CI (nessuna copia della logica nella UI); la validazione non apre connessioni non previste |
| H-401 | `agents/runner/` | Solo operazioni in allowlist; senza rete; log con hash; nessun commit/push | T-401, T-402, E2E-PoC-1/2 | Rete, scritture fuori workspace, riproducibilità hash |
| H-402 | `agents/roles/*.md` (analyst, developer, reviewer) | `tools:` senza Bash libero | T-403, V8 | Insieme completo dei ruoli |
| H-403 | `agents/workflow/gate.py` | Nessun avanzamento senza approvazione umana | T-404, V16 | Approvazione vuota o di un agente rifiutata |
| H-404 | `agents/policy/policy.json`, `floor.json` | Pavimento hard-deny non rimovibile | T-405, V8 | Ogni voce del pavimento coperta |
| H-405 | asset `ecc-code-reviewer` + A/B | Recall ≥ baseline, falsi allarmi ≤ baseline, ≥ +1 difetto (Q-6) | T-406 | Elenco D1–D10 approvato 2026-10-29 e da congelare **prima** (**[N]** file del diff e chi semina, Q-5) |
| H-406 | `docs/poc-report.md` | Ogni criterio 1–10 con evidenza; esito GO/NO-GO/piano B al SUPERVISORE | E2E-PoC-6 | Criterio 10 = N/A motivato (Q-12); nessun criterio senza evidenza |
| H-407 | `agents/plugin/.claude-plugin/plugin.json`, `workflows/limet-change.js` minimo | Workflow riconosciuto e invocato in sessione Claude Code reale; 2 agenti in parallelo; gate; ripresa; altrimenti piano B nel rapporto | T-407 + V13–V19 | HOME isolato (Q-13); nessuna scrittura in `~/.claude` reale; cache del plugin |
| H-308 | schemi `plan/result/findings/verdict/reading/merge/risk`, `state.json`, `events.jsonl` | Schemi con campi `risk` (task, severità, descrizione, evidenza, ≥ 2 opzioni, raccomandazione, stato decisione) | T-308 | Mappatura stati R17 → 4 stati LIMET |
| H-408 | `agents/plugin/scheduler/limet-scheduler.js` | Funzioni pure: `ready`, onde disgiunte, `budget = min(...)`, priorità | T-408 | Cap rispettati; nessun I/O |
| H-409 | `agents/plugin/skills/limet-runner/` | Verifica SHA e worktree pulito; redazione segreti; `INFRA_ERROR`; solo riepilogo nel contesto LLM | T-409 | SHA diverso rifiutato |
| H-611 | `agents/plugin/agents/_common.md` | Ogni ruolo lo richiama; regole KB §3.8 | T-610 | Modelli come da tabella |
| H-501 | test agnosticità | Nessun hook/tool/memoria/`.claude/plans` negli asset | T-501 | Insieme completo degli asset |
| H-502 | asset `ecc-*` + rules per ruolo | 4 regole `common/` escluse | T-502 | Prefisso `ecc-` ovunque |
| H-503 | test nomi/piani | Nessun file di piano ECC fuori dai documenti LIMET; proposte in `ecosystem/proposals/` | T-503 | Nessuna collisione di nomi |
| H-601…H-610 | `agents/plugin/agents/*.md` | Ruolo conforme alla riga di IN §3.5 / §5 (modello, tools, output) | T-403, T-601…T-609 | Nessun Bash libero; reviewer ≠ modello developer |
| H-612 | `risk-assessor`, `RISK-<task>.json`, voci in `QUESTIONS.md` | Lanciato a VERIFIED; sola lettura; ≥ 2 opzioni; non corregge | T-611 | «Nessun rischio» sempre con elenco controlli |
| H-701…H-711 | `limet-change.js` e moduli | Come da riga di IN §5 (R-collegati) | T-701, T-703…T-711 + V1–V11, V14–V18 | Stato scritto solo dal workflow |
| H-702 | `limet-agents.ps1/.sh` | `init|update` idempotente; non sovrascrive `limits.json`, `runner.config.json`, `.mcp.json` | T-702, V13 | Nessuna modifica a `~/.claude` |
| H-712 | documenti LIMET M.1–M.9 IT/EN | Ciclo LIMET e gate | T-712, V19 | Re-index a cura dell'utente |
| H-713 | `night` in `limits.json`, macchina a stati del run non presidiato, kill switch, dead-man switch | Il run termina **sempre** per una delle cause previste (task esauriti, durata, budget, max task, kill switch, dead-man) e lascia il repository ripartibile da checkpoint; con `stopOnQuestions` = `false` il task dubbio va in `WAITING_SUPERVISOR`, i **dipendenti** si bloccano e gli **indipendenti** proseguono; `maxDurationMin` = 480 di default; nessun permesso è ampliato rispetto alla modalità presidiata | T-713, E2E-AUT-1, E2E-AUT-2 | **Neutralità dei permessi** (ADR 0003 §2): confronto automatico dei permessi effettivi presidiato vs non presidiato, devono coincidere; nessun `deny` di H-404/H-710 è derogabile dalla configurazione `night`; il kill switch è letto a ogni passo, non solo all'avvio |
| H-714 | registro delle decisioni autonome + sezione «decisioni autonome» di `REPORT_NOTTURNO.md` | Ogni decisione presa dentro il perimetro di autonomia (ADR 0003 §3) è registrata con task, motivazione, evidenza e modo di annullarla; un run senza decisioni autonome produce comunque la sezione, esplicitamente vuota; il report non dichiara mai «completato» un run `INCOMPLETO` | T-714, E2E-AUT-1 | Nessun segreto e nessun prompt integrale nel registro (coerente con il vincolo del `metrics-observer`); il registro è append-only; una decisione fuori perimetro non viene registrata ma **impedita** |
| H-715 | allow-list dei ruoli non presidiati | Girano non presidiati **solo** `developer`, `tester`, `runner`, `test-reader`, `reviewer`, `metrics-observer`; al turno di `analyst`, `validator`, `integrator`, `risk-assessor` o `commit-commands` il task passa a `WAITING_SUPERVISOR` con motivazione esplicita e il run prosegue sugli indipendenti | T-715, E2E-AUT-1 | L'allow-list è **applicata dal codice** e non configurabile verso l'alto da `limits.json`; un ruolo ignoto è trattato come escluso (default negato, non default permesso) |
| H-800 | `docs/ecc-hooks-analysis.md` | Ogni dei 27 hook con decisione motivata approvata | Revisione | Nessun codice hook eseguito |
| H-800b | hook HELIOS propri | Cross-platform, senza rete, workspace-only, log con hash, nessun file ECC in esecuzione | 5 criteri per hook (A6); nomi **[N]** dopo H-800 | Nessun codice ECC copiato in esecuzione |
| H-801 | `agents/policy/llm-registry.json` | Chiamata non registrata → CI fallisce | T-801 | Elenco completo delle chiamate |
| H-802 | wrapper AgentShield | Pin, hash, SBOM, senza rete e `--opus` | T-802 | Licenze dipendenze (Q-10) |
| H-803 | script setup `HLS_*` | Solo con autorizzazione esplicita; non tocca collection esistenti | T-803 | Conteggio punti delle collection esistenti invariato |
| H-804 | accesso KB degli agenti | Sola lettura; consultazioni in `events.jsonl`; degrado senza contesto | T-804 | Tester senza Graphify né codice del developer |
| H-8b01…H-8b08 | `api/`, `ui/` | Come da riga di IN §5 | T-8b01…T-8b10, T-405, T-304 | C23–C27 |
| H-8b09 | gate di avvio + dialog di prima configurazione (`api/`, `ui/`) | Nessuna operazione della dashboard parte con esito diverso da `OK`; la dialog si apre pre-compilata con i valori dell'autodiscovery e con i soli campi mancanti segnalati; al salvataggio il file viene scritto nella destinazione scelta e la verifica rieseguita | T-8b11, E2E-CFG-1 | La UI **chiama** il modulo H-309, non riscrive la logica; la dialog non accetta segreti; nessuna scrittura fuori dalla destinazione dichiarata; gate non aggirabile da chiamata diretta all'API (C23) |
| H-8b10 | pannello di avvio del run non presidiato (`api/`, `ui/`) | Prima dell'avvio la UI mostra perimetro di autonomia, ruoli ammessi, limiti (`maxDurationMin`, budget, max task) e condotta su domanda bloccante; il run parte **solo** da sessione umana autenticata; durante il run il kill switch è raggiungibile e ha effetto entro il passo successivo | T-8b13, E2E-AUT-2 | C24: nessun avvio pianificato senza umano; la UI **non** può ampliare i permessi né derogare ai `deny` (neutralità dei permessi); avvio non attivabile da chiamata diretta all'API senza i controlli di H-8b01 (C23) |
| H-901 | scaffold E2E, `E2E_VERIFICATION.md` | V1–V24 passati su 3 OS | E2E | Costo token entro tetto 100 USD |
| H-902 | `agents/plugin/README.md` | Installazione, configurazione, cache, errori comuni | V13 | Comandi del README eseguiti davvero |
| H-1001 | log immutabili, sandbox, SBOM, audit | Audit a 5 criteri tutti superati; esito in `docs/audit-hardening.md` | Audit a 5 criteri (A6); nessun T-xxx **[N]** | Dipendenze e licenze |
| H-1101 | manuali, NOTICE, runbook | Revisione approvata | Revisione | NOTICE coerente con il ledger |
| H-1201 | rollout sul pilota | Metriche, rollback provato, parere legale | Pilota | Pilota scelto (**[N]**) |

## Strategia di test — per ogni task

| # task | Unit test previsti/esistenti | Test e2e (rif. `E2E_VERIFICATION.md`) | Se omesso, motivazione |
| ------ | ----------------------------- | -------------------------------------- | ---------------------- |
| H-101 | no | no | Documento decisionale. Verifica: approvazione scritta del SUPERVISORE (registrata nell'ADR) |
| H-102 | no | no | Documento. Verifica: approvazione SUPERVISORE; controllo manuale che contenga regola C28 e i non-negoziabili §2.3 |
| H-103 | no | no | Documento decisionale. Verifica: decisione scritta (IN §5 Done) |
| H-301 | no | E2E-PoC-5 (originali invariati) | Azione manuale del SUPERVISORE; verifica per confronto con gli originali |
| H-302 | sì: T-302 | no | La scansione segreti in CI è esaminata da T-302; non serve e2e |
| H-303 | sì: T-303 | no | Schemi esercitati direttamente da T-303 |
| H-304 | sì: T-304 | E2E-PoC-5 (parità sugli originali) | — |
| H-305 | sì: T-305 | no | Test statico sui confini |
| H-306 | sì: T-306 | E2E-PoC-4 (CI su 3 OS) | La matrice si dimostra solo girando la CI |
| H-309 | sì: T-309 | E2E-CFG-1 (via H-8b09) | Il modulo si esercita con unit test su file temporanei; lo scenario di prima configurazione è e2e perché coinvolge la UI |
| H-8b09 | sì: T-8b11 | E2E-CFG-1 | — |
| H-8b10 | sì: T-8b13 | E2E-AUT-2 | Il pannello mostra il perimetro e lo stop; l'effetto reale del kill switch è E2E-AUT-2 |
| H-307 | no | no | Modifica di una riga di documentazione. Verifica: `Select-String -Path CLAUDE.md -Pattern 'C:\\Progetti\\CEREBRO'` senza risultati (eseguita dal SUPERVISORE) |
| H-401 | sì: T-401, T-402 | E2E-PoC-1, E2E-PoC-2 | — |
| H-402 | sì: T-403 | V8 | — |
| H-403 | sì: T-404 | V16 (parte gate) | — |
| H-404 | sì: T-405 | V8 | — |
| H-405 | sì: T-406 | no | A/B misurato da T-406 |
| H-406 | no | E2E-PoC-6 (revisione del rapporto) | Documento di sintesi. Verifica: ogni criterio 1–10 cita l'evidenza |
| H-407 | sì: T-407 (manifest) | V13, V14, V15, V16, V17, V18, V19 | Il test automatico non può provare la registrazione reale in Claude Code: la coprono V13/V14 |
| H-201 | sì: T-201 | no | — |
| H-202 | sì: T-202 | no | — |
| H-203 | no | no | Documento/checklist. Verifica: firma del SUPERVISORE |
| H-204 | sì: T-204 | no | — |
| H-308 | sì: T-308 | no | — |
| H-408 | sì: T-408 | V15, V2 | — |
| H-409 | sì: T-409 | V3 | — |
| H-611 | sì: T-610 | no | — |
| H-501, H-502, H-503 | sì: T-501, T-502, T-503 | no | — |
| H-601 | sì: T-403 | no | — |
| H-602 | sì: T-601 | V1 | — |
| H-603 | sì: T-602 | V1, V3, V8 | — |
| H-604 | sì: T-603 | V10 | — |
| H-605 | sì: T-604 | V6 | — |
| H-606 | sì: T-605 | V4 | — |
| H-607 | sì: T-606 | V1 | — |
| H-608 | sì: T-607 | V1 | — |
| H-609 | sì: T-608 | V20, V21, V22, V23, V24 | — |
| H-610 | sì: T-609 | V12 | — |
| H-612 | sì: T-611 | V1 (sezione rischi nel report) | — |
| H-701 | sì: T-701 | V14, V17 | — |
| H-702 | sì: T-702 | V13 | — |
| H-703…H-711 | sì: T-703…T-711 | V1, V2, V3, V5, V6, V7, V8, V9, V10, V11, V15, V16, V18 (mappa in E2E_VERIFICATION) | — |
| H-712 | sì: T-712 | V19 | Verifica documentale |
| H-713 | sì: T-713 | E2E-AUT-1, E2E-AUT-2 | Le condizioni di fine corsa (durata, budget, kill switch, dead-man) si verificano con unit test su orologio e registro simulati; il comportamento end-to-end su un run reale è E2E-AUT-2 |
| H-714 | sì: T-714 | E2E-AUT-1 | Il contenuto del registro si verifica con unit test; la presenza della sezione nel report del mattino dopo un run con parcheggio è E2E-AUT-1 |
| H-715 | sì: T-715 | E2E-AUT-1 | L'allow-list si verifica con unit test, ruolo per ruolo, incluso un ruolo ignoto (default negato) |
| H-800 | no | no | Documento di analisi. Verifica: decisione motivata per ogni hook, approvata dal SUPERVISORE |
| H-800b | **[N]** nomi test da fissare dopo H-800 | no | **Verifica definita (audit A6, 2026-10-30)**: per **ogni** hook replicato un test deve dimostrare (1) esecuzione identica su Windows e Linux/macOS (C13), (2) nessuna connessione di rete, (3) nessuna scrittura fuori dal workspace e in particolare su `~/.claude` (C05), (4) evento di log con hash prodotto a ogni invocazione, (5) nessun file ECC presente nel percorso di esecuzione (C04). Un hook senza i 5 punti non si replica. Nomi e conteggio dei test si fissano con l'esito di H-800 |
| H-801, H-802, H-803, H-804 | sì: T-801, T-802, T-803, T-804 | no | — |
| H-8b01…H-8b08 | sì: T-8b01…T-8b10, T-405, T-304 | V22 (solo report SPA), E2E dashboard in Fase 9 | — |
| H-901 | sì: V1–V24 | tutti (E2E_VERIFICATION) | — |
| H-902 | no | V13 (installazione seguendo il README) | Documento |
| H-1001 | **[N]** nessun T-xxx in IN §6 | V23 (privacy/sicurezza) | **Verifica definita (audit A6, 2026-10-30)**: audit documentale con esito osservabile, 5 criteri tutti obbligatori — (1) **log immutabili**: catena di hash verificata su un run reale, un'alterazione manuale di una riga deve essere rilevata; (2) **sandbox**: prova che il runner gira senza rete e senza scrittura fuori dal workspace (riuso delle asserzioni di T-401/T-402); (3) **SBOM**: file SBOM generato in CI, completo delle dipendenze dirette e transitive, confrontato con il lockfile; (4) **licenze**: nessuna dipendenza fuori dall'allowlist del ledger H-202; (5) **segreti**: scansione del repository e dei log senza riscontri (coerente con V23). Esito scritto in `docs/audit-hardening.md` con evidenza per criterio; un criterio «non determinabile» equivale a non superato |
| H-1101 | no | no | Documento. Verifica: revisione approvata |
| H-1201 | no | rollout pilota con metriche | Verifica: metriche del pilota, rollback provato, parere legale |

## Registro modifiche — una riga per file toccato

> Compilare **al momento della modifica**. Per file in un altro modulo, rimando al registro di quel modulo.

| Task | File | Tipo | Cosa è cambiato | Perché | Verifica collegata |
| ---- | ---- | ---- | --------------- | ------ | ------------------ |
| — (avvio) | `limet/changes/0001-helios-poc/PLAN_helios-poc.md` | creato | Piano del PoC con scope, domande Q-1…Q-11, rischi | Richiesta SUPERVISORE 2026-10-29 | Revisione e approvazione SUPERVISORE |
| — (avvio) | `limet/changes/0001-helios-poc/TASK_LIST.md` | creato | Elenco task, strategia di test, mappa criteri | idem | idem |
| — (avvio) | `limet/changes/0001-helios-poc/TEST_PLAN.md` | creato | Test T-xxx con comandi di riesecuzione | idem | idem |
| — (avvio) | `limet/changes/0001-helios-poc/E2E_VERIFICATION.md` | creato | Scenari e2e V8, V13–V19 e criteri go/no-go | idem | idem |
| — (avvio) | `limet/changes/0001-helios-poc/PLAN_helios-poc.md`, `TASK_LIST.md`, `TEST_PLAN.md`, `E2E_VERIFICATION.md` | modificato | Registrate risposte Q-4…Q-7, Q-12, Q-13; PLAN APPROVATO | Decisioni del SUPERVISORE 2026-10-29 | Revisione SUPERVISORE |
| H-101 | `docs/adr/0001-helios.md` | creato | ADR fondativo (stato Proposto): owner, perimetro ECC, pin, revoca | Task H-101 | Approvazione SUPERVISORE (revisione documento) |
| H-102 | `docs/CONSTITUTION.md` | creato | 7 non negoziabili (IN §2.3, C28) con verifica e fonte; approvata 2026-10-29 20:18 | Task H-102 | Approvazione SUPERVISORE; controllo «C28» e «valutazione» |
| H-103 | `docs/adr/0002-runtime.md` | creato | ADR runtime: Python ≥ 3.11, JS solo scheduler/workflow, comandi test; approvato 2026-10-29 20:24 | Task H-103 | Approvazione SUPERVISORE |
| — (guide) | `guides/README.md`, `guides/01-SETUP-DOPO-IL-CLONE.md` | creati | **Nuova cartella `guides/`.** Indice delle guide operative + guida di configurazione dopo il clone in 6 passi: prerequisiti verificabili, clone, **divieto di eseguire `limet.ps1`** (rischio R-8) con procedura di recupero, adattamento dei percorsi locali (con rimando a H-309), registrazione in CEREBRO e indicizzazione `CRB_helios`, Graphify, ordine di lettura dei documenti, tabella «cose da non fare mai», diagnostica | Richiesta SUPERVISORE 2026-10-30: chi clona HELIOS deve poter configurare tutto senza conoscere il progetto. Collocazione in `guides/` decisa dal SUPERVISORE (scartate le alternative «amplia H-1101» e «solo README») | Prova sul campo: NARO esegue la guida su macchina pulita e riporta il punto esatto in cui si blocca. Materiale da consolidare in **H-1101** (manuali) |
| — (guide) | `README.md` | modificato | Tabella «Struttura del repository»: aggiunta `guides/`, `log/` ora cita anche la guida ai branch; sezione «Per iniziare» riscritta con il rimando in testa alla guida di setup | idem | Lettura del README da parte di chi clona |
| — (versionamento) | `.gitignore` | creato | **Nuovo file.** Negazioni `!/limet/`, `!/AGENTS.md`, `!/CLAUDE.md` (precedenza sul blocco `LIMET:EXCLUDE` di `.git/info/exclude`, mai sovrascritte da `limet.ps1`); esclusione di `config/helios.local.json` (H-309, C20), `graphify-out/`, `.graphifyignore`, artefatti Python, file di lock Office | Decisione SUPERVISORE 2026-10-30: in HELIOS `limet/` è **prodotto** (H-301, H-712), non strumentazione locale; i 4 documenti di progettazione esistevano in una sola copia locale | `git check-ignore -q` su repo temporaneo e su HELIOS: `limet/**`, `AGENTS.md`, `CLAUDE.md` **non** ignorati; `graphify-out/` e `config/helios.local.json` ignorati; `git status` mostra 33 file. Nucleo minimo: sarà esteso da **H-302** |
| — (versionamento) | `limet/changes/0001-helios-poc/PLAN_helios-poc.md` | modificato | §8: nuovo rischio **R-8** (`limet.ps1 update` sovrascrive la documentazione LIMET ora versionata, con l'evidenza che `limet/changes/` non è mai toccata); nuova riga di storico | idem | Controllo di `git status` dopo ogni `limet.ps1 update`; rilevante per H-712 |
| — (proprietà dei file) | `.gitignore` | modificato | **Regola di proprietà.** Sostituito `!/limet/` con `/limet/*` + `!/limet/changes/` + `!/limet/archive/`: **24 file tolti dal versionamento** (`FRAMEWORK_MANUAL.md`, `MASTER_INDEX.md`, `ONBOARDING_CHECKLIST.md`, `scripts/` ×3, `templates/` ×18). `AGENTS.md` e `CLAUDE.md` restano versionati. Commento esplicativo del merge silenzioso. La regola copre da sola ogni futura `changes/000N-…` | Obiezione del SUPERVISORE: se un altro clone esegue `limet.ps1 update`, git merga i file rigenerati **riga per riga e senza conflitto**, cancellando in silenzio i documenti master. Scartate perché inefficaci: wrapper con snapshot, manifesto SHA-256, hook `pre-commit`, workflow CI, branch protection + `CODEOWNERS` (diff da 1187 righe non rivedibile) | `git check-ignore -q --no-index` su 11 percorsi: i 24 file installer **ignorati**; `limet/changes/0001-…`, `limet/changes/0002-futuro/` (futura), `limet/archive/`, `AGENTS.md`, `CLAUDE.md`, `guides/` **versionati**. Serve `git rm --cached` sui 24 già tracciati da `787d117` |
| — (proprietà dei file) | `guides/01-SETUP-DOPO-IL-CLONE.md` | modificato | **Passo 2 capovolto**: da «⛔ non eseguire `limet.ps1`» a «✅ eseguilo, ricostruisce il framework in locale», con verifica `git status` vuoto e spiegazione del perché è sicuro. **Passo 3 riscritto come «Cosa va adattato a mano»**: tabella file/righe (AGENTS 52 e 75, CLAUDE 54 e 77), divieto di `git add -A`, criterio «diff rivedibile» che motiva la differenza fra i due gruppi di file. Passo 4: `register_project.py instructions` da ⛔ a ⚠️. Premessa con tabella «nel repo / da ricostruire»; «cose da non fare mai» e diagnostica riallineate | La guida descriveva un divieto che non serve più e taceva ciò che invece va fatto a mano | Prova sul campo: NARO esegue la guida su macchina pulita; `git status` vuoto dopo il passo 2 |
| — (proprietà dei file) | `limet/changes/0001-helios-poc/PLAN_helios-poc.md` | modificato | §8: **R-8 chiusa** (eliminata, non mitigata) con la spiegazione del merge silenzioso e la conseguenza per **H-712** (le modifiche a LIMET vanno fatte nel repo sorgente o, dopo H-301, nella copia interna); nuovo rischio **R-8b** (percorso CEREBRO in `AGENTS.md`/`CLAUDE.md`, impatto basso, risolto da H-309); nuova riga di storico che corregge le due precedenti | idem | Conteggi invariati: 76 task / 65 test. Nessun task aggiunto |
| — (non presidiata) | `docs/adr/0003-modalita-non-presidiata.md` | creato | ADR della **modalità non presidiata (UNATTENDED)**: esclusione motivata dell'auto-mode assoluto; principio di **neutralità dei permessi**; perimetro di autonomia; azioni di ruolo ≠ decisioni; allow-list dei ruoli; parametri `night` (chiude ALA §13 q.9); fine corsa garantita; prova a posteriori; avvio da sessione umana; collocazione dopo il GO | Richiesta SUPERVISORE 2026-10-30: «verifica se fattibile, poi dimmelo e poi pianifichiamo». Decisioni Q-14…Q-18 | Revisione SUPERVISORE; coerenza con IN §1/§2.3, C24, C28, ALA R3/R8/R12/R19/R21 |
| — (non presidiata) | `limet/changes/0001-helios-poc/TASK_LIST.md` | modificato | Aggiunti **H-713**, **H-714**, **H-715** (Fase 7) e **H-8b10** (Fase 8b) alla tabella principale, all'indice per fase (Fase 7: 12+1→15+1; Fase 8b: 9→10), al totale (72→**76**), alle schede di dettaglio con i controlli C28 specifici e alla tabella «Strategia di verifica per task» | idem | T-713, T-714, T-715, T-8b13, E2E-AUT-1, E2E-AUT-2 |
| — (non presidiata) | `limet/changes/0001-helios-poc/TEST_PLAN.md` | modificato | Aggiunti **T-713** (10 metodi), **T-714** (8 metodi), **T-715** (7 metodi) e **T-8b13** (5 casi UI) con stato pre/post e comandi a livello classe e metodo, alla profondità di T-309; nuova sottosezione di dettaglio con autorevisione §5.1; totale 61 → **65**; nota di testa e nota sui percorsi dei file aggiornate | idem | Autorevisione §5.1 riportata nel documento: cinque alterazioni plausibili del codice e il test che le intercetta |
| — (non presidiata) | `limet/changes/0001-helios-poc/E2E_VERIFICATION.md` | modificato | Nuovi scenari **E2E-AUT-1** (11 passi: parcheggio, dipendenti bloccati, indipendenti avanzano, ruolo escluso, commit sì / push-merge no, report e registro, confronto permessi, ripresa) ed **E2E-AUT-2** (10 passi: durata, budget, kill switch creato a run avviato, dead-man, checkpoint per ogni causa, avvio senza sessione umana, configurazione che amplia i permessi); intestazione aggiornata per dichiarare che **completano V11 senza sostituirlo** | idem | Da eseguire dopo il GO; V11 di ALA resta valido |
| — (non presidiata) | `limet/changes/0001-helios-poc/PLAN_helios-poc.md` | modificato | §2 conteggi (76 task, 65 test, + E2E-AUT-1/2); §4 nuove domande **Q-14…Q-18** con risposta, data e autore; §5.4 Fase 7 e Fase 8b + nuovo gruppo **D-bis**; §5.7 righe R3, R8, R18, R20 e nota di testa; §6 72→76; §7 61→65 e scenari; **nuova §10 «Modalità non presidiata»**; tre righe di storico | idem | Coerenza incrociata fra i quattro documenti e l'ADR 0003 |
| — (non presidiata) | `log/HELIOS_Distribuzione_task.xlsx` | modificato | Rigenerato per i quattro nuovi task: nuovi pacchetti **PK-19b** (H-713 + H-715, SCIBETTA), **PK-20b** (H-714, NARO) e **PK-26c** (H-8b10, NARO); 34 → **37** pacchetti; Post-GO 50 → **54** (Pre-GO invariato a 22, il PoC non cambia); bilanciamento SCIBETTA **36** / NARO **36** / SUPERVISORE 4. Assegnazione ancora **[P]** | Allineamento della distribuzione ai 76 task | Rilettura in sola lettura con openpyxl: 54 righe Post-GO, H-713/H-714/H-715 e H-8b10 presenti, 37 pacchetti; approvazione SUPERVISORE dell'assegnazione (ancora **[P]**) |
| — (audit A1) | `limet/changes/0001-helios-poc/PLAN_helios-poc.md` | modificato | §5.2: C11–C18 e C20–C27 riportano ora il testo deciso in R05 §2 con citazione puntuale; etichetta da **[I]** a **[F]**; nota di verifica sostituita | Audit 2026-10-30: le decisioni vincolanti erano riassunte come interpretazione non verificata | Confronto riga per riga con R05 §2 (riga «DECISO dal SUPERVISORE») e R05 §3; revisione SUPERVISORE |
| — (audit A2) | `limet/changes/0001-helios-poc/PLAN_helios-poc.md` | modificato | Nuova §5.7: matrice di copertura R1–R27 e M.1–M.9 (ALA) → task H-xxx → scenari V; nessun requisito scoperto | Audit 2026-10-30: ALA è vincolante (IN §9) ma la copertura dei suoi requisiti non era dimostrabile | Controllo incrociato ID ALA ↔ TASK_LIST ↔ E2E_VERIFICATION; revisione SUPERVISORE |
| — (audit A4) | `limet/changes/0001-helios-poc/TASK_LIST.md` | modificato | Riga «verifica completezza» del registro: «69 test» → **58**, con nota di correzione | Audit 2026-10-30: dato obsoleto in contrasto con PLAN §7 e TEST_PLAN | Conteggio degli ID `T-xxx` su IN §6 = 58 |
| — (audit A5) | `limet/changes/0001-helios-poc/PLAN_helios-poc.md`, `TEST_PLAN.md` | modificato | Q-11 chiusa parzialmente (framework e comandi confermati da H-103); residuo riaperto come **Q-11b**; riferimenti allineati nel TEST_PLAN | Audit 2026-10-30: Q-11 risultava «APERTA» nel PLAN ma «chiusa» nella scheda H-103 (`done`) | Coerenza PLAN §4 ↔ TASK_LIST H-103 ↔ TEST_PLAN |
| — (audit A6) | `limet/changes/0001-helios-poc/TASK_LIST.md`, `PLAN_helios-poc.md` | modificato | Verifica definita per **H-800b** (5 criteri osservabili per ogni hook replicato) e **H-1001** (audit a 5 criteri, esito in `docs/audit-hardening.md`); PLAN §7 allineato | Audit 2026-10-30: due task senza strategia di verifica, in contrasto con la regola LIMET e con C28 | Criteri osservabili con esito binario; «non determinabile» = non superato |
| — (audit A9) | `limet/changes/0001-helios-poc/PLAN_helios-poc.md` | modificato | §8 riscritta come tabella con probabilità, impatto, esposizione, titolare e segnale di innesco; aggiunta R-7 (chiusa dall'audit) e la regola di applicazione di C28 | Audit 2026-10-30: C28 impone la valutazione dopo ogni task ma mancava il formato su cui scriverla | Revisione SUPERVISORE; ricalibrazione delle stime con le misure del PoC |
| — (config locale) | `limet/changes/0001-helios-poc/TASK_LIST.md` | modificato | Aggiunti **H-309** (Fase 3, configurazione locale) e **H-8b09** (gate di avvio della dashboard) alla tabella principale, all'indice per fase (Fase 3: 7→8, Fase 8b: 8→9), al totale (70→**72**), alle schede di dettaglio e alla tabella «Strategia di verifica per task» | Richiesta SUPERVISORE 2026-10-30: chi clona HELIOS non deve ereditare la configurazione di un altro; attua C20 | T-309, T-8b11, T-8b12, E2E-CFG-1 |
| — (config locale) | `limet/changes/0001-helios-poc/TEST_PLAN.md` | modificato | Aggiunti **T-309** (10 metodi: precedenza, validazione semantica, `configVersion`, esempio senza dati locali, file ignorato da git, autodiscovery, nessuna rete), **T-8b11** (gate API) e **T-8b12** (dialog UI); totale 58 → **61**; nota sui percorsi dei file di test allineata | idem | Autorevisione §5.1: ogni metodo asserisce un comportamento distinto e può fallire per il motivo dichiarato |
| — (config locale) | `limet/changes/0001-helios-poc/E2E_VERIFICATION.md` | modificato | Nuovo scenario **E2E-CFG-1** (prima configurazione: gate, dialog pre-compilata, percorso inesistente, percorso non semanticamente valido, scrittura, `git check-ignore`, riavvio, precedenza `HELIOS_CONFIG`, chiamata diretta all'API, `helios doctor`); intestazione aggiornata per distinguerlo dagli scenari ALA | idem | Da eseguire su Windows e su Unix |
| — (config locale) | `limet/changes/0001-helios-poc/PLAN_helios-poc.md` | modificato | §3 conteggi (72 task, 61 test, + E2E-CFG-1); §5.2 C20 e C23 «Incide su» con H-309/H-8b09; §5.4 Fase 3 e Fase 8b; §5.7 matrice R22 e M.5; §6 70→72; §7 58→61 e E2E-CFG-1; nuova riga di storico con le decisioni del SUPERVISORE | idem | Coerenza incrociata fra i quattro documenti |
| — (config locale) | `log/HELIOS_Distribuzione_task.xlsx` | modificato | Rigenerato: Pre-GO 21→**22** (H-309 in **PK-07b**, dopo H-301), Post-GO 49→**50** (H-8b09 in **PK-26b**, dopo H-309/H-8b01/H-8b03); 32→**34** pacchetti; bilanciamento SCIBETTA **34** / NARO **34** / SUPERVISORE 4 | idem | Rilettura in sola lettura con openpyxl (righe, pacchetti e assegnazioni verificate); approvazione SUPERVISORE dell'assegnazione (ancora **[P]**) |
| — (distribuzione correzione) | `log/HELIOS_Distribuzione_task.xlsx` | modificato | PK-05 diviso in PK-05 (H-306) e PK-05b (H-302); PK-05b/06/07 «dopo H-301»; 32 pacchetti | Le dipendenze di H-302/H-304/H-305/H-307 su H-301 rendevano non avviabile parte dell'Onda 1 | Revisione SUPERVISORE |
| — (distribuzione) | `log/HELIOS_Distribuzione_task.xlsx` | creato | Fogli Dashboard, Pre-GO (21 task), Post-GO (49), Pacchetti (32, PK-00…PK-30 + PK-05b), Sincronizzazione, Istruzioni; assegnazione NARO/SCIBETTA **[P]** | Richiesta SUPERVISORE 2026-10-29 «distribuzione task»; non modifica task né requisiti | Revisione e approvazione SUPERVISORE dell'assegnazione |
| — (dettaglio) | `limet/changes/0001-helios-poc/E2E_VERIFICATION.md`, `TASK_LIST.md`, `PLAN_helios-poc.md` | modificato | E2E: passi dettagliati V1–V12, V20–V24; TASK_LIST: schede di dettaglio per task; PLAN: storico | Richiesta SUPERVISORE «documenti più dettagliati» 2026-10-29 | Revisione SUPERVISORE |
| — (verifica completezza) | `limet/changes/0001-helios-poc/PLAN_helios-poc.md`, `TASK_LIST.md`, `TEST_PLAN.md`, `E2E_VERIFICATION.md` | modificato | Estesi a tutte le Fasi 1–12 + 8b: 70 task, **58** test T-xxx (la cifra «69» scritta in origine qui era errata — corretta il 2026-10-30 dall'audit, rilievo A4; conteggio verificato su IN §6), V1–V24, C01–C28, Q1–Q9, regola planner ECC, gate go/no-go come decisione, piano B | Richiesta SUPERVISORE (verifica di completezza) 2026-10-29 | Tabella di copertura in chat; revisione SUPERVISORE |

## Esito implementazione (da compilare a lavoro concluso)

- **Data**: —
- **Sintesi esito**: —
- **Scostamenti rispetto al piano**: —
- **Esito test**: — (`TEST_EXECUTION.md`)
