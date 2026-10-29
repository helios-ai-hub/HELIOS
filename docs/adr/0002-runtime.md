# ADR 0002 — Runtime e struttura dei test

- **Stato**: Approvato dal SUPERVISORE il 2026-10-29 20:24
- **Task**: H-103 (Fase 1)
- **Data**: 2026-10-29
- **Fonte**: `HELIOS_ANALISI_PER_LIMET.md` §3.1 (runtime), §5 Fase 1, §6, §8 Q4 e Q8 [F]; C13 (`05_roadmap_e_rischi.md`)
- **Nota**: formalizza decisioni già prese dal SUPERVISORE (2026-10-28). Non introduce requisiti nuovi.

## Contesto

HELIOS deve funzionare su Windows e Unix con la stessa suite (C13). Serve un runtime unico e portabile per il core, con una sola eccezione motivata.

## Decisione

1. **Core, runner e API in Python ≥ 3.11**, test con **pytest** (Q4, opzione A) [F: IN §3.1, §8 Q4].
2. **UI in TypeScript/React**, test con `npm --prefix ui test` (Q4) [F: IN §3.1]. Il framework di test della UI (es. Vitest/Jest) non è fissato in IN: **[N]**.
3. **Eccezione deliberata (Q8, opzione A)**: **JavaScript (Node ≥ 18)** solo per scheduler (`agents/plugin/scheduler/limet-scheduler.js`) e workflow `limet-change.js`, con test `node --test` [F: IN §3.1, §8 Q8, H-408].
4. **Confine tra i due linguaggi: il contratto JSON** (IN §3.3). Nessun altro scambio.
5. **Struttura e comandi dei test** (nomi proposti in IN §6, resi definitivi da questo ADR [I]):
   - Python, classe: `python -m pytest <file>::<Classe>`
   - Python, metodo: `python -m pytest <file>::<Classe>::<metodo>`
   - Scheduler: `node --test agents/plugin/scheduler/limet-scheduler.test.js` (metodo: `--test-name-pattern="<nome>"`)
   - UI: `npm --prefix ui test -- <file> -t "<nome>"`
   - T-8b01…T-8b05 e T-8b08 sotto `tests/api/`.
6. Il codice non usa API esclusive di una piattaforma; i percorsi si gestiscono con `pathlib` (C13) [I].

## Alternative scartate

- Tutto in JavaScript/TypeScript o tutto in Python: scartate dal SUPERVISORE (Q4/Q8). Il plugin Claude Code e ALA R4/R23 richiedono JS per scheduler e workflow.

## Conseguenze

- Chiude Q-11 (nomi dei test) per quanto definibile; i nomi esatti dei file nascono con il codice di ogni task e vanno verificati in H-901 e nel TEST_PLAN.
- Due toolchain da installare nel runner (Python e Node), entrambe su Windows e Unix.

## Punti [N]

- Framework di test della UI e versione minima di Node/npm oltre a «Node ≥ 18».
- Gestore dei pacchetti Python (pip/uv/poetry) non fissato in IN.

## Storico

| Data | Modifica |
|------|----------|
| 2026-10-29 | Creazione (Proposto) |
