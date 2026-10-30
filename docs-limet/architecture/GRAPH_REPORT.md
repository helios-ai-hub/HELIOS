# Graph Report - HELIOS  (2026-10-30)

## Corpus Check
- cluster-only mode — file stats not available

## Summary
- 101 nodes · 96 edges · 9 communities
- Extraction: 100% EXTRACTED · 0% INFERRED · 0% AMBIGUOUS
- Token cost: 0 input · 0 output

## Graph Freshness
- Built from commit: `45938de7`
- Run `git rev-parse HEAD` and compare to check if the graph is stale.
- Run `graphify update .` after code changes (no API cost).

## Community Hubs (Navigation)
- Analisi — Progetto «Gestione biblioteca» (nuovo progetto da zero)
- HELIOS
- copilot-instructions.md
- SCIBETTA
- NARO
- 01 — Setup dopo il clone
- HELIOS — Guida ai branch per NARO e SCIBETTA
- AGENTS.md
- CLAUDE.md

## God Nodes (most connected - your core abstractions)
1. `SCIBETTA` - 20 edges
2. `NARO` - 19 edges
3. `Analisi — Progetto «Gestione biblioteca» (nuovo progetto da zero)` - 13 edges
4. `01 — Setup dopo il clone` - 11 edges
5. `HELIOS — Guida ai branch per NARO e SCIBETTA` - 7 edges
6. `HELIOS` - 6 edges
7. `Guide operative di HELIOS` - 3 edges
8. `4. Sequenza di lavoro` - 3 edges
9. `0. Istruzioni a LIMET` - 1 edges
10. `10. Domande contestuali aperte (da porre all'utente, non assumere)` - 1 edges

## Surprising Connections (you probably didn't know these)
- None detected - all connections are within the same source files.

## Communities (9 total, 0 thin omitted)

### Community 0 - "Analisi — Progetto «Gestione biblioteca» (nuovo progetto da zero)"
Cohesion: 0.14
Nodes (13): 0. Istruzioni a LIMET, 10. Domande contestuali aperte (da porre all'utente, non assumere), 11. Rischi, 1. Obiettivo, 2. Perimetro, 3. Decisioni di stack (già prese), 4. Requisiti candidati (da trasformare in SPEC), 5. Database su Docker — vincoli e implicazioni (+5 more)

### Community 1 - "HELIOS"
Cohesion: 0.15
Nodes (9): Convenzioni usate nelle guide, Guide operative di HELIOS, Regola che vale per tutte le guide, Che cos'è, Come si procede, HELIOS, Per iniziare, Principi (+1 more)

### Community 2 - "copilot-instructions.md"
Cohesion: 0.40
Nodes (4): Documentation to maintain · Documentazione da mantenere, LIMET — metodo di lavoro spec-driven per questo progetto, Project context tools (CEREBRO RAG + Graphify) · Strumenti di contesto del progetto, Project overview · Panoramica del progetto

### Community 3 - "SCIBETTA"
Cohesion: 0.10
Nodes (20): 6. Se qualcosa non torna, 7. Punti da decidere con il SUPERVISORE, PK-01 · Contratti JSON, PK-05 · CI a matrice (3 OS), PK-05b · Segreti fuori dal repo, PK-06 · Shim compat e test confini, PK-07b · Configurazione locale non versionata, PK-08 · Runner con allowlist e policy permessi (+12 more)

### Community 4 - "NARO"
Cohesion: 0.11
Nodes (19): NARO, PK-02 · Inventario e ledger ECC, PK-03 · Revisione asset e matrice catena, PK-07 · Correzione percorso CEREBRO in CLAUDE.md, PK-10 · Gate umano meccanico sul PLAN, PK-11 · Prova del plugin Claude Code (V13/V14), PK-13 · Rapporto go/no-go (compila NARO, decide il SUPERVISORE), PK-15 · Scheduler e regole comuni dei ruoli (+11 more)

### Community 5 - "01 — Setup dopo il clone"
Cohesion: 0.18
Nodes (11): 01 — Setup dopo il clone, Ci sei. Cosa leggere, in quest'ordine, Le cose da non fare mai, Passo 0 — Prerequisiti, Passo 1 — Clona il repository, Passo 2 — ✅ Installa il framework LIMET in locale, Passo 3 — Cosa va adattato a mano, Passo 4 — Registra HELIOS nel tuo CEREBRO (+3 more)

### Community 6 - "HELIOS — Guida ai branch per NARO e SCIBETTA"
Cohesion: 0.22
Nodes (9): 0. Le 7 regole (leggile una volta, poi non sbagli), 1. Setup una tantum, 2. Come trovo il mio pacchetto nell'Excel (vale per tutti i branch), 3. Flusso standard di ogni pacchetto, 4. Sequenza di lavoro, 5. Pacchetti, uno per uno, HELIOS — Guida ai branch per NARO e SCIBETTA, NARO (+1 more)

### Community 7 - "AGENTS.md"
Cohesion: 0.40
Nodes (4): Documentation to maintain · Documentazione da mantenere, LIMET — metodo di lavoro spec-driven per questo progetto, Project context tools (CEREBRO RAG + Graphify) · Strumenti di contesto del progetto, Project overview · Panoramica del progetto

### Community 8 - "CLAUDE.md"
Cohesion: 0.40
Nodes (4): Documentation to maintain · Documentazione da mantenere, LIMET — metodo di lavoro spec-driven per questo progetto, Project context tools (CEREBRO RAG + Graphify) · Strumenti di contesto del progetto, Project overview · Panoramica del progetto

## Knowledge Gaps
- **85 isolated node(s):** `0. Istruzioni a LIMET`, `10. Domande contestuali aperte (da porre all'utente, non assumere)`, `11. Rischi`, `1. Obiettivo`, `2. Perimetro` (+80 more)
  These have ≤1 connection - possible missing edges or undocumented components.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `SCIBETTA` connect `SCIBETTA` to `HELIOS`?**
  _High betweenness centrality (0.234) - this node is a cross-community bridge._
- **Why does `NARO` connect `NARO` to `HELIOS`?**
  _High betweenness centrality (0.224) - this node is a cross-community bridge._
- **Why does `01 — Setup dopo il clone` connect `01 — Setup dopo il clone` to `HELIOS`?**
  _High betweenness centrality (0.132) - this node is a cross-community bridge._
- **What connects `0. Istruzioni a LIMET`, `10. Domande contestuali aperte (da porre all'utente, non assumere)`, `11. Rischi` to the rest of the system?**
  _85 weakly-connected nodes found - possible documentation gaps or missing edges._
- **Should `Analisi — Progetto «Gestione biblioteca» (nuovo progetto da zero)` be split into smaller, more focused modules?**
  _Cohesion score 0.14285714285714285 - nodes in this community are weakly interconnected._
- **Should `SCIBETTA` be split into smaller, more focused modules?**
  _Cohesion score 0.1 - nodes in this community are weakly interconnected._
- **Should `NARO` be split into smaller, more focused modules?**
  _Cohesion score 0.10526315789473684 - nodes in this community are weakly interconnected._