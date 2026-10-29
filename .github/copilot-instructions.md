<!-- LIMET:START -->
## LIMET — metodo di lavoro spec-driven per questo progetto

Questo progetto segue il framework **LIMET** per bug fixing e sviluppo di nuove feature con
agenti AI. Documentazione locale in `limet/` (manuale: `limet/FRAMEWORK_MANUAL.md`,
checklist: `limet/ONBOARDING_CHECKLIST.md`, template: `limet/templates/`,
lavoro in corso: `limet/changes/`, lavoro concluso: `limet/archive/`).

Regole vincolanti (vedi il manuale per il dettaglio):
- Prima di modificare codice non banale, creare/consultare un documento di piano o proposta in
  `limet/changes/` usando `templates/PLAN_TEMPLATE.md` o, per modifiche ampie,
  `templates/CHANGE_PROPOSAL_TEMPLATE.md` + `templates/SPEC_TEMPLATE.md`.
- Ogni ambiguità va registrata come domanda contestuale (con risposta e data), mai assunta in
  silenzio.
- **Nessun task può essere marcato `done` senza una strategia di verifica**: unit test dedicati
  e/o test e2e approfonditi con istruzioni passo-passo, salvo motivazione esplicita di omissione.
  Quando un task passa a `done`, annotare anche l'ora (`YYYY-MM-DD HH:MM`).
- **Qualità dei test, non solo presenza**: prima di dichiarare completo uno unit test, esegui
  l'autorevisione (manuale §5.1) e riportane l'esito. Criterio di arresto: *quale singola
  alterazione plausibile del codice di produzione NON farebbe fallire nessun test?* Se una
  risposta esiste, manca un test. Verifica inoltre che ogni assert possa fallire per il motivo
  dichiarato, che il valore atteso non sia derivato dall'oggetto sotto test e che sia coperto il
  contratto del metodo (input assenti, vuoti, non validi), non solo il percorso felice.
- **Approvazione umana**: prima di implementare, chiedi all'utente l'approvazione del piano/proposta e registrala nel documento (chi ha approvato + data).
- **Tracciabilità e comunicazione**: ogni file creato/modificato va registrato subito nella sezione "Registro modifiche" del `TASK_LIST.md` (una riga per file: task, percorso, tipo, cosa è cambiato, perché, verifica collegata), non a fine feature; se il file vive in un altro modulo, il dettaglio sta nel registro di quel modulo e qui resta un rimando. A fine task comunica all'utente l'elenco delle modifiche (file, cosa è cambiato, perché).
- **MAI eseguire build o test**: non lanciare MAI build (`./mvnw package`) né test (`./mvnw test`) in autonomia — l'output riempie il contesto di rumore. Quando serve, dai all'utente il comando, chiedi di eseguirlo e di comunicare l'esito, e resta in standby finché non arriva.
- Non eseguire comandi con effetti persistenti (commit, push, migrazioni, deploy) senza
  autorizzazione esplicita dell'utente.
- A inizio sessione/feature, consultare `limet/ONBOARDING_CHECKLIST.md`.
- A modifica conclusa e verificata, spostare/archiviare i documenti in `limet/archive/`
  usando `templates/ARCHIVE_ENTRY_TEMPLATE.md`.
- Il re-index e gli aggiornamenti Graphify spettano all'utente, mai all'agente. Dopo modifiche a
  codice/documenti, ricordarlo periodicamente (al termine di un task o dell'archiviazione):
  Launcher -> Percorso progetto -> Re-index -> Incrementale -> clic Re-index.
- Se questo progetto fa parte di un workspace multi-progetto (più repository correlati o più
  moduli), verificare se esiste una cartella `limet-workspace/` nella cartella padre condivisa:
  in tal caso consultare `limet-workspace/MODULE_MAP.md` prima di modifiche che potrebbero
  toccare altri progetti, e usare `limet-workspace/templates/CROSS_PROJECT_CHANGE_TEMPLATE.md`
  per coordinare modifiche cross-progetto — vedi `FRAMEWORK_MANUAL.md` Appendice C.
- Per aggiornare questa cartella locale a una nuova versione di LIMET, rieseguire lo script di
  installazione (`scripts/limet.ps1 update` o `scripts/limet.sh update`) dal repository
  sorgente del framework.
<!-- LIMET:END -->

<!-- LIMET-CONTEXT:START -->
## Project context tools (CEREBRO RAG + Graphify) · Strumenti di contesto del progetto

**EN** — This project's documentation is indexed in Qdrant collection `CRB_helios`.
Its code is indexed in the Graphify knowledge graph.

Query the documentation (semantic RAG):
  python "C:\software\cerebro\scripts\query_qdrant.py" --project helios search "<query>" --limit 5

Query the code (knowledge graph):
  graphify query "<question>"   /   graphify explain <symbol>   /   graphify path <A> <B>

Rules:
- **RAG**: BEFORE answering any question about the project, call the `limet_search` MCP tool and ground your answer in its results. If the tool is unavailable, fall back to the `query_qdrant.py` command below.
- Search CEREBRO/Qdrant FIRST. If the document is not indexed (0 results or missing), read it
  LOCALLY from `docs-limet/` or `limet/`.
- Re-indexing and Graphify updates belong to the user, never the agent. Use local docs until indexed.
- **Code questions** (where is X implemented, who uses Y, how does flow Z work): FIRST use
  `graphify query` / `graphify explain` / `graphify path`; THEN confirm exact lines and usages
  with a targeted text search (grep). Do not start directly from grep.
- After code/document changes, periodically remind the user (at task completion or archiving):
  Launcher -> project path -> Re-index -> Incrementale -> click Re-index.
- If Qdrant, Ollama or Graphify are unreachable, say so and proceed with the local docs.

---

**IT** — La documentazione di questo progetto è indicizzata nella collection Qdrant `CRB_helios`.
Il codice è indicizzato nel knowledge graph Graphify.

Interroga la documentazione (RAG semantico):
  python "C:\software\cerebro\scripts\query_qdrant.py" --project helios search "<query>" --limit 5

Interroga il codice (knowledge graph):
  graphify query "<domanda>"   /   graphify explain <simbolo>   /   graphify path <A> <B>

Regole:
- **RAG**: PRIMA di rispondere a qualsiasi domanda sul progetto, chiama lo strumento MCP `limet_search` e fonda la risposta sui suoi risultati. Se lo strumento non è disponibile, usa il comando `query_qdrant.py` qui sotto.
- Cerca PRIMA in CEREBRO/Qdrant. Se il documento non è indicizzato (0 risultati o assente), leggilo
  LOCALMENTE da `docs-limet/` o `limet/`.
- Il re-index e gli aggiornamenti Graphify spettano all'utente, mai all'agente. Usa i doc locali
  finché non sono indicizzati.
- **Domande sul codice** (dov'è implementato X, chi usa Y, come funziona il flusso Z): PRIMA usa
  `graphify query` / `graphify explain` / `graphify path`; POI conferma righe e usi esatti con
  una ricerca testuale mirata (grep). Non partire direttamente da grep.
- Dopo modifiche a codice/documenti, ricordare periodicamente all'utente (al termine di un task
  o dell'archiviazione): Launcher -> Percorso progetto -> Re-index -> Incrementale -> clic Re-index.
- Se Qdrant, Ollama o Graphify non sono raggiungibili, segnalalo e procedi con i doc locali.
<!-- LIMET-CONTEXT:END -->

<!-- PROJECT:START -->
## Project overview · Panoramica del progetto

**EN** — Project `helios` — CEREBRO collection `CRB_helios`.

Complete local Markdown inventory under docs-limet/ (excluding _templates; not proof of indexing):
  - `docs-limet\analisi-input\00_README.md`
  - `docs-limet\analisi-input\01_analisi_ECC.md`
  - `docs-limet\analisi-input\02_analisi_limet-agents.md`
  - `docs-limet\analisi-input\03_matrice_confronto.md`
  - `docs-limet\analisi-input\04_architettura_HELIOS.md`
  - `docs-limet\analisi-input\05_roadmap_e_rischi.md`
  - `docs-limet\analisi-input\06_sintesi_non_tecnica.md`
  - `docs-limet\analisi-input\ANALISI_limet-agents_con_workflow_claude.md`
  - `docs-limet\analisi-input\HELIOS_ANALISI_PER_LIMET.md`
  - `docs-limet\analisi-input\REGISTRO_SKILLS_AGENTI_ECC.md`
  - `docs-limet\architecture\GRAPH_REPORT.md`

For indexed sources, consult CEREBRO's projects.json and query the collection above.

Code graph (Graphify): `graphify-out/` (report: `graphify-out/GRAPH_REPORT.md`).

---

**IT** — Progetto `helios` — collection CEREBRO `CRB_helios`.

Inventario locale completo dei Markdown in docs-limet/ (esclusi _templates; non attesta l'indicizzazione):
  - `docs-limet\analisi-input\00_README.md`
  - `docs-limet\analisi-input\01_analisi_ECC.md`
  - `docs-limet\analisi-input\02_analisi_limet-agents.md`
  - `docs-limet\analisi-input\03_matrice_confronto.md`
  - `docs-limet\analisi-input\04_architettura_HELIOS.md`
  - `docs-limet\analisi-input\05_roadmap_e_rischi.md`
  - `docs-limet\analisi-input\06_sintesi_non_tecnica.md`
  - `docs-limet\analisi-input\ANALISI_limet-agents_con_workflow_claude.md`
  - `docs-limet\analisi-input\HELIOS_ANALISI_PER_LIMET.md`
  - `docs-limet\analisi-input\REGISTRO_SKILLS_AGENTI_ECC.md`
  - `docs-limet\architecture\GRAPH_REPORT.md`

Per le fonti indicizzate, consulta projects.json di CEREBRO e interroga la collection indicata sopra.

Grafo del codice (Graphify): `graphify-out/` (report: `graphify-out/GRAPH_REPORT.md`).

## Documentation to maintain · Documentazione da mantenere

**EN** — Follow limet/templates/CODEBASE_ANALYSIS_TEMPLATE.md to maintain docs-limet/module-map.md, architecture.md, decisions.md, dependencies.md, conventions.md and glossary.md with file:line citations and Mermaid diagrams. UPDATE existing documents. Maintain docs-limet/NON_TECHNICAL_SUMMARY.md after relevant changes. Re-indexing belongs to the user, never the agent. After code/document changes, periodically remind the user (at task completion or archiving): Launcher -> project path -> Re-index -> Incrementale -> click Re-index.

**IT** — Segui limet/templates/CODEBASE_ANALYSIS_TEMPLATE.md per mantenere docs-limet/module-map.md, architecture.md, decisions.md, dependencies.md, conventions.md e glossary.md con citazioni file:line e diagrammi Mermaid. AGGIORNA i documenti esistenti. Mantieni docs-limet/NON_TECHNICAL_SUMMARY.md dopo modifiche rilevanti. Il re-index spetta all'utente, mai all'agente. Dopo modifiche a codice/documenti, ricordarlo periodicamente (al termine di un task o dell'archiviazione): Launcher -> Percorso progetto -> Re-index -> Incrementale -> clic Re-index.
<!-- PROJECT:END -->
