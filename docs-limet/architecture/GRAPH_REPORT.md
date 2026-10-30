# Graph Report - HELIOS  (2026-10-30)

## Corpus Check
- cluster-only mode — file stats not available

## Summary
- 26 nodes · 23 edges · 3 communities
- Extraction: 100% EXTRACTED · 0% INFERRED · 0% AMBIGUOUS
- Token cost: 0 input · 0 output

## Graph Freshness
- Built from commit: `48d53345`
- Run `git rev-parse HEAD` and compare to check if the graph is stale.
- Run `graphify update .` after code changes (no API cost).

## Community Hubs (Navigation)
- Analisi — Progetto «Gestione biblioteca» (nuovo progetto da zero)
- HELIOS
- copilot-instructions.md

## God Nodes (most connected - your core abstractions)
1. `Analisi — Progetto «Gestione biblioteca» (nuovo progetto da zero)` - 13 edges
2. `HELIOS` - 6 edges
3. `0. Istruzioni a LIMET` - 1 edges
4. `10. Domande contestuali aperte (da porre all'utente, non assumere)` - 1 edges
5. `11. Rischi` - 1 edges
6. `1. Obiettivo` - 1 edges
7. `2. Perimetro` - 1 edges
8. `3. Decisioni di stack (già prese)` - 1 edges
9. `4. Requisiti candidati (da trasformare in SPEC)` - 1 edges
10. `5. Database su Docker — vincoli e implicazioni` - 1 edges

## Surprising Connections (you probably didn't know these)
- None detected - all connections are within the same source files.

## Communities (3 total, 0 thin omitted)

### Community 0 - "Analisi — Progetto «Gestione biblioteca» (nuovo progetto da zero)"
Cohesion: 0.14
Nodes (13): 0. Istruzioni a LIMET, 10. Domande contestuali aperte (da porre all'utente, non assumere), 11. Rischi, 1. Obiettivo, 2. Perimetro, 3. Decisioni di stack (già prese), 4. Requisiti candidati (da trasformare in SPEC), 5. Database su Docker — vincoli e implicazioni (+5 more)

### Community 1 - "HELIOS"
Cohesion: 0.29
Nodes (6): Che cos'è, Come si procede, HELIOS, Per iniziare, Principi, Struttura del repository

### Community 2 - "copilot-instructions.md"
Cohesion: 0.40
Nodes (4): Documentation to maintain · Documentazione da mantenere, LIMET — metodo di lavoro spec-driven per questo progetto, Project context tools (CEREBRO RAG + Graphify) · Strumenti di contesto del progetto, Project overview · Panoramica del progetto

## Knowledge Gaps
- **21 isolated node(s):** `0. Istruzioni a LIMET`, `10. Domande contestuali aperte (da porre all'utente, non assumere)`, `11. Rischi`, `1. Obiettivo`, `2. Perimetro` (+16 more)
  These have ≤1 connection - possible missing edges or undocumented components.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **What connects `0. Istruzioni a LIMET`, `10. Domande contestuali aperte (da porre all'utente, non assumere)`, `11. Rischi` to the rest of the system?**
  _21 weakly-connected nodes found - possible documentation gaps or missing edges._
- **Should `Analisi — Progetto «Gestione biblioteca» (nuovo progetto da zero)` be split into smaller, more focused modules?**
  _Cohesion score 0.14285714285714285 - nodes in this community are weakly interconnected._