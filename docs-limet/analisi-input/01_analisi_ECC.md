# 01 — Analisi di ECC (affaan-m/ECC)

> HELIOS · studio di fattibilità · documento 01/05 · data di analisi: 2026-10-28
> Etichette: **[F]** fatto verificato · **[I]** ipotesi · **[P]** proposta · **[N]** non verificato (legenda completa in `00_README.md`).
> Fonte primaria: clone **in sola lettura** di `https://github.com/affaan-m/ECC`, commit `25278e7d7b6cf6e6a4e7a6c8e10f14ff22f0eb5b` (v2.2.3, licenza MIT). Percorsi relativi alla radice del repository. Il clone temporaneo è stato eliminato a fine studio.
> Fonti web: `https://ecc.tools` e pagine collegate (registro di 34 link in `appendici/ecc-web.md`).

## 0. Come leggere questo documento (limiti dell'evidenza)

- **[F]** Tutti i conteggi sono stati ricalcolati dal clone: 71 agenti (`agents/`), 311 cartelle di skill (`skills/`, 614 file), 97 comandi (`commands/`), 122 file di regole (`rules/`), 372 script (`scripts/`), 518 test (`tests/`), 4.649 file totali.
- **[F]** I conteggi dichiarati altrove non coincidono: `plugins/ecc/.codex-plugin/plugin.json` parla di «249 skill»; il builder di `https://ecc.tools/skills` di «91 skill, 18 agenti, 48 comandi (157 totali)»; `COMMANDS-QUICK-REF.md` di «94 slash command»; `rules/common/agents.md` di «69 agenti»; `https://skills.sh/affaan-m/ecc` di «327 agent skills». I numeri del README/plugin.json (71/311/97) coincidono con il clone.
- **[N] Lettura non integrale.** La lettura integrale richiesta non è stata completabile nei limiti operativi: per le 311 skill e i 71 agenti è stato letto in modo sistematico il frontmatter, le intestazioni e le righe di riferimento (e il corpo completo dei file chiave: `planner`, `plan-canvas`, `unified-memory`, `plan-orchestrate`, `orch-*`, `tdd-workflow`, `commands/plan*.md`, `commands/prp-*.md`, `hooks/hooks.json`, ecc.). Il dettaglio è nella sezione «Limiti» di ciascun file in `appendici/`. Ogni cella derivata da scansione automatica è marcata [I].
- **[N]** La cronologia git non è verificabile (clone shallow a 1 commit): autori, frequenza e storia dei commit non sono stati analizzati.

## 1. Che cos'è ECC

- **[F]** `README.md`: ECC è presentato come «operating layer for agent harnesses»: raccolta di skill, agenti, hook, comandi e strumenti per agenti di coding. Pipeline dichiarata: `plan -> test -> implement -> review -> verify -> remember -> improve`; motto «Optimize the context window. Persist everything else.» `SOUL.md`: principi Agent-First, Test-Driven, Security-First, Immutability, Plan Before Execute.
- **[F]** Nome: rinominato da «Everything Claude Code» a «ECC» (v2.0.0, 2026-06-09); dalla v2.2.3 il nome non è più espanso (`docs/releases/*`).
- **[F]** Distribuzione: pacchetto npm `ecc-universal` (v2.2.3, MIT, node ≥ 18, provenance SLSA — `https://registry.npmjs.org/ecc-universal/latest`), plugin Claude Code `ecc@ecc` (`.claude-plugin/plugin.json`, `marketplace.json`). Installazione dichiarata: `npx ecc-universal@2.2.3 setup`; requisiti: Node ≥ 18, Git, Claude Code ≥ 2.1.
- **[F]** Maintainer unico (`.github/CODEOWNERS`: `* @affaan-m`; README: «a single maintainer»). Release: 8 datate in `CHANGELOG.md` in ~7 mesi (2.2.3 del 2026-10-01); 2.1.0 e 2.2.1 hanno note in `docs/releases/` ma **non** nel CHANGELOG (**[I]** cadenza reale ≥ 10 release in 7 mesi).
- **[F]** Evoluzione del catalogo: 1.10.0 = 38 agenti/156 skill/72 comandi → 2.0.0 = 64/261/84 → 2.2.3 = 71/311/97 (`docs/releases/*`, `docs/ROADMAP.md`). **[F]** `docs/ROADMAP.md` ammette che il numero di skill è «a liability» (sovrapposizioni, skill non referenziate) e prevede un piano di condensazione.
- **[I]** Velocità alta + catalogo in rapida crescita + rename recenti ⇒ rischio di deriva di struttura/API tra versioni (rilevante per qualsiasi porting con pin).

## 2. Architettura

### 2.1 Strati

| Strato | Contenuto | Evidenza |
|---|---|---|
| Asset prompt | `agents/` (71), `skills/` (311), `commands/` (97 shim), `rules/` (122 file) | **[F]** conteggi dal clone |
| Runtime hook | `hooks/hooks.json` (27 entry, 8 eventi) + `scripts/hooks/` (~57 script Node/Bash/Python) | **[F]** `hooks/README.md`, `hooks.metadata.json` |
| Installer/CLI | `scripts/ecc.js` (dispatcher `ecc`), `install-apply.js`, manifest `manifests/*.json` (49 moduli, 7 profili) | **[F]** `package.json`, `manifests/` |
| Servizi locali | Plan Canvas (127.0.0.1:4517), Control Pane (127.0.0.1:8765), Unified Memory (vault + MCP stdio), state store SQLite (sql.js) | **[F]** `scripts/` |
| Alpha | `ecc2/` (Rust, TUI/CLI di orchestrazione, v0.1.0, «alpha, non finito») | **[F]** `ecc2/README.md`, `Cargo.toml` |
| Adapter harness | `.claude-plugin`, `.codex-plugin`, `.qoder-plugin`, `.cursor`, `.opencode`, `.gemini`, `.zed`, `.kimi`, `.pi`, … | **[F]** `docs/architecture/harness-adapter-compliance.md` |

- **[F]** «La radice è la source of truth»: le directory adapter proiettano gli stessi asset (`harness-adapter-compliance.md`). Le skill sono la superficie primaria; i comandi sono shim di compatibilità (12 comandi ritirati sono in `legacy-command-shims/`, non caricati dal plugin).
- **[F]** `docs/capability-surface-selection.md`: regola/skill/MCP/CLI/API ciascuno nella superficie più stretta. `docs/SKILL-PLACEMENT-POLICY.md`: skill curate (spedite) vs learned/imported/evolved (`~/.claude/...`, mai spedite, provenienza obbligatoria).
- **[F]** `docs/architecture/platform-value-loop.md`: il Control Pane è «release-candidate direction» finché non esiste una demo riproducibile.

### 2.2 Dipendenze (core vs opzionali)

- **[F] Runtime npm** (`package.json`): `@iarna/toml` (ISC), `ajv`, `js-yaml`, `sql.js`, `zod` (MIT). Python ≥ 3.11 solo per `src/llm` e `instinct-cli.py`; Rust/cargo solo per `ecc2`; bash per `observe.sh`; `tmux` per l'orchestrazione a worktree (**[I]** non nativo su Windows). Licenze delle dipendenze dev/pip/cargo: **[N]**.
- **[F] Dipendenze da Claude Code:** hook completi solo su Claude Code (profili off/minimal/standard/strict); `plugin.json` richiede Claude Code ≥ 2.1 (auto-caricamento di `hooks/hooks.json`); i nomi dei tool negli agenti includono forme specifiche (`mcp__playwright__*`, `mcp__context7__*`); gli hook invocano la CLI `claude -p` (riassunti, observer) con le credenziali dell'utente.
- **[F] Servizi esterni opzionali:** GitHub App «ECC Tools» (hosted, invia dati ad Anthropic), ECC Pro (a pagamento), Itô Compute, Nasiko (richiedono login/control plane esterni), fal.ai/VideoDB/Nutrient ecc. in singole skill.

## 3. Agenti (71)

- **[F]** Modelli: 61 `sonnet`, 4 `opus` (`planner`, `architect`, `spec-miner`, `healthcare-reviewer`), 6 `haiku`. Tutti hanno il blocco «Prompt Defense Baseline».
- **[F] Permessi:** 55/71 hanno `Bash` e 28/71 hanno `Write`/`Edit` fra i tool; i limiti di scrittura (es. `spec-miner` solo in `openspec/specs/`) sono **solo testo del prompt**, non imposti dal runtime. **[I]** per un uso sotto `limet-agents` i `tools:` vanno riscritti.
- **[F]** `agent.yaml` non elenca alcun agente; ECC dichiara un IR neutro `schemas/agent.schema.json` per proiettare gli agenti su altri harness.

| Famiglia | N. | Agenti rappresentativi | Esegue comandi? | Scrive? |
|---|---|---|---|---|
| Planning/architettura | 9 | `planner`, `architect`, `code-architect`, `code-explorer`, `spec-miner`, `gan-planner` | `code-architect` sì (Bash) | `planner` no; `spec-miner` sì (`openspec/specs/`) |
| Implementazione/test | 9 | `tdd-guide`, `e2e-runner`, `gan-generator`, `gan-evaluator`, `doc-updater`, `refactor-cleaner` | sì (`npm test`, e2e) | sì |
| Build-fix per linguaggio | 12 | `build-error-resolver`, `java-build-resolver`, `go-/rust-/kotlin-/…-build-resolver` | sì (build) | sì (codice, «diff minimi») |
| Review per linguaggio/dominio | 23 | `code-reviewer`, `typescript-/python-/java-/kotlin-/go-reviewer`, `database-reviewer`, … | Bash presente (read-only solo da prompt) | no |
| Review trasversale/qualità | 8 | `security-reviewer`, `silent-failure-hunter`, `type-design-analyzer`, `comment-analyzer`, `commit-reviewer`, `agent-evaluator` | Bash presente | no |
| Orchestrazione/harness | 4 | `loop-operator`, `harness-optimizer`, `conversation-analyzer`, `docs-lookup` | `harness-optimizer` esegue script ECC | sì (`loop-operator`, `harness-optimizer`) |
| Fuori dominio software | 5 | `chief-of-staff`, `marketing-agent`, `seo-specialist`, `opensource-forker/-sanitizer` | — | alcuni |

- **[N]** Le note per agente (somma delle famiglie = 70; un agente non riconciliato nelle tabelle) sono in `appendici/ecc-agents.md`. Il file lì dichiara che ~60 agenti su 71 sono stati letti per frontmatter/intestazioni/riferimenti e non integralmente.
- **[F]** `planner` è **di sola lettura** (`Read, Grep, Glob`): il file `.claude/plans/*.plan.md` lo scrive il **comando** `/plan`, e il gate di conferma è nel comando, non nell'agente (`commands/plan.md`).
- **[F]** Invocazione: gli agenti sono richiamati dal testo di comandi/skill (Task/Agent tool) o via frontmatter (`security-scan.md`: `agent: ecc:security-reviewer`); `AGENTS.md` (radice ECC) istruisce l'uso «automatico» (`planner` per feature complesse, `code-reviewer` dopo ogni modifica, `tdd-guide` per bug/feature).

## 4. Skill (311)

- **[F]** Conteggio: 175 cartelle `a–l` (273 file, ~2,3 MB) e 136 `m–z`+cifre (341 file, ~2,6 MB). Skill pesanti in file: `taste-application` (81 file), `angular-developer` (35), `remotion-video-creation` (32), `skill-comply` (23).
- **[F] Provenienza** (`origin` in frontmatter, campione): `ECC` 382, `community` 58, `ECC direct-port` 10, `Health1` 4, altri minori (conteggio su ~1.075 file md, non solo skill).
- **[F] Struttura di installazione**: le skill sono raggruppate in **49 moduli** (`manifests/install-modules.json`) che costituiscono la mappa funzionale più affidabile:

| Modulo (esempi) | Contenuto | Costo/stabilità dichiarati | Note |
|---|---|---|---|
| `workflow-quality` (core, 52 percorsi) | `tdd-workflow`, `plan-canvas`, `verification-loop`, `eval-harness`, `strategic-compact`, `continuous-learning` | stable | mescola asset neutri e meccanismi ECC |
| `framework-language` (71 percorsi) | skill per linguaggi/framework (es. `springboot-*`, `quarkus-*`, `django-*`) | — | **[I]** candidate principali al porting |
| `security` | `security-review`, `security-scan`, … | — | `security-scan` usa `npx ecc-agentshield` |
| `database`, `devops-infra`, `agentic-patterns` (38), `machine-learning`, `documentation-*` | skill di dominio | — | valore variabile |
| `orchestration` | script tmux/worktree + `dmux-workflows` | beta | solo claude/claude-project/codex/opencode |
| `skill-unified-memory`, `nasiko-control-plane`, `ito-compute`, `media-generation`, `business-content`, `social-distribution`, `supply-chain-domain` | servizi/SaaS/media/business | heavy/experimental | fuori perimetro |
| 13 moduli `docs-<locale>` | traduzioni | — | non rilevanti |

- **[F] Skill del ciclo plan/spec** (rilevanti per la sostituzione con LIMET): `plan-canvas`, `plan-orchestrate`, `orch-pipeline` + `orch-add-feature/-change-feature/-fix-defect/-refine-code/-build-mvp`, `blueprint`, `intent-driven-development`, `brownfield-retrospective`, `contract-first`, `context-and-decisions`, `change-impact`, `living-docs-governance`, `docs-governance`, `architecture-decision-records`, `product-capability`, `product-lens`, `ralphinho-rfc-pipeline`, `story-lifecycle`, `dev-team`, `ecc-recipes`, `tdd-workflow` (sezione «Plan Handoff»).
- **[F] Skill che duplicano orchestrazione/memoria/loop:** `agentic-os`, `autonomous-agent-harness`, `ck`, `continuous-learning-v2`, `gateguard`, `claude-devfleet`, `team-agent-orchestration`, `team-builder`, `parallel-execution-optimizer`, `santa-method`, `unified-memory`, `nanoclaw-repl`, `recursive-decision-ledger`, `operator-approval-loop`.
- **[F] Skill con dipendenze pesanti/a pagamento/rete** (non integrabili senza revisione): `exa`, `fal`, `ito-*`, `codehealth`, `agent-payment-x402`, `videodb`, `social-publisher`, `x-api`, `mailtrap-email-integration`, `nutrient-document-processing`, `taste-*` (fal.ai), `security-scan` (AgentShield), `skill-comply` e `plankton-code-quality` (lanciano `claude -p`, costo token), `repo-scan` (clona un commit esterno), `windows-desktop-e2e` (solo Windows).
- **[F]** `manifests/pi-core.json` elenca un profilo curato («Pi core», 123 skill + 24 prompt, offline, senza hook) che **esclude** hook, roster agenti/`orch-*`, continuous-learning, loop, hookify, cost tracking, session save/resume, gateguard e tutto ciò che dipende da rete/SaaS. **[I]** È la migliore lista ufficiale di cosa è «puro prompt» e portabile fuori da Claude Code; è il punto di partenza per l'allowlist di HELIOS.
- **[I]** Tabella per-skill (nome, scopo, dipendenze, uso, rischio): in `appendici/ecc-skills-a-l-1.md`, `ecc-skills-a-l-2.md`, `ecc-skills-m-z.md` (le colonne «Scrive», «Claude?», «Agn.», «Rischio» sono in gran parte dedotte da description/intestazioni e conteggi di parole chiave: **non** verificate sul corpo).

## 5. Comandi (97) e ciclo spec-driven

- **[F]** `commands/` = 97 file; nomi invocabili come `/ecc:<nome>` (forma breve se non ambigua). Famiglie: piano/spec (`plan`, `plan-prd`, `plan-canvas`, `prp-*`, `orch-*`, `epic-*` ×7, `feature-dev`, `multi-*`, `jira`), review/build per linguaggio (`code-review`, `build-fix`, `cpp-/go-/rust-/kotlin-/python-/react-…`), sessione (`save-session`, `resume-session`, `sessions`, `checkpoint`), apprendimento (`evolve`, `learn*`), operazioni (`loop-start`, `gan-build`, `harness-audit`, `security-scan`, `cost-report`, …). Tabella dei 97 in `appendici/ecc-hooks-commands-rules.md` §2.3.
- **[F]** **Non esiste un flusso «spec-driven» dedicato** in stile spec-kit (grep su `/specify`, `spec-kit`: nessuna corrispondenza). Il ciclo è **PRD → plan → TDD → review → PR**, in tre filoni sovrapposti (`docs/PLAN-PRD-PATTERN.md`, `commands/plan*.md`, `commands/prp-*.md`, `skills/orch-*`):
  1. **Filone nuovo:** `/plan-prd` → `.claude/prds/{n}.prd.md` (tabella «Delivery Milestones»); `/plan` (inline, senza subagent) → `.claude/plans/{n}.plan.md` (Source PRD, Patterns to Mirror con `path:line`, Files to Change, Tasks, Validation, Risks, Acceptance); in modalità PRD **modifica il PRD** (milestone `pending→in-progress`).
  2. **Confirm gate:** `plan.md`: «WAIT for user CONFIRM before touching any code». **[F]** È solo prosa nel prompt: nessun hook né CI lo applica. Alternativa visuale: `/plan-canvas` (verdetto «Approve plan» = conferma).
  3. **Filone PRP legacy** (`prp-prd`, `prp-plan`, `prp-implement`, `prp-commit`, `prp-pr`): file «Adapted from PRPs-agentic-eng by Wirasm» (licenza upstream **[N]**); `prp-implement` crea il branch `feat/<piano>` ed esegue 5 livelli di validazione; sposta il piano in `.../completed/`.
  4. **Filone `orch-*`:** classificatore di taglia (trivial/small/standard/large) → Research → Plan → **GATE 1 (approva piano)** → TDD → `code-reviewer` (+`security-reviewer`) → commit → **GATE 2 (conferma commit)**. `orch-fix-defect`: prima test di regressione rosso, poi fix.
- **[F]** `rules/common/development-workflow.md` (**sempre caricato** se installato) sancisce Research → Plan (agente `planner`) → TDD (80%) → code review → commit conventional. `rules/common/agents.md` istruisce Claude a usare `ecc:planner`, `ecc:code-reviewer`, `ecc:tdd-guide` «senza prompt dell'utente».
- **[F] Altri:** `/multi-plan`/`/multi-execute` richiedono il runtime esterno `ccg-workflow` **non incluso**; `/epic-*` coordina via GitHub (`scripts/github-coordination.js`, un branch per epic, nessun branch per task, `config/github-native-coordination.json`); `/jira` è il punto «ticket → piano».
- **[I]** Per la sostituzione con LIMET i punti d'innesto sono: non installare (o shadowing) `plan`, `plan-prd`, `prp-*`, `orch-*`, `blueprint`, `plan-orchestrate`, `epic-*`; neutralizzare `rules/common/{development-workflow,agents,testing,git-workflow}.md`; verificare gli accoppiamenti inversi (`tdd-workflow` e `/code-review` leggono `.claude/prds|plans|reviews`; **[N]** accoppiamento effettivo nel codice da verificare in Fase 2).

## 6. Hook (27 entry, 8 eventi)

- **[F]** Meccanismo: `hooks/hooks.json` (53 KB) contiene solo ciò che Claude Code accetta; ID/descrizioni in `hooks/hooks.metadata.json` (validato da `scripts/ci/validate-hooks.js`). Ogni hook passa da `scripts/hooks/run-with-flags.js` (filtro per profilo `ECC_HOOK_PROFILE` = minimal/standard/strict e per `ECC_DISABLED_HOOKS`). Schema ammesso: 18 eventi; ECC usa solo tipo `command`.
- **[F] Hook che possono bloccare** (exit 2/deny): `pre:bash:block-no-verify`; `pre:config-protection` (blocca modifiche a config linter/formatter esistenti); `pre:edit-write:gateguard-fact-force` (**nega la prima modifica di ogni file** finché l'agente non fornisce «fatti»; stato in `~/.gateguard`); `pre:bash:gateguard-fact-force` e variante PowerShell (comandi distruttivi); `pre:mcp-health-check`; `pre:bash:commit-quality` (solo `strict`); `pre:bash:dispatcher` (fail-closed oltre `ECC_HOOK_INPUT_MAX_BYTES`, default 1 MiB); hookify con regole `block`.
- **[F] Hook di stato/telemetria locale** (`.*` su Pre/PostToolUse): `observe` (continuous learning), `session-activity-tracker`, `command-log-audit` (scrive `bash-commands.log`), `cost-tracker`, `metrics-bridge` → file in `~/.claude/` e `~/.claude/metrics/`. **[F]** Nessuna telemetria verso servizi ECC trovata (grep su `hooks/`, `scripts/hooks/`; **[N]** non esaustivo). `stop:session-end`/`llm-summary` eseguono `claude -p` con gli ultimi turni (egress verso l'LLM dell'utente).
- **[F] Hook di contesto:** `session:start` inietta fino a 8.000 caratteri (sessioni salvate, instincts); `stop:plan-canvas-pending` e `stop:hookify-runtime` possono forzare un altro turno.
- **[F] Consenso:** `ecc install` richiede `--enable-hooks` e dichiara 6 gruppi di effetti (scritture automatiche sul sorgente, riscrittura comandi, egress LLM da transcript, rete MCP, gate permessi, record sessione/costo) (`scripts/lib/install/hook-consent.js`). Il profilo `minimal` dell'installer **non include hook**.
- **[I] Conflitti** con un progetto governato da LIMET: GateGuard e `config-protection` bloccano agenti che modificano molti file/configurazioni in autonomia; due plugin con hook `Stop` che riavviano il turno possono entrare in ping-pong; `SessionStart` concorre con il contesto LIMET/AGENTS.md; Claude Code esegue in parallelo tutti gli hook di tutti i plugin senza ordine garantito (**[I]**, da verificare nella documentazione Claude Code).

## 7. Rules (122 file)

- **[F]** `rules/common/` (10 file, senza frontmatter `paths:` ⇒ **sempre caricati**), 21 directory per linguaggio/dominio (111 file con `paths:`: caricati solo sui file corrispondenti). Normativo: immutabilità «CRITICAL», file 200–400 righe, funzioni < 50 righe, copertura 80%, TDD obbligatorio, commit conventional, uso automatico degli agenti.
- **[F]** Installazione consigliata in `~/.claude/rules/ecc/` o `.claude/rules/ecc/` (namespace per evitare collisioni di nomi generici).
- **[I]** Le rules di linguaggio con `paths:` (es. `rules/java/*`, `rules/typescript/*`) sono il tipo di asset più facilmente portabile; quelle `common/` contraddicono le policy LIMET (§1.3: l'agente non esegue build/test) e vanno escluse.

## 8. Runtime e servizi locali

| Componente | Funzione | Evidenza |
|---|---|---|
| **Plan Canvas** | review di un `*.plan.md` nel browser; server HTTP `127.0.0.1:4517` (idle 30 min), long-poll `await`, difese loopback (allowlist Host/Origin, CSP, body ≤ 1 MB); Mermaid dal CDN (fallback offline) | **[F]** `scripts/plan-canvas.js`, `scripts/lib/plan-canvas/server.js`, `skills/plan-canvas/SKILL.md`; ispirato a `kunchenguid/lavish-axi`, «implementazione originale» |
| **Control Pane** | dashboard `127.0.0.1:8765`; allowlist di azioni = sottocomandi `ecc2`; **azioni abilitate di default** (`--read-only` per disattivare) | **[F]** `scripts/control-pane.js` |
| **Unified Memory** | vault markdown locale (`<git root>/.ecc/memory`, `~/.ecc/memory`), formato `ecc.memory.v1`, MCP stdio `ecc-memory-mcp`; **ricerca solo lessicale** (nessun embedding/vector DB/rete) | **[F]** `scripts/lib/memory-vault*.js` |
| **State store** | SQLite (sql.js) `~/.claude/ecc/state.db`: sessioni, skillRun, decisioni, governanceEvent, workItem | **[F]** `scripts/lib/state-store/*` |
| **Instincts / continuous-learning-v2** | hook → `observations.jsonl`; observer in background `claude --model haiku --print` (**disabilitato di default**); injection di instincts a SessionStart | **[F]** `skills/continuous-learning-v2/*` |
| **AgentShield** | scanner di sicurezza di configurazioni agentiche (102 regole dichiarate); repo/pacchetto **separato** `ecc-agentshield` v1.6.0 (MIT su npm; dipende da `@anthropic-ai/sdk`; `--opus` invia contenuto ad API LLM) | **[F]** README ECC, `registry.npmjs.org/ecc-agentshield/latest`; codice dello scanner **non** nel clone; README: «ECC does not supply an audited AgentShield pin» |
| **Orchestrazione worktree** | piano JSON → git worktree + sessioni tmux; default dry-run | **[F]** `scripts/orchestrate-worktrees.js` |
| **ecc2** (Rust) | TUI/CLI di orchestrazione multi-harness; lancia `claude --print`, `codex exec`, … | **[F]** `ecc2/README.md` (alpha) |

- **[F] Installer:** due percorsi mutuamente esclusivi: `ecc setup --mode claude-plugin` (usa la CLI `claude plugin marketplace add/install`, **modifica `~/.claude/settings.json`**, disabilita la commit-attribution) e `ecc install` (file gestiti, `ownership-guard`: non sovrascrive file non registrati; stato in `~/.claude/ecc/install-state.json`; `uninstall`/`doctor`/`repair`). **[F]** Target: 16 (claude, claude-project, cursor, codex, copilot, gemini, opencode, …). **[F]** Il target `copilot` installa solo agenti e skill (nessun hook).
- **[F]** Parità funzionale completa solo su Claude Code (`docs/architecture/harness-adapter-compliance.md`, README «Platform Support»); Windows nativo «con limitazioni» (issue #2489, #2626, #2674 citate dal README).

## 9. Sicurezza, licenza, governance

- **Licenza [F]:** `LICENSE` MIT, «Copyright (c) 2026 Affaan Mustafa». Eccezioni: `skills/osint-investigation/` CC-BY-SA-4.0; 8 skill con `license: Apache-2.0` senza NOTICE; comandi `prp-*` e `code-review` «Adapted from PRPs-agentic-eng by Wirasm» senza licenza upstream dichiarata; `skills/taste-application/SOURCE.md` (zip fornito da un utente); skill ispirate a repository terzi (`lavish-axi`, `blueprint`, `ralphinho`, `frontend-slides`); nessun NOTICE, nessun CLA/DCO. Dettaglio e punti da verificare con un legale in `05_roadmap_e_rischi.md` §6 e `appendici/ecc-docs-license.md` §3.
- **Hosted vs open [F]:** il repo MIT è utilizzabile senza account. La GitHub App «ECC Tools» (`https://github.com/apps/ecc-tools`) è hosted: la privacy policy (marzo 2026) dichiara l'invio di **messaggi di commit, nomi degli autori e «limited code samples» all'API Anthropic Claude**, cache dei risultati fino a 7 giorni, nessuna menzione di DPA, regione dei dati o certificazioni (`https://ecc.tools/privacy`). Piani: Free $0 (solo repo pubblici), Pro $19/seat/mese, Enterprise su contatto (`https://ecc.tools/pricing`).
- **Sicurezza dichiarata [F]:** `SECURITY.md` (segnalazioni via GitHub Security Advisories; SLA 48 h/7 gg/14 gg); `the-security-guide.md` (vettori d'attacco, CVE Claude Code citati, «lethal trifecta»); `docs/security/supply-chain-incident-response.md`.
- **[F]** `docs/ECC-PRO-SECURITY-ROADMAP.md`: roadmap orientata a convertire il funnel gratuito in Pro (CTA nell'output CLI e nei commenti PR). **[I]** Possibile spinta commerciale futura sul tooling gratuito.
- **[F]** GateGuard: dichiarata A/B «+2,25 punti qualità», ma il pilot interno misura solo il costo (~+45% turni) senza effetti significativi (`CHANGELOG.md`, `docs/gateguard/question-effectiveness.md`).

## 10. Punti di estensione dichiarati [F]

Nuovo linguaggio in `rules/<lang>/` (5 file); nuovo hook (script in `scripts/hooks/` + ID in `hooks.metadata.json`); regole utente hookify (`.claude/hookify.*.local.md`; quelle tracciate da Git sono ignorate salvo `ECC_HOOKIFY_ALLOW_TRACKED=1`); `ecc-install.json` (`target/profile/modules/include/exclude/options`); nuove skill/comandi/agenti registrati in `package.json`, `manifests/install-components.json`, `install-modules.json`; `agent.schema.json` (IR). `config/project-stack-mappings.json` mappa stack→regole/skill/comandi e suggerisce permessi allow/deny.

## 11. Elementi riutilizzabili in HELIOS (sintesi)

| Elemento | Riutilizzabilità | Motivo |
|---|---|---|
| Rules per linguaggio con `paths:` (java, typescript, python, …) | **[I]** alta | testo puro, scoped, nessuna dipendenza |
| Reviewer di dominio (`java-reviewer`, `typescript-reviewer`, `database-reviewer`, `security-reviewer`, `silent-failure-hunter`, `type-design-analyzer`, `comment-analyzer`, `pr-test-analyzer`) come «lenti» di review | **[I]** alta | read-only per scopo; da riscrivere i `tools:` (senza Bash) |
| Skill `pi-core` (prompt puri) selezionate | **[I]** media-alta | già curate come portabili da ECC |
| AgentShield come passo di scansione del runner | **[I]** media | repo separato, MIT (npm), ma dipendenza LLM opzionale e pin non fornito |
| Plan Canvas come sola UI di lettura del PLAN | **[I]** bassa-media | locale, ma richiede runtime `ecc-universal`, hook e CDN; rischio di violare «gli agenti non scrivono documenti LIMET» se usato come loop di feedback |
| Installer `ecc install` con profilo `minimal` | **[I]** media (Piano B) | non sovrascrive file non gestiti, reversibile |
| `plan`/`prp`/`orch`/`epic`, Unified Memory, continuous-learning, GateGuard, GitHub App, Itô, Nasiko, ecc2, skill SaaS/media | **[P]** da escludere | sostituite da LIMET/CEREBRO o in conflitto (vedi `03_matrice_confronto.md`) |
