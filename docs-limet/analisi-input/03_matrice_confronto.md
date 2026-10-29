# 03 — Matrice di confronto

> HELIOS · documento 03/05. Tipo di dato: **F** verificato · **I** deduzione/ipotesi · **N** non reperito. Decisione: **M** mantenuta · **P** portata · **A** adattata · **L** sostituita da LIMET · **W** wrapper · **X** esclusa · **V** da verificare. ID C## = criticità in `05`.

## 1. Agenti

| Componente | limet-agents (design) | ECC | Sovrapposizione | Decisione | Motivazione | Crit. | Dato |
|---|---|---|---|---|---|---|---|
| analyst | sì (opus) | `planner`, `architect`, `code-explorer` | piano/analisi | **L** (planner escluso), `architect`/`code-explorer` come lenti **A** | il PLAN è di LIMET | C06 | F/I |
| developer | sì | `code-architect`, `refactor-cleaner` | scrittura codice | **M** (ruoli LIMET) | ECC con Bash/Write | C02 | F |
| tester | sì | `tdd-guide`, `e2e-runner` | test | **W**: solo prompt, esecuzione nel runner | eseguono comandi | C02 | F |
| runner | script | build-resolver ×12 | build | **M** LIMET; resolver **X** | il runner è l'unico a eseguire | C02,C18 | F |
| reviewer | sì (opus) | 23 reviewer + trasversali | review | **A**: lenti di dominio senza Bash | valore aggiunto | C02 | F/I |
| validator | sì | `security-reviewer` (parziale) | sicurezza | **A** | — | C02 | I |
| orchestrazione | workflow LIMET | `loop-operator`, `harness-optimizer` | orchestrazione | **X** | duplica | C06 | F |
| fuori dominio | — | marketing, SEO, chief-of-staff | nessuna | **X** | non pertinenti | — | F |

## 2. Skill (per famiglia; tabella per-skill in `appendici/`)

| Famiglia | N. circa | Decisione | Rischio manutenzione | Crit. | Dato |
|---|---|---|---|---|---|
| Linguaggi/framework (modulo `framework-language`) | 71 percorsi | **P** previa allowlist e test agnosticità | medio | C07,C17 | F/I |
| Qualità (tdd-workflow, verification-loop) | 52 percorsi | **A** (senza esecuzione) | medio | C02 | I |
| Plan/spec (`orch-*`, `blueprint`, `plan-orchestrate`, `plan-canvas`) | ~25 | **L** / **X** | — | C06,C09 | F |
| Orchestrazione/memoria (agentic-os, unified-memory, continuous-learning-v2, …) | ~15 | **X** | — | C08 | F |
| SaaS/media/pagamento (exa, fal, ito-*, videodb, x402, …) | ~15 | **X** | — | C11 | F |
| Sicurezza (`security-scan` → AgentShield) | 2–3 | **V** (fase 2) | alto | C10 | F |
| CC-BY-SA (`osint-investigation`) | 1 | **X** | licenza | C07 | F |
| Traduzioni `docs-<locale>` | 13 moduli | **X** | — | — | F |

## 3. Workflow

| Workflow | LIMET | ECC | Decisione | Crit. |
|---|---|---|---|---|
| Piano | PLAN fasi 0–7, gate | `/plan`, `/plan-prd`, `prp-*`, `orch-*` | **L** | C06 |
| Implementazione | developer + runner | `tdd-workflow`, `/prp-implement` | **L**, TDD come lente **A** | C02,C03 |
| Review | reviewer | `/code-review` | **A** | C06 |
| Commit/PR | autorizzazione utente | `/prp-commit`, `/pr`, `orch GATE 2` | **X** (policy: mai commit autonomi) | C02 |
| Epic/Jira | — | `/epic-*`, `/jira` | **V** | — |

## 4. Hook

| Hook ECC | Decisione | Crit. |
|---|---|---|
| GateGuard, config-protection, commit-quality | **X** | C04 |
| block-no-verify | **V** (hook LIMET equivalente) | C04 |
| observe, cost-tracker, session-activity, command-log | **X** (metriche da metrics-observer LIMET) | C05,C08 |
| session:start, stop:* | **X** | C05 |

## 5. Comandi, configurazioni, dipendenze

| Elemento | Decisione | Note |
|---|---|---|
| 97 comandi | **X** salvo allowlist **P** (con prefisso `ecc-`) | C06 |
| `ecc setup`, plugin `ecc@ecc` | **X** | C05 |
| `ecc install` profilo `minimal` | **W** solo Piano B | C05 |
| rules `common/` | **X** (4 file) / **A** | C03 |
| rules per linguaggio (`paths:`) | **P** | C03 |
| Dipendenze npm (ajv, zod, sql.js…) | non necessarie con porting testuale | C12 |
| Python/Rust/tmux ECC | **X** | C13 |
| Windows + Unix | requisito su ogni componente HELIOS | C13 |

## 6. Conflitti [F salvo diversa indicazione]

| Tipo | Conflitto | Soluzione (vedi `05`) |
|---|---|---|
| Responsabilità | ECC esegue build/test/commit vs runner LIMET | C02 |
| Configurazione | rules `common/` sempre caricate | C03 |
| Naming | `/plan`, `planner`, `code-reviewer` | C06 |
| Lifecycle | SessionStart/Stop hook, **I** ordine hook non garantito | C04,C05 |
| Memoria | `.ecc/memory` vs CEREBRO | C08 |
| Licenza | CC-BY-SA, Apache, Wirasm | C07 |
| Piattaforma | tmux/hook su Windows | C13 |

## 7. Gap

Senza equivalente in ECC: gate meccanici, runner, stati pratica, tracciabilità artefatti, metriche LIMET (ALA). Senza equivalente in LIMET: rules di linguaggio, reviewer di dominio, skill per framework. **N**: `CRB_limet`, stato Qdrant.

## 8. CEREBRO e fusione in progetto unico

| Capacità | CEREBRO [F] | ECC [F] | Decisione |
|---|---|---|---|
| Retrieval | Qdrant + Ollama `nomic-embed-text`, MCP `limet_search`, hook UserPromptSubmit | lessicale, `.ecc/memory` | CEREBRO (C08) |
| Orchestrazione/stato/osservabilità | nessuna | state store SQLite, Control Pane | LIMET (C09) |

| Elemento | Oggi | Dopo la fusione **[P]** | Crit. |
|---|---|---|---|
| Repository | LIMET (`odyssey2073/limet`) + CEREBRO (remoto **N**) | un repo, moduli `limet/`, `cerebro/`, `agents/` | C21 |
| Punti d'ingresso legacy | percorsi e script separati | shim in `compat/` | C19 |
| `projects.json` | nel dir CEREBRO, può contenere credenziali | fuori repo, solo `projects.example.json` | C20 |
| CI | solo CEREBRO (pytest, windows-latest) | matrice unica Win/Linux/macOS per modulo | C22, C13 |
