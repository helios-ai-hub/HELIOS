# 02 — Analisi di limet-agents

> HELIOS · documento 02/05. Etichette: **[F]** fatto · **[I]** ipotesi · **[P]** proposta · **[N]** non verificato.
> Fonti: `ANALISI_limet-agents_con_workflow_claude.md` (ALA), `FEASIBILITY_O1_CLAUDE_CODE.html` v2.0 (FO1), repository `C:\Progetti\VSC\LIMET\`, `C:\software\cerebro\`.

## 0. Stato di fatto

- **[F]** `C:\software\limet-agents\` **non esiste**: limet-agents è un **design** (ALA), non codice. Le sezioni «agenti, skill, hook» descrivono quindi il progetto, non un'implementazione. La fattibilità si valuta sul design (**[I]**), da confermare con il PoC (`05` C14).
- **[F]** FO1: verdetto «FATTIBILE» con modifiche documentali preliminari a LIMET; perimetro = LIMET + Claude Code nativo.

## 1. LIMET (base operativa) [F]

Fonte: `C:\Progetti\VSC\LIMET\` (manuali IT/EN, 15 template, `scripts/`).
- Ciclo a fasi 0–7; regola §1.3: l'agente **non esegue build/test** e **non fa commit/push** senza autorizzazione.
- `scripts/limet.ps1` / `limet.sh` (blocchi in `AGENTS.md`, `@AGENTS.md` in `CLAUDE.md`), `limet-index` (CEREBRO + Graphify), launcher (:8799), `limet_related.py` (25 test). Nessuna CI nel repo LIMET.
- `GUIDA_ECC_BIBLIOTECA.md`: piano di un esperimento ECC mai eseguito; utile come disegno A/B (metriche §7).
- **[F]** `CLAUDE.md` punta a `C:\Progetti\CEREBRO` (errato: CEREBRO è in `C:\software\cerebro`).

## 2. Requisiti e ruoli del design (ALA)

- **[F]** Requisiti R1–R27 (ALA): R13 gate, R17 stati, R19 permessi deny su `./mvnw*`, `git push*`, `git merge*`, R23 Dynamic Workflow `limet-change.js`, R24 retrocompatibilità, R25–R27 metriche/dashboard.
- **[F]** Ruoli (ALA §3, FO1 §1.3):

| Ruolo | Modello | Responsabilità | Esegue comandi? |
|---|---|---|---|
| analyst | opus | analisi, PLAN | no |
| developer ×N | sonnet | codice (worktree isolati) | no |
| tester ×M | sonnet | scrittura test | no |
| integrator | — | integra rami | no |
| runner | **script**, non LLM | esegue build/test | sì (unico) |
| test-reader | haiku | legge esiti | no |
| reviewer | opus (≠ developer) | review | no |
| validator | opus | validazione finale | no |
| metrics-observer | — | metriche | no |
| e2e-designer / e2e-reader | — | scenari E2E | no |

- **[F]** Gate meccanici (FO1 §2.4), stati (§2.2), scheduler (§2.3), scheletro workflow (§2.5), protocollo supervisore «fermati e chiedi» (§3), costi 20–45 USD / 6–10 h (§6).

## 3. Punti di forza [I]
Tracciabilità per artefatti; esecuzione confinata nel runner; review con modello diverso; compatibilità con CEREBRO read-only; regole utente già codificate (nessun commit/push/build autonomi).

## 4. Lacune [I]
Nessuna implementazione; nessun catalogo di skill per linguaggio/dominio; nessuna rule di stile per linguaggio; review specializzate assenti; stato CEREBRO/Qdrant non verificato **[N]**; `CRB_limet` non registrato **[N]**.

## 5. Vincoli
- **[F]** Scope ALA §2: esclusi framework di terzi, altri strumenti AI, repository esterni → HELIOS richiede ADR di eccezione (`05` C01).
- **[F]** ALA §13: skill esterne ammesse solo se **LIMET-agnostiche** (`05` C17).
- **[P]** Vincolo di piattaforma: tutto deve funzionare su Windows **e** Unix (`05` C13). LIMET ha già `limet.ps1`/`limet.sh`; limet-agents deve condividere un core portabile.
- **[P]** Vincolo di progetto: LIMET, CEREBRO e HELIOS vivono in un unico repository a moduli (`04` §6, `05` C19–C22); la separazione attuale (LIMET in `C:\Progetti\VSC\LIMET`, CEREBRO in `C:\software\cerebro`) è di fatto, non di design.
- **[F]** Policy utente: nessuna build/test autonome, nessun commit/push.

## 6. Compatibilità con ECC

| Area | Giudizio | Evidenza |
|---|---|---|
| Asset testuali (rules con `paths:`, reviewer, skill pure) | compatibile **[I]** | `01` §4, §7, §11 |
| Agenti con Bash/Write | incompatibile senza adattamento del frontmatter | `01` §3 vs ALA R19 |
| Hook, GateGuard, memoria ECC | incompatibili | `01` §6, §8 |
| Flusso `/plan` ecc. | sostituito da LIMET | `01` §5 |
| Install globale `~/.claude` | incompatibile con isolamento | `01` §8 |
