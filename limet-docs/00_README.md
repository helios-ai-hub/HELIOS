# 00 — HELIOS: studio di fattibilità («un ECC con agenti, guidato da LIMET»)

> Data: 2026-10-28 · lingua: italiano · sola lettura delle fonti; nessun file esistente modificato.
> **Aggiornato il 2026-10-29:** lo stato di esecuzione reale di HELIOS (fasi, task, test, decisioni D29–D37/Q10–Q11, rischi C28, gate PoC) è in `HELIOS_ANALISI_PER_LIMET.md` §10. Sintesi: pianificazione LIMET completata e approvata; H-101 scritto in attesa di approvazione; nessun codice, nessun test eseguito, gate PoC non raggiunto. La SPA `HELIOS_STUDIO.html` non è in questa cartella e non è stata rigenerata.

## 1. Sintesi esecutiva
HELIOS porta in `limet-agents` ciò che di ECC (`affaan-m/ECC`, v2.2.3, commit `25278e7`, MIT) è riutilizzabile, sostituendo il suo flusso PRD→plan→TDD con i workflow LIMET, con Claude Code che esegue tutti gli agenti. **Richiesta del SUPERVISORE:** LIMET e CEREBRO confluiscono in HELIOS come moduli di un unico progetto, così ogni loro modifica si gestisce in un solo repository (`04` §6, criticità C19–C22), con un'unica dashboard web React per gestire tutti e tre (`04` §8, C23–C27).

## 2. Verdetto **[P]**
**FATTIBILE CON CONDIZIONI.** Non è fattibile «mantenere tutte le funzionalità ECC»: hook, memoria, flusso `/plan`, esecuzione di build/test/commit di ECC contraddicono LIMET (§1.3) e vanno esclusi o sostituiti, con motivazione in `03`. Condizioni: ADR di eccezione allo scope (C01), PoC di limet-agents (che oggi **non esiste**, C14), verifica legale delle licenze (C07), **funzionamento su Windows e Unix** (C13).

## 3. Raccomandazione architetturale **[P]**
Opzione 5 *ibrida*: porting selettivo di asset testuali ECC pinnati (rules per linguaggio, lenti di review, skill pure), LIMET unico orchestratore, runner unico esecutore, CEREBRO unico provider di contesto. Piano B: wrapper senza copia (profilo `minimal`, hook off).

## 4. Principale evidenza **[F]**
71 agenti, 311 skill, 97 comandi, 27 hook, 122 rules (ricalcolati dal clone); nessun flusso spec-driven dedicato, solo prosa di conferma; GateGuard ~+45% turni; ECC memory solo lessicale; `limet-agents` assente. Dettagli e fonti in `01`, `02`.

## 5. Criticità e risoluzione al 100%
28 criticità (C01–C28) in `05` §2; per ciascuna ≥2 opzioni che la eliminano completamente, con prezzo e verifica; scelta ★ raccomandata. Decisioni da prendere: `05` §3.

## 6. Effort e roadmap **[I]**
69–107 persona-giorni, 12 fasi + fase 8b dashboard React (la fase 3 include il consolidamento dei tre moduli): discovery, inventario, contratti, PoC, porting skill, agenti, sostituzione spec-driven, hook/comandi, dashboard, E2E (matrice Windows+Linux+macOS), hardening, documentazione, rilascio progressivo. Go/no-go misurabili in `05` §5.

## 7. Indice
`01_analisi_ECC.md` · `02_analisi_limet-agents.md` · `03_matrice_confronto.md` · `04_architettura_HELIOS.md` · `05_roadmap_e_rischi.md` · `HELIOS_STUDIO.html` (SPA offline).

## 8. Fonti analizzate
- Vincolanti: `FEASIBILITY_O1_CLAUDE_CODE.html`, `ANALISI_limet-agents_con_workflow_claude.md` (cartella LIMET).
- Locali: `C:\Progetti\VSC\LIMET\`, `C:\software\cerebro\` (letti; `projects.json` non riportato). `C:\software\limet-agents\` **non esiste**.
- Web: `https://github.com/affaan-m/ECC` (clone shallow, commit `25278e7`), `https://ecc.tools` e pagine collegate, `registry.npmjs.org` (JSON). Non raggiungibili: pagine npmjs (403), `llms.txt` (404).

## 9. Limiti **[N]**
Lettura non integrale di skill e agenti; cronologia git non analizzata; stato Qdrant/Ollama e `CRB_limet` non verificati; licenze di dipendenze dev e AgentShield; termini ECC Pro; statistiche del sito.

## 10. Legenda
**[F]** fatto verificato (con fonte) · **[I]** ipotesi · **[P]** proposta · **[N]** non verificato.
