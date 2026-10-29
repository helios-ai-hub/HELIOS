# 04 — Architettura HELIOS

> HELIOS · documento 04/05. Etichette: **[F]** fatto · **[I]** ipotesi · **[P]** proposta · **[N]** non verificato. Rif.: ALA, FO1 (vincolanti), `01`–`03`, `05` (criticità C##).

## 1. Principi **[P]**
1. LIMET orchestra; Claude Code esegue gli agenti; nessun altro orchestratore (ALA §2, FO1 §1.2).
2. Solo il runner esegue build/test; mai commit/push senza autorizzazione (FO1 §1.3, ALA R19).
3. ECC = fornitore di asset testuali pinnati (commit `25278e7`), mai runtime.
4. Ogni criticità ha un'opzione risolutiva scelta (`05` §2–3).
5. **Cross-platform:** Windows e Unix con la stessa suite (C13); core in runtime portabile (Node o Python), niente tmux/bash/PowerShell nel core.
6. Deviazioni dai documenti vincolanti: HELIOS è un **nuovo progetto autonomo** (decisione del SUPERVISORE, C01) che contiene LIMET, CEREBRO ed ECC (allowlist) e ha **proprie regole** (costituzione HELIOS, da redigere), che ereditano i vincoli non negoziabili: nessuna build/test/commit/push autonomi, gate umani, Windows+Unix. Un ADR fondativo documenta lo scope.
7. **Progetto unico** (richiesta del SUPERVISORE): LIMET, CEREBRO e HELIOS sono moduli di un solo repository, così ogni modifica a LIMET o CEREBRO si gestisce in un'unica sede (C19–C22): confini di modulo imposti, shim di compatibilità per i consumatori esistenti, segreti fuori dal repo, CI unica a matrice Windows/Linux/macOS.

## 2. Architettura target **[P]**

```
Utente/SUPERVISORE ── approva gate ──┐
                                     ▼
Claude Code (esegue gli agenti) ◄── Workflow LIMET (limet-change.js, stati, gate)
   │ ruoli: analyst, developer×N, tester×M, reviewer, validator, readers
   │ contesto read-only: CEREBRO (MCP limet_search, Qdrant+Ollama)
   │ asset ECC portati: rules per linguaggio, lenti di review, skill pure (prefisso ecc-)
   ▼
Runner (script portabile, allowlist, senza rete, log immutabili) → esiti/artefatti
```

## 3. Responsabilità

| Attore | Responsabilità | Non fa |
|---|---|---|
| Claude Code **[F]** | esegue agenti/subagent, tool, permessi | non decide il flusso |
| LIMET **[P]** | stati, gate, scheduler, artefatti, metriche | non esegue codice |
| Agenti | produrre piano/codice/test/review come file | non eseguono build/test/git |
| Runner | build, test, scansioni; log hash | non committa/pusha |
| Skill/rules ECC portate | conoscenza di dominio per ruolo | no hook/tool/memoria/stato |
| CEREBRO | contesto read-only per i ruoli ammessi (analyst, validator, risk-assessor, developer, reviewer; tester solo documenti), collection `CRB_<slug>` e `HLS_*` (Q9) | nessuna orchestrazione |

Chi avvia chi **[P]**: il workflow LIMET avvia ogni agente tramite Claude Code; analyst→PLAN→gate umano→developer/tester (worktree)→runner→test-reader→reviewer (modello diverso)→validator→gate finale. Contesto passato come artefatti su file (schemi JSON, FO1 §1.3). Errori: retry limitato a N, poi stato «fermati e chiedi» (FO1 §3).

## 4. Hook, skill, artefatti
- Hook ECC: nessuno (C04, C05). Eventuali hook LIMET propri, cross-platform.
- Skill ECC: allowlist con ledger licenze e test di agnosticità (C07, C17); applicate per ruolo, non globali (C03).
- Artefatti: quelli LIMET (ALA §6); nessun file `.claude/plans|prds` (C06).

## 5. Opzioni architetturali valutate

| Criterio | 1 Merge diretto | 2 Porting selettivo | 3 Wrapper/adapter | 4 Modulare con componenti ECC separati | 5 Ibrida ★ |
|---|---|---|---|---|---|
| Descrizione | copiare ECC in limet-agents | copiare solo asset scelti | ECC installato, adapter lo neutralizza | ECC come moduli esterni | porting (lane A) + moduli opzionali (B) + esclusi (C) |
| Vantaggi | massima copertura | controllo, pochi rischi | nessuna copia | aggiornamenti upstream | equilibrio |
| Svantaggi | collisioni, hook, licenze | lavoro manuale | ECC attivo a runtime | runtime e dipendenze ECC | più componenti da governare |
| Complessità / effort **[I]** | alta / 60–90 gg | media / 40–60 | media / 30–45 | alta / 50–70 | media / 46–70 |
| Rischi | C02–C07 tutti aperti | C07, C12 | C04, C05, C13 | C05, C10, C13 | C07, C12 |
| Manutenibilità | bassa | media | media | bassa | media |
| Reversibilità | bassa | alta | alta | media | alta |
| Compat. Claude Code | alta | alta | alta | alta | alta |
| Compat. workflow LIMET | bassa | alta | media | media | alta |
| Impatto skill | tutte, conflitti | allowlist | tutte caricate | per modulo | allowlist |
| Impatto spec-driven | doppio flusso | sostituito | da neutralizzare | da neutralizzare | sostituito |
| Valutazione | no | buona | piano B | sconsigliata | **raccomandata** |

Le stime sono **[I]**; nessuna è misurata.
**Raccomandazione [P]:** Opzione 5 con tutte le scelte ★ di `05` §3 ⇒ in pratica un porting selettivo (Opz. 2) rafforzato da un adapter solo per Piano B. **Piano B:** Opzione 3 (profilo `minimal`, senza hook). **Alternativa clean-room:** C01-B se la verifica legale (C07) fallisce.

## 6. Ruolo di CEREBRO e struttura del progetto unico **[P]**
Layout proposto (monorepo, C22-A):
```
helios/            (repository unico)
├─ limet/          metodo, manuali IT/EN, template, scripts (limet.ps1/.sh, limet-index, launcher, limet_related.py)
├─ cerebro/        RAG: ingest, query_qdrant, MCP limet_search, hook limet_rag.py (+ projects.example.json)
├─ agents/         plugin Claude Code limet-agents (agents/plugin/), workflow limet-change.js, ruoli/agenti, runner, asset ECC portati + ecc-ledger.json
├─ ui/             dashboard web React + TypeScript (viste: Portfolio, LIMET, HELIOS, CEREBRO, criticità/licenze)
├─ api/            backend sottile loopback: read model dai file/stato/CEREBRO, azioni solo da allowlist
├─ compat/         shim dei vecchi punti d'ingresso (C19-A)
└─ .github/        CI a matrice windows/ubuntu/macos, scansione segreti
```
Confini **[P]**: `agents/` consuma `cerebro/` solo via MCP/CLI pubblici; `cerebro/` non conosce `agents/`; `limet/` non dipende dal codice. Configurazione e segreti fuori dal repo (C20-A). Una modifica a LIMET o CEREBRO è un PR nello stesso repository, con la suite di parità e la CI di tutti i moduli (C22-C).
Ruolo di CEREBRO: provider di contesto read-only per i ruoli ammessi via MCP `limet_search` (analyst, validator, risk-assessor, developer, reviewer; il tester solo documenti e mai Graphify, per non vedere il codice del developer); sostituisce la memoria ECC (C08); nessun ruolo in orchestrazione, stato, osservabilità (**[F]**: non ha tali funzioni). **Base di conoscenza (Q9, deciso 2026-10-29):** `CRB_<slug>` con i documenti del progetto (indicizzati dall'utente) e `HLS_*` con asset ECC e matrice catena (creata solo in setup con autorizzazione); gli agenti leggono soltanto, senza Qdrant/Ollama proseguono senza contesto e lo segnalano. Dettaglio in `HELIOS_ANALISI_PER_LIMET.md` §3.8. Prerequisiti: Qdrant :6333 e Ollama **[F]** attivi (verificati), `CRB_limet` assente, correzione del percorso in `CLAUDE.md`. Nomi collection `CRB_<slug>` invariati.

## 7. Interfacce e contratti **[P]**
- **Manifest asset** (`ecc-ledger.json`): path, hash SHA-256, licenza, origine, commit `25278e7`, ruolo destinatario, esito test agnosticità.
- **Contratto agente→runner:** richiesta dichiarativa `{operazione ∈ allowlist, workspace, parametri}`; risposta `{esito, hash_log, artefatti}`.
- **Contratto ruolo:** frontmatter con `tools:` minimi (mai Bash), modello, file di output.
- **Portabilità:** percorsi relativi, separatori normalizzati, fine riga LF, nessun comando di shell nel core (C13).

## 8. Dashboard web unica (React) **[P]**
Una sola interfaccia per gestire HELIOS, LIMET e CEREBRO (sostituisce il launcher :8799 tramite shim, C19). Nessuna parte è implementata né verificata **[N]**.

| Vista | Contenuto | Fonte dati (C27) |
|---|---|---|
| Portfolio | pratiche/progetti, stato, gate in attesa, costi | artefatti LIMET + stato workflow |
| LIMET | ciclo fasi 0–7, template, documenti, checklist | file del modulo `limet/` |
| HELIOS | DAG dei workflow (ruoli, stati, retry), gate, log del runner, metriche R25–R27, ledger asset ECC/licenze | event log e artefatti |
| CEREBRO | collection `CRB_*`, stato Qdrant/Ollama, ingest, playground di ricerca (read-only) | API CEREBRO |
| Criticità | catalogo C01–C28, opzione scelta, stato | `05_roadmap_e_rischi.md` / dati derivati |
| Permessi agenti (C02) | policy per agente, permesso effettivo, audit, rollback; pavimento hard-deny non modificabile | file di policy versionato |

Grafica avanzata **[P]**: grafo interattivo dei workflow, timeline degli stati, heatmap dei costi/esiti, grafici di metriche, tema chiaro/scuro; implementata in SVG/Canvas con fallback (C26). Sicurezza: loopback + token + allowlist (C23); i gate sono approvabili solo da sessione umana e la UI non esegue build/test/git (C24); dipendenze governate (C25). Stack candidato **[I]**: React + TypeScript + bundler moderno; librerie grafiche permissive da selezionare in Fase 8b. Funziona su Windows e Unix (C13).

## 9. Esempio di workflow **[P]**
1. Utente apre pratica → LIMET crea stato `analisi`.
2. analyst (opus) legge CEREBRO + rules di linguaggio → PLAN (artefatto LIMET). Può consultare in sola lettura i planner ECC: le proposte tecniche le integra; quelle che toccano i requisiti si fermano in «fermati e chiedi» e decide il SUPERVISORE (C06).
3. Gate umano approva PLAN.
4. developer×N in worktree scrivono codice; tester scrivono test; nessuna esecuzione.
5. Runner (Windows o Unix, stesso script) esegue build/test → log hash.
6. test-reader riassume; reviewer (≠ developer) applica lenti ECC portate (es. `ecc-java-review`).
7. validator → gate finale; commit/push solo su autorizzazione umana.
8. **Valutazione rischi post-task (C28) [P]:** nel prodotto HELIOS, a ogni task VERIFIED un agente `risk-assessor`; durante lo sviluppo di HELIOS con LIMET puro la stessa valutazione è fatta dall'LLM che ha sviluppato il task, prima di dichiararlo concluso. Valuta rischi, errori e possibili malfunzionamenti causati dal task (regressioni su altri moduli, casi limite, sicurezza, prestazioni, compatibilità, dati). Se ne trova, li segnala al SUPERVISORE in `ecosystem/risks/` e in `QUESTIONS.md`, ciascuno con evidenza e **almeno due opzioni risolutive** (pro/contro, raccomandazione); non corregge da solo. Un rischio alto blocca solo i task dipendenti finché il SUPERVISORE non decide.
