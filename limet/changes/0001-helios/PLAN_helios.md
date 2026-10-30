# Piano — HELIOS: progetto completo (Fasi 1–12 + 8b), con PoC go/no-go (Fase 4) come gate di decisione

> Etichette: **[F]** fatto verificato · **[I]** ipotesi/stima · **[P]** proposta · **[N]** non verificato / informazione mancante.
> Fonti (cartella `docs-limet\analisi-input\`): **IN** = `HELIOS_ANALISI_PER_LIMET.md`; **00…06** = file affiancati; **R05** = `05_roadmap_e_rischi.md`; **ALA** = `ANALISI_limet-agents_con_workflow_claude.md`; **FO1** = `FEASIBILITY_O1_CLAUDE_CODE.html`; **REG** = `REGISTRO_SKILLS_AGENTI_ECC.md`. I rimandi `..\NN` di IN si leggono come file nella stessa cartella.
> Scelta del formato: **PLAN** (non CHANGE_PROPOSAL+SPEC) perché la richiesta del SUPERVISORE indica PLAN + TASK_LIST + TEST_PLAN + E2E_VERIFICATION. **Ambito esteso il 2026-10-29** (verifica di completezza): il piano copre l'intero progetto (Fasi 1–12 + 8b); il PoC (Fase 4) è il gate go/no-go a metà piano, non la fine.

## Metadati

- **Data creazione**: 2026-10-29
- **Autore/agente**: GitHub Copilot CLI (agente LIMET)
- **Stato**: `APPROVATO`
- **Approvato da / data**: SUPERVISORE (@mcibetta) — 2026-10-29 (risposta «si»; versione PoC). **Riconfermato sul piano ESTESO (fasi 1–12 + 8b) il 2026-10-29 20:13** («Approvo il piano esteso»). Restano aperte Q-8…Q-11, non bloccanti per l'avvio
- **Riferimento richiesta originale**: messaggio del SUPERVISORE «Avvia un nuovo progetto LIMET chiamato HELIOS», 2026-10-29
- **Repo target del codice futuro**: `C:\Progetti\VSC\HELIOS-PLATFORM\HELIOS` (risposta del SUPERVISORE, 2026-10-29) **[F]**. Nota: IN §1.3/Q5 cita `C:\Progetti\VSC\HELIOS`; prevale la risposta del SUPERVISORE.

## 1. Obiettivo

Realizzare HELIOS (evoluzione di LIMET con catena di agenti, plugin Claude Code `limet-agents`, hook propri, dashboard, knowledge base) in 13 fasi (1–12 + 8b, IN §4, 69–107 persona-giorni **[I]**). Al centro del piano il **PoC go/no-go (decisione C14, Fase 4)** verifica la fattibilità: ruoli senza Bash libero, runner con allowlist e senza rete, gate umano meccanico sul PLAN, pavimento hard-deny, un asset ECC portato con A/B, plugin Claude Code (H-407). Il rapporto `docs/poc-report.md` sui 10 criteri R05 §5 **[F]** è un **punto di decisione del SUPERVISORE**; le Fasi 5–12 partono solo dopo GO (§5.5). In questa fase si producono **solo documenti**: nessun codice (vincolo del SUPERVISORE).

## 2. Perimetro (scope)

### Dentro perimetro
- Documenti LIMET: questo PLAN, `TASK_LIST.md` (79 task), `TEST_PLAN.md` (66 test T-xxx), `E2E_VERIFICATION.md` (V1–V24 + E2E-PoC + E2E-CFG-1 + E2E-AUT-1/2).
- **Tutte le fasi 1–12 + 8b** (§5.4). Esecuzione immediata: Fasi 1, 3 e 4 (PoC); Fase 2 anticipabile; Fasi 5–12 condizionate al GO.

### Fuori perimetro (esplicitamente escluso)
- Codice HELIOS in questa fase documentale.
- Qualsiasi modifica ai file di input (in particolare FO1 e ALA), alle installazioni LIMET/CEREBRO di COSMO e mudeopen, alle collection Qdrant esistenti **[F]** (vincolo SUPERVISORE; IN §2.3).
- Creazione di collection `HLS_*` (solo H-803, con autorizzazione, IN §8 Q9).
- Build, test, commit, push da parte dell'agente (vincolo SUPERVISORE; AGENTS.md).

## 3. Stato attuale (analisi preliminare)

- **[F]** Repo `helios-ai-hub/HELIOS`: contiene `AGENTS.md`, `CLAUDE.md`, `limet\`, `docs-limet\`, `graphify-out\`, `.github`; **nessun codice HELIOS** (verificato in questa sessione).
- **[F]** Ambiente (dato di IN, non riverificato in questa sessione): Claude Code 2.1.311 in `C:\Users\mscibetta\.local\bin\claude.exe`; Qdrant :6333 e Ollama :11434 (`nomic-embed-text`) attivi; collection `CRB_csi_cosmo`, `CRB_mudeopenbtcet`, `CRB_mudeopensrvet`, `project_docs`; `CRB_limet` assente.
- **[F]** ECC pinnato a commit `25278e7` (v2.2.3): 71 agenti, 311 skill, 97 comandi, 27 hook, 122 rules (01 §0–§7).
- **[F]** In ECC il gate di conferma del piano è solo prosa nel prompt (01 §5): H-403 lo rende meccanico.
- **[F]** In ECC 55/71 agenti hanno `Bash` e i limiti di scrittura sono solo testo (01 §3): motivo di H-402/H-404.
- **[N]** Sintassi, percorso e invocazione dei Dynamic Workflow in Claude Code 2.1.311: da provare in H-407 (IN §3 plugin; ALA §Verifica 4).
- **[N]** Compatibilità del runner Python con Windows nativo: **[I]** da validare nel PoC (issue ECC #2489/#2626/#2674 citate in R05).
- **[N]** Progetto pilota «biblioteca»: i file `GUIDA_ECC_BIBLIOTECA.md` e `ANALISI_PROGETTO_BIBLIOTECA*.md` citati non sono nella cartella di input; ubicazione e stack ignoti.

## 4. Domande contestuali aperte

| # | Domanda | Risposta | Data | Da chi |
| - | ------- | -------- | ---- | ------ |
| Q-1 | Il prompt cita C01–C27; l'input include C28 (valutazione rischi dopo ogni task). C28 è vincolante? | Sì, C01–C28 vincolanti | 2026-10-29 | SUPERVISORE |
| Q-2 | Repo target: `C:\Progetti\VSC\HELIOS` (IN) o `...\HELIOS-PLATFORM\HELIOS`? | `C:\Progetti\VSC\HELIOS-PLATFORM\HELIOS` | 2026-10-29 | SUPERVISORE |
| Q-3 | Cartella della change | `limet\changes\0001-helios\` | 2026-10-29 | SUPERVISORE |
| Q-4 | **[N]** H-405 richiede l'allowlist ECC di Fase 2 (H-201…H-204) o basta un asset scelto a mano? Quale asset ECC portare? | Asset scelto a mano, Fase 2 fuori dal PoC; asset = `code-reviewer` (ECC, sola lettura) su delega del SUPERVISORE («scegli tu») | 2026-10-29 | SUPERVISORE (scelta: agente) |
| Q-5 | Progetto pilota «biblioteca»: percorso, stack (JDK/Maven?), documenti di riferimento | **CHIUSA 2026-10-29 20:23.** Percorso: `C:\Progetti\VSC\HELIOS-PLATFORM\HELIOS\test\` (contiene `ANALISI_PROGETTO_BIBLIOTECA.md`; `GUIDA_ECC_BIBLIOTECA.md` non presente, **[N]** non bloccante). Stack [F: `test\ANALISI_PROGETTO_BIBLIOTECA.md` righe 46–50]: Java 17, Spring Boot 3, Flyway, Maven Wrapper, PIT/JaCoCo/SpotBugs, PostgreSQL 16 su Docker, Testcontainers, SPA statica. **JDK: 17 LTS** (scelto su delega «scegli il più adatto»: minimo di Spring Boot 3, indicato dall'analisi del pilota, supportato da PIT/JaCoCo/SpotBugs; verificato `java -version` = 17.0.7). Prerequisiti ancora da verificare dal SUPERVISORE: `JAVA_HOME` (risulta non definito correttamente), Docker attivo, Maven Wrapper | 2026-10-29 | SUPERVISORE (delega) |
| Q-6 | **[N]** Metrica e baseline per A/B (T-406, criterio R05 §5 n.6): quale metrica, quale soglia, quale baseline | Metrica: difetti noti trovati (recall) su un diff con difetti seminati, a parità di falsi allarmi. Baseline: reviewer minimo scritto da noi (prompt semplice, stessi tool, stesso modello). Soglia «meglio»: recall ≥ baseline e falsi allarmi ≤ baseline, con almeno +1 difetto trovato su 10 difetti seminati. **Elenco dei 10 difetti seminati APPROVATO dal SUPERVISORE il 2026-10-29 (bozza dell'analista):** D1 SQL injection; D2 segreto in chiaro in configurazione; D3 endpoint di modifica senza autorizzazione; D4 `Optional.get()` senza controllo; D5 off-by-one nella paginazione; D6 `String` confrontate con `==`; D7 operazione multi-tabella senza `@Transactional`; D8 risorsa non chiusa; D9 `catch` vuoto; D10 query N+1 in un ciclo. **Regole:** elenco e righe esatte congelati prima di lanciare l'A/B; stesso diff per ECC e baseline; difetto «trovato» solo se indicati file e riga o causa; falsi allarmi contati su righe non seminate. **[N]** Resta: file del diff e chi semina i difetti (dipende dal pilota, Q-5) | 2026-10-29 | SUPERVISORE |
| Q-7 | **[N]** Autenticazione per il PoC (abbonamento vs API key) e tetto costi (IN Q2 = 100 USD per workflow, criterio n.8): come si misura il costo con abbonamento? | Autenticazione: abbonamento (login Claude). Misura del costo **[P]** (scelta delegata all'agente): stima equivalente API = token del log del runner × tariffa API pubblica, dichiarata come stima **[I]**, non come costo reale; token non disponibili = `N/D` (ALA V23). **[N]** Tariffe e fonte dei token da fissare in H-407 | 2026-10-29 | SUPERVISORE (auth); agente (proposta) |
| Q-8 | Soglie gate (copertura differenziale, mutation score, `taskCap`), `.claude/` tracciato in git?, strumenti mutation/static per stack non Java (ALA §10) | **DECISA su delega del SUPERVISORE (2026-10-29 20:17: «decidi tu», «sì se non hanno path locali», «scegli i migliori»).** (1) Soglie **[P]**, da ricalibrare con le misure del PoC/pilota: copertura differenziale ≥ 80 %; mutation score ≥ 60 % sulle sole classi nuove (PIT, ALA R13/§153; se lento, degrado a copertura con evento esplicito); `taskCap` = 12. Eccezioni solo firmate in `exceptions.json`. (2) `.claude/` **tracciato in git** purché senza path locali/segreti; override locali in `.claude/settings.local.json`, in `.gitignore`. (3) Strumenti **[P]**: Java PIT + JaCoCo + SpotBugs (ALA); Python mutmut + coverage.py + ruff + mypy; JS/TS Stryker + c8 + ESLint + tsc. Nomi e versioni da verificare in H-103/H-501 (licenze e compatibilità Windows/Unix, C13) | 2026-10-29 | SUPERVISORE (delega) |
| Q-9 | **[N]** Remote/storia di CEREBRO: IN §2.4 cita `https://github.com/odyssey2073/cerebro.git` (17 commit), IN §8/R05 lo danno [N]. Riverificare prima di H-301 | **APERTA** | — | — |
| Q-10 | **[N]** Licenze dipendenze AgentShield (fino a SBOM H-802) e parere legale prima del rilascio | **APERTA** (non blocca il PoC) | — | — |
| Q-11 | **[N]** Nomi di file/classi/metodi di test di IN §6: sono proposte; conferma framework in H-103 | **CHIUSA PARZIALMENTE con H-103 (`done` 2026-10-29 20:24):** framework e comandi di riesecuzione a livello classe/metodo sono confermati e recepiti in `TEST_PLAN.md`. **Resta [N] (riaperto come Q-11b):** i nomi definitivi di file/classi/metodi si fissano quando esiste il codice, task per task | 2026-10-30 | allineamento audit (A5) |
| Q-11b | **[N]** Nomi definitivi di file, classi e metodi di test: si fissano con il codice, al momento dell'implementazione di ciascun task | **APERTA** (non bloccante: i comandi di riesecuzione sono già definiti da H-103) | — | — |
| Q-12 | **[N]** Il criterio 10 di R05 §5 (test sicurezza dashboard C23–C24) non è valutabile nel PoC (dashboard in Fase 8): escluso, N/A motivato o prototipo minimo? | N/A motivato nel rapporto (dashboard fuori dal PoC), da valutare in Fase 8 | 2026-10-29 | SUPERVISORE |
| Q-13 | **[N]** E2E: nel PoC gli stati completi ALA R17 (V18) e l'ambiente isolato (HOME separato) per V13/V14 sono in scope? | HOME/profilo isolato per V13/V14: sì. V18 / stati R17 completi: no, nel PoC solo workflow minimo; V18 completo in Fase 7 | 2026-10-29 | SUPERVISORE |
| Q-14 | **Modalità non presidiata** — chiude la questione aperta **ALA §13 q.9**: `night.stopOnQuestions` sì/no, durata massima, chi è reperibile | **DECISA**: `stopOnQuestions` = **`false`** (task in `WAITING_SUPERVISOR`, dipendenti bloccati, indipendenti proseguono — R8 non globale, H-707); durata massima **480 minuti**; **nessun reperibile** → run **auto-contenuto**, nessuna notifica attesa in corsa. *Conseguenza*: limite di durata e dead-man switch diventano requisiti di sicurezza primari, e ogni blocco deve restare ripartibile da checkpoint (`resumeFromRunId`) | 2026-10-30 | SUPERVISORE |
| Q-15 | **Modalità non presidiata** — perimetro di autonomia dell'agente | **DECISA**, perimetro **minimo**: solo (1) scelte implementative interne a un task già approvato, (2) retry entro `maxAttempts` = 3 (R12), (3) ordine fra i task pronti dentro il grafo confermato (R3). Restano **fuori**: scrittura di unit test, split di un task (R3) e ogni voce già vietata da C06, C28, C24, R19, R21, R20 | 2026-10-30 | SUPERVISORE |
| Q-16 | **Modalità non presidiata** — quali ruoli possono girare non presidiati | **DECISA**: ammessi `developer`, `tester`, `runner`, `test-reader`, `reviewer`, `metrics-observer`; **esclusi** `analyst`, `validator`, `integrator`, `risk-assessor` (oltre a `commit-commands`, già escluso da ALA §13 q.12). L'allow-list è applicata dal codice (H-715), non è una convenzione | 2026-10-30 | SUPERVISORE |
| Q-17 | **Modalità non presidiata** — collocazione nel piano | **DECISA**: solo **dopo il GO** (Fase 7+). Perimetro del PoC e criteri del gate C14 invariati | 2026-10-30 | SUPERVISORE |
| Q-18 | **Modalità non presidiata** — conflitto rilevato fra Q-15 (commit fuori perimetro) e Q-16 (`developer` ammesso), dato che ALA §1.3 definisce il `developer` come chi «commit sul ramo del task» | **DECISA**: il commit sul ramo del task è **parte del ruolo**, non una decisione autonoma. Di notte il `developer` committa come di giorno; `push` e `merge` restano vietati (R19, R21). Q-15 limita le **decisioni**, non le **azioni già definite dal ruolo**. *Motivazione*: senza commit il `runner` non può operare («mai su codice non committato», ALA §1.3) e il checkpoint perde l'ancoraggio a uno SHA | 2026-10-30 | SUPERVISORE |

## 5. Approccio proposto

Ordine **PoC-first** (richiesta SUPERVISORE; C14): prima i prerequisiti minimi (decisioni scritte e base di repo), poi il PoC, poi il rapporto go/no-go.

```
Fase 1 (H-101→H-103)  ─┐
Fase 3 (H-301→H-307)  ─┴─► H-401 runner ─► H-404 policy ─► H-402 ruoli ─► H-403 gate
                                                         └► H-405 asset ECC + A/B
                                       H-407 plugin Claude Code (parallelo a H-402/H-403)
                                       ──────────► H-406 rapporto go/no-go
```

### 5.1 Decisioni vincolanti applicate al PoC (non riaperte)

| ID | Contenuto (sintesi) | Dove incide nel PoC | Fonte |
| -- | ------------------- | ------------------- | ----- |
| C14 | PoC go/no-go prima della costruzione | Intera change | R05 §3, IN §4 |
| C28 | Dopo ogni task: valutazione rischi/errori/malfunzionamenti; se ne trova, li riporta al SUPERVISORE con evidenza e ≥ 2 opzioni (pro/contro, raccomandazione), senza autocorreggere; se non ne trova, elenca i controlli eseguiti; esito nella nota del task | Regola operativa per **ogni** task (colonna Note della TASK_LIST) | IN §2.3 n.7, R05 §3 |
| Q4 | Python ≥ 3.11 per core/runner/api; UI TypeScript | H-103, test pytest | IN §8 Q4 |
| Q8 | JavaScript solo per scheduler e workflow | H-407 (workflow minimo) | IN §8 Q8 |
| Q7 | Monorepo HELIOS | struttura repo | IN §8 Q7 |
| Q9 | Knowledge base `CRB_<slug>` + `HLS_*` (read-only, H-803) | nessuna scrittura Qdrant nel PoC | IN §8 Q9 |
| Altre C01–C28 | Vincolanti, non riaperte: tabella completa in §5.2 | tutte le fasi | R05 §2–3 |

> Il catalogo completo C01–C28 e Q1–Q9 è riportato in §5.2–§5.3. Nessuna decisione è riaperta; ogni deviazione la decide solo il SUPERVISORE.

### 5.2 Decisioni C01–C28 (vincolanti; R05 §2–3, titolo = intestazione R05)

> **Verifica di trascrizione eseguita il 2026-10-30 (rilievo A1 dell'audit).** Tutte le 28 righe
> sono state confrontate una per una con il testo integrale di R05 §2 (riga «DECISO dal
> SUPERVISORE (2026-10-28)») e con la tabella di sintesi R05 §3. La colonna «Decisione» riporta
> ora il testo deciso, non una sintesi: l'etichetta passa da **[I]** a **[F]** per C11–C18 e
> C20–C27. Le colonne «Incide su» restano **[I]** (derivazione dai task IN §5, non presente in
> R05). Nessuna decisione è stata riaperta: la verifica riguarda la fedeltà della trascrizione.

| ID | Tema (R05) | Decisione | Incide su |
| -- | ---------- | --------- | --------- |
| C01 | Eccezione allo scope «niente framework di terzi» (ALA §2) e a FO1 | A: HELIOS progetto autonomo con proprie regole | H-101, H-102 |
| C02 | Agenti ECC che eseguono build/test/commit | A + permessi configurabili da dashboard (con pavimento non modificabile) | H-401, H-404, H-710, H-8b04 |
| C03 | Rules `common/` sempre caricate | A: 4 regole escluse, altre per ruolo | H-502, T-502 |
| C04 | Hook bloccanti e invasivi | A: nessun hook ECC; analisi e replica propria | H-800, H-800b |
| C05 | Mutazione `~/.claude`, egress, contesto iniettato | A + nessun invio a LLM salvo indispensabile e registrato | H-801, T-801 |
| C06 | Sovrapposizione spec-driven, collisione nomi | A: prefisso `ecc-`, flusso di pianificazione ECC rimosso | H-503, T-503 |
| C07 | Licenze eterogenee | A: registro licenze, solo MIT verificato | H-202, T-202 |
| C08 | Memoria duplicata | A: CEREBRO unica memoria | H-501 |
| C09 | Plan Canvas / Control Pane | A: non portati; dashboard HELIOS | Fase 8b |
| C10 | Supply chain AgentShield | B: pin + hash + SBOM, runner senza rete, senza `--opus` | H-802, T-802 |
| C11 | GitHub App / ecc.tools | **A [F]** (`R05 §2 C11`): App mai installata; blocco a livello di organizzazione GitHub | fuori perimetro |
| C12 | Manutenzione ECC | **A [F]** (`R05 §2 C12`): fork/vendor pinned a `25278e7`, nessun aggiornamento automatico; ogni upgrade è una decisione con test di non regressione | pin `25278e7`; H-203 |
| C13 | Windows **e** Unix | **A [F]** (`R05 §2 C13`): core in un unico runtime portabile, CI a matrice Windows/Linux/macOS | H-306, T-306 |
| C14 | `limet-agents` non esiste: PoC go/no-go | **A [F]** (`R05 §2 C14`): PoC verticale minimo prima di qualsiasi porting ECC | Fase 4, H-406 |
| C15 | Evidenza incompleta su ECC | **A [F]** (`R05 §2 C15`): inventario scriptato al 100% in Fase 2, revisione manuale degli asset in allowlist | Fase 2, H-201 |
| C16 | Qualità output e costo | **A [F]** (`R05 §2 C16`): esperimento A/B con soglie go/no-go; un asset senza miglioramento misurato non entra | H-405, T-406 |
| C17 | «LIMET-agnostico» non verificato | **A [F]** (`R05 §2 C17`): test statico di compatibilità nella CI di `limet-agents` | H-501 |
| C18 | Runner: isolamento e fiducia nei log | **A [F]** (`R05 §2 C18`): allowlist, input dichiarativi, processo senza rete, log con hash, mai commit/push | H-401, T-401/402 |
| C19 | Retrocompatibilità consumatori | A: HELIOS con LIMET interno, installazioni attuali intoccate | H-304, T-304 |
| C20 | Segreti, dati e stato | **A [F]** (`R05 §2 C20`): segreti e config fuori dal repo, solo `projects.example.json` versionato, dati Qdrant e `cache/` mai versionati | H-302, H-309, T-302, T-309 |
| C21 | Cronologia git, ownership, remoti | **A [F]** (`R05 §2 C21`): nuovo repository HELIOS con import della storia in sottocartelle; repository originali invariati — remoto CEREBRO **[N]**, Q-9 | H-301 |
| C22 | Confini (monolite) e release unica | **A [F]** (`R05 §2 C22`): monorepo a moduli con confini imposti (`limet/`, `cerebro/`, `agents/`), API pubbliche dichiarate, test che vietano import fuori contratto, versioning per modulo + tag unico; CI a matrice (opzione C) come supporto raccomandato | H-305, T-305; H-306 |
| C23 | Superficie di attacco UI | **A [F]** (`R05 §2 C23`): loopback, token di sessione, allowlist Host/Origin, CSRF, CSP, azioni solo via API con allowlist | H-8b01, H-8b09, T-8b01…04, T-8b11 |
| C24 | Separazione umano/agente nella UI | **A [F]** (`R05 §2 C24`): i gate si approvano solo da sessione umana interattiva; la UI non esegue build/test/git | H-8b05, T-8b08 |
| C25 | Dipendenze frontend | **A [F]** (`R05 §2 C25`): lockfile, licenze permissive, audit e SBOM in CI, versioni pinnate, aggiornamenti solo via PR | H-8b07, T-8b10 |
| C26 | Grafica avanzata vs portabilità/prestazioni/accessibilità | **A [F]** (`R05 §2 C26`): SVG/Canvas con librerie permissive, WebGL solo opzionale con fallback, matrice browser, budget prestazioni e test WCAG 2.1 AA in CI — la modalità leggera (opzione C) **non** è stata scelta | H-8b06, T-8b09 |
| C27 | Fonte unica dei dati | **A [F]** (`R05 §2 C27`): read model derivato dai file LIMET, dallo stato workflow e dalle API CEREBRO; nessun DB di stato proprio | H-8b02, T-8b05 |
| C28 | Valutazione dei rischi dopo ogni task | A | ogni task; H-612, T-611 |

### 5.3 Decisioni Q1–Q9 (IN §8; vincolanti)

| ID | Decisione | Fonte |
| -- | --------- | ----- |
| Q1 | Progetto pilota «biblioteca» (ubicazione **[N]**, Q-5) | IN §8 |
| Q2 | Tetto di costo 100 USD per workflow | IN §8, R05 §5 crit. 8 |
| Q3 | SUPERVISORE unico titolare / owner ADR | IN §8 |
| Q4 | Python ≥ 3.11 (core, runner, API); UI TypeScript/React | IN §8 Q4 |
| Q5 | Repo `HELIOS` (account `odyssey2073`); percorso locale per il SUPERVISORE: `C:\Progetti\VSC\HELIOS-PLATFORM\HELIOS` | IN §8 Q5; Q-2 |
| Q6 | Nessuna modalità grafica «leggera» ora (C26-C) | IN §8 Q6 |
| Q7 | Monorepo; plugin in `agents/plugin/` | IN §8 Q7 |
| Q8 | JavaScript solo per scheduler/workflow; ADR `docs/adr/0002-runtime.md` | IN §8 Q8 |
| Q9 | Base di conoscenza `CRB_<slug>` + `HLS_*` in sola lettura; `HLS_*` creata solo da H-803 con autorizzazione | IN §8 Q9 |

### 5.4 Piano completo per fasi (IN §4; effort **[I]** in persona-giorni, totale 69–107)

Percorso critico: 1→3→4→5→9→10→12. **Ordine di esecuzione**: 1 → (2 ∥ 3) → 4 → **GATE go/no-go (H-406)** → 5 → 6 → 7 (8 e 8b dopo il gate, in parallelo dove possibile) → 9 → (10 ∥ 11) → 12.

| Fase | Obiettivo | Dipende da | Task | Criteri di accettazione (IN §4) |
| ---- | --------- | ---------- | ---- | ------------------------------- |
| 1 Discovery (2–3) | Congelare requisiti | — | H-101…H-103 | ADR approvato; Q1–Q9 chiuse |
| 2 Inventario ECC (4–6) | Leggere il 100% degli asset | 1 | H-201…H-204 | 0 asset in allowlist non letti; `chain-matrix.md` completa (71 agenti + 311 skill) |
| 3 Contratti e consolidamento (11–16) | Monorepo, shim, CI, configurazione locale | 1 | H-301…H-307, H-309 | Parità verde su Windows e Unix; installazioni originali invariate; `helios doctor` valida la configurazione locale |
| **4 PoC go/no-go (8–12)** | Verificare la fattibilità | 1, 3 | H-401…H-407 | Criteri R05 §5 (1–10); piano B: wrapper senza copia (04 §5) |
| 5 Porting skill/rules (5–8) | Portare asset in allowlist; **avviare la documentazione viva** | 2, 3, 4 | H-501…H-506 | CI verde; metrica ≥ baseline (C16); `architecture.md`, `module-map.md`, `USER_GUIDE.md` esistenti e aggiornati a ogni fase successiva |
| 6 Catena agenti (10–15) | Tutti i ruoli ALA §3 | 4, 5 | H-601…H-612 (+ H-308, H-409 [P]) | Nessun `Bash` libero; ruoli come da tabella modelli |
| 7 Workflow completo (8–12) | `limet-change.js`, scheduler, stato, gate, integrazione, **modalità non presidiata** | 4, 6 | H-701…H-715 (+ H-408 [P]) | Scenari ALA V1–V14 verdi in sandbox; 0 collisioni con piano ECC; un run non presidiato termina sempre per causa dichiarata ed è ripartibile da checkpoint (E2E-AUT-1, E2E-AUT-2) |
| 8 Hook e comandi (2–4) | Analisi hook ECC, repliche sicure, KB | 4 | H-800, H-800b, H-801…H-804 | Ogni hook con decisione motivata; 0 scritture fuori workspace |
| 8b Dashboard (15–25) | UI + API | 3, 4 | H-8b01…H-8b10 | Viste in sola lettura complete; azioni dopo test C23–C24; budget C26; nessuna operazione senza configurazione locale valida (E2E-CFG-1); avvio del run non presidiato solo da sessione umana, con perimetro mostrato e kill switch raggiungibile (E2E-AUT-2) |
| 9 E2E (6–10) | Scenari V1–V24 su 3 OS | 5–8 | H-901, H-902 | Scenari passati su ogni OS |
| 10 Hardening (4–6) | Sicurezza e audit | 9 | H-1001 | Audit completato |
| 11 Documentazione (3–4) | Manuali e NOTICE | 9 | H-1101 | Revisione approvata |
| 12 Rilascio (3–5) | Rollout progressivo | 10, 11 | H-1201 | Pilota con metriche; parere legale ottenuto |

**Fasi 5–7: gruppi di esecuzione (IN §5)**

| Gruppo | Task | Dipende da |
| ------ | ---- | ---------- |
| A. Contratti e strumenti di base | H-308, H-409, H-408, H-611 | H-303, H-401 |
| B. Asset ECC | H-501, H-502, H-503 | H-204, H-308 |
| C. Ruoli (ognuno richiama `_common`) | H-602…H-610, H-601 | gruppo A, H-402 |
| D. Workflow | H-701, H-703…H-711, H-702 | gruppi A e C, H-407 |
| D-bis. Modalità non presidiata | H-713, H-714, H-715 | gruppo D (H-706, H-707, H-708, H-709, H-710) |
| E. Valutazione rischi di prodotto | H-612 | H-308, H-701, H-706 |
| F. Modifiche documentali a LIMET | H-712 | nessuna in IN; **[P]** dipendenza esplicita dal plugin (ALA §7) |

**[N]** IN §4 contiene la contraddizione «Fase 4 dipende da 1, 3» e «le fasi 4–5 non iniziano senza esito positivo della precedente»: si applica la lettura prudente (Fase 4 dopo 1 e 3; Fase 5 solo dopo GO). La fase di H-308/H-409/H-408 non è assegnata da IN: **[P]** 6/6/7 (gruppo A).

### 5.5 Gate go/no-go del PoC — punto di decisione, non fine del piano

Al termine della Fase 4 H-406 produce `docs/poc-report.md` sui criteri 1–10 (R05 §5); **il SUPERVISORE decide** GO / NO-GO / NO-GO parziale per asset o lane. Con **GO** partono le Fasi 5–12 (le loro change si aprono qui o in change separate, a scelta del SUPERVISORE). Con **NO-GO** vale il **piano B: wrapper senza copia di ECC** (04 §5): le Fasi 5–12 vanno rivalutate con il SUPERVISORE prima di qualsiasi avvio. Criterio 10: N/A motivato nel PoC (Q-12), valutato in Fase 8b.

### 5.6 Regole trasversali

- **Planner ECC (decisione del SUPERVISORE, IN §3.5)**: agenti e skill di pianificazione ECC (planner, architect e simili) sono solo **consulenti dell'analyst, in sola lettura**; non scrivono file di piano né in `.claude/plans`; producono **proposte** in `ecosystem/proposals/`. Proposta tecnica (split, ordine, rischi) → integrata dall'analyst; proposta che **aggiunge/modifica/elimina un requisito** → l'analyst si ferma («fermati e chiedi», R8), la registra in `QUESTIONS.md` con opzioni e motivazioni e procede **solo dopo la decisione del SUPERVISORE**. Il piano resta di LIMET. Task: H-503, H-602, H-707.
- **Valutazione del rischio dopo ogni task (C28, IN §2.3 n.7)**: già applicata da ora (colonna Note); nel prodotto automatizzata da H-612/T-611 (`risk-assessor` ≠ developer, sola lettura, `RISK-<task>.json`, ≥ 2 opzioni, senza autocorrezione).
- **Gate H-406** come sopra; **niente avvio Fasi 5+ senza GO**.

### 5.7 Matrice di copertura dei requisiti vincolanti ALA (R1–R27, M.1–M.9)

> Aggiunta il 2026-10-30 (rilievo **A2** dell'audit). IN §9 dichiara ALA documento **vincolante e
> prevalente**; prima di questa tabella la copertura dei suoi requisiti da parte dei 70 task non
> era dimostrabile. Le colonne «Task» e «Scenari» sono **[I]** (derivazione da titoli e schede di
> `TASK_LIST.md` / `E2E_VERIFICATION.md`); le colonne «Requisito» sono **[F]** (ALA §… come
> citato). I test `T-xxx` seguono la numerazione del task corrispondente in `TEST_PLAN.md`.
> Nessun requisito risulta scoperto: non ci sono righe **[N]**. Aggiornata il 2026-10-30 con
> H-309/H-8b09 (configurazione locale) su R22 e M.5, e con H-713/H-714/H-715/H-8b10 (modalità
> non presidiata, ADR 0003) su R3, R8, R18 e R20.

| Req. | Sintesi (ALA) | Task H-xxx | Scenari |
| ---- | ------------- | ---------- | ------- |
| R1 | Esegue la change solo su richiesta esplicita, leggendo i soli documenti LIMET | H-701, H-403 | V14 |
| R2 | `analyst` produce `plan.json` (task, file, dipendenze, stima, rischio) e propone split | H-602 | V15 |
| R3 | Gate umano sul grafo (conferma / senza split / ferma), registrato in `state.json` | H-703, H-715 | V16, E2E-AUT-1 |
| R4 | Scheduler a funzioni pure: `ready`, onde a file disgiunti, priorità | H-408, H-704 | V1, V2, V15 |
| R5 | Worktree e rami dedicati; il tester non vede il diff del developer | H-603, H-604, H-704 | V1 |
| R6 | Integrazione su `integration/NNNN`; avanzamento solo sullo stesso SHA | H-705 | V6, V18 |
| R7 | Nessun task `done` senza TEST_PLAN compilato o eccezione motivata | H-711 | V7 |
| R8 | «Fermati e chiedi»: commit WIP, `blocked` con domande, nessuno stop globale | H-707, H-713 | V9, V16, E2E-AUT-1 |
| R9 | Risposte in `ecosystem/QUESTIONS.md`; una domanda equivalente non si ripete | H-707, H-712 | V9 |
| R10 | Build/test solo via skill `limet-runner`; verifica SHA e worktree pulito | H-409, H-401 | V3, V8 |
| R11 | `test-reader` classifica i fallimenti (difetto codice vs difetto test) | H-606 | V4 |
| R12 | `maxAttempts` = 3 prima di fermarsi; mai un terzo tentativo cieco | H-708 | V11 |
| R13 | Gate di merge obbligatori; eccezioni solo firmate in `exceptions.json` | H-711 | V7, V10 |
| R14 | `validator`: `conforme / non conforme / non determinabile` con `evidence[]` | H-608 | V16 |
| R15 | Bug fix: la regressione fallisce prima del fix e passa dopo, su SHA diversi | H-711 | V5 |
| R16 | E2E con Playwright Test Agents (`e2e-designer` / `e2e-reader`) | H-610, H-901 | V12 |
| R17 | `state.json` scritto solo dal workflow, con schema e validazione | H-706 | V17, V18 |
| R18 | Report notturno / di conformità / finale, con «Da leggere oggi» | H-709, H-714 | V12, E2E-AUT-1 |
| R19 | Permessi a livello di tool: `deny` su build, `git push`, `git merge` | H-710, H-404 | V8 |
| R20 | Limiti da `limits.json` (agenti, build, tentativi, budget, modelli, notte) | H-708, H-713, H-8b10 | V11, E2E-AUT-2 |
| R21 | Merge a 3 livelli: ramo task → `integration/NNNN` → `main` (solo utente) | H-705, H-711 | V6 |
| R22 | Installer `limet-agents.ps1/.sh` `init\|update` idempotente | H-702, H-309 | V13, E2E-CFG-1 |
| R23 | Dynamic Workflow **nativo** di Claude Code in `workflows/limet-change.js` | H-701, H-407 | V13, V14 |
| R24 | Compatibilità retroattiva: modalità multi-agente **opt-in**, LIMET classico invariato | H-304, H-712 | V19 |
| R25 | Raccolta eventi affidabile, schema versionato, timestamp UTC, qualità dei dati | H-609 | V20, V21 |
| R26 | `REPORT_METRICHE.html` (SPA) con filtri e confronto storico | H-609, H-709 | V22, V24 |
| R27 | Misurazione separata dall'interpretazione; accuratezza e privacy | H-609, H-801 | V21, V23 |
| M.1 | Build/test all'utente, alla pipeline o a un esecutore deterministico non LLM | H-409, H-712 | V8 |
| M.2 | L'orchestratore esegue solo se PLAN/SPEC/TASK_LIST/TEST_PLAN esistono; non li crea | H-403, H-701, H-712 | V14 |
| M.3 | Appendice D «Esecuzione multi-agente»: ID, `ecosystem/`, mappatura stati | H-712 | — |
| M.4 | Nuovi template `QUESTION`/`INVARIANTS`/`CONFORMITY_REPORT`, `vale_per`, NFR in SPEC | H-712 | — |
| M.5 | Correggere il puntatore CEREBRO (`C:\software\cerebro`) e registrare `CRB_limet` | H-307, H-803, H-309 | E2E-CFG-1 |
| M.6 | Documentare che il Dynamic Workflow esegue solo una change già formalizzata | H-712, H-902 | V14 |
| M.7 | Criteri di accettazione espliciti per il workflow Claude (registrazione, avvio, gate, ripresa) | H-407, H-712 | V13, V16 |
| M.8 | Compatibilità retroattiva formalizzata: multi-agente opt-in | H-712, H-304 | V19 |
| M.9 | Osservabilità del ciclo di vita + `REPORT_METRICHE.html`: schema, limiti, privacy | H-609, H-712 | V20–V24 |

### Alternative considerate e scartate
- **Wrapper senza copia di ECC** (piano B, 04 §5): non scelto ora; è il ripiego se un criterio go/no-go fallisce su un asset/lane (R05 §5).
- **Includere subito Fase 2**: scartato fino alla risposta a Q-4, perché IN §4 non la richiede come dipendenza del PoC.

## 6. Todo/elenco attività

Dettaglio in `TASK_LIST.md` (stessa cartella): **79 task** H-xxx (70 da IN §5; + H-309 e H-8b09 per la configurazione locale; + H-713, H-714, H-715 e H-8b10 per la modalità non presidiata; + H-504, H-505 e H-506 per la documentazione viva — tutti aggiunti su richiesta del SUPERVISORE il 2026-10-30), indice per fase in fondo alla tabella.

## 7. Strategia di verifica (obbligatoria)

- **Unit test previsti**: sì — `TEST_PLAN.md` copre tutti i **58** test T-xxx di IN §6 con comandi a livello classe e metodo, più i **3** aggiunti per la configurazione locale (T-309, T-8b11, T-8b12), i **4** per la modalità non presidiata (T-713, T-714, T-715, T-8b13) e **1** per la documentazione viva (T-504): totale **66**. Task senza test: H-101, H-102, H-103, H-203, H-301, H-307, H-406, H-505, H-506, H-800, H-902, H-1101, H-1201. **H-800b e H-1001** non hanno T-xxx in IN §6 **[N]** ma non sono più privi di verifica: l'audit del 2026-10-30 (rilievo A6) ha definito per H-800b 5 criteri osservabili per ogni hook replicato e per H-1001 un audit a 5 criteri con esito in `docs/audit-hardening.md` — dettaglio in `TASK_LIST.md`, tabella «Strategia di verifica per task». **H-505 e H-506** sono documentali ma hanno criteri osservabili dichiarati nella stessa tabella (comandi eseguiti davvero per H-505; quattro criteri binari per H-506).
- **Test e2e previsti**: sì — `E2E_VERIFICATION.md`: tutti gli scenari ALA **V1–V24**, E2E-PoC-1…6, **E2E-CFG-1** (configurazione locale, Fase 8b) ed **E2E-AUT-1/E2E-AUT-2** (modalità non presidiata, Fase 7+, dopo il GO; completano V11 senza sostituirlo); nel PoC solo V8, V13–V19 (V13/V14 obbligatori).
- `TEST_EXECUTION.md` si genera **a esecuzione avvenuta** dal SUPERVISORE (l'agente non esegue test).
- **Chi esegue**: il SUPERVISORE. L'agente fornisce i comandi e attende l'esito (AGENTS.md).

## 8. Rischi e impatti

> Formato introdotto il 2026-10-30 (rilievo **A9** dell'audit): C28 impone la valutazione dei
> rischi dopo **ogni** task, ma il registro non aveva le colonne su cui scrivere quella
> valutazione. Probabilità e impatto sono **[I]** (stima dell'analista, da ricalibrare con le
> misure del PoC); il «segnale di innesco» è ciò che, se osservato, obbliga a riaprire il rischio
> e a portarlo al SUPERVISORE con almeno due opzioni, senza correzione autonoma.

| ID | Rischio | Prob. | Impatto | Esposizione | Titolare | Segnale di innesco | Mitigazione |
| -- | ------- | ----- | ------- | ----------- | -------- | ------------------ | ----------- |
| R-1 | Sintassi dei Dynamic Workflow non verificata **[N]** | alta | alto | **critica** | analista (H-407) | `limet-change` non si registra o non si avvia nella versione installata di Claude Code | H-407 è PoC; fallback in `docs/poc-report.md` (piano B); criterio go/no-go n.3 |
| R-2 | Runner non portabile su Windows nativo **[I]** | media | alto | **alta** | analista (H-401, H-306) | un test del runner passa su Linux e fallisce su Windows nella CI a matrice | T-306 su 3 OS; C13 opzione A (runtime unico portabile); criterio n.7 |
| R-3 | Nomi di test proposti ma non esistenti **[N]** | alta | basso | media | analista (H-103) | un comando di riesecuzione non corrisponde ad alcun test reale | H-103 ha confermato framework e comandi (Q-11); i nomi si fissano con il codice (Q-11b) |
| R-4 | Costo oltre 100 USD per workflow (criterio n.8) | media | medio | media | SUPERVISORE | la stima token×tariffa supera 70 USD su un singolo run del PoC | Q-7 chiusa prima di H-405/H-407; `budgetUsd` in `limits.json` (R20); checkpoint e ripresa (H-708) |
| R-5 | Deriva di ECC tra versioni **[I]** | bassa | medio | bassa | analista (H-203) | un asset in allowlist cambia comportamento o licenza rispetto al pin | pin `25278e7` (C12 opzione A); upgrade solo con test di non regressione |
| R-6 | Toccare le installazioni LIMET/CEREBRO in uso per COSMO e mudeopen | bassa | **molto alto** | **alta** | analista (ogni task) | qualunque scrittura fuori dal repo HELIOS, o variazione del conteggio punti di una collection esistente | tutto dentro il repo HELIOS; sandbox e HOME isolato per H-407; nessuna scrittura su `~/.claude` (criterio n.1); C19/C20 opzione A; T-803 verifica i conteggi prima/dopo |
| R-7 | Copertura dei requisiti ALA non dimostrata | — | — | **chiuso 2026-10-30** | analista | — | Risolto dall'audit: matrice §5.7 (R1–R27, M.1–M.9 → task → scenari) |
| R-8 | `limet.ps1 update` sovrascrive la documentazione LIMET versionata | ~~alta~~ **nulla** | ~~medio~~ | ~~alta~~ **chiusa** | SUPERVISORE | — | **Rischio eliminato il 2026-10-30, non mitigato.** I 24 file rigenerati dall'installer (`FRAMEWORK_MANUAL.md`, `MASTER_INDEX.md`, `ONBOARDING_CHECKLIST.md`, `scripts/` ×3, `templates/` ×18) sono stati **tolti dal versionamento**: `limet.ps1` li riscrive e `git status` resta vuoto, quindi non esiste più il canale attraverso cui il danno viaggiava. Causa originaria: un file rigenerato da un tool, se versionato, viene mergiato da git **riga per riga**; un `update` su un altro clone riporta il file alla versione standard e git lo merga **senza conflitto**, cancellando in silenzio il contenuto di HELIOS. Si versiona ora solo `limet/changes/` e `limet/archive/`, le due cartelle che l'installer non tocca mai (righe 119, 149-151). **Conseguenza per H-712**: le modifiche alla documentazione LIMET vanno fatte nel repo sorgente di LIMET o, dopo H-301, nella copia interna a HELIOS — mai nei file installati qui |
| R-8b | `AGENTS.md`/`CLAUDE.md` versionati contengono il percorso assoluto di CEREBRO | media | **basso** | bassa | chiunque cloni | in `git status` compaiono i due file modificati sulle sole righe 52/75 (AGENTS) e 54/77 (CLAUDE) | Scelta deliberata: restano versionati perché sono il punto d'ingresso degli agenti AI e il loro diff è di **2 righe, rivedibile a occhio** — al contrario dei 24 file di R-8. Chi ha CEREBRO altrove adatta le righe in locale e non le committa (`guides/01-SETUP-DOPO-IL-CLONE.md` passo 3). **Risolto alla radice da H-309** (`config/helios.local.json`) |

- **Impatto su altri componenti**: nessuno (fase documentale; il codice futuro vive in questo repo).
- **Compatibilità all'indietro**: LIMET classico invariato (ALA R24); verificato da V19.
- **Applicazione di C28**: dopo ogni task l'agente rivaluta questa tabella, aggiunge le righe nuove e riporta al SUPERVISORE gli innesti osservati con almeno due opzioni; nessuna correzione autonoma. Nel prodotto la stessa regola è automatizzata da H-612/T-611 (`risk-assessor`).

## 9. Note operative

- **Commit/push**: solo SUPERVISORE (H-301 è azione dell'utente: import di LIMET/CEREBRO).
- **Build/test**: solo SUPERVISORE; l'agente dà i comandi.
- **Ambiente**: Python ≥ 3.11 e `pytest`; Node ≥ 18 per T-407; Claude Code 2.1.311.
- **Re-index**: al termine, Launcher → Percorso progetto → Re-index → Incrementale → clic Re-index (a cura del SUPERVISORE).

## 10. Modalità non presidiata (UNATTENDED)

> Sezione aggiunta il 2026-10-30 su richiesta del SUPERVISORE. Decisioni di riferimento:
> **ADR `docs/adr/0003-modalita-non-presidiata.md`** e domande **Q-14…Q-18** (§4).
> Vale **solo dopo il GO** del gate C14 (Q-17).

### 10.1 Che cosa è, e che cosa non è

HELIOS **non** sarà usabile in «auto-mode» inteso come esecuzione di tutti i task fino alla fine
senza intervento umano. Quella forma contraddice la definizione del prodotto — IN §1 **[F]**:
«Non è *autonomo senza limiti*: si ferma e chiede» e «**pause obbligatorie** in cui decide il
SUPERVISORE» — ed è vietata dai non-negoziabili IN §2.3 n.1 e n.2, da C24, da C28 e dai gate
ALA R3 e R8. Non è stata pianificata e non lo sarà senza una riapertura esplicita di quelle
decisioni, che spetta al solo SUPERVISORE.

HELIOS **sarà** usabile in **modalità non presidiata**: l'esecuzione di una **tratta di task fra
due gate umani** mentre nessuno è davanti allo schermo. Il nome è deliberatamente «non
presidiata» e non «autonoma», perché ciò che cambia è la **presenza**, non l'**autorità**.

### 10.2 Principio fondante — neutralità dei permessi

> La modalità non presidiata cambia la **supervisione**, mai l'**autorità**.
> Un agente in UNATTENDED ha **esattamente** gli stessi permessi che ha in modalità presidiata.

Senza questo vincolo la modalità diventerebbe un modo per aggirare C02, C24 e il pavimento
hard-deny di H-404. È verificato da `T-713::test_permessi_identici_a_modalita_presidiata` e dal
passo 10 di E2E-AUT-1.

### 10.3 Perimetro

| | Contenuto | Fonte |
| --- | --- | --- |
| **Dentro** | Scelte implementative interne a un task già approvato; retry entro `maxAttempts` = 3; ordine fra i task pronti dentro il grafo confermato | Q-15; R12, R3 |
| **Fuori** | Dubbi su requisiti; rischi alto/critico; `push` e `merge`; approvazione PLAN e validazione finale; sforamento di budget/durata/numero task; split di un task; scrittura di test fuori dal TEST_PLAN | C06, C28, R19, R21, C24, R20, R3 |
| **Azione di ruolo, non decisione** | Il `developer` **committa sul ramo del task** anche di notte | Q-18; ALA §1.3 |

### 10.4 Ruoli

Ammessi: `developer`, `tester`, `runner`, `test-reader`, `reviewer`, `metrics-observer`.
Esclusi: `analyst`, `validator`, `integrator`, `risk-assessor`, `commit-commands`.

L'allow-list è **applicata dal codice** (H-715), con default negato per i ruoli ignoti.
Conseguenze accettate: l'integrazione su `integration/NNNN` resta lavoro presidiato del mattino;
C28 non viene mai esercitato in assenza del SUPERVISORE; nessuna conformità matura di notte.

### 10.5 Parametri e fine corsa

`night.stopOnQuestions` = `false` (parcheggio, non stop globale); `night.maxDurationMin` = 480;
nessun reperibile, quindi il run è **auto-contenuto**. Un run termina **sempre** per una causa
dichiarata: task esauriti, durata, budget, numero massimo di task, **kill switch** o **dead-man
switch**. Per ogni causa deve esistere un checkpoint utilizzabile con `resumeFromRunId`: è la
condizione che rende accettabile la non reperibilità.

### 10.6 Task, test e scenari

| Elemento | Riferimento |
| --- | --- |
| Task | **H-713** (modalità, stati, kill/dead-man switch), **H-714** (registro decisioni autonome), **H-715** (allow-list ruoli) — Fase 7; **H-8b10** (pannello di avvio) — Fase 8b |
| Test | **T-713**, **T-714**, **T-715**, **T-8b13** (dettaglio in `TEST_PLAN.md`) |
| E2E | **E2E-AUT-1** (domanda bloccante → parcheggio → report), **E2E-AUT-2** (fine corsa e ripartenza). V11 di ALA resta valido e viene **completato**, non sostituito |

### 10.7 Punti [N]

- **[N]** Soglia del dead-man switch (minuti di silenzio del registro eventi): da fissare in
  H-713 con un default documentato e reso configurabile.

---

## Storico aggiornamenti del piano

| Data | Modifica | Motivo |
| ---- | -------- | ------ |
| 2026-10-29 | Creazione bozza (DRAFT) | Avvio progetto HELIOS |
| 2026-10-29 | Chiuse Q-1…Q-7, Q-12, Q-13 con risposte del SUPERVISORE; documenti allineati | Domande poste una alla volta |
| 2026-10-29 | Stato → APPROVATO dal SUPERVISORE | Approvazione prima dell'implementazione |
| 2026-10-29 | H-101 `done` (ADR 0001 approvato) | Approvazione SUPERVISORE |
| 2026-10-29 | H-102 `done` (CONSTITUTION), H-103 `done` (ADR 0002); approvazione piano esteso riconfermata 20:13 | Approvazione SUPERVISORE |
| 2026-10-29 | Creato `log/HELIOS_Distribuzione_task.xlsx`: 31 pacchetti di task adiacenti assegnati a NARO e SCIBETTA **[P]** (33 task ciascuno + 4 al SUPERVISORE), punti di sincronizzazione | Richiesta SUPERVISORE; assegnazione da approvare; nessun requisito cambiato |
| 2026-10-29 | `log/HELIOS_Distribuzione_task.xlsx` corretto: PK-05 diviso in PK-05 (H-306, avviabile subito) e PK-05b (H-302, dopo H-301); PK-05b/06/07 marcati «dopo H-301» (dipendenze da TASK_LIST: H-302/H-304/H-305/H-307 → H-301). Ora 32 pacchetti | Richiesta SUPERVISORE; l'Onda 1 non era tutta avviabile; nessun requisito cambiato |
| 2026-10-29 | Regola SUPERVISORE: LIMET e CEREBRO importati in HELIOS (H-301) sono usati dal prodotto HELIOS; durante lo sviluppo di HELIOS si usano LIMET e CEREBRO esterni (installazioni esistenti). Nessun requisito cambiato | Chiarimento SUPERVISORE |
| 2026-10-30 | **Audit dei documenti LIMET** (richiesta SUPERVISORE). Esito: copertura conforme (70/70 task, 58/58 test, 24/24 scenari V, 28/28 decisioni C); validabilità parziale; efficienza accettabile. Perimetro di rimedio approvato dal SUPERVISORE: **A1, A2, A4, A5, A6, A9**. Applicati: **A1** §5.2 C11–C18 e C20–C27 verificate riga per riga su R05 §2–3, da **[I]** a **[F]** con citazione; **A2** nuova §5.7 con la matrice R1–R27 / M.1–M.9 → task → scenari; **A4** corretto il dato «69 test» → 58 nel registro della TASK_LIST; **A5** Q-11 chiusa parzialmente e residuo riaperto come Q-11b; **A6** definita la verifica di H-800b e H-1001; **A9** §8 riscritta con probabilità, impatto, titolare e segnale di innesco (formato richiesto da C28). Nessuna decisione riaperta, nessun file di input toccato | Verificare che la progettazione sia validabile ed efficiente |
| 2026-10-30 | **Configurazione locale non versionata** (richiesta SUPERVISORE). Decisioni: file `config/helios.local.json` ignorato + `config/helios.local.example.json` versionato; **catena di precedenza `HELIOS_CONFIG` → file nel repo → file per utente** (`locazione = entrambi`, risposta del 2026-10-30); verifica **non** solo nella dashboard ma in un **modulo di bootstrap condiviso** usato da runner, CLI, installer, agenti e CI, con validazione semantica a tre esiti `OK` / `INCOMPLETO` / `NON VALIDO`; la dialog della dashboard è solo un front-end dello stesso modulo. Nuovi task approvati: **H-309** (Fase 3, pre-GO) e **H-8b09** (Fase 8b) → 70 → **72** task; nuovi test **T-309**, **T-8b11** (gate API), **T-8b12** (dialog UI) → 58 → **61**; nuovo scenario **E2E-CFG-1**. Attua C20; nessuna decisione C01–C28 riaperta | Chi clona HELIOS non deve ereditare la configurazione di un altro, e nessuna operazione deve partire senza configurazione valida |
| 2026-10-30 | Rigenerato `log/HELIOS_Distribuzione_task.xlsx` per i due nuovi task: nuovi pacchetti **PK-07b** (H-309, SCIBETTA, Onda 1 dopo H-301) e **PK-26b** (H-8b09, NARO, dopo PK-07b/PK-25/PK-26); 32 → **34** pacchetti; Pre-GO 21 → 22, Post-GO 49 → 50; bilanciamento SCIBETTA 34 / NARO 34 / SUPERVISORE 4. Assegnazione ancora **[P]** | Allineamento della distribuzione ai 72 task; nessun requisito cambiato |
| 2026-10-30 | Rilievi dell'audit **fuori perimetro**, censiti e non applicati: **A3/A16** 46 test su 58 senza stato pre/post né asserzione (rinviati a dopo il GO, per ondata); **A7** segnaposto `<RUN>`/`<SANDBOX>`/`<INVOCA>` e soglie V10/V21 mancanti; **A8** dipendenze espresse a livello di gruppo; **A10** una sola change contiene l'intero programma; **A11** nessuna stima di complessità per task; **A12** generatore della distribuzione fuori dal repository; **A13** documenti `docs-limet/` previsti da AGENTS.md assenti; **A14** xlsx mai aperto in Excel; **A15** repository pubblico | Decisione del SUPERVISORE del 2026-10-30 sul perimetro di rimedio |
| 2026-10-30 | **Verifica di fattibilità dell'«auto-mode»** (richiesta SUPERVISORE). Esito: la forma **assoluta** (tutti i task senza intervento umano) è **esclusa** — contraddice IN §1 e i non-negoziabili IN §2.3 n.1–2 e richiederebbe di riaprire C02, C24, C28; **non proposta**. La forma **limitata** è fattibile ed era già metà progettata (R20 notte, R18 report notturno, V11, H-707/H-708/H-709): formalizzata come **modalità non presidiata (UNATTENDED)** nell'**ADR 0003**, con il principio di **neutralità dei permessi** (la modalità cambia la supervisione, mai l'autorità). Chiusa la questione aperta **ALA §13 q.9**. Nuove domande contestuali **Q-14…Q-18** decise dal SUPERVISORE | Richiesta del SUPERVISORE: «verifica se fattibile, poi dimmelo e poi pianifichiamo» |
| 2026-10-30 | **Pianificata la modalità non presidiata** (dopo il GO, Fase 7+, Q-17). Nuovi task **H-713**, **H-714**, **H-715** (Fase 7) e **H-8b10** (Fase 8b) → 72 → **76**; nuovi test **T-713**, **T-714**, **T-715**, **T-8b13** → 61 → **65**; nuovi scenari **E2E-AUT-1** ed **E2E-AUT-2** (completano V11, non lo sostituiscono); nuova §10 del PLAN; §5.7 aggiornata su R3, R8, R18, R20; nuovo gruppo di esecuzione **D-bis**. Nessuna decisione C01–C28 riaperta; nessun file di input toccato | Richiesta SUPERVISORE del 2026-10-30, dopo l'esito positivo della verifica di fattibilità |
| 2026-10-30 | **Regola di proprietà dei file versionati** (decisione SUPERVISORE; **corregge le due righe precedenti**). Problema sollevato dal SUPERVISORE: se un altro clone esegue `limet.ps1 update`, i file rigenerati tornano alla versione standard e **git li merga senza conflitto**, cancellando in silenzio i documenti master — git merga riga per riga, non comprende il testo. Difese scartate perché inefficaci contro un merge silenzioso: wrapper con snapshot, manifesto SHA-256, hook `pre-commit`, workflow di verifica, branch protection + `CODEOWNERS` (un diff da 1187 righe non è rivedibile da un umano). **Soluzione strutturale adottata: si versiona solo ciò che scriviamo noi.** `.gitignore` riscritto con `/limet/*` + `!/limet/changes/` + `!/limet/archive/`: **24 file tolti dal versionamento** (`FRAMEWORK_MANUAL.md`, `MASTER_INDEX.md`, `ONBOARDING_CHECKLIST.md`, `scripts/` ×3, `templates/` ×18). `AGENTS.md` e `CLAUDE.md` **restano versionati** (decisione SUPERVISORE): contengono solo 2 righe macchina-dipendenti, diff rivedibile a occhio → nuovo rischio **R-8b**, basso. Verificato con `git check-ignore --no-index`. Rischio **R-8 eliminato**, non mitigato. Guida `01-SETUP-DOPO-IL-CLONE.md` capovolta: il passo 2 passa da «⛔ non eseguire `limet.ps1`» a «✅ eseguilo, è il modo di ricostruire il framework in locale», e il passo 3 elenca **cosa adattare a mano**. Conteggi invariati: **76 task / 65 test** | Il SUPERVISORE richiede che chi clona possa lavorare e committare senza spianare i documenti master; una regola strutturale elimina la classe di errore invece di sorvegliarla |
| 2026-10-30 | **Guida di configurazione dopo il clone** (richiesta SUPERVISORE). Nuova cartella **`guides/`** (collocazione decisa dal SUPERVISORE; scartate «ampliare H-1101» e «solo README») con `README.md` di indice e `01-SETUP-DOPO-IL-CLONE.md`. Contiene i prerequisiti verificabili, il **divieto di eseguire `limet.ps1` su questo repo** (rischio R-8) con procedura di recupero, l'adattamento dei percorsi locali in `AGENTS.md`/`CLAUDE.md` in attesa di **H-309**, la registrazione in CEREBRO con le tre cartelle `--docs` corrette, l'indicizzazione e verifica di `CRB_helios`, l'ordine di lettura dei documenti e la tabella delle cose da non fare mai. Nessun task nuovo: i conteggi restano **76 task / 65 test**; il materiale confluirà in **H-1101** (manuali) | Chi clona HELIOS deve poter configurare tutto senza conoscere il progetto; serve a NARO come primo utilizzatore |
| 2026-10-30 | **`limet/`, `AGENTS.md` e `CLAUDE.md` resi versionabili** (decisione SUPERVISORE). Motivo: in HELIOS `limet/` non è strumentazione locale ma **prodotto** (H-301 la importa nel monorepo, H-712 ne modifica la documentazione); inoltre i quattro documenti di progettazione esistevano in **una sola copia locale**, senza storia né backup. Soluzione adottata: nuovo **`.gitignore`** con pattern di **negazione** (`!/limet/`, `!/AGENTS.md`, `!/CLAUDE.md`), che ha precedenza sul blocco auto-gestito `LIMET:EXCLUDE` di `.git/info/exclude` e **non viene mai sovrascritto** da `limet.ps1` (che agisce solo fra i propri marcatori). Scartato `git add -f`: avrebbe funzionato solo sui file esistenti, lasciando invisibili in `git status` i documenti futuri. Verificato con `git check-ignore` su repo temporaneo e poi su HELIOS: 33 file versionabili; `graphify-out/`, `.graphifyignore` e `config/helios.local.json` restano esclusi. Nuovo rischio **R-8** censito (§8). Il `.gitignore` è nucleo minimo e sarà esteso da H-302 | Mettere al sicuro la progettazione e allineare il repository a H-301/H-712; nessun requisito cambiato |
| 2026-10-30 | **Documentazione viva pianificata** (richiesta SUPERVISORE: «abbiamo previsto una guida uso utente e un documento di architettura?»). La risposta era **no**: H-902 (README plugin, Fase 9) e H-1101 (manuali, Fase 11) arrivano a fine corsa, e nessun task descriveva l'architettura *reale*; `04_architettura_HELIOS.md` è un **input** non mantenuto. Riapre il rilievo **A13** dell'audit (documenti `docs-limet/` prescritti da `AGENTS.md` e assenti), che il 2026-10-30 era stato dichiarato fuori perimetro. Nuovi task **H-504** (`docs-limet/architecture.md` + `module-map.md`), **H-505** (`docs/USER_GUIDE.md`) e **H-506** (`glossary.md`, `conventions.md`, `NON_TECHNICAL_SUMMARY.md`) → 76 → **79**; nuovo test **T-504** → 65 → **66**. Collocati in **Fase 5**, non in Fase 11, perché l'aggiornamento è **incrementale** (obbligatorio alla chiusura di ogni fase successiva) e H-1101 diventa revisione, non stesura. Esclusi `decisions.md` (duplicherebbe `docs/adr/`) e `dependencies.md` (già nel ledger H-202 e nell'SBOM H-1001). Il perimetro del PoC e il gate C14 **non cambiano** | Richiesta SUPERVISORE 2026-10-30, approvata esplicitamente («OK per entrambi», poi «vai con 3»); nessun requisito cambiato |
| 2026-10-30 | **Cartella della change rinominata** `0001-helios-poc` → **`0001-helios`**, e `PLAN_helios-poc.md` → **`PLAN_helios.md`** (decisione SUPERVISORE). Il nome risaliva al 2026-10-29, quando l'ambito era il solo PoC; dopo l'estensione a Fasi 1–12 + 8b diceva meno del contenuto e faceva apparire l'intero progetto come materiale del solo PoC — anche agli agenti, perché `limet/changes/` è indicizzata in `CRB_helios` e il percorso finisce nei metadati dei chunk. Nessun requisito, task, test o conteggio modificato | Chiarezza del nome; **richiede un re-index con `--prune`**: i chunk col vecchio percorso non vengono rimossi da soli (`ingest_docs.py` non cancella mai di default) |
| 2026-10-30 | Rigenerato `log/HELIOS_Distribuzione_task.xlsx` per la documentazione viva: nuovi pacchetti **PK-16b** (H-504 — SCIBETTA) e **PK-16c** (H-505, H-506 — NARO); 37 → **39** pacchetti; Post-GO 54 → **57**, **Pre-GO invariato a 22** (il perimetro del PoC e il gate C14 non cambiano); bilanciamento SCIBETTA **37** / NARO **38** / SUPERVISORE 4. Assegnazione ancora **[P]** | Allineamento della distribuzione ai 79 task; nessun requisito cambiato |
| 2026-10-30 | Rigenerato `log/HELIOS_Distribuzione_task.xlsx` per la modalità non presidiata: nuovi pacchetti **PK-19b** (H-713, H-715 — SCIBETTA), **PK-20b** (H-714 — NARO), **PK-26c** (H-8b10 — NARO); 34 → **37** pacchetti; Post-GO 50 → **54**, Pre-GO invariato a 22 (il perimetro del PoC non cambia); bilanciamento SCIBETTA **36** / NARO **36** / SUPERVISORE 4. Assegnazione ancora **[P]** | Allineamento della distribuzione ai 76 task; nessun requisito cambiato |
| 2026-10-30 | **Conflitto rilevato e risolto dall'agente** fra Q-15 (commit fuori perimetro di autonomia) e Q-16 (`developer` ammesso non presidiato), dato che ALA §1.3 definisce il `developer` come chi «commit sul ramo del task». Portato al SUPERVISORE come **Q-18** invece di essere assunto: deciso che il commit sul ramo del task è **azione di ruolo**, non decisione autonoma | Vincolo LIMET: le ambiguità si segnalano, non si assumono |
| 2026-10-29 | Q-5 chiusa: pilota in `test\`, JDK 17 LTS (delega) | Risposta SUPERVISORE; nessun requisito cambiato |
| 2026-10-29 | Q-8 decisa su delega del SUPERVISORE (soglie 80/60/12 [P], `.claude/` in git senza path locali, strumenti per stack) | Delega «decidi tu / scegli i migliori»; nessun requisito cambiato |
| 2026-10-29 | Dettaglio: passi E2E V1–V12 e V20–V24, schede task | Richiesta SUPERVISORE; nessun requisito cambiato |
| 2026-10-29 | Ambito esteso a Fasi 1–12 + 8b: §5.2 C01–C28, §5.3 Q1–Q9, §5.4 fasi/gruppi, §5.5 gate come decisione + piano B, §5.6 regola planner ECC e C28; TASK_LIST 70 task, TEST_PLAN 58 test, E2E V1–V24 | Verifica di completezza richiesta dal SUPERVISORE. Approvazione precedente data per la versione PoC: **riconfermata dal SUPERVISORE sull'ambito esteso il 2026-10-29 20:13** |
