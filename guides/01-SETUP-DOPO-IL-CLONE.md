# 01 — Setup dopo il clone

> **Leggi questa pagina prima di toccare qualunque cosa.**
> Serve a chiunque prenda HELIOS per la prima volta su una macchina nuova.
> Dall'inizio alla fine sono 6 passi. Non saltarne nessuno e non cambiarne l'ordine.

**Il principio da tenere a mente:** il repository contiene **solo ciò che scriviamo noi** —
i documenti di progettazione (`limet/changes/`), la documentazione (`docs/`, `docs-limet/`)
e le istruzioni per gli agenti (`AGENTS.md`, `CLAUDE.md`). Tutto ciò che un tool è in grado
di **rigenerare** non è versionato: lo ricostruisci tu in locale, ed è esattamente ciò che
fanno i passi qui sotto.

| Nel repository (arriva col clone) | Da ricostruire in locale (passi 2-5) |
|---|---|
| `limet/changes/`, `limet/archive/` | `limet/FRAMEWORK_MANUAL.md`, `MASTER_INDEX.md`, `ONBOARDING_CHECKLIST.md` |
| `docs/`, `docs-limet/`, `guides/`, `log/` | `limet/templates/` (18 file), `limet/scripts/` (3 file) |
| `AGENTS.md`, `CLAUDE.md`, `README.md` | la collection Qdrant `CRB_helios`, `graphify-out/` |

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
| LIMET **sorgente** | la cartella esiste sul tuo disco | es. `C:\Progetti\VSC\LIMET`. Serve al passo 2: è da qui che il framework viene installato dentro il clone |

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

✅ Devi vedere queste cartelle e questi file:

```
.github/  docs/  docs-limet/  guides/  limet/  log/  test/
AGENTS.md  CLAUDE.md  README.md  .gitignore
```

✅ Dentro `limet/` trovi **solo** `changes/` e `archive/`. È corretto: il resto del
framework (manuale, checklist, indice, `templates/`, `scripts/`) lo installi tu al passo 2.

Se `limet/changes/0001-helios-poc/` **manca**, hai clonato un branch vecchio: passa a
`mk1` o `dev` e ricontrolla.

---

## Passo 2 — ✅ Installa il framework LIMET in locale

> **«Ma io LIMET ce l'ho già»** — serve lo stesso, e questa è la distinzione da capire:
>
> | | Cos'è | Dove sta |
> |---|---|---|
> | **LIMET sorgente** | l'installer e le edizioni `it`/`en` | `C:\Progetti\VSC\LIMET` — è il prerequisito del passo 0, e **ce l'hai già** |
> | **LIMET nel progetto** | `FRAMEWORK_MANUAL.md`, `ONBOARDING_CHECKLIST.md`, `MASTER_INDEX.md`, `templates/`, `scripts/` dentro `HELIOS\limet\` | nel tuo clone — e **non c'è**, perché non è versionato |
>
> Il passo 2 fa il secondo: prende dal sorgente che già possiedi e installa nel clone.
> È **per progetto**, esattamente come per ogni altro progetto che segue LIMET.
> Appena clonato, `limet/` contiene solo `changes/` e `archive/`: senza questo passo
> restano lì, e gli agenti AI cercherebbero un `limet/FRAMEWORK_MANUAL.md` inesistente.

```bash
powershell -ExecutionPolicy Bypass -File "<LIMET>\scripts\limet.ps1" \
  init -ProjectPath "<HELIOS>" -Lang it
```

Sostituisci `<LIMET>` con la tua installazione (es. `C:\Progetti\VSC\LIMET`) e `<HELIOS>`
con la cartella del clone. Scrivi i percorsi **con i backslash e fra virgolette**: da Git Bash
la forma `/c/...` viene tradotta in modo imprevedibile quando la passa a PowerShell.

> ⚠️ **`-Lang it` non è facoltativo.** Il valore predefinito è `en`: senza quel parametro
> installi l'edizione inglese e riscrivi in inglese il blocco LIMET di `AGENTS.md` e
> `CLAUDE.md`, che sono **versionati** — otterresti un diff da scartare. HELIOS usa
> l'edizione **italiana**.

Il comando crea i file che mancavano: `limet/FRAMEWORK_MANUAL.md`,
`limet/ONBOARDING_CHECKLIST.md`, `limet/MASTER_INDEX.md`, `limet/templates/`,
`limet/scripts/`, e aggiorna i blocchi marcati in `AGENTS.md` e `CLAUDE.md`.

✅ **Verifica subito che il repository sia rimasto pulito:**

```bash
git status --short
```

**Atteso: nessuna riga, oppure al massimo `AGENTS.md` e `CLAUDE.md` modificati.**

**Perché funziona.** Quei file sono esclusi dal versionamento (vedi `.gitignore`): l'installer
li riscrive liberamente e git non se ne accorge, perché non sono affari suoi. È una scelta
deliberata — un file rigenerato da un tool, se fosse versionato, verrebbe mergiato da git
riga per riga e un `update` su un altro clone cancellerebbe **in silenzio** il contenuto di
HELIOS, senza produrre alcun conflitto (rischio **R-8**, `PLAN_helios-poc.md` §8).

I documenti di progettazione in `limet/changes/` non vengono toccati in nessun caso:
l'installer in quella cartella non entra mai.

🔁 `limet.ps1 update` è altrettanto sicuro e si può rilanciare quando vuoi, per allinearti
a una versione più recente di LIMET — **sempre con `-Lang it`**:

```bash
powershell -ExecutionPolicy Bypass -File "<LIMET>\scripts\limet.ps1" \
  update -ProjectPath "<HELIOS>" -Lang it
```

⚠️ **Se `git status` mostra altri file modificati**, fermati e avvisa il SUPERVISORE: vuol
dire che l'esclusione non ha funzionato come previsto.

---

## Passo 3 — Cosa va adattato a mano

Due file sono versionati **anche se i tool li riscrivono**: `AGENTS.md` e `CLAUDE.md`.
Sono il punto d'ingresso per gli agenti AI, quindi devono esserci già al clone. Il prezzo è
che contengono un percorso assoluto, e quel percorso è di chi li ha generati.

**L'unica cosa da adattare, oggi, è questa:**

| File | Righe | Contenuto | Cosa fare |
|---|---|---|---|
| `AGENTS.md` | 52 e 75 | `python "C:\software\cerebro\scripts\query_qdrant.py" …` | Sostituisci il percorso con quello del **tuo** CEREBRO |
| `CLAUDE.md` | 54 e 77 | la stessa riga (il file è una copia di `AGENTS.md` più l'import `@AGENTS.md`) | Idem |

Sono 2 righe per file, la versione italiana e quella inglese dello stesso comando.
Trovale così:

```bash
grep -n "software.cerebro" AGENTS.md CLAUDE.md
```

- **Il percorso coincide con il tuo** → non devi fare nulla. Passa al passo 4.
- **Il percorso è diverso** → correggilo nei due file.

> ⛔ **Non committare questa modifica.** È configurazione della tua macchina, non del
> progetto: se la mandi nel repository la imponi a tutti gli altri.
>
> Prima di ogni commit controlla, e se vedi quei due file modificati **solo** per il
> percorso, tienili fuori:
>
> ```bash
> git status --short
> git restore --staged AGENTS.md CLAUDE.md    # se li hai già aggiunti
> ```
>
> Usa sempre `git add <file>` mirato. **Mai `git add -A` o `git add .`**: è il modo più
> rapido per spedire la propria configurazione a tutti.

**Perché questi due file sì e gli altri no.** La differenza non è «chi li scrive» ma
**quanto è rivedibile il diff**: qui sono 2 righe che saltano all'occhio in una pull request;
in `FRAMEWORK_MANUAL.md` sarebbero oltre mille, impossibili da controllare. Per questo quelli
non sono versionati affatto (passo 2) e questi sì.

**[N] Soluzione definitiva:** l'attrito sparisce con il task **H-309**, che introduce
`config/helios.local.json` (un file per utente, mai versionato) e un modulo di bootstrap che
legge i percorsi da lì. Fino ad allora la correzione resta manuale, e va rifatta dopo ogni
`limet.ps1` o `register_project.py instructions`.

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

> ⚠️ **`register_project.py instructions` rigenera i blocchi dentro `AGENTS.md` e
> `CLAUDE.md`**, che sono versionati. Non è vietato — serve se vuoi i blocchi aggiornati —
> ma dopo averlo eseguito **ricontrolla `git status`** e non committare le righe con i tuoi
> percorsi (passo 3).

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
| Usare `git add -A` o `git add .` | Spedisce nel repository la tua configurazione locale. Aggiungi sempre file per file |
| Eseguire `limet.ps1` senza `-Lang it` | Il default è `en`: riscriverebbe in inglese i blocchi di `AGENTS.md` e `CLAUDE.md`, che sono versionati |
| Committare `AGENTS.md`/`CLAUDE.md` con i **tuoi** percorsi | È configurazione locale, non di progetto (passo 3) |
| Toccare le collection Qdrant di COSMO o mudeopen | Vincolo del SUPERVISORE; usa solo `CRB_helios` |
| Cancellare una collection Qdrant | Mai, per nessun motivo |
| Far eseguire build, test, commit, push o merge a un agente | `docs/CONSTITUTION.md`, non negoziabile n.1 |
| Committare su `main` | Il merge su `main` lo fa solo il SUPERVISORE |
| Versionare file che un tool rigenera | È la regola che rende sicuro il passo 2. Se un tool sa ricrearlo, sta in `.gitignore` |
| Committare file `~$*.xlsx` | Sono lock temporanei di Excel; già esclusi da `.gitignore` |

---

## Se qualcosa non torna

1. **`git status` mostra modifiche che non hai fatto tu** — se sono `AGENTS.md` e
   `CLAUDE.md`, è normale dopo il passo 2: vedi il passo 3. Se sono **altri file**,
   fermati e avvisa il SUPERVISORE.
2. **Ricerca CEREBRO a vuoto** — rilancia `ingest_docs.py` e controlla che Qdrant e Ollama
   rispondano (passo 0).
3. **`limet/` sembra incompleta** — mancano manuale, checklist o `templates/`? Non è un
   errore del clone: esegui il passo 2.
4. In ogni altro caso: **fermati e chiedi al SUPERVISORE.** Non improvvisare.
