# HELIOS — Costituzione (non negoziabili)

- **Stato**: Approvata dal SUPERVISORE il 2026-10-29 20:18
- **Task**: H-101…H-102 (Fase 1)
- **Fonte**: `HELIOS_ANALISI_PER_LIMET.md` §2.3 [F] (n. 1–6), n. 7 = C28 [P]; ADR `docs/adr/0001-helios.md`
- **Nota**: questo documento riporta i non negoziabili in forma verificabile. Non introduce requisiti nuovi. Ogni deviazione la decide solo il SUPERVISORE.

| # | Regola | Come si verifica | Fonte |
|---|--------|------------------|-------|
| 1 | Nessuna build, test, commit, push o merge autonomi. Build e test li esegue solo il runner (su allowlist) o l'utente; commit e push solo l'utente. | Il runner usa un'allowlist; nessun agente ha `Bash` libero; gli esiti dei test sono riportati dall'utente. | IN §2.3 n.1 |
| 2 | Gate umani su PLAN e validazione finale. Si approvano solo da sessione umana. | Il gate rifiuta approvazioni non provenienti da sessione umana (C24). | IN §2.3 n.2, C24 |
| 3 | Funziona su Windows **e** Unix con la stessa suite di test. | La stessa suite passa su entrambe le piattaforme (C13). | IN §2.3 n.3, C13 |
| 4 | Nessun invio di contenuto a un LLM, salvo automazione approvata e registrata. | Ogni invio è registrato (log/ledger); nessun invio non registrato. | IN §2.3 n.4, C05 |
| 5 | Nessun segreto nel repository; nessuna scrittura fuori dal workspace; nessuna modifica a `~/.claude`. | Scansione segreti; controllo dei percorsi di scrittura; HOME isolato nelle prove (Q-13). | IN §2.3 n.5 |
| 6 | Nessuna modifica a file preesistenti dei progetti esterni; nessuna sincronizzazione su COSMO_CEREBRO_DOCS. | Diff dei progetti esterni vuoto prima e dopo; nessuna scrittura su COSMO_CEREBRO_DOCS. | IN §2.3 n.6 |
| 7 | **Valutazione rischi dopo ogni task (C28).** Prima di dichiarare un task concluso, l'LLM valuta rischi, errori e malfunzionamenti dovuti a quel task. Se ne trova, li segnala al SUPERVISORE con evidenza (file/riga/test) e almeno 2 opzioni con pro/contro e raccomandazione, senza decidere né correggere da solo. Se non ne trova, elenca i controlli eseguiti. | L'esito è nella voce del task («Esito implementazione») e, se aperti, nelle domande contestuali. Nel prodotto: `risk-assessor` e `RISK-<task>.json` (H-612, T-611). Giudizio dell'LLM, non prova: non sostituisce i test. | IN §2.3 n.7, C28 |

## Regole di processo collegate (LIMET)

- Ogni ambiguità o requisito mancante va in `QUESTIONS.md` / domande contestuali; il SUPERVISORE decide. Le proposte dei planner ECC che toccano i requisiti non si integrano (IN §2.3 «Regola planner»).
- Nessun task è `done` senza strategia di verifica; l'ora si annota (`YYYY-MM-DD HH:MM`).
- Le decisioni C01–C28 e Q1–Q9 sono vincolanti.

## Storico

| Data | Modifica | Autore |
|------|----------|--------|
| 2026-10-29 | Creazione (bozza) | Analista LIMET |
