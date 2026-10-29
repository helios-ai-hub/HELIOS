# 05 — Roadmap, rischi e criticità

> HELIOS · documento 05/05. Etichette: **[F]** fatto · **[I]** ipotesi · **[P]** proposta · **[N]** non verificato.
> Rif.: `01_analisi_ECC.md`, `02_analisi_limet-agents.md`, `03_matrice_confronto.md`, `04_architettura_HELIOS.md`; documenti vincolanti `FEASIBILITY_O1_CLAUDE_CODE.html` (FO1) e `ANALISI_limet-agents_con_workflow_claude.md` (ALA).
> Stime di effort: tutte **[I]** (persona-giorni, intervallo; nessuna misura reale disponibile).

## 1. Regola di questo documento: ogni criticità ha opzioni risolutive al 100%

Per **ogni criticità** (C01–C28) sono proposte **almeno due opzioni** che la **eliminano completamente** (rischio residuo sulla criticità = 0, non "mitigato"). Ogni opzione dichiara: cosa elimina, il prezzo (funzionalità perse/effort) e la **condizione di verifica** che prova l'eliminazione. Se un'opzione riduce soltanto il rischio, non è elencata come risolutiva. Il SUPERVISORE sceglie un'opzione per criticità (sezione 3); la scelta raccomandata è marcata ★ **[P]**.

Nota di onestà **[I]**: "100%" vale per la criticità descritta, non per l'intero progetto; l'eliminazione si ottiene spesso rinunciando a una funzionalità ECC (coerente con la classificazione "esclusa con motivazione" richiesta dallo studio).

## 2. Catalogo criticità e opzioni risolutive

### C01 — Eccezione allo scope «niente framework di terzi» (ALA §2) e a FO1
**DECISO dal SUPERVISORE (2026-10-28):** HELIOS è un **nuovo progetto autonomo** che contiene al suo interno LIMET, CEREBRO ed ECC (asset in allowlist) e ha **proprie nuove regole** (costituzione HELIOS). Lo scope «niente framework di terzi» di ALA §2/FO1 resta valido per limet-agents *come definito in quei documenti*, ma non governa HELIOS: l'ADR fondativo di HELIOS fissa owner, perimetro ECC (allowlist), durata/pin e criteri di revoca. Equivale a C-A: il conflitto di scope scompare perché lo scope è ridefinito dal nuovo progetto. **[P]** Le «nuove regole» (da redigere in Fase 1) devono ereditare i vincoli non negoziabili: nessuna build/test/commit/push autonomi, gate umani, funzionamento Windows+Unix.
Evidenza originaria: **[F]** ALA §2 e FO1 limitano lo studio a LIMET + Claude Code nativo; HELIOS importa asset di terzi.
- **A ★** Decisione formale scritta (ADR) che amplia lo scope: owner, perimetro (solo asset ECC in allowlist), durata (fino alla v. pin), criteri di revoca. Elimina: l'incoerenza con i documenti vincolanti. Prezzo: 1 ADR + aggiornamento ALA §2. Verifica: ADR firmato, ALA/FO1 emendati.
- **B** Nessun uso runtime di ECC: si estraggono idee e si **riscrivono** da zero i contenuti (clean-room). Elimina: dipendenza da terzi e questione di scope. Prezzo: effort ×2–3 sul porting, nessun beneficio di aggiornamenti.
- **C** HELIOS come progetto separato con proprio scope, LIMET lo consuma come fornitore esterno versionato. Elimina: conflitto di scope (i vincoli ALA valgono solo per limet-agents). Prezzo: un secondo repository da governare.

### C02 — Agenti ECC che eseguono build/test/commit (viola LIMET §1.3 e policy «mai build/test»)
**DECISO dal SUPERVISORE (2026-10-28):** opzione A **più** configurazione dei permessi dalla dashboard. **[P]** Regole di progetto: (1) i permessi di ogni agente vivono in un file di policy versionato (fonte unica, C27) che la dashboard legge e modifica; (2) esiste un **pavimento non modificabile dalla UI** (hard-deny nel runner e nelle permission di Claude Code): `./mvnw*`, build/test, `git commit/push/merge`, accesso a segreti e rete non autorizzata; la UI può restringere, non rimuovere questo pavimento; (3) ogni modifica richiede conferma umana interattiva, motivazione e registro di audit (chi/quando/prima/dopo) con rollback; (4) la UI mostra il permesso *effettivo* (policy + pavimento) e un test di verifica; (5) un agente non può modificare la propria policy (C24); (6) qualsiasi permesso di scrittura/esecuzione oltre il pavimento passa dal runner con allowlist. Vedi anche C23/C24.
Evidenza: **[F]** 55/71 agenti hanno Bash, 26 Write; `tdd-guide`, resolver, `e2e-runner` eseguono comandi (`01` §3). ALA: runner = script, agenti senza esecuzione (R19).
- **A ★** Riscrittura obbligatoria del frontmatter `tools:` in fase di porting (senza Bash; solo Read/Grep/Glob/Write limitato) + deny permission su `./mvnw*`, `git push*`, `git merge*` (ALA R19). Elimina: capacità di esecuzione. Verifica: test statico che nessun asset portato contenga `Bash` e prova di deny.
- **B** Escludere tutti gli agenti con Bash/Write e portare solo i prompt come *checklist* di review (skill, non agenti). Elimina: gli agenti stessi.
- **C** Eseguire gli agenti ECC solo dentro il runner a **allowlist** in un sandbox senza rete/credenziali, producendo log immutabili. Elimina: esecuzione non controllata (resta il costo di costruire il sandbox).

### C03 — Rules `common/` sempre caricate (TDD 80%, commit, uso automatico di planner)
**DECISO dal SUPERVISORE (2026-10-28): opzione A.** Non si installano `common/{testing,development-workflow,agents,git-workflow}`; le altre regole si caricano solo per ruolo. Le idee utili delle 4 escluse possono essere riprese più avanti come regole HELIOS proprie.
Evidenza: **[F]** `rules/common/*` senza `paths:`; contraddice FO1 §1.3 / policy utente.
- **A ★** Non installare `common/{testing,development-workflow,agents,git-workflow}` e caricare le rules per ruolo (frontmatter `paths:` o inclusione esplicita nel prompt del ruolo). Elimina: caricamento globale.
- **B** Non portare nessuna rule `common/`; solo regole di linguaggio con `paths:`. Elimina: ogni conflitto di workflow.
- **C** Riscrivere le 4 regole come regole LIMET (riuso del testo, non del file). Elimina: contraddizione per costruzione.

### C04 — Hook bloccanti e invasivi (GateGuard, config-protection, commit-quality)
**DECISO dal SUPERVISORE (2026-10-28): opzione A.** Nessun hook ECC; le garanzie vengono dai gate meccanici LIMET.
Evidenza: **[F]** GateGuard nega la prima modifica di ogni file; costo ~+45% turni senza effetto significativo misurato (`01` §6, §9).
- **A ★** Nessun hook ECC: profilo `minimal` / porting senza `hooks.json`. Le garanzie vengono dai gate meccanici LIMET (FO1 §2.4). Elimina: ogni hook ECC.
- **B** Abilitare solo hook in allowlist esplicita (es. `block-no-verify`), con `ECC_DISABLED_HOOKS` per tutti gli altri e test di conflitto. Elimina: gli hook non in allowlist; richiede test per ciascuno ammesso.
- **C** Riscrivere gli hook desiderati come hook LIMET (codice proprio). Elimina: codice di terzi eseguibile.

### C05 — Mutazione di `~/.claude`, egress da transcript, contesto iniettato a SessionStart
**DECISO dal SUPERVISORE (2026-10-28): opzione A + regola sugli LLM.** Installazione solo a livello progetto (`.claude/`), nessun `ecc setup`/plugin globale, asset copiati come testi statici. **Regola:** il contenuto delle conversazioni/transcript non viene inviato a un LLM **a meno che la chiamata sia indispensabile al funzionamento di un automatismo approvato**. **[P]** Attuazione: (1) nessun automatismo di sola comodità che chiami un LLM (riassunti di sessione, observer, apprendimento continuo: esclusi, già coperti da C04/C08); (2) ogni chiamata LLM ulteriore rispetto agli agenti pilotati da Claude Code è dichiarata in un registro (scopo, dati inviati, destinatario, motivo di indispensabilità) e approvata dal SUPERVISORE; (3) la dashboard mostra il registro e segnala chiamate non registrate; (4) test CI: nessun asset portato invoca `claude -p`, SDK LLM o endpoint esterni non in registro.
Evidenza: **[F]** `ecc setup` modifica `~/.claude/settings.json`; hook scrivono in `~/.claude`, `~/.gateguard`; `llm-summary` esegue `claude -p` (`01` §6, §8).
- **A ★** Installazione solo a livello progetto (`.claude/`) e nessun `ecc setup`/plugin globale; i file sono copiati come asset statici. Elimina: scritture e hook globali.
- **B** Esecuzione in profilo utente isolato (`CLAUDE_CONFIG_DIR`/utente dedicato) usa-e-getta. Elimina: contaminazione dell'ambiente reale.
- **C** Nessuna installazione: solo lettura del repository ECC pinnato come sorgente di porting. Elimina: ogni effetto a runtime.

### C06 — Sovrapposizione spec-driven e collisione di nomi (`/plan`, `planner`, `code-reviewer`)
**DECISO dal SUPERVISORE (2026-10-28): opzione A, precisata.** Prefisso fisso `ecc-` a tutti gli asset portati; il flusso di pianificazione ECC **non è autore del piano** (non portato né shadowed come comandi/flusso): `plan`, `plan-prd`, `plan-canvas`, `prp-*`, `orch-*`, `blueprint`, `plan-orchestrate`, `epic-*`, agente `planner`. La pianificazione è solo LIMET. **Precisazione:** planner, architect e simili possono essere usati come **consulenti in sola lettura dell'analyst**: scrivono proposte in `ecosystem/proposals/`, mai file di piano né in `.claude/plans`. Le proposte tecniche l'analyst le integra nel `plan.json` (gate umano sul grafo); una proposta che aggiunge, modifica o elimina un **requisito** non si integra: «fermati e chiedi», voce in `QUESTIONS.md` con opzioni, decide il SUPERVISORE.
Evidenza: **[F]** forme brevi `/plan`, `/code-review`, `/pr`, agenti `planner`, `architect`; ECC consiglia namespace `ecc:` (`01` §5, §8).
- **A ★** Prefisso fisso `ecc-` a tutti gli asset portati + non portare `plan*`, `prp-*`, `orch-*`, `blueprint`, `plan-orchestrate`, `epic-*`. Elimina: collisioni e doppio flusso.
- **B** Namespace del plugin (`ecc:`) e non portare gli asset di piano. Elimina: collisione di nomi (non il doppio flusso, che resta escluso per scelta).
- **C** Allowlist chiusa: *solo* gli asset elencati nel ledger esistono nel progetto; il resto è assente. Elimina: qualsiasi collisione non prevista.

### C07 — Licenze eterogenee (CC-BY-SA, Apache senza NOTICE, «adapted from Wirasm», taste, branding)
**DECISO dal SUPERVISORE (2026-10-28): opzione A.** Registro per file (hash, licenza, origine); allowlist solo MIT verificata; esclusi CC-BY-SA, Apache senza NOTICE, `prp-*`/`code-review` (Wirasm), taste e asset con branding; NOTICE per le parti MIT. Resta consigliata una revisione legale prima del rilascio.
Evidenza: **[F]** `01` §9; nessun NOTICE/CLA. Nessun parere legale: verifica richiesta.
- **A ★** Ledger per file (hash, licenza, origine) + allowlist **solo MIT verificata**; esclusione di CC-BY-SA, Apache senza NOTICE, prp-*/code-review (Wirasm), taste, asset con branding; NOTICE per le parti MIT. Elimina: ogni asset a licenza dubbia.
- **B** Riscrittura clean-room degli asset desiderati. Elimina: obblighi derivati.
- **C** Parere legale preventivo e approvazione per asset; gli asset non approvati restano fuori. Elimina: incertezza (prezzo: tempo/costo legale).

### C08 — Memoria duplicata (Unified Memory / continuous-learning vs CEREBRO)
**DECISO dal SUPERVISORE (2026-10-28): opzione A.** Esclusi `unified-memory`, `continuous-learning*`, `observe`; CEREBRO è l'unica memoria/provider di contesto (read-only per gli agenti).
Evidenza: **[F]** ECC memory solo lessicale, vault `.ecc/memory`; CEREBRO = Qdrant+Ollama (RAG su doc).
- **A ★** Disattivare/escludere `unified-memory`, `continuous-learning*`, `observe`; CEREBRO unico provider di contesto (read-only). Elimina: seconda memoria.
- **B** Usare la memoria ECC come unica e dismettere CEREBRO. Elimina: duplicazione (ma perde RAG semantico e LIMET index: sconsigliata).
- **C** Memoria ECC attiva solo in sandbox di sessione, mai persistente. Elimina: persistenza duplicata.

### C09 — Plan Canvas / Control Pane (azioni attive di default; rischio che l'agente scriva il PLAN)
**DECISO dal SUPERVISORE (2026-10-28): opzione A.** Plan Canvas e Control Pane non si portano; il piano LIMET si rivede nella dashboard HELIOS.
Evidenza: **[F]** Control Pane azioni abilitate di default; Plan Canvas modifica/commenta `plan.md` (`01` §8).
- **A ★** Non portare né Plan Canvas né Control Pane; il PLAN LIMET si revisiona su file/launcher LIMET (:8799). Elimina: superficie e rischio di scrittura.
- **B** Usare Plan Canvas solo come viewer con `--read-only`, senza sync file e senza feedback agli agenti. Elimina: scrittura (verificare in PoC).
- **C** Replicare solo la UX in LIMET launcher (codice proprio). Elimina: dipendenza.

### C10 — Supply chain AgentShield (`ecc-agentshield`, dipendenza `@anthropic-ai/sdk`, nessun pin auditato)
**DECISO dal SUPERVISORE (2026-10-28): opzione B.** AgentShield adottato con versione bloccata + hash + SBOM, eseguito dal runner senza rete e senza `--opus`.
Nota del SUPERVISORE (2026-10-28): HELIOS funziona su Claude Code, quindi la dipendenza dall'SDK Anthropic **non è di per sé un'incompatibilità**. I punti che restano aperti sono: (1) l'opzione `--opus` invia contenuto a un LLM (regola C05: ammessa solo se indispensabile e registrata); (2) nessun pin/audit fornito da ECC; (3) supply chain di un pacchetto npm separato. **[P]** Con questo chiarimento l'opzione B diventa la più equilibrata.
Evidenza: **[F]** `01` §8.
- **A ★** Non adottarlo in fase 1; alternativa: scanner già presente nello standard dell'azienda o regole di scansione proprie. Elimina: dipendenza.
- **B** Pin di versione + hash + SBOM + esecuzione nel runner senza rete e senza `--opus`. Elimina: egress e deriva di versione (richiede audit una tantum).
- **C** Vendoring del codice dopo audit di licenza e sicurezza. Elimina: dipendenza da registry.

### C11 — GitHub App / ecc.tools (invio di commit, autori e codice ad Anthropic)
Evidenza: **[F]** `https://ecc.tools/privacy`, nessun DPA citato.
- **A ★** Non installare mai l'App; blocco per policy sull'organizzazione GitHub. Elimina: egress.
- **B** Solo con DPA firmato e verifica legale; altrimenti A. Elimina: assenza di garanzie contrattuali.

**DECISO dal SUPERVISORE (2026-10-28): opzione A.** App mai installata, blocco a livello di organizzazione GitHub.

### C12 — Manutenzione: maintainer unico, catalogo in rapida crescita, deriva API
Evidenza: **[F]** `01` §1.
- **A ★** Fork/vendor pinned al commit `25278e7`, nessun aggiornamento automatico; ogni upgrade = decisione con test di non regressione. Elimina: deriva imprevista.
- **B** Snapshot unico "congelato" (copia statica nel repo, nessun legame upstream). Elimina: dipendenza dall'upstream.
- **C** Riscrittura clean-room (vedi C01-B). Elimina: dipendenza.

**DECISO dal SUPERVISORE (2026-10-28): opzione A.** Fork/vendor pinned a `25278e7`, upgrade solo per decisione con test di non regressione.

### C13 — Requisito vincolante: l'applicazione deve funzionare su Windows **e** Unix (Linux/macOS)
Evidenza: **[F]** requisito del SUPERVISORE; README ECC cita limitazioni su Windows nativo (issue #2489, #2626, #2674); **[I]** tmux non nativo su Windows; **[F]** LIMET ha già `limet.ps1`/`limet.sh` (doppia implementazione).
Vincolo di progetto **[P]**: ogni componente HELIOS (runner, hook propri, script, installer del porting, test) deve passare la stessa suite su Windows e su Linux/macOS.
- **A ★** Componenti scritti in un unico runtime portabile (Node ≥ 18 o Python ≥ 3.11, già presenti nello stack LIMET/CEREBRO) con `pathlib`/`path`, senza shell-specifici; nessun uso di tmux, bash o PowerShell nel core; asset ECC portati solo testuali (OS-indipendenti). Elimina: dipendenze OS alla radice. Verifica: CI a matrice (windows-latest, ubuntu-latest, macos-latest) con suite identica; fine-riga/percorsi/permessi coperti da test.
- **B** Doppia implementazione sottile (`.ps1`/`.sh`, come già `limet.ps1/.sh`) su un core comune, con test di parità dei risultati su entrambi gli OS. Elimina: assenza di supporto di un OS (prezzo: doppia manutenzione degli entry-point).
- **C** Esecuzione del core in container (Linux) su entrambi gli host, con wrapper nativi minimi. Elimina: differenze di runtime (prezzo: Docker obbligatorio; in conflitto con installazioni senza Docker).

**DECISO dal SUPERVISORE (2026-10-28): opzione A.** Core in unico runtime portabile, CI a matrice Windows/Linux/macOS.
### C14 — limet-agents non esiste (solo design): fattibilità valutata su carta
Evidenza: **[F]** `C:\software\limet-agents` assente.
- **A ★** PoC verticale minimo di limet-agents (3 ruoli + runner + 1 gate) **prima** di qualsiasi porting ECC; go/no-go (§5). Elimina: l'incertezza di base.
- **B** Prototipo con Dynamic Workflow `limet-change.js` (ALA R23) su una pratica reale. Elimina: la fattibilità solo teorica.

**DECISO dal SUPERVISORE (2026-10-28): opzione A.** PoC verticale minimo prima di qualsiasi porting ECC.

### C15 — Evidenza incompleta su ECC (lettura non integrale, conteggi incoerenti)
Evidenza: **[N]** `01` §0.
- **A ★** Fase 2 di inventario con script che legge il 100% di file e genera una tabella per skill/agente, con revisione manuale degli asset in allowlist. Elimina: lettura parziale per gli asset adottati.
- **B** Adottare solo asset già letti integralmente. Elimina: dipendenza da parti non lette.

**DECISO dal SUPERVISORE (2026-10-28): opzione A.** Inventario scriptato al 100% in Fase 2, revisione manuale degli asset in allowlist.

### C16 — Qualità degli output e costo in token
Evidenza: **[F]** ALA R-metriche, FO1 §6 costi 20–45 USD; GateGuard +45% turni.
- **A ★** Esperimento A/B (come `GUIDA_ECC_BIBLIOTECA.md` §7) con soglie go/no-go: se un asset non migliora le metriche, non entra. Elimina: asset senza beneficio misurato.
- **B** Budget a tetto per workflow, interruzione automatica oltre soglia. Elimina: sforamento (non la qualità: abbinare ad A).

**DECISO dal SUPERVISORE (2026-10-28): opzione A.** Esperimento A/B con soglie go/no-go; asset senza miglioramento misurato non entra.

### C17 — «LIMET-agnostico» non verificato per gli asset portati
Evidenza: ALA §13.
- **A ★** Test di compatibilità statico: asset = solo prompt (nessun hook/tool/memoria/riferimento a `.claude/plans`), eseguito in CI di limet-agents. Elimina: asset non agnostici ammessi.
- **B** Revisione manuale firmata per asset. Elimina: errore sistematico (non rapido).

**DECISO dal SUPERVISORE (2026-10-28): opzione A.** Test statico di compatibilità in CI di limet-agents.

### C18 — Runner: isolamento e fiducia nei log
Evidenza: FO1 §2.4; ALA R19.
- **A ★** API a allowlist, input dichiarativi, processo senza rete, log immutabili (hash), mai commit/push. Elimina: esecuzione arbitraria.
- **B** Esecuzione in container usa-e-getta senza credenziali. Elimina: accesso a segreti e rete.

**DECISO dal SUPERVISORE (2026-10-28): opzione A.** Allowlist, input dichiarativi, processo senza rete, log con hash, mai commit/push.

### Fusione LIMET + CEREBRO + HELIOS in un unico progetto (richiesta del SUPERVISORE)
Presupposto **[F]**: oggi sono separati: LIMET in `C:\Progetti\VSC\LIMET\` (repository `odyssey2073/limet`; documenti, template, `scripts/`), CEREBRO in `C:\software\cerebro\` (Python, Qdrant+Ollama, MCP, CI pytest su windows-latest, `projects.json` con progetti registrati csi_cosmo, mudeopensrvet, mudeopenbtcet). Il repository remoto di CEREBRO **[N]** non è stato verificato. Questo studio **non modifica** nessuno dei due: la fusione è una proposta da eseguire in Fase 3.

### C19 — Retrocompatibilità dei consumatori esistenti (percorsi, comandi, porte)
Evidenza: **[F]** `CLAUDE.md`/`copilot-instructions` e progetti COSMO/mudeopen puntano a `C:\software\cerebro\scripts\query_qdrant.py`, `limet.ps1/.sh`, hook `limet_rag.py`, MCP `limet_search`; porte :8789, :8777, :8799; collection `CRB_<slug>` esistenti.
- **A ★** Layout unico con **shim di compatibilità**: i vecchi punti d'ingresso (`cerebro.sh`, `query_qdrant.py`, `ingest_docs.py`, `limet.ps1/.sh`, hook/MCP) restano come wrapper sottili che delegano al nuovo modulo, stessi argomenti, stesse porte e nomi collection. Elimina: rottura dei consumatori. Verifica: suite di parità (stessi input → stessi output) su Windows e Unix, eseguita sui progetti registrati.
- **B** Migrazione con script che riscrive tutti i riferimenti (percorsi, config MCP, istruzioni) nei progetti consumatori, con backup e rollback. Elimina: percorsi legacy (prezzo: toccare ogni progetto; richiede autorizzazione per ciascuno).
- **C** Giunzioni/symlink dai vecchi percorsi (`C:\software\cerebro`) al nuovo (junction su Windows, symlink su Unix). Elimina: rottura dei percorsi senza modificare i consumatori (prezzo: privilegi/gestione per OS).

**DECISO dal SUPERVISORE (2026-10-28): opzione A, con condizione.** HELIOS usa un proprio LIMET interno e **non tocca** i LIMET/CEREBRO in uso per COSMO e mudeopen (restano dove sono, invariati). Gli shim A servono solo a garantire la parità di comportamento dentro HELIOS e a una eventuale futura migrazione volontaria; nessun progetto consumatore viene modificato.

### C20 — Segreti, dati e stato (`projects.json`, collection Qdrant, cache)
Evidenza: **[F]** `projects.json` può contenere credenziali (non riportate in questo studio); `cache/` e Qdrant contengono dati indicizzati.
- **A ★** Configurazione e segreti **fuori dal repository** (variabili d'ambiente/secret store; nel repo solo `projects.example.json`), dati Qdrant e `cache/` mai versionati; nomi collection invariati. Elimina: segreti nel repo e migrazione dei dati.
- **B** Segreti cifrati nel repo (es. SOPS/git-crypt) con chiavi esterne. Elimina: segreti in chiaro (prezzo: gestione chiavi).
- **C** Scansione segreti obbligatoria (pre-commit + CI) più import **senza storia** dei file di config. Elimina: segreti già presenti nella cronologia importata (abbinare ad A/B).

**DECISO dal SUPERVISORE (2026-10-28): opzione A.** Segreti e config fuori dal repo, solo `projects.example.json` versionato, dati Qdrant e `cache/` mai versionati.

### C21 — Cronologia git, ownership e remoti
Evidenza: **[F]** LIMET ha remoto `odyssey2073/limet`; **[N]** remoto/storia di CEREBRO non verificati; nel repo LIMET sono presenti modifiche locali non committate non mie (`.obsidian/*`, `it/ONBOARDING_CHECKLIST.md`).
- **A ★** Nuovo repository HELIOS con import per `git subtree`/`filter-repo` in sottocartelle (`limet/`, `cerebro/`), storia preservata; repository originali archiviati in sola lettura con tag di riferimento. Elimina: perdita di storia e ambiguità su quale sia la fonte.
- **B** Import **squash** (un commit per origine, con riferimento al commit sorgente). Elimina: storia contaminata da segreti/rumore (prezzo: storia persa).
- **C** Mantenere LIMET come repository esistente e portarvi dentro CEREBRO e HELIOS. Elimina: creazione di un nuovo repo (prezzo: remoto e nome «limet» ereditati).
Nota: commit/push sono sempre a cura dell'utente; nessuna delle opzioni è eseguita da Copilot.

**DECISO dal SUPERVISORE (2026-10-28): opzione A.** Nuovo repository HELIOS con import della storia in sottocartelle; repository originali invariati.

### C22 — Perdita di confini (monolite) e release unica
Evidenza: **[I]** fondere documentazione (LIMET), servizi Python (CEREBRO) e orchestrazione (HELIOS) aumenta l'accoppiamento; CEREBRO ha CI, LIMET no.
- **A ★** Monorepo a **moduli con confini imposti**: `limet/` (metodo, template, script), `cerebro/` (RAG), `agents/` (plugin `limet-agents`, workflow, ruoli, runner, asset ECC); API pubbliche dichiarate; test automatici che vietano import/riferimenti fuori dai contratti; versioning per modulo + tag di release unico. Elimina: dipendenze incrociate non dichiarate.
- **B** Monorepo con **workspace** e pacchetti separati (Python packages + Node workspace) con dipendenze esplicite, pubblicabili singolarmente. Elimina: accoppiamento implicito (prezzo: packaging).
- **C** CI unica a matrice (windows/ubuntu/macos) con job per modulo e **gate di non regressione** per ogni modulo prima del merge. Elimina: modifiche a un modulo che rompono un altro (abbinare ad A o B).

**DECISO dal SUPERVISORE (2026-10-28): opzione A** (CI a matrice di C come supporto raccomandato).
### Dashboard web unica React (HELIOS + LIMET + CEREBRO) — richiesta del SUPERVISORE
Presupposto **[F]**: oggi esiste un launcher LIMET su :8799 e CEREBRO ha porte :8789/:8777; ECC ha un Control Pane con **azioni abilitate di default** (`01` §8), esempio di ciò da evitare. La UI non è stata progettata né implementata: tutto è **[P]**. Le librerie citate sono candidate **[I]** (licenza da verificare alla scelta, nessuna versione verificata).

### C23 — Superficie di attacco della UI di gestione (azioni, rete, sessione)
- **A ★** Backend che ascolta solo su loopback (`127.0.0.1`), token di sessione casuale per avvio, allowlist Host/Origin, protezione CSRF, CSP restrittiva, body limitato; **tutte** le azioni passano da API con allowlist (nessuna shell). Elimina: accesso da rete e comandi arbitrari. Verifica: test automatici (richiesta da Origin esterno, senza token, con payload fuori allowlist → rifiutate) su Windows e Unix.
- **B** Dashboard **solo lettura** in fase 1; ogni azione resta in CLI/LIMET. Elimina: ogni azione dalla UI (prezzo: nessuna gestione dalla UI finché non si abilita la fase 2).
- **C** Accesso con identità aziendale (OIDC) davanti alla UI, ruoli per azione. Elimina: accesso anonimo (richiede un IdP **[N]** non verificato).

**DECISO dal SUPERVISORE (2026-10-28): opzione A.** Loopback, token di sessione, allowlist Host/Origin, CSRF, CSP, azioni solo via API con allowlist.

### C24 — Separazione umano/agente: la UI non deve diventare un canale di approvazione o esecuzione per gli agenti
Evidenza: **[F]** ALA R13/R19, FO1 §3: gate umani, nessuna build/test/commit/push autonomi; lezione Plan Canvas (`01` §8).
- **A ★** I gate si approvano solo da UI con sessione interattiva umana (token non esposto agli agenti, conferma esplicita); la UI invia richieste al workflow LIMET, **non esegue** build/test/git; commit/push restano manuali. Elimina: approvazioni o esecuzioni da parte di agenti.
- **B** Approvazioni solo via CLI con conferma interattiva; UI in sola lettura per i gate. Elimina: gate dalla UI.
- **C** Approvazione firmata (firma/segreto che l'agente non può leggere) verificata dal workflow. Elimina: approvazioni falsificabili.

**DECISO dal SUPERVISORE (2026-10-28): opzione A.** Gate approvati solo da sessione umana interattiva; la UI non esegue build/test/git.

### C25 — Dipendenze frontend (npm), licenze e supply chain
- **A ★** Lockfile obbligatorio, allowlist di licenze permissive, `npm audit`/scansione in CI, SBOM, versioni pinnate, aggiornamenti solo con PR. Elimina: dipendenze non approvate.
- **B** Insieme minimo di dipendenze (React + 1 libreria grafica + 1 di routing) e build vendorizzata. Elimina: la maggior parte della superficie (prezzo: più codice proprio).
- **C** Build riproducibile in ambiente isolato senza rete dopo il vendoring. Elimina: deriva dal registry.
Nota: la compilazione/esecuzione dei test della UI è sempre a cura dell'utente (policy).

**DECISO dal SUPERVISORE (2026-10-28): opzione A.** Lockfile, licenze permissive, audit e SBOM in CI, versioni pinnate, aggiornamenti solo via PR.

### C26 — Grafica avanzata vs portabilità, prestazioni e accessibilità (Windows/Unix, browser)
- **A ★** Grafica in SVG/Canvas con librerie permissive (candidate **[I]**: React Flow per il DAG dei workflow, D3 o ECharts per metriche/timeline, Recharts per grafici semplici), WebGL solo opzionale con fallback; matrice browser supportata (Chromium, Firefox, Safari), budget di prestazioni e test di accessibilità (WCAG 2.1 AA) in CI. Elimina: dipendenza da GPU/OS specifici.
- **B** Rendering lato server (immagini/SVG) per i grafici pesanti. Elimina: dipendenza dal browser (prezzo: meno interattività).
- **C** Modalità «leggera» selezionabile (senza animazioni/effetti). Elimina: problemi su macchine limitate (abbinare ad A).

**DECISO dal SUPERVISORE (2026-10-28): opzione A** (la modalità leggera C non è stata scelta).

### C27 — Fonte unica dei dati: la UI non deve creare un secondo stato
- **A ★** Backend sottile con **read model derivato** dai file/artefatti LIMET, dallo stato del workflow e dalle API CEREBRO; nessun database di stato proprio; cache rigenerabile. Elimina: divergenza tra UI e artefatti.
- **B** Event log append-only (file) scritto solo dal workflow e letto dalla UI. Elimina: scritture concorrenti della UI.
- **C** Interfaccia dati dichiarata (schemi JSON versionati) condivisa tra moduli e UI, con test di contratto. Elimina: rotture silenziose tra versioni.

**DECISO dal SUPERVISORE (2026-10-28): opzione A.** Read model derivato da file LIMET, stato workflow e API CEREBRO; nessun DB di stato proprio.

### C28 — Valutazione dei rischi dopo ogni task
- **A ★** Al termine di ogni task (VERIFIED) un agente di sola lettura, con modello diverso dal developer, valuta rischi, errori e possibili malfunzionamenti dovuti a quel task; se ne trova li segnala al SUPERVISORE con evidenza e opzioni risolutive (≥ 2, con pro/contro e raccomandazione) e non corregge in autonomia. Elimina: problemi che passano i test del task ma emergono dopo.
- **B** Valutazione solo a fine change (report finale). Elimina: costo per task; lascia propagare i problemi ai task dipendenti.
- **C** Valutazione per task solo oltre una soglia di rischio/dimensione. Elimina: costo sui task banali.

**DECISO dal SUPERVISORE (2026-10-28): opzione A** (richiesta esplicita). **Doppia applicazione [P]:** (1) subito, come **regola per l'LLM** che sviluppa HELIOS con LIMET puro (dopo ogni task, valutazione di rischi/errori/malfunzionamenti, segnalazione al SUPERVISORE con opzioni, nessuna correzione autonoma); (2) nel prodotto, come ruolo `risk-assessor`. Limite **[F]**: è un giudizio del modello, non una prova; richiede evidenze e non sostituisce i test.

## 3. Decisioni richieste al SUPERVISORE

| ID | Opzione raccomandata ★ | Alternative risolutive | Stato |
|---|---|---|---|
| C01 | **DECISO**: HELIOS progetto autonomo con proprie regole | — | chiuso |
| C02 | **DECISO**: A + permessi configurabili da dashboard (con pavimento non modificabile) | — | chiuso |
| C03 | **DECISO**: A (4 regole escluse, altre per ruolo) | — | chiuso |
| C04 | **DECISO**: A (nessun hook ECC) | — | chiuso |
| C05 | **DECISO**: A + nessun invio a LLM salvo indispensabile e registrato | — | chiuso |
| C06 | **DECISO**: A (prefisso `ecc-`, flusso di pianificazione ECC rimosso) | — | chiuso |
| C07 | **DECISO**: A (registro licenze, solo MIT verificato) | — | chiuso |
| C08 | **DECISO**: A (CEREBRO unica memoria) | — | chiuso |
| C09 | **DECISO**: A (non portati; dashboard HELIOS) | — | chiuso |
| C10 | **DECISO**: B (pin + hash + SBOM, runner senza rete, senza --opus) | — | chiuso |
| C11 | A | B | **DECISO: A** |
| C12 | A | B, C | **DECISO: A** |
| C13 | A | B | **DECISO: A** |
| C14 | A | B | **DECISO: A** |
| C15 | A | B | **DECISO: A** |
| C16 | A | B (+A) | **DECISO: A** |
| C17 | A | B | **DECISO: A** |
| C18 | A | B | **DECISO: A** |
| C19 | A | B, C | **DECISO: A** (HELIOS con LIMET interno, installazioni attuali intoccate) |
| C20 | A | B, C (+A/B) | **DECISO: A** |
| C21 | A | B, C | **DECISO: A** (remoto CEREBRO **[N]**) |
| C22 | A | B, C (+A/B) | **DECISO: A** |
| C23 | A | B, C | **DECISO: A** |
| C24 | A | B, C | **DECISO: A** |
| C25 | A | B, C | **DECISO: A** |
| C26 | A | B, C (+A) | **DECISO: A** |
| C27 | A | B, C | **DECISO: A** |
| C28 | A | B, C | **DECISO: A** (valutazione rischi dopo ogni task) |

**[P]** Con tutte le scelte ★ l'architettura è: asset ECC testuali portati e pinnati, nessun hook né servizio ECC, nessuna memoria ECC, esecuzione solo tramite runner LIMET. Costo funzionale: si rinuncia a Plan Canvas, Control Pane, AgentShield (fase 1), GateGuard, memoria ECC e orchestrazione ECC.

## 4. Roadmap a 12 fasi (effort **[I]**, persona-giorni)

| # | Fase | Complessità | Dipendenze | Deliverable | Criterio di completamento | Effort | Parallelizzabile |
|---|---|---|---|---|---|---|---|
| 1 | Discovery e congelamento requisiti | bassa | — | ADR scope (C01), scelte C01–C28 (tutte decise dal SUPERVISORE il 2026-10-28) | tutte le criticità con opzione scelta | 2–3 | no |
| 2 | Inventario e normalizzazione ECC | media | 1 | tabella per-asset 100% letti (C15), ledger licenze (C07) | 0 asset non letti in allowlist | 4–6 | sì |
| 3 | Contratti di integrazione e **consolidamento LIMET + CEREBRO + HELIOS in un progetto** (C19–C22) | alta | 1 | schemi artefatti, naming `ecc-`, formato ruoli (C06, C17); layout monorepo, shim di compatibilità, suite di parità, CI a matrice | contratti approvati; suite di parità verde su Windows e Unix; consumatori registrati invariati | 11–16 | parz. |
| 4 | PoC verticale limet-agents + 1 asset ECC | alta | 1,3 | 3 ruoli + runner + 1 gate (C14, C18) | criteri go/no-go §5 | 8–12 | no |
| 5 | Porting skill/rules | media | 2,3,4 | asset in allowlist con test di agnosticità (C17) | CI verde | 5–8 | sì |
| 6 | Integrazione agenti (reviewer) | media | 4,5 | reviewer ECC riusati con frontmatter senza Bash (C02), corpo invariato | test statico verde | 4–6 | sì |
| 7 | Sostituzione flusso spec-driven | media | 4 | `plan*`/`prp-*`/`orch-*` assenti, workflow LIMET attivo (C06) | nessuna collisione | 2–3 | no |
| 8 | Hook e comandi | bassa | 4 | hook LIMET propri o nessuno (C04, C05) | 0 scritture fuori workspace | 2–4 | sì |
| 8b | **Dashboard web React unica** (HELIOS + LIMET + CEREBRO; C23–C27) | alta | 3,4 | backend sottile + UI React/TypeScript: viste Portfolio, LIMET, HELIOS (DAG workflow, gate, costi), CEREBRO, criticità/licenze; test di contratto; sostituzione del launcher :8799 tramite shim | viste in sola lettura complete; azioni abilitate solo dopo i test di sicurezza C23–C24; accessibilità e prestazioni nei budget (C26) | 15–25 | sì (dopo i contratti dati) |
| 9 | Test end-to-end (CI a matrice Windows + Linux + macOS, C13) | alta | 5–8 | scenari ALA V1–V24 sul PoC, su tutti gli OS | scenari passati su ogni OS | 6–10 | parz. |
| 10 | Hardening | media | 9 | log immutabili, sandbox, SBOM (C10, C18) | audit completato | 4–6 | sì |
| 11 | Documentazione | bassa | 9 | manuali, NOTICE, runbook | revisione approvata | 3–4 | sì |
| 12 | Rilascio progressivo | media | 10,11 | rollout per progetto pilota, rollback | pilota con metriche (C16) | 3–5 | no |

Totale **[I]** 69–107 persona-giorni (include fusione ~8–12 e dashboard 15–25; senza questi due blocchi 46–70; ×3 circa se C01-B/C07-B clean-room). Percorso critico: 1→3→4→5→9→10→12; la dashboard (8b) è parallelizzabile dopo i contratti dati.

## 5. Criteri go/no-go del PoC (misurabili)

1. 0 scritture fuori dal workspace e 0 modifiche a `~/.claude` (verifica con diff prima/dopo).
2. 0 connessioni di rete non autorizzate (log di rete).
3. 0 asset portati con Bash/hook/memoria (test statico C17/C02).
4. Runner riproducibile: stesso input → stesso esito/hash di log.
5. Tutte le chiamate agente→runner tracciate.
6. Almeno una metrica (difetti trovati in review o tempo) migliore del baseline LIMET senza ECC (C16), altrimenti no-go sull'asset.
7. Stessa suite PoC verde su Windows e su Linux/macOS (C13).
8. Costo per workflow ≤ 100 USD (tetto deciso dal SUPERVISORE; FO1 §6: 20–45 USD come riferimento).
9. Suite di parità dopo la fusione: i comandi legacy (`query_qdrant.py`, `limet.ps1/.sh`, MCP `limet_search`) danno gli stessi esiti sui progetti registrati; 0 segreti nel repository (scansione) (C19–C21).
10. Dashboard: test di sicurezza C23–C24 superati (richieste da Origin esterno, senza token o fuori allowlist rifiutate; nessuna azione di build/test/git dalla UI) su Windows e Unix.
Qualsiasi criterio fallito = no-go sull'asset/lane interessato; piano B = wrapper senza copia (Opzione 3, `04` §5).

## 6. Licenze da inoltrare al legale (nessun parere definitivo)

`skills/osint-investigation` (CC-BY-SA-4.0); 8 skill Apache-2.0 senza NOTICE; `prp-*`/`code-review` (Wirasm, licenza upstream non dichiarata); `taste-application` (origine zip utente); marchi «ECC»/«Claude» e loghi sponsor; licenza `ecc-agentshield` e dipendenze; termini ECC Pro e GitHub App. Con C07-A tutti restano **fuori** dalla distribuzione finché non approvati.

## 7. Prerequisiti PoC e decisioni aperte

Prerequisiti: ADR C01; Claude Code ≥ 2.1 **[F]** (2.1.311 verificato, da ricontrollare nel PoC); Qdrant/Ollama attivi **[F]** (verificati); `CRB_limet` assente **[F]**: HELIOS non crea collection esistenti (decisione del SUPERVISORE). Questioni aperte: nessuna; Q1–Q9 e C01–C28 sono tutte decise (v. `HELIOS_ANALISI_PER_LIMET.md` §8).

*Aggiornato il 2026-10-29:* ADR C01 **scritto** (`docs/adr/0001-helios.md`, stato `PROPOSTO`, in attesa di approvazione); Claude Code **2.1.312** sulla macchina di sviluppo [F]; PoC **non iniziato**, gate go/no-go **non raggiunto**; nuove decisioni D29–D37 e Q10–Q11 in `HELIOS_ANALISI_PER_LIMET.md` §10.4; proposta di valutare i criteri 9–10 di §5 come «N/A in Fase 4» registrata in `limet/changes/0001-helios-roadmap-completa/QUESTIONS.md` (Q-A1), **non applicata** in attesa del SUPERVISORE.
