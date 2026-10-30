# HELIOS

**HELIOS** unisce tre mondi in un unico progetto: **«ECC con gli assistenti, guidato da LIMET»**, con una dashboard web per gestirli.

> Stato: **fase di progettazione**. Il repository contiene al momento solo documentazione; il codice parte dopo l'approvazione del piano e l'esito positivo della prova di fattibilità (PoC go/no-go).

## Che cos'è

| Componente | Ruolo |
|---|---|
| **LIMET** | Il metodo di lavoro: ordine delle attività, approvazioni umane, tracciabilità e documentazione. È il regista. |
| **CEREBRO** | La memoria di progetto: permette agli assistenti AI di consultare la documentazione indicizzata (RAG). |
| **ECC** | Raccolta esterna (licenza MIT) di assistenti AI, regole e automatismi per Claude Code. Se ne portano solo le parti utili e compatibili. |
| **Dashboard HELIOS** | Un'unica interfaccia web per gestire i tre mondi (prima in sola lettura). |

## Principi

- **LIMET guida, ECC esegue le parti testuali:** gli assistenti propongono e commentano; le decisioni restano all'umano.
- **Nessuna azione rischiosa automatica:** gli assistenti non lanciano build o test e non fanno commit o push da soli.
- **Si prendono da ECC solo le parti utili:** regole per linguaggio, revisori in sola lettura, skill testuali verificate. Pianificazione e memoria di ECC sono sostituite da LIMET e CEREBRO.
- **Windows e Unix:** tutto deve funzionare su entrambi.
- **Nessun segreto nel repository.**

## Come si procede

1. Decisioni e inventario (con registro delle licenze).
2. Unione di LIMET, CEREBRO e HELIOS in un solo progetto, senza rompere le installazioni esistenti.
3. **Prova di fattibilità (PoC)** con criteri chiari di successo e stop: decide GO / NO-GO.
4. Porting delle parti ECC scelte e sostituzione della pianificazione con LIMET.
5. Dashboard (React), poi test su più sistemi, hardening, documentazione e rilascio graduale.

## Struttura del repository

| Cartella | Contenuto |
|---|---|
| `docs/` | Costituzione del progetto e decisioni architetturali (`adr/`) |
| `docs-limet/` | Analisi di input e documentazione di progetto |
| `guides/` | **Guide operative passo-passo** (setup dopo il clone, ecc.) |
| `limet/` | Documenti LIMET di progetto: piani, task list, piano dei test, verifica E2E (`changes/`, `archive/`). Il resto del framework non è versionato: lo installa il setup |
| `log/` | Distribuzione dei task tra le risorse e guida ai branch |
| `test/` | Materiale del progetto pilota |

## Per iniziare

**Hai appena clonato il repository?** Parti da qui: [`guides/01-SETUP-DOPO-IL-CLONE.md`](guides/01-SETUP-DOPO-IL-CLONE.md).

Poi:

- Sintesi non tecnica: `docs-limet/analisi-input/06_sintesi_non_tecnica.md`
- Decisioni fondanti: `docs/CONSTITUTION.md` e `docs/adr/`
- Piano e task in corso: `limet/changes/0001-helios/`
- Come lavorare a branch e pull request: `log/HELIOS_GUIDA_BRANCH.md`
