# Graph Report - HELIOS  (2026-10-29)

## Corpus Check
- cluster-only mode — file stats not available

## Summary
- 200 nodes · 188 edges · 19 communities (18 shown, 1 thin omitted)
- Extraction: 100% EXTRACTED · 0% INFERRED · 0% AMBIGUOUS
- Token cost: 0 input · 0 output

## Graph Freshness
- Built from commit: `0138b69d`
- Run `git rev-parse HEAD` and compare to check if the graph is stale.
- Run `graphify update .` after code changes (no API cost).

## Community Hubs (Navigation)
- 2. Catalogo criticità e opzioni risolutive
- HELIOS — Analisi e piano di implementazione (input per LIMET)
- 01 — Analisi di ECC (affaan-m/ECC)
- Analisi — Ecosistema multi-agente `limet-agents` (Claude Code + LIMET)
- 00 — HELIOS: studio di fattibilità («un ECC con agenti, guidato da LIMET»)
- 04 — Architettura HELIOS
- 03 — Matrice di confronto
- HELIOS in parole semplici
- 02 — Analisi di limet-agents
- 4. Requisiti candidati (da trasformare in SPEC con scenari Dato/Quando/Allora)
- 10. Stato di esecuzione — aggiornato il 2026-10-29 (riallineamento a fine sessione)
- 3. Architettura target
- 5. Backlog di task atomici
- 05 — Roadmap, rischi e criticità
- 13. Skill e componenti esistenti riusabili
- 7. Governance
- copilot-instructions.md
- Appendice — Registro completo skill e agenti ECC @25278e7
- README.md

## God Nodes (most connected - your core abstractions)
1. `2. Catalogo criticità e opzioni risolutive` - 31 edges
2. `Analisi — Ecosistema multi-agente `limet-agents` (Claude Code + LIMET)` - 17 edges
3. `01 — Analisi di ECC (affaan-m/ECC)` - 13 edges
4. `HELIOS — Analisi e piano di implementazione (input per LIMET)` - 12 edges
5. `00 — HELIOS: studio di fattibilità («un ECC con agenti, guidato da LIMET»)` - 11 edges
6. `04 — Architettura HELIOS` - 10 edges
7. `10. Stato di esecuzione — aggiornato il 2026-10-29 (riallineamento a fine sessione)` - 9 edges
8. `3. Architettura target` - 9 edges
9. `5. Backlog di task atomici` - 9 edges
10. `03 — Matrice di confronto` - 9 edges

## Surprising Connections (you probably didn't know these)
- None detected - all connections are within the same source files.

## Communities (19 total, 1 thin omitted)

### Community 0 - "2. Catalogo criticità e opzioni risolutive"
Cohesion: 0.06
Nodes (31): 2. Catalogo criticità e opzioni risolutive, C01 — Eccezione allo scope «niente framework di terzi» (ALA §2) e a FO1, C02 — Agenti ECC che eseguono build/test/commit (viola LIMET §1.3 e policy «mai build/test»), C03 — Rules `common/` sempre caricate (TDD 80%, commit, uso automatico di planner), C04 — Hook bloccanti e invasivi (GateGuard, config-protection, commit-quality), C05 — Mutazione di `~/.claude`, egress da transcript, contesto iniettato a SessionStart, C06 — Sovrapposizione spec-driven e collisione di nomi (`/plan`, `planner`, `code-reviewer`), C07 — Licenze eterogenee (CC-BY-SA, Apache senza NOTICE, «adapted from Wirasm», taste, branding) (+23 more)

### Community 1 - "HELIOS — Analisi e piano di implementazione (input per LIMET)"
Cohesion: 0.12
Nodes (16): 0. Leggimi prima: HELIOS in parole semplici, 1.1 Cosa si costruisce **[P]**, 1.2 Cosa è escluso, 1.3 Installazioni esistenti **[F]**, 1.4 Decisioni vincolanti e conseguenze implementative, 1. Sintesi e perimetro, 2.1 Cosa dicono (mappa fonti → uso), 2.2 Stato di fatto **[F]** (+8 more)

### Community 2 - "01 — Analisi di ECC (affaan-m/ECC)"
Cohesion: 0.12
Nodes (15): 01 — Analisi di ECC (affaan-m/ECC), 0. Come leggere questo documento (limiti dell'evidenza), 10. Punti di estensione dichiarati [F], 11. Elementi riutilizzabili in HELIOS (sintesi), 1. Che cos'è ECC, 2.1 Strati, 2.2 Dipendenze (core vs opzionali), 2. Architettura (+7 more)

### Community 3 - "Analisi — Ecosistema multi-agente `limet-agents` (Claude Code + LIMET)"
Cohesion: 0.12
Nodes (15): 0. Istruzioni a LIMET (come usare questa analisi), 10. Domande contestuali aperte (da porre all'utente, non assumere), 11. Rischi principali (dalla fonte, integrati), 12. Scomposizione suggerita (indicativa, per la TASK_LIST), 14. Criteri di completamento dell'infrastruttura, 1. Contesto e obiettivo, 2. Perimetro, 3. Attori (+7 more)

### Community 4 - "00 — HELIOS: studio di fattibilità («un ECC con agenti, guidato da LIMET»)"
Cohesion: 0.17
Nodes (11): 00 — HELIOS: studio di fattibilità («un ECC con agenti, guidato da LIMET»), 10. Legenda, 1. Sintesi esecutiva, 2. Verdetto **[P]**, 3. Raccomandazione architetturale **[P]**, 4. Principale evidenza **[F]**, 5. Criticità e risoluzione al 100%, 6. Effort e roadmap **[I]** (+3 more)

### Community 5 - "04 — Architettura HELIOS"
Cohesion: 0.18
Nodes (10): 04 — Architettura HELIOS, 1. Principi **[P]**, 2. Architettura target **[P]**, 3. Responsabilità, 4. Hook, skill, artefatti, 5. Opzioni architetturali valutate, 6. Ruolo di CEREBRO e struttura del progetto unico **[P]**, 7. Interfacce e contratti **[P]** (+2 more)

### Community 6 - "03 — Matrice di confronto"
Cohesion: 0.20
Nodes (9): 03 — Matrice di confronto, 1. Agenti, 2. Skill (per famiglia; tabella per-skill in `appendici/`), 3. Workflow, 4. Hook, 5. Comandi, configurazioni, dipendenze, 6. Conflitti [F salvo diversa indicazione], 7. Gap (+1 more)

### Community 7 - "HELIOS in parole semplici"
Cohesion: 0.20
Nodes (9): 1. Di che cosa stiamo parlando, 2. Possiamo farlo?, 3. Perché conviene, 4. Perché non prendere tutto, 5. Che cosa possiamo portare da ECC, 6. Come lo facciamo (in breve), 7. Impegno e rischi, 8. Cosa serve da te, SUPERVISORE (+1 more)

### Community 8 - "02 — Analisi di limet-agents"
Cohesion: 0.22
Nodes (8): 02 — Analisi di limet-agents, 0. Stato di fatto, 1. LIMET (base operativa) [F], 2. Requisiti e ruoli del design (ALA), 3. Punti di forza [I], 4. Lacune [I], 5. Vincoli, 6. Compatibilità con ECC

### Community 9 - "4. Requisiti candidati (da trasformare in SPEC con scenari Dato/Quando/Allora)"
Cohesion: 0.22
Nodes (9): 4. Requisiti candidati (da trasformare in SPEC con scenari Dato/Quando/Allora), Esecuzione e orchestrazione, Esecuzione test (runner), Gate e validazione, Installazione e distribuzione, Osservabilità, metriche e report HTML SPA, Requisiti non funzionali (NFR), Stato, report, sicurezza (+1 more)

### Community 10 - "10. Stato di esecuzione — aggiornato il 2026-10-29 (riallineamento a fine sessione)"
Cohesion: 0.22
Nodes (9): 10.1 Stato delle fasi (§4), 10.2 Stato dei task H-xxx (§5), 10.3 Esito dei test T-xxx (§6), 10.4 Decisioni nuove o modificate (ID progressivi dopo C28 e Q9), 10.5 Scostamenti dal progetto originale (con motivo), 10.6 Rischi emersi (valutazioni C28) e trattamento, 10.7 Esito del gate go/no-go del PoC, 10.8 Elementi aperti o rinviati (+1 more)

### Community 11 - "3. Architettura target"
Cohesion: 0.22
Nodes (9): 3.1 Moduli e confini **[P]** (`..\04` §6, C22), 3.2 Runtime **[P]**, 3.3 API pubbliche **[P]** (`..\04` §7), 3.4 Flusso dati (C27) **[P]**, 3.5 Workflow **[P]** (`..\04` §9), 3.6 Forma di consegna: plugin Claude Code `limet-agents` **[F/P]**, 3.7 Dashboard **[P]** (`..\04` §8), 3.8 Base di conoscenza per gli agenti (Q9) **[P]** (+1 more)

### Community 12 - "5. Backlog di task atomici"
Cohesion: 0.22
Nodes (9): 5. Backlog di task atomici, Fase 1, Fase 2, Fase 3, Fase 4 (PoC), Fase 5–7, Fase 8, Fase 8b (+1 more)

### Community 13 - "05 — Roadmap, rischi e criticità"
Cohesion: 0.25
Nodes (7): 05 — Roadmap, rischi e criticità, 1. Regola di questo documento: ogni criticità ha opzioni risolutive al 100%, 3. Decisioni richieste al SUPERVISORE, 4. Roadmap a 12 fasi (effort **[I]**, persona-giorni), 5. Criteri go/no-go del PoC (misurabili), 6. Licenze da inoltrare al legale (nessun parere definitivo), 7. Prerequisiti PoC e decisioni aperte

### Community 14 - "13. Skill e componenti esistenti riusabili"
Cohesion: 0.29
Nodes (7): 13.1 Ammesse (LIMET-agnostiche), 13.2 Escluse, 13.3 Server MCP PostgreSQL (accesso DB in sola lettura), 13.4 Resta da scrivere (nessun equivalente trovato nella fonte), 13.5 Regole di integrazione, 13.6 Requisiti di progettazione della dashboard metriche, 13. Skill e componenti esistenti riusabili

### Community 15 - "7. Governance"
Cohesion: 0.33
Nodes (6): 7.1 Licenze (C07) **[F]**, 7.2 Pin ECC (C12) e AgentShield (C10), 7.3 Sicurezza UI (C23–C25), 7.4 CI (C13, C22), 7.5 Regole operative per chi implementa, 7. Governance

### Community 16 - "copilot-instructions.md"
Cohesion: 0.40
Nodes (4): Documentation to maintain · Documentazione da mantenere, LIMET — metodo di lavoro spec-driven per questo progetto, Project context tools (CEREBRO RAG + Graphify) · Strumenti di contesto del progetto, Project overview · Panoramica del progetto

### Community 17 - "Appendice — Registro completo skill e agenti ECC @25278e7"
Cohesion: 0.50
Nodes (3): Agenti, Appendice — Registro completo skill e agenti ECC @25278e7, Skills

## Knowledge Gaps
- **168 isolated node(s):** `C01 — Eccezione allo scope «niente framework di terzi» (ALA §2) e a FO1`, `C02 — Agenti ECC che eseguono build/test/commit (viola LIMET §1.3 e policy «mai build/test»)`, `C03 — Rules `common/` sempre caricate (TDD 80%, commit, uso automatico di planner)`, `C04 — Hook bloccanti e invasivi (GateGuard, config-protection, commit-quality)`, `C05 — Mutazione di `~/.claude`, egress da transcript, contesto iniettato a SessionStart` (+163 more)
  These have ≤1 connection - possible missing edges or undocumented components.
- **1 thin communities (<3 nodes) omitted from report** — run `graphify query` to explore isolated nodes.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `HELIOS — Analisi e piano di implementazione (input per LIMET)` connect `HELIOS — Analisi e piano di implementazione (input per LIMET)` to `10. Stato di esecuzione — aggiornato il 2026-10-29 (riallineamento a fine sessione)`, `3. Architettura target`, `5. Backlog di task atomici`, `7. Governance`?**
  _High betweenness centrality (0.052) - this node is a cross-community bridge._
- **Why does `2. Catalogo criticità e opzioni risolutive` connect `2. Catalogo criticità e opzioni risolutive` to `05 — Roadmap, rischi e criticità`?**
  _High betweenness centrality (0.034) - this node is a cross-community bridge._
- **Why does `Analisi — Ecosistema multi-agente `limet-agents` (Claude Code + LIMET)` connect `Analisi — Ecosistema multi-agente `limet-agents` (Claude Code + LIMET)` to `4. Requisiti candidati (da trasformare in SPEC con scenari Dato/Quando/Allora)`, `13. Skill e componenti esistenti riusabili`?**
  _High betweenness centrality (0.021) - this node is a cross-community bridge._
- **What connects `C01 — Eccezione allo scope «niente framework di terzi» (ALA §2) e a FO1`, `C02 — Agenti ECC che eseguono build/test/commit (viola LIMET §1.3 e policy «mai build/test»)`, `C03 — Rules `common/` sempre caricate (TDD 80%, commit, uso automatico di planner)` to the rest of the system?**
  _168 weakly-connected nodes found - possible documentation gaps or missing edges._
- **Should `2. Catalogo criticità e opzioni risolutive` be split into smaller, more focused modules?**
  _Cohesion score 0.06451612903225806 - nodes in this community are weakly interconnected._
- **Should `HELIOS — Analisi e piano di implementazione (input per LIMET)` be split into smaller, more focused modules?**
  _Cohesion score 0.11764705882352941 - nodes in this community are weakly interconnected._
- **Should `01 — Analisi di ECC (affaan-m/ECC)` be split into smaller, more focused modules?**
  _Cohesion score 0.125 - nodes in this community are weakly interconnected._