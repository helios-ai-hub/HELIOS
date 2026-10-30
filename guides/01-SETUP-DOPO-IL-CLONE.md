# 01 — Setup dopo il clone

> **Leggi questa pagina prima di toccare qualunque cosa.**
> Serve a chiunque prenda HELIOS per la prima volta su una macchina nuova.
> Dall'inizio alla fine sono 6 passi. Non saltarne nessuno e non cambiarne l'ordine.

**Il principio da tenere a mente:** il repository contiene **già** il metodo di lavoro
(`limet/`) e le istruzioni per gli agenti (`AGENTS.md`, `CLAUDE.md`). Quello che **non**
può contenere è la tua configurazione locale: percorsi, database vettoriale, modelli.
Il setup consiste esattamente in questo — ricostruire la parte locale, senza toccare
quella condivisa.

---

## Passo 0 — Prerequisiti

Verifica di avere tutto **prima** di iniziare. Ogni riga è un comando da eseguire.

| Cosa | Comando di verifica | Atteso |
|---|---|---|
| Git | `git --version` | ≥ 2.30 |
| Python | `python --version` | **≥ 3.11** |
| CEREBRO installato | la cartella esiste sul tuo disco | es. `C:\software\cerebro` |
| Qdrant attivo | `curl http://localhost:6333/healthz` | risposta OK |
| Ollama attivo | `curl http://localhost:11434/api/tags` | elenco modelli |
| LIMET (sorgente) | la cartella esiste sul tuo disco | es. `C:\Progetti\VSC\LIMET` |

Se Qdrant o Ollama non rispondono:

```bash
docker start qdrant
ollama serve
```

✅ **Se tutte le righe sono verdi, procedi. Altrimenti fermati qui.**

---

## Passo 1 — Clona il repository

```bash
cd /c/Progetti/VSC/HELIOS-PLATFORM        # o la cartella che preferisci
git clone https://github.com/helios-ai-hub/HELIOS.git
cd HELIOS
git branch -a
```

✅ Devi vedere i branch `main`, `dev`, `mk1`, `test`.

✅ Devi vedere queste cartelle:

```
.github/  docs/  docs-limet/  guides/  limet/  log/  test/
AGENTS.md  CLAUDE.md  README.md  .gitignore
```

Se `limet/`, `AGENTS.md` o `CLAUDE.md` **mancano**, hai clonato un branch vecchio:
passa a `mk1` o `dev` e ricontrolla.

---

## Passo 2 — ⛔ NON eseguire l'installer di LIMET

> ⛔ **Non eseguire `limet.ps1 init` né `limet.ps1 update` su questo repository.**

**Perché.** In HELIOS la cartella `limet/` non è strumentazione locale: è **prodotto**
(il task H-301 importa LIMET e CEREBRO nel monorepo, H-712 ne modifica la documentazione).
Arriva già completa con il clone.

L'installer farebbe due danni:

1. **Sovrascrive** `limet/FRAMEWORK_MANUAL.md`, `limet/ONBOARDING_CHECKLIST.md`,
   `limet/MASTER_INDEX.md` e tutta `limet/templates/` con la versione upstream
   (`Copy-Item -Force`). È il rischio **R-8** censito nel `PLAN_helios-poc.md` §8.
2. **Riscrive** `AGENTS.md` e `CLAUDE.md` con i percorsi della **tua** macchina,
   producendo modifiche che non vanno committate.

I documenti di progettazione in `limet/changes/` **non** sarebbero toccati (l'installer
non entra mai in quella cartella), ma il resto sì.

**Se l'hai già eseguito per errore**, annulla le modifiche non volute:

```bash
git status --short          # guarda cosa è cambiato
git restore limet/ AGENTS.md CLAUDE.md
```

---

## Passo 3 — Adatta i percorsi locali (solo se i tuoi sono diversi)

`AGENTS.md` e `CLAUDE.md` contengono il percorso di CEREBRO della macchina su cui sono
stati generati:

```
C:\software\cerebro\scripts\query_qdrant.py
```

Controlla il tuo:

```bash
grep -n "software.cerebro" AGENTS.md CLAUDE.md
```

- **Il percorso coincide con il tuo** → non fare nulla, passa al passo 4.
- **Il percorso è diverso** → modificalo nei due file con il tuo percorso reale.

> ⛔ **Non committare questa modifica.** È configurazione tua, non del progetto.
> Prima di ogni commit controlla `git status` e, se vedi `AGENTS.md` o `CLAUDE.md`
> modificati solo per i percorsi, escludili:
>
> ```bash
> git restore --staged AGENTS.md CLAUDE.md
> ```

**[N] Soluzione definitiva:** questo attrito sparisce con il task **H-309**, che introduce
`config/helios.local.json` (file per utente, mai versionato) e un modulo di bootstrap che
legge i percorsi da lì. Fino ad allora la correzione resta manuale.

---

## Passo 4 — Registra HELIOS nel tuo CEREBRO

Il database vettoriale è **locale alla tua macchina**: la collection `CRB_helios` non
viaggia con il repository e va ricostruita. Sostituisci `<CEREBRO>` con il tuo percorso
di installazione e `<HELIOS>` con la cartella del clone.

```powershell
cd <CEREBRO>

.venv\Scripts\python scripts\register_project.py add helios `
    --root "<HELIOS>" `
    --docs "<HELIOS>\docs-limet" "<HELIOS>\limet\changes" "<HELIOS>\limet\archive"
```

Le tre cartelle `--docs` sono quelle giuste: l'analisi di input, i documenti di lavoro in
corso e quelli archiviati.

> ⛔ **Non eseguire `register_project.py instructions`.**
> Quel comando rigenera il blocco di istruzioni dentro `AGENTS.md` e `CLAUDE.md`, che qui
> sono **già versionati**: otterresti solo modifiche da scartare (stesso problema del passo 3).

✅ Verifica che il progetto risulti registrato:

```powershell
.venv\Scripts\python scripts\register_project.py list
```

---

## Passo 5 — Indicizza la documentazione

```powershell
cd <CEREBRO>
.venv\Scripts\python scripts\ingest_docs.py --project helios
```

✅ Verifica che la collection si sia popolata e risponda:

```powershell
.venv\Scripts\python scripts\query_qdrant.py --project helios count
.venv\Scripts\python scripts\query_qdrant.py --project helios search "modalita non presidiata" --limit 3
```

La ricerca deve restituire passaggi del `PLAN_helios-poc.md` o dell'ADR sulla modalità non
presidiata. Se restituisce 0 risultati, l'indicizzazione non è andata a buon fine: **fermati**.

> ⛔ **Non toccare le collection di altri progetti** (`CRB_cosmo`, `project_docs`, le
> collection di mudeopen). HELIOS usa **solo** `CRB_helios`. Mai cancellare una collection.

🔁 L'indicizzazione è ripetibile: dopo aver modificato documenti, rilanciala
(Launcher → percorso progetto → Re-index → Incrementale).

---

## Passo 6 — Grafo del codice (facoltativo, per ora)

Il progetto è in **fase documentale**: non c'è ancora codice sorgente, quindi il grafo
Graphify ha poco da indicizzare. Quando il codice ci sarà, le query utili sono:

```bash
graphify query "<domanda>"
graphify explain <simbolo>
graphify path <A> <B>
```

**[N]** Il comando esatto di indicizzazione dipende dalla tua installazione: si esegue dal
Launcher. La cartella `graphify-out/` **non** è versionata (è in `.gitignore`): si rigenera
in locale.

---

## Ci sei. Cosa leggere, in quest'ordine

| # | Documento | Perché |
|---|---|---|
| 1 | `README.md` | Che cos'è HELIOS in due minuti |
| 2 | `docs-limet/analisi-input/06_sintesi_non_tecnica.md` | Il progetto senza gergo tecnico |
| 3 | `docs/CONSTITUTION.md` | I 7 non negoziabili |
| 4 | `docs/adr/` | Le decisioni architetturali (0001 fondativo, 0002 runtime, 0003 modalità non presidiata) |
| 5 | `limet/changes/0001-helios-poc/PLAN_helios-poc.md` | Il piano: perimetro, decisioni C01–C28, domande Q |
| 6 | `limet/changes/0001-helios-poc/TASK_LIST.md` | I 76 task, con dipendenze e criteri di completamento |
| 7 | `limet/changes/0001-helios-poc/TEST_PLAN.md` | I 65 test e i comandi di riesecuzione |
| 8 | `limet/changes/0001-helios-poc/E2E_VERIFICATION.md` | Gli scenari di verifica end-to-end |
| 9 | `log/HELIOS_GUIDA_BRANCH.md` | **Come lavorare**: pacchetti, branch, pull request |
| 10 | `log/HELIOS_Distribuzione_task.xlsx` | Chi fa cosa (assegnazione ancora **[P]**, da approvare) |

---

## Le cose da non fare mai

| ⛔ | Perché |
|---|---|
| Eseguire `limet.ps1 init` / `update` su questo repo | Sovrascrive documentazione versionata (rischio **R-8**) |
| Eseguire `register_project.py instructions` | Riscrive `AGENTS.md` e `CLAUDE.md` versionati |
| Toccare le collection Qdrant di COSMO o mudeopen | Vincolo del SUPERVISORE; usa solo `CRB_helios` |
| Cancellare una collection Qdrant | Mai, per nessun motivo |
| Far eseguire build, test, commit, push o merge a un agente | `docs/CONSTITUTION.md`, non negoziabile n.1 |
| Committare su `main` | Il merge su `main` lo fa solo il SUPERVISORE |
| Committare `AGENTS.md`/`CLAUDE.md` con i tuoi percorsi | È configurazione locale, non di progetto |
| Committare file `~$*.xlsx` | Sono lock temporanei di Excel; già esclusi da `.gitignore` |

---

## Se qualcosa non torna

1. `git status --short` — se mostra modifiche che non hai fatto tu, probabilmente hai
   eseguito l'installer LIMET (passo 2).
2. Ricerca CEREBRO a vuoto — rilancia `ingest_docs.py` e controlla che Qdrant e Ollama
   rispondano (passo 0).
3. In ogni altro caso: **fermati e chiedi al SUPERVISORE.** Non improvvisare.
