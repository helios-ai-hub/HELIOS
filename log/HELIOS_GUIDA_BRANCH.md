# HELIOS — Guida ai branch per NARO e SCIBETTA

> Guida operativa, passo-passo, generata dall'Excel `HELIOS_Distribuzione_task_1.xlsx` (fogli *Pacchetti*, *Pre-GO*, *Post-GO*).
> **Proposta [P]**: la convenzione dei branch è marcata `[N]` nell'Excel e va approvata dal SUPERVISORE.

## 0. Le 7 regole (leggile una volta, poi non sbagli)

1. **Un pacchetto (PK) = un branch.** Non un branch per task, non un branch per persona.
2. **Non lavorare mai su `dev` né su `mk1`.** Si lavora solo sul branch del proprio PK.
3. **Si parte sempre da `dev` aggiornato** (`git pull` prima di creare il branch).
4. **Non iniziare un PK se i suoi prerequisiti non sono già in `dev`** (campo *Parti solo se*). Il foglio `Pacchetti` lo chiama *Dipende da*.
5. **Tocca solo i percorsi del tuo PK** (campo *Percorsi*). Se `git status` mostra altro, fermati e leggi la sezione 6.
6. **Un commit per task**, con l'ID nel messaggio: `H-201: titolo del task`.
7. **Commit e push li fai tu**, mai l'agente (regola dell'Excel). Si entra in `dev` solo con **Pull Request** rivista dall'altra persona.

## 1. Setup una tantum

```powershell
# Vai nella cartella del tuo clone di HELIOS (esempio: la cartella di lavoro del progetto)
cd C:\Progetti\VSC\HELIOS-PLATFORM\HELIOS

git fetch origin
git switch dev
git pull origin dev
git branch --show-current     # deve stampare: dev
```

Se `git switch dev` dice che il branch non esiste: `git switch -c dev origin/dev`.

## 2. Come trovo il mio pacchetto nell'Excel (vale per tutti i branch)

1. Apri `HELIOS_Distribuzione_task_1.xlsx` → foglio **`Pacchetti`**.
2. Filtra la colonna **`Risorsa`** sul tuo nome (NARO o SCIBETTA).
3. Ordina per **`Onda`**. Prendi il primo pacchetto con `Stato` diverso da *Fatto* e i cui **`Dipende da (pacchetti)`** sono tutti *Fatto*. Quello è il tuo prossimo PK.
4. Colonna **`Task H-xxx`**: sono i task del pacchetto. Colonna **`Percorsi toccati`**: i soli file/cartelle che puoi modificare.
5. Apri il foglio **`Pre-GO`** (PK fino a PK-13) oppure **`Post-GO`** (dopo il GO), filtra la colonna **`Pacchetto`** sul PK scelto: ottieni le righe dei task. Lavorali **dall'alto in basso** (colonna `Ordine`).
6. Quando inizi: `Stato` = *In corso* sui task del PK e, nella colonna **`Branch di riferimento`**, scrivi il nome del branch. Quando finisci: `Stato` = *In review*, `% avanzamento` = 100%, compila `Commit (hash)` e `Fine`. A merge fatto: *Fatto*.

> Attenzione: la colonna `Ordine` si rinumera quando l'Excel viene rigenerato (è già successo tra le due versioni). **Fidati dell'ID `H-xxx` e del PK, non del numero d'ordine.**

## 3. Flusso standard di ogni pacchetto

Ogni sezione PK qui sotto contiene i comandi già pronti. Il flusso è sempre lo stesso:

1. `git switch dev` → `git pull origin dev` → `git switch -c <branch>`
2. Per ogni task, in ordine: lavori → `git status` (controlla i percorsi) → `git add -A` → `git commit -m "H-xxx: titolo"`
3. `git push -u origin <branch>`
4. Su GitHub apri la **Pull Request** con base **`dev`** (il link compare nell'output del push, oppure da *Pull requests → New*). Revisore: l'altra persona. Metodo di merge: **Create a merge commit** (non squash).
5. Dopo il merge: `git switch dev` → `git pull origin dev` → `git branch -d <branch>`

Se la PR segnala conflitti: `git fetch origin`, `git merge origin/dev`, risolvi, `git add -A`, `git commit`, `git push`. Se non sai risolverli, fermati e chiama l'altra persona: non forzare mai (`--force`).

Prima di ogni `git add -A` esegui `git status` e verifica che compaiano **solo** i percorsi indicati nel PK.

## 4. Sequenza di lavoro

### NARO

| # | Pacchetto | Branch | Onda | Task | Parti dopo |
|---|---|---|---|---|---|
| 1 | **PK-02** Inventario e ledger ECC | `naro/pk-02-ecc-ledger` | Onda 1 | H-201, H-202 | PK-00 |
| 2 | **PK-03** Revisione asset e matrice catena | `naro/pk-03-ecc-review-matrix` | Onda 1 | H-203, H-204 | PK-02 |
| 3 | **PK-07** Correzione percorso CEREBRO in CLAUDE.md | `naro/pk-07-cerebro-claude-md` | Onda 1 (dopo H-301) | H-307 | PK-04 |
| 4 | **PK-10** Gate umano meccanico sul PLAN | `naro/pk-10-human-gate` | Onda 2 | H-403 | PK-01 |
| 5 | **PK-11** Prova del plugin Claude Code (V13/V14) | `naro/pk-11-plugin-proof` | Onda 2 | H-407 | PK-01 |
| 6 | **PK-13** Rapporto go/no-go (compila NARO, decide il SUPERVISORE) | `naro/pk-13-go-nogo-report` | Onda 2 | H-406 | PK-08…PK-12 |
| 7 | **PK-15** Scheduler e regole comuni dei ruoli | `naro/pk-15-scheduler-common` | Onda 3 (dopo GO) | H-408, H-611 | GO; PK-08 |
| 8 | **PK-16** Porting asset ECC | `naro/pk-16-ecc-porting` | Onda 4 (dopo GO) | H-501, H-502, H-503 | PK-03, PK-14 |
| 9 | **PK-18** Ruoli di controllo | `naro/pk-18-control-roles` | Onda 4 (dopo GO) | H-606, H-607, H-608, H-609, H-610, H-601 | PK-14, PK-15, PK-09 |
| 10 | **PK-26** UI (viste, permessi, grafica, npm) e sostituzione launcher | `naro/pk-26-ui-views` | Onda 4+ (dopo GO) | H-8b03, H-8b04, H-8b06, H-8b07, H-8b08 | PK-25, PK-08, PK-06 |
| 11 | **PK-20** Stato/eventi, report e gate di merge | `naro/pk-20-state-report-gate` | Onda 5 (dopo GO) | H-706, H-709, H-711 | PK-14…PK-18, H-407 |
| 12 | **PK-22** Installer e modifiche documentali a LIMET | `naro/pk-22-installer-limet-docs` | Onda 5 (dopo GO) | H-702, H-712 | H-407; H-712 dopo H-407 (ALA §7) |
| 13 | **PK-26b** Gate di configurazione all'avvio della dashboard | `naro/pk-26b-config-gate` | Onda 4+ (dopo GO) | H-8b09 | PK-07b (H-309), PK-25 (H-8b01), PK-26 (H-8b03) |
| 14 | **PK-21** Valutazione rischi di prodotto (C28) | `naro/pk-21-risk-assessment` | Onda 6 (dopo GO) | H-612 | PK-19 (H-701), PK-20 (H-706), H-308 |
| 15 | **PK-20b** Registro delle decisioni autonome e report notturno | `naro/pk-20b-autonomous-log` | Onda 5+ (dopo GO) | H-714 | PK-19b (H-713), PK-20 (H-709) |
| 16 | **PK-26c** Pannello di avvio del run non presidiato | `naro/pk-26c-unattended-panel` | Onda 5+ (dopo GO) | H-8b10 | PK-19b (H-713), PK-20b (H-714), PK-26 (H-8b03), PK-26b (H-8b09) |
| 17 | **PK-28** README plugin e manuali | `naro/pk-28-readme-manuals` | Onda 7 (dopo GO) | H-902, H-1101 | PK-27 |
| 18 | **PK-30** Rollout sul pilota | `naro/pk-30-pilot-rollout` | Onda 9 (dopo GO) | H-1201 | PK-28, PK-29 |

### SCIBETTA

| # | Pacchetto | Branch | Onda | Task | Parti dopo |
|---|---|---|---|---|---|
| 1 | **PK-01** Contratti JSON | `scibetta/pk-01-contracts` | Onda 1 | H-303 | PK-00 |
| 2 | **PK-05** CI a matrice (3 OS) | `scibetta/pk-05-ci-matrix` | Onda 1 | H-306 | PK-00 (H-103) — avviabile subito |
| 3 | **PK-05b** Segreti fuori dal repo | `scibetta/pk-05b-secrets` | Onda 1 (dopo H-301) | H-302 | PK-04 (H-301), PK-05 |
| 4 | **PK-06** Shim compat e test confini | `scibetta/pk-06-compat-boundaries` | Onda 1 (dopo H-301) | H-304, H-305 | PK-04 (H-301) |
| 5 | **PK-07b** Configurazione locale non versionata | `scibetta/pk-07b-local-config` | Onda 1 (dopo H-301) | H-309 | PK-04 (H-301), PK-05b (.gitignore), PK-01 (contratti) |
| 6 | **PK-08** Runner con allowlist e policy permessi | `scibetta/pk-08-runner-policy` | Onda 2 | H-401, H-404 | PK-01, PK-05, PK-05b, PK-06, PK-07 (Fase 3 chiusa) |
| 7 | **PK-09** Ruoli PoC (analyst, developer, reviewer) | `scibetta/pk-09-roles-poc` | Onda 2 | H-402 | PK-08 (H-404), PK-01 |
| 8 | **PK-12** Asset ECC portato + A/B | `scibetta/pk-12-ecc-ab` | Onda 2 | H-405 | PK-08, PK-09 |
| 9 | **PK-14** Contratti della catena e skill runner | `scibetta/pk-14-chain-contracts` | Onda 3 (dopo GO) | H-308, H-409 | GO; PK-08 |
| 10 | **PK-23** Hook ECC e registro chiamate LLM | `scibetta/pk-23-ecc-hooks-llm` | Onda 3+ (dopo GO) | H-800, H-800b, H-801, H-802 | GO; PK-08 |
| 11 | **PK-25** API loopback e read model | `scibetta/pk-25-api-loopback` | Onda 3+ (dopo GO) | H-8b01, H-8b02, H-8b05 | GO; PK-06, PK-08 |
| 12 | **PK-17** Ruoli di sviluppo | `scibetta/pk-17-dev-roles` | Onda 4 (dopo GO) | H-602, H-603, H-604, H-605 | PK-14, PK-15, PK-09 |
| 13 | **PK-19** Workflow core e permessi tool | `scibetta/pk-19-workflow-core` | Onda 5 (dopo GO) | H-701, H-703, H-704, H-705, H-707, H-708, H-710 | PK-14…PK-18, H-407 |
| 14 | **PK-19b** Modalità non presidiata: motore e allow-list dei ruoli | `scibetta/pk-19b-unattended` | Onda 5+ (dopo GO) | H-713, H-715 | PK-19 (H-707, H-708), PK-20 (H-706), PK-21 (H-612) |
| 15 | **PK-24** Knowledge base HLS_* e accesso agenti | `scibetta/pk-24-kb-hls` | Onda 6 (dopo GO) | H-803, H-804 | PK-03, PK-15 |
| 16 | **PK-27** Scenari E2E V1–V24 | `scibetta/pk-27-e2e` | Onda 7 (dopo GO) | H-901 | Fasi 5–8 |
| 17 | **PK-29** Hardening | `scibetta/pk-29-hardening` | Onda 8 (dopo GO) | H-1001 | PK-27 |

**SUPERVISORE** (non toccare): PK-00 (chiuso) e **PK-04** (import di `limet/` e `cerebro/` in `dev`, commit/push del SUPERVISORE). Finché PK-04 non è in `dev`, non partono PK-05b, PK-06, PK-07, PK-07b.

**Gate GO/NO-GO**: il PK-13 (NARO) chiude le Onde 0–2. I pacchetti dall'**Onda 3 in poi** partono solo dopo la decisione **GO** del SUPERVISORE.

## 5. Pacchetti, uno per uno

# NARO

### PK-02 · Inventario e ledger ECC

- **Branch:** `naro/pk-02-ecc-ledger`
- **Risorsa:** NARO · **Onda 1** · Step `N1`
- **Parti solo se in `dev` ci sono già:** PK-00
- **Puoi lavorare in parallelo con:** PK-01, PK-04
- **Percorsi che puoi toccare:** agents/tools/inventory_ecc.py, agents/assets/ecc-ledger.json, NOTICE
- **Test da far passare:** T-201, T-202

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-02** (`Risorsa` = NARO).
2. Foglio `Pre-GO` → filtra `Pacchetto` = **PK-02** → trovi: H-201 (Ordine 5), H-202 (Ordine 6).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 5 | **H-201** Script di inventario ECC | Leggere il 100% dei file ECC e contarli | H-101 [P] |
| 6 | **H-202** Ledger licenze + allowlist MIT | Ogni asset con hash/licenza/origine; non-MIT esclusi | H-201 [P] |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c naro/pk-02-ecc-ledger

# 3) Task H-201 - Script di inventario ECC  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-201: Script di inventario ECC"

# 3) Task H-202 - Ledger licenze + allowlist MIT  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-202: Ledger licenze + allowlist MIT"

# 4) Pubblica il branch
git push -u origin naro/pk-02-ecc-ledger

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d naro/pk-02-ecc-ledger
```

> **Note dal foglio:** Owner di agents/assets/ = NARO.

---

### PK-03 · Revisione asset e matrice catena

- **Branch:** `naro/pk-03-ecc-review-matrix`
- **Risorsa:** NARO · **Onda 1** · Step `N2`
- **Parti solo se in `dev` ci sono già:** PK-02
- **Puoi lavorare in parallelo con:** PK-01, PK-05, PK-06
- **Percorsi che puoi toccare:** docs/ecc-review.md, docs/chain-matrix.md, agents/assets/chain-matrix.json
- **Test da far passare:** T-204

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-03** (`Risorsa` = NARO).
2. Foglio `Pre-GO` → filtra `Pacchetto` = **PK-03** → trovi: H-203 (Ordine 7), H-204 (Ordine 8).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 7 | **H-203** Revisione manuale asset in allowlist | Checklist firmata su ogni asset ammesso | H-202 [P] |
| 8 | **H-204** Matrice catena | Assegnare 71 agenti + 311 skill a un ruolo o escluderli con motivo | H-201, H-202 [P] |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c naro/pk-03-ecc-review-matrix

# 3) Task H-203 - Revisione manuale asset in allowlist  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-203: Revisione manuale asset in allowlist"

# 3) Task H-204 - Matrice catena  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-204: Matrice catena"

# 4) Pubblica il branch
git push -u origin naro/pk-03-ecc-review-matrix

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d naro/pk-03-ecc-review-matrix
```

> **Note dal foglio:** Matrice da approvare dal SUPERVISORE.

---

### PK-07 · Correzione percorso CEREBRO in CLAUDE.md

- **Branch:** `naro/pk-07-cerebro-claude-md`
- **Risorsa:** NARO · **Onda 1 (dopo H-301)** · Step `N3`
- **Parti solo se in `dev` ci sono già:** PK-04
- **Puoi lavorare in parallelo con:** PK-05, PK-06
- **Percorsi che puoi toccare:** cerebro/CLAUDE.md [P: copia interna]

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-07** (`Risorsa` = NARO).
2. Foglio `Pre-GO` → filtra `Pacchetto` = **PK-07** → trovi: H-307 (Ordine 14).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 14 | **H-307** Correzione percorso CEREBRO in `CLAUDE.md` | Eliminare il riferimento `C:\Progetti\CEREBRO` nella copia interna | H-301 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c naro/pk-07-cerebro-claude-md

# 3) Task H-307 - Correzione percorso CEREBRO in `CLAUDE.md`  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-307: Correzione percorso CEREBRO in `CLAUDE.md`"

# 4) Pubblica il branch
git push -u origin naro/pk-07-cerebro-claude-md

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d naro/pk-07-cerebro-claude-md
```

> **Note dal foglio:** [N] percorso esatto del file nella copia importata.

---

### PK-10 · Gate umano meccanico sul PLAN

- **Branch:** `naro/pk-10-human-gate`
- **Risorsa:** NARO · **Onda 2** · Step `N4`
- **Parti solo se in `dev` ci sono già:** PK-01
- **Puoi lavorare in parallelo con:** PK-08, PK-09, PK-11
- **Percorsi che puoi toccare:** agents/workflow/gate.py
- **Test da far passare:** T-404

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-10** (`Risorsa` = NARO).
2. Foglio `Pre-GO` → filtra `Pacchetto` = **PK-10** → trovi: H-403 (Ordine 19).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 19 | **H-403** Gate umano meccanico sul PLAN | Nessun avanzamento senza approvazione umana | H-303 [P] |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c naro/pk-10-human-gate

# 3) Task H-403 - Gate umano meccanico sul PLAN  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-403: Gate umano meccanico sul PLAN"

# 4) Pubblica il branch
git push -u origin naro/pk-10-human-gate

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d naro/pk-10-human-gate
```

> **Note dal foglio:** Approvazione solo da sessione umana (C24).

---

### PK-11 · Prova del plugin Claude Code (V13/V14)

- **Branch:** `naro/pk-11-plugin-proof`
- **Risorsa:** NARO · **Onda 2** · Step `N5`
- **Parti solo se in `dev` ci sono già:** PK-01
- **Puoi lavorare in parallelo con:** PK-08, PK-09, PK-10
- **Percorsi che puoi toccare:** agents/plugin/
- **Test da far passare:** T-407

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-11** (`Risorsa` = NARO).
2. Foglio `Pre-GO` → filtra `Pacchetto` = **PK-11** → trovi: H-407 (Ordine 20).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 20 | **H-407** Prova del plugin Claude Code | Dimostrare registrazione e invocazione reale del workflow in sessione Claude Code (V13/V14) | H-103, H-303 [P] |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c naro/pk-11-plugin-proof

# 3) Task H-407 - Prova del plugin Claude Code  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-407: Prova del plugin Claude Code"

# 4) Pubblica il branch
git push -u origin naro/pk-11-plugin-proof

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d naro/pk-11-plugin-proof
```

> **Note dal foglio:** [N] sintassi Dynamic Workflow in Claude Code 2.1.311; se non riconosciuto: piano B in poc-report.md.

---

### PK-13 · Rapporto go/no-go (compila NARO, decide il SUPERVISORE)

- **Branch:** `naro/pk-13-go-nogo-report`
- **Risorsa:** NARO · **Onda 2** · Step `N6`
- **Parti solo se in `dev` ci sono già:** PK-08…PK-12
- **Puoi lavorare in parallelo con:** nessuno
- **Percorsi che puoi toccare:** docs/poc-report.md

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-13** (`Risorsa` = NARO).
2. Foglio `Pre-GO` → filtra `Pacchetto` = **PK-13** → trovi: H-406 (Ordine 22).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 22 | **H-406** Rapporto go/no-go | Giudicare i 10 criteri di R05 §5 | H-401…H-405, H-407 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c naro/pk-13-go-nogo-report

# 3) Task H-406 - Rapporto go/no-go  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-406: Rapporto go/no-go"

# 4) Pubblica il branch
git push -u origin naro/pk-13-go-nogo-report

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d naro/pk-13-go-nogo-report
```

> **Note dal foglio:** PUNTO DI DECISIONE: GO / NO-GO / piano B. Le Onde 3+ partono solo dopo GO.

---

### PK-15 · Scheduler e regole comuni dei ruoli

- **Branch:** `naro/pk-15-scheduler-common`
- **Risorsa:** NARO · **Onda 3 (dopo GO)** · Step `N7`
- **Parti solo se in `dev` ci sono già:** GO; PK-08
- **Puoi lavorare in parallelo con:** PK-14
- **Percorsi che puoi toccare:** agents/plugin/scheduler/, agents/plugin/agents/_common.md
- **Test da far passare:** T-408, T-610

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-15** (`Risorsa` = NARO).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-15** → trovi: H-408 (Ordine 25), H-611 (Ordine 26).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 25 | **H-408** Scheduler a funzioni pure (JS) | Decidere cosa parte e quando (onde, budget) | H-303, H-401 (gruppo A) |
| 26 | **H-611** `_common` e rules per ruolo | Regole condivise dei ruoli, «fermati e chiedi», regole KB §3.8 | H-303, H-401 (gruppo A) |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c naro/pk-15-scheduler-common

# 3) Task H-408 - Scheduler a funzioni pure (JS)  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-408: Scheduler a funzioni pure (JS)"

# 3) Task H-611 - `_common` e rules per ruolo  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-611: `_common` e rules per ruolo"

# 4) Pubblica il branch
git push -u origin naro/pk-15-scheduler-common

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d naro/pk-15-scheduler-common
```

> **Note dal foglio:** _common.md: owner NARO, sola lettura per SCIBETTA.

---

### PK-16 · Porting asset ECC

- **Branch:** `naro/pk-16-ecc-porting`
- **Risorsa:** NARO · **Onda 4 (dopo GO)** · Step `N8`
- **Parti solo se in `dev` ci sono già:** PK-03, PK-14
- **Puoi lavorare in parallelo con:** PK-17
- **Percorsi che puoi toccare:** agents/assets/ (ecc-*), test di agnosticità
- **Test da far passare:** T-501, T-502, T-503

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-16** (`Risorsa` = NARO).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-16** → trovi: H-501 (Ordine 27), H-502 (Ordine 28), H-503 (Ordine 29).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 27 | **H-501** Test di agnosticità statico | Nessun hook/tool/memoria/`.claude/plans` negli asset | H-204, H-308 |
| 28 | **H-502** Porting per ruolo di rules e skill | Asset `ecc-*` con rules per ruolo | H-204, H-308 |
| 29 | **H-503** Nessun file di piano ECC / collisioni | Proposte in `ecosystem/proposals/`, mai in file di piano | H-204, H-308 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c naro/pk-16-ecc-porting

# 3) Task H-501 - Test di agnosticità statico  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-501: Test di agnosticità statico"

# 3) Task H-502 - Porting per ruolo di rules e skill  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-502: Porting per ruolo di rules e skill"

# 3) Task H-503 - Nessun file di piano ECC / collisioni  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-503: Nessun file di piano ECC / collisioni"

# 4) Pubblica il branch
git push -u origin naro/pk-16-ecc-porting

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d naro/pk-16-ecc-porting
```

> **Note dal foglio:** Aspetta H-308 (PK-14).

---

### PK-18 · Ruoli di controllo

- **Branch:** `naro/pk-18-control-roles`
- **Risorsa:** NARO · **Onda 4 (dopo GO)** · Step `N9`
- **Parti solo se in `dev` ci sono già:** PK-14, PK-15, PK-09
- **Puoi lavorare in parallelo con:** PK-17
- **Percorsi che puoi toccare:** agents/plugin/agents/{test-reader,reviewer,validator,metrics-observer,e2e-designer,e2e-reader}.md, agents/metrics/
- **Test da far passare:** T-605, T-606, T-607, T-608, T-609, T-403

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-18** (`Risorsa` = NARO).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-18** → trovi: H-606 (Ordine 34), H-607 (Ordine 35), H-608 (Ordine 36), H-609 (Ordine 37), H-610 (Ordine 38), H-601 (Ordine 39).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 34 | **H-606** Ruolo `test-reader` (haiku) | Classifica i fallimenti con estratti reali | gruppo A, H-402 |
| 35 | **H-607** Ruolo `reviewer` (opus ≠ developer) | Findings con severità/file/riga; non corregge | gruppo A, H-402 |
| 36 | **H-608** Ruolo `validator` (opus) | conforme/non conforme/non determinabile con evidenze | gruppo A, H-402 |
| 37 | **H-609** `metrics-observer` + aggregatore | Interpreta solo metriche già aggregate | gruppo A, H-402 |
| 38 | **H-610** Ruoli `e2e-designer`/`e2e-reader` | E2E Playwright eseguiti dal runner | gruppo A, H-402 |
| 39 | **H-601** Reviewer specializzati ECC | Lenti `ecc-*` riusate con overlay minimo (estende H-607) | gruppo A, H-402, H-607 [P] |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c naro/pk-18-control-roles

# 3) Task H-606 - Ruolo `test-reader` (haiku)  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-606: Ruolo `test-reader` (haiku)"

# 3) Task H-607 - Ruolo `reviewer` (opus ≠ developer)  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-607: Ruolo `reviewer` (opus ≠ developer)"

# 3) Task H-608 - Ruolo `validator` (opus)  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-608: Ruolo `validator` (opus)"

# 3) Task H-609 - `metrics-observer` + aggregatore  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-609: `metrics-observer` + aggregatore"

# 3) Task H-610 - Ruoli `e2e-designer`/`e2e-reader`  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-610: Ruoli `e2e-designer`/`e2e-reader`"

# 3) Task H-601 - Reviewer specializzati ECC  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-601: Reviewer specializzati ECC"

# 4) Pubblica il branch
git push -u origin naro/pk-18-control-roles

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d naro/pk-18-control-roles
```

> **Note dal foglio:** Stessa cartella di PK-17 ma file distinti.

---

### PK-26 · UI (viste, permessi, grafica, npm) e sostituzione launcher

- **Branch:** `naro/pk-26-ui-views`
- **Risorsa:** NARO · **Onda 4+ (dopo GO)** · Step `N13`
- **Parti solo se in `dev` ci sono già:** PK-25, PK-08, PK-06
- **Puoi lavorare in parallelo con:** PK-25 (dopo contratto API)
- **Percorsi che puoi toccare:** ui/, [N] shim launcher :8799
- **Test da far passare:** T-8b06, T-405, T-8b07, T-8b09, T-8b10, T-304

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-26** (`Risorsa` = NARO).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-26** → trovi: H-8b03 (Ordine 65), H-8b04 (Ordine 66), H-8b06 (Ordine 67), H-8b07 (Ordine 68), H-8b08 (Ordine 69).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 65 | **H-8b03** `ui/` viste | Portfolio, LIMET, HELIOS, CEREBRO, Criticità | H-8b02 |
| 66 | **H-8b04** Vista Permessi agenti | Conferma, motivazione, audit, rollback | H-404, H-8b03 |
| 67 | **H-8b06** Grafica e accessibilità | SVG/Canvas, WCAG 2.1 AA, budget prestazioni (C26) | H-8b03 |
| 68 | **H-8b07** Governance dipendenze npm | Lockfile, licenze, audit, SBOM (C25) | H-8b03 |
| 69 | **H-8b08** Sostituzione launcher :8799 | Tramite shim | H-304, H-8b03 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c naro/pk-26-ui-views

# 3) Task H-8b03 - `ui/` viste  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-8b03: `ui/` viste"

# 3) Task H-8b04 - Vista Permessi agenti  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-8b04: Vista Permessi agenti"

# 3) Task H-8b06 - Grafica e accessibilità  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-8b06: Grafica e accessibilità"

# 3) Task H-8b07 - Governance dipendenze npm  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-8b07: Governance dipendenze npm"

# 3) Task H-8b08 - Sostituzione launcher :8799  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-8b08: Sostituzione launcher :8799"

# 4) Pubblica il branch
git push -u origin naro/pk-26-ui-views

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d naro/pk-26-ui-views
```

> **Note dal foglio:** La UI non esegue build/test/git (C23–C24).

---

### PK-20 · Stato/eventi, report e gate di merge

- **Branch:** `naro/pk-20-state-report-gate`
- **Risorsa:** NARO · **Onda 5 (dopo GO)** · Step `N10`
- **Parti solo se in `dev` ci sono già:** PK-14…PK-18, H-407
- **Puoi lavorare in parallelo con:** PK-19
- **Percorsi che puoi toccare:** [N] file di stato, report e gate di merge (da fissare in H-308/H-701)
- **Test da far passare:** T-706, T-709, T-711

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-20** (`Risorsa` = NARO).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-20** → trovi: H-706 (Ordine 47), H-709 (Ordine 48), H-711 (Ordine 49).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 47 | **H-706** Stato ed eventi | `state.json`/`events.jsonl`, `STALE` dopo merge | gruppi A e C, H-407 |
| 48 | **H-709** Report notturno/conformità/finale | Mai «completato» se incompleto; sezione rischi H-612 | gruppi A e C, H-407 |
| 49 | **H-711** Gate di merge R13 + bug-fix R15 | Merge solo con tutti i gate; regressione fallisce prima del fix | gruppi A e C, H-407 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c naro/pk-20-state-report-gate

# 3) Task H-706 - Stato ed eventi  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-706: Stato ed eventi"

# 3) Task H-709 - Report notturno/conformità/finale  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-709: Report notturno/conformità/finale"

# 3) Task H-711 - Gate di merge R13 + bug-fix R15  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-711: Gate di merge R13 + bug-fix R15"

# 4) Pubblica il branch
git push -u origin naro/pk-20-state-report-gate

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d naro/pk-20-state-report-gate
```

> **Note dal foglio:** Contatto: contratto state.json / events.jsonl di H-308. NARO non modifica limet-change.js.

---

### PK-22 · Installer e modifiche documentali a LIMET

- **Branch:** `naro/pk-22-installer-limet-docs`
- **Risorsa:** NARO · **Onda 5 (dopo GO)** · Step `N12`
- **Parti solo se in `dev` ci sono già:** H-407; H-712 dopo H-407 (ALA §7)
- **Puoi lavorare in parallelo con:** PK-19
- **Percorsi che puoi toccare:** scripts/limet-agents.ps1/.sh, limet/ (documentazione) [P]
- **Test da far passare:** T-702, T-712

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-22** (`Risorsa` = NARO).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-22** → trovi: H-702 (Ordine 54), H-712 (Ordine 55).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 54 | **H-702** Installer `limet-agents.ps1/.sh` | `init\|update` idempotente, plugin facoltativo | gruppi A e C, H-407 |
| 55 | **H-712** Modifiche documentali a LIMET M.1–M.9 | Ciclo LIMET IT/EN; re-index a cura dell'utente | nessuna in IN; [P] dopo H-407 (ALA §7) |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c naro/pk-22-installer-limet-docs

# 3) Task H-702 - Installer `limet-agents.ps1/.sh`  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-702: Installer `limet-agents.ps1/.sh`"

# 3) Task H-712 - Modifiche documentali a LIMET M.1–M.9  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-712: Modifiche documentali a LIMET M.1–M.9"

# 4) Pubblica il branch
git push -u origin naro/pk-22-installer-limet-docs

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d naro/pk-22-installer-limet-docs
```

> **Note dal foglio:** Ciclo LIMET IT/EN; re-index a cura del SUPERVISORE.

---

### PK-26b · Gate di configurazione all'avvio della dashboard

- **Branch:** `naro/pk-26b-config-gate`
- **Risorsa:** NARO · **Onda 4+ (dopo GO)** · Step `N13b`
- **Parti solo se in `dev` ci sono già:** PK-07b (H-309), PK-25 (H-8b01), PK-26 (H-8b03)
- **Puoi lavorare in parallelo con:** nessuno
- **Percorsi che puoi toccare:** api/ (gate), ui/ (dialog di prima configurazione)
- **Test da far passare:** T-8b11

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-26b** (`Risorsa` = NARO).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-26b** → trovi: H-8b09 (Ordine 70).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 70 | **H-8b09** Gate di configurazione all'avvio della dashboard | Nessuna operazione senza configurazione valida; dialog di prima configurazione | H-309, H-8b01, H-8b03 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c naro/pk-26b-config-gate

# 3) Task H-8b09 - Gate di configurazione all'avvio della dashboard  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-8b09: Gate di configurazione all'avvio della dashboard"

# 4) Pubblica il branch
git push -u origin naro/pk-26b-config-gate

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d naro/pk-26b-config-gate
```

> **Note dal foglio:** Il gate chiama il modulo di H-309: nessuna logica duplicata. Non aggirabile da chiamata diretta all'API (C23). Verifica: T-8b11, T-8b12, E2E-CFG-1.

---

### PK-21 · Valutazione rischi di prodotto (C28)

- **Branch:** `naro/pk-21-risk-assessment`
- **Risorsa:** NARO · **Onda 6 (dopo GO)** · Step `N11`
- **Parti solo se in `dev` ci sono già:** PK-19 (H-701), PK-20 (H-706), H-308
- **Puoi lavorare in parallelo con:** PK-23, PK-24, PK-25
- **Percorsi che puoi toccare:** agents/plugin/agents/risk-assessor.md, RISK-<task>.json
- **Test da far passare:** T-611

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-21** (`Risorsa` = NARO).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-21** → trovi: H-612 (Ordine 53).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 53 | **H-612** Valutazione rischi post-task (prodotto) | `risk-assessor` al passaggio a VERIFIED (C28), `RISK-<task>.json` + `QUESTIONS.md` | H-308, H-701, H-706 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c naro/pk-21-risk-assessment

# 3) Task H-612 - Valutazione rischi post-task (prodotto)  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-612: Valutazione rischi post-task (prodotto)"

# 4) Pubblica il branch
git push -u origin naro/pk-21-risk-assessment

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d naro/pk-21-risk-assessment
```

> **Note dal foglio:** risk-assessor ≠ developer, sola lettura.

---

### PK-20b · Registro delle decisioni autonome e report notturno

- **Branch:** `naro/pk-20b-autonomous-log`
- **Risorsa:** NARO · **Onda 5+ (dopo GO)** · Step `N10b`
- **Parti solo se in `dev` ci sono già:** PK-19b (H-713), PK-20 (H-709)
- **Puoi lavorare in parallelo con:** PK-19b
- **Percorsi che puoi toccare:** [N] registro decisioni autonome, REPORT_NOTTURNO.md (sezione «decisioni autonome»)
- **Test da far passare:** T-714, T-1

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-20b** (`Risorsa` = NARO).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-20b** → trovi: H-714 (Ordine 52).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 52 | **H-714** Registro delle decisioni autonome | Ogni decisione presa in autonomia registrata con evidenza e reversibilità; sezione «decisioni autonome» in `REPORT_NOTTURNO.md` | H-709, H-713 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c naro/pk-20b-autonomous-log

# 3) Task H-714 - Registro delle decisioni autonome  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-714: Registro delle decisioni autonome"

# 4) Pubblica il branch
git push -u origin naro/pk-20b-autonomous-log

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d naro/pk-20b-autonomous-log
```

> **Note dal foglio:** ADR 0003 §8 (prova a posteriori, R18). Contatto: formato del registro definito in H-713. Verifica: T-714, E2E-AUT-1.

---

### PK-26c · Pannello di avvio del run non presidiato

- **Branch:** `naro/pk-26c-unattended-panel`
- **Risorsa:** NARO · **Onda 5+ (dopo GO)** · Step `N13c`
- **Parti solo se in `dev` ci sono già:** PK-19b (H-713), PK-20b (H-714), PK-26 (H-8b03), PK-26b (H-8b09)
- **Puoi lavorare in parallelo con:** nessuno
- **Percorsi che puoi toccare:** api/ (avvio run non presidiato), ui/ (pannello perimetro, limiti, kill switch)
- **Test da far passare:** T-8b13, T-2

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-26c** (`Risorsa` = NARO).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-26c** → trovi: H-8b10 (Ordine 71).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 71 | **H-8b10** Avvio del run non presidiato dalla dashboard | Perimetro e limiti mostrati prima dell'avvio; avvio solo da sessione umana (C24); kill switch raggiungibile durante il run | H-713, H-714, H-715, H-8b05 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c naro/pk-26c-unattended-panel

# 3) Task H-8b10 - Avvio del run non presidiato dalla dashboard  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-8b10: Avvio del run non presidiato dalla dashboard"

# 4) Pubblica il branch
git push -u origin naro/pk-26c-unattended-panel

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d naro/pk-26c-unattended-panel
```

> **Note dal foglio:** C24: il run non presidiato lo avvia SEMPRE una sessione umana. Mostra perimetro e limiti prima dell'avvio; kill switch raggiungibile a run in corso. Verifica: T-8b13, E2E-AUT-2.

---

### PK-28 · README plugin e manuali

- **Branch:** `naro/pk-28-readme-manuals`
- **Risorsa:** NARO · **Onda 7 (dopo GO)** · Step `N14`
- **Parti solo se in `dev` ci sono già:** PK-27
- **Puoi lavorare in parallelo con:** PK-29
- **Percorsi che puoi toccare:** agents/plugin/README.md, docs/ (manuali, NOTICE, runbook)

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-28** (`Risorsa` = NARO).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-28** → trovi: H-902 (Ordine 73), H-1101 (Ordine 74).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 73 | **H-902** README e troubleshooting plugin | Installazione, configurazione, errori | H-901 [P] |
| 74 | **H-1101** Manuali, NOTICE, runbook | Documentazione finale | H-901 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c naro/pk-28-readme-manuals

# 3) Task H-902 - README e troubleshooting plugin  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-902: README e troubleshooting plugin"

# 3) Task H-1101 - Manuali, NOTICE, runbook  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-1101: Manuali, NOTICE, runbook"

# 4) Pubblica il branch
git push -u origin naro/pk-28-readme-manuals

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d naro/pk-28-readme-manuals
```

> **Note dal foglio:** Revisione approvata dal SUPERVISORE.

---

### PK-30 · Rollout sul pilota

- **Branch:** `naro/pk-30-pilot-rollout`
- **Risorsa:** NARO · **Onda 9 (dopo GO)** · Step `N15`
- **Parti solo se in `dev` ci sono già:** PK-28, PK-29
- **Puoi lavorare in parallelo con:** nessuno
- **Percorsi che puoi toccare:** docs/ (metriche, rollback)

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-30** (`Risorsa` = NARO).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-30** → trovi: H-1201 (Ordine 76).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 76 | **H-1201** Rollout sul pilota | Metriche, rollback, parere legale | H-1001, H-1101 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c naro/pk-30-pilot-rollout

# 3) Task H-1201 - Rollout sul pilota  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-1201: Rollout sul pilota"

# 4) Pubblica il branch
git push -u origin naro/pk-30-pilot-rollout

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d naro/pk-30-pilot-rollout
```

> **Note dal foglio:** [N] parere legale; pilota da scegliere.

---

# SCIBETTA

### PK-01 · Contratti JSON

- **Branch:** `scibetta/pk-01-contracts`
- **Risorsa:** SCIBETTA · **Onda 1** · Step `S1`
- **Parti solo se in `dev` ci sono già:** PK-00
- **Puoi lavorare in parallelo con:** PK-02, PK-03, PK-04
- **Percorsi che puoi toccare:** agents/contracts/
- **Test da far passare:** T-303

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-01** (`Risorsa` = SCIBETTA).
2. Foglio `Pre-GO` → filtra `Pacchetto` = **PK-01** → trovi: H-303 (Ordine 4).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 4 | **H-303** Contratti §3.3 come schemi JSON | Definire il linguaggio comune tra ruoli e runner | H-103 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c scibetta/pk-01-contracts

# 3) Task H-303 - Contratti §3.3 come schemi JSON  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-303: Contratti §3.3 come schemi JSON"

# 4) Pubblica il branch
git push -u origin scibetta/pk-01-contracts

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d scibetta/pk-01-contracts
```

> **Note dal foglio:** Congela gli schemi prima di Fase 4: modifiche successive solo da SCIBETTA (owner di agents/contracts/).

---

### PK-05 · CI a matrice (3 OS)

- **Branch:** `scibetta/pk-05-ci-matrix`
- **Risorsa:** SCIBETTA · **Onda 1** · Step `S2`
- **Parti solo se in `dev` ci sono già:** PK-00 (H-103) — avviabile subito
- **Puoi lavorare in parallelo con:** PK-02, PK-03
- **Percorsi che puoi toccare:** .github/workflows/ci.yml
- **Test da far passare:** T-306

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-05** (`Risorsa` = SCIBETTA).
2. Foglio `Pre-GO` → filtra `Pacchetto` = **PK-05** → trovi: H-306 (Ordine 10).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 10 | **H-306** CI a matrice | Stessa suite su windows/ubuntu/macos | H-103 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c scibetta/pk-05-ci-matrix

# 3) Task H-306 - CI a matrice  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-306: CI a matrice"

# 4) Pubblica il branch
git push -u origin scibetta/pk-05-ci-matrix

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d scibetta/pk-05-ci-matrix
```

> **Note dal foglio:** Dipende solo da H-103 (TASK_LIST). Va chiuso prima di PK-05b: entrambi toccano ci.yml, stessa persona.

---

### PK-05b · Segreti fuori dal repo

- **Branch:** `scibetta/pk-05b-secrets`
- **Risorsa:** SCIBETTA · **Onda 1 (dopo H-301)** · Step `S2b`
- **Parti solo se in `dev` ci sono già:** PK-04 (H-301), PK-05
- **Puoi lavorare in parallelo con:** PK-03, PK-06, PK-07
- **Percorsi che puoi toccare:** .gitignore, projects.example.json, .github/workflows/ci.yml (scansione segreti)
- **Test da far passare:** T-302

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-05b** (`Risorsa` = SCIBETTA).
2. Foglio `Pre-GO` → filtra `Pacchetto` = **PK-05b** → trovi: H-302 (Ordine 11).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 11 | **H-302** Segreti fuori dal repo | `projects.example.json`, `.gitignore` per `cache/` e dati Qdrant, scansione segreti in CI | H-301 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c scibetta/pk-05b-secrets

# 3) Task H-302 - Segreti fuori dal repo  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-302: Segreti fuori dal repo"

# 4) Pubblica il branch
git push -u origin scibetta/pk-05b-secrets

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d scibetta/pk-05b-secrets
```

> **Note dal foglio:** Dipende da H-301. Tocca ci.yml dopo PK-05 (stessa persona). [N] percorso dello script di scansione segreti.

---

### PK-06 · Shim compat e test confini

- **Branch:** `scibetta/pk-06-compat-boundaries`
- **Risorsa:** SCIBETTA · **Onda 1 (dopo H-301)** · Step `S3`
- **Parti solo se in `dev` ci sono già:** PK-04 (H-301)
- **Puoi lavorare in parallelo con:** PK-03, PK-07
- **Percorsi che puoi toccare:** compat/, tests/test_module_boundaries.py
- **Test da far passare:** T-304, T-305

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-06** (`Risorsa` = SCIBETTA).
2. Foglio `Pre-GO` → filtra `Pacchetto` = **PK-06** → trovi: H-304 (Ordine 12), H-305 (Ordine 13).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 12 | **H-304** Shim `compat/` | Mantenere invariati comandi legacy (`cerebro.sh`, `query_qdrant.py`, `ingest_docs.py`, `limet.ps1/.sh`, hook/MCP): stessi argomenti, porte, nomi collection | H-301 |
| 13 | **H-305** Test dei confini tra moduli | Evitare dipendenze incrociate non previste | H-301 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c scibetta/pk-06-compat-boundaries

# 3) Task H-304 - Shim `compat/`  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-304: Shim `compat/`"

# 3) Task H-305 - Test dei confini tra moduli  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-305: Test dei confini tra moduli"

# 4) Pubblica il branch
git push -u origin scibetta/pk-06-compat-boundaries

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d scibetta/pk-06-compat-boundaries
```

> **Note dal foglio:** Dipendono da H-301. Parità su Windows e Unix (C13).

---

### PK-07b · Configurazione locale non versionata

- **Branch:** `scibetta/pk-07b-local-config`
- **Risorsa:** SCIBETTA · **Onda 1 (dopo H-301)** · Step `S3b`
- **Parti solo se in `dev` ci sono già:** PK-04 (H-301), PK-05b (.gitignore), PK-01 (contratti)
- **Puoi lavorare in parallelo con:** PK-07
- **Percorsi che puoi toccare:** config/helios.local.example.json, config/helios.config.schema.json, modulo di bootstrap helios.config, .gitignore
- **Test da far passare:** T-309

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-07b** (`Risorsa` = SCIBETTA).
2. Foglio `Pre-GO` → filtra `Pacchetto` = **PK-07b** → trovi: H-309 (Ordine 15).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 15 | **H-309** Configurazione locale (`helios.local.json`) | Evitare che chi clona HELIOS erediti i percorsi e le configurazioni di un altro | H-301, H-303 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c scibetta/pk-07b-local-config

# 3) Task H-309 - Configurazione locale (`helios.local.json`)  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-309: Configurazione locale (`helios.local.json`)"

# 4) Pubblica il branch
git push -u origin scibetta/pk-07b-local-config

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d scibetta/pk-07b-local-config
```

> **Note dal foglio:** Modulo condiviso da dashboard, runner, CLI, installer e CI: non duplicare la logica nella UI. Tocca .gitignore dopo PK-05b (stessa persona). Attua C20; abilita PK-26b.

---

### PK-08 · Runner con allowlist e policy permessi

- **Branch:** `scibetta/pk-08-runner-policy`
- **Risorsa:** SCIBETTA · **Onda 2** · Step `S4`
- **Parti solo se in `dev` ci sono già:** PK-01, PK-05, PK-05b, PK-06, PK-07 (Fase 3 chiusa)
- **Puoi lavorare in parallelo con:** PK-10, PK-11
- **Percorsi che puoi toccare:** agents/runner/, agents/policy/policy.json, agents/policy/floor.json
- **Test da far passare:** T-401, T-402, T-405

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-08** (`Risorsa` = SCIBETTA).
2. Foglio `Pre-GO` → filtra `Pacchetto` = **PK-08** → trovi: H-401 (Ordine 16), H-404 (Ordine 17).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 16 | **H-401** Runner con allowlist | Eseguire solo operazioni ammesse, senza rete, con log hashato, senza commit/push | H-303, H-103 [P] |
| 17 | **H-404** Policy permessi con pavimento hard-deny | Impedire che permessi pericolosi siano rimovibili | H-401 [P] |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c scibetta/pk-08-runner-policy

# 3) Task H-401 - Runner con allowlist  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-401: Runner con allowlist"

# 3) Task H-404 - Policy permessi con pavimento hard-deny  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-404: Policy permessi con pavimento hard-deny"

# 4) Pubblica il branch
git push -u origin scibetta/pk-08-runner-policy

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d scibetta/pk-08-runner-policy
```

> **Note dal foglio:** Output del runner = contratto di PK-01 (sola lettura per NARO).

---

### PK-09 · Ruoli PoC (analyst, developer, reviewer)

- **Branch:** `scibetta/pk-09-roles-poc`
- **Risorsa:** SCIBETTA · **Onda 2** · Step `S5`
- **Parti solo se in `dev` ci sono già:** PK-08 (H-404), PK-01
- **Puoi lavorare in parallelo con:** PK-10, PK-11
- **Percorsi che puoi toccare:** agents/roles/*.md
- **Test da far passare:** T-403

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-09** (`Risorsa` = SCIBETTA).
2. Foglio `Pre-GO` → filtra `Pacchetto` = **PK-09** → trovi: H-402 (Ordine 18).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 18 | **H-402** Ruoli PoC | analyst, developer, reviewer con `tools:` senza Bash libero | H-404, H-303 [P] |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c scibetta/pk-09-roles-poc

# 3) Task H-402 - Ruoli PoC  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-402: Ruoli PoC"

# 4) Pubblica il branch
git push -u origin scibetta/pk-09-roles-poc

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d scibetta/pk-09-roles-poc
```

> **Note dal foglio:** Nessun Bash libero nei tools.

---

### PK-12 · Asset ECC portato + A/B

- **Branch:** `scibetta/pk-12-ecc-ab`
- **Risorsa:** SCIBETTA · **Onda 2** · Step `S6`
- **Parti solo se in `dev` ci sono già:** PK-08, PK-09
- **Puoi lavorare in parallelo con:** PK-11
- **Percorsi che puoi toccare:** tests/test_ab_metrics.py, [N] cartella dell'asset ecc-code-reviewer
- **Test da far passare:** T-406

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-12** (`Risorsa` = SCIBETTA).
2. Foglio `Pre-GO` → filtra `Pacchetto` = **PK-12** → trovi: H-405 (Ordine 21).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 21 | **H-405** Asset ECC portato + A/B | Portare un asset `ecc-*` e confrontarlo con la baseline | H-402, H-401 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c scibetta/pk-12-ecc-ab

# 3) Task H-405 - Asset ECC portato + A/B  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-405: Asset ECC portato + A/B"

# 4) Pubblica il branch
git push -u origin scibetta/pk-12-ecc-ab

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d scibetta/pk-12-ecc-ab
```

> **Note dal foglio:** D1–D10 approvati (Q-6); pilota in test\ (Q-5).

---

### PK-14 · Contratti della catena e skill runner

- **Branch:** `scibetta/pk-14-chain-contracts`
- **Risorsa:** SCIBETTA · **Onda 3 (dopo GO)** · Step `S7`
- **Parti solo se in `dev` ci sono già:** GO; PK-08
- **Puoi lavorare in parallelo con:** PK-15
- **Percorsi che puoi toccare:** agents/contracts/ (nuovi schemi), agents/plugin/skills/limet-runner/
- **Test da far passare:** T-308, T-409

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-14** (`Risorsa` = SCIBETTA).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-14** → trovi: H-308 (Ordine 23), H-409 (Ordine 24).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 23 | **H-308** Contratti della catena | Schemi `plan/result/findings/verdict/reading/merge/risk`, `state.json`, `events.jsonl`, stati R17 | H-303, H-401 (gruppo A) |
| 24 | **H-409** Skill `limet-runner` | Wrapper del runner H-401 verso il workflow | H-303, H-401 (gruppo A) |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c scibetta/pk-14-chain-contracts

# 3) Task H-308 - Contratti della catena  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-308: Contratti della catena"

# 3) Task H-409 - Skill `limet-runner`  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-409: Skill `limet-runner`"

# 4) Pubblica il branch
git push -u origin scibetta/pk-14-chain-contracts

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d scibetta/pk-14-chain-contracts
```

> **Note dal foglio:** Sblocca PK-16 e i ruoli (sync point).

---

### PK-23 · Hook ECC e registro chiamate LLM

- **Branch:** `scibetta/pk-23-ecc-hooks-llm`
- **Risorsa:** SCIBETTA · **Onda 3+ (dopo GO)** · Step `S10`
- **Parti solo se in `dev` ci sono già:** GO; PK-08
- **Puoi lavorare in parallelo con:** PK-14…PK-22
- **Percorsi che puoi toccare:** docs/ecc-hooks-analysis.md, agents/policy/llm-registry.json, [N] wrapper AgentShield
- **Test da far passare:** T-801, T-802

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-23** (`Risorsa` = SCIBETTA).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-23** → trovi: H-800 (Ordine 56), H-800b (Ordine 57), H-801 (Ordine 58), H-802 (Ordine 59).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 56 | **H-800** Analisi degli hook ECC | Decisione motivata per ognuno dei 27 hook | H-401 [P]; anticipabile in Fase 2 (IN §5) |
| 57 | **H-800b** Replica sicura hook utili | Hook HELIOS propri, mai codice ECC eseguito | H-800 |
| 58 | **H-801** Registro chiamate LLM (C05) | Ogni chiamata LLM extra registrata | H-401 [P] |
| 59 | **H-802** Wrapper AgentShield | Pin, hash, SBOM, senza rete/`--opus` | H-401 [P] |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c scibetta/pk-23-ecc-hooks-llm

# 3) Task H-800 - Analisi degli hook ECC  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-800: Analisi degli hook ECC"

# 3) Task H-800b - Replica sicura hook utili  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-800b: Replica sicura hook utili"

# 3) Task H-801 - Registro chiamate LLM (C05)  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-801: Registro chiamate LLM (C05)"

# 3) Task H-802 - Wrapper AgentShield  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-802: Wrapper AgentShield"

# 4) Pubblica il branch
git push -u origin scibetta/pk-23-ecc-hooks-llm

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d scibetta/pk-23-ecc-hooks-llm
```

> **Note dal foglio:** H-800 anticipabile in Fase 2 (IN §5). [N] Q-10 licenze AgentShield.

---

### PK-25 · API loopback e read model

- **Branch:** `scibetta/pk-25-api-loopback`
- **Risorsa:** SCIBETTA · **Onda 3+ (dopo GO)** · Step `S12`
- **Parti solo se in `dev` ci sono già:** GO; PK-06, PK-08
- **Puoi lavorare in parallelo con:** PK-26 (dopo contratto API)
- **Percorsi che puoi toccare:** api/
- **Test da far passare:** T-8b01, T-8b04, T-8b05, T-8b08

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-25** (`Risorsa` = SCIBETTA).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-25** → trovi: H-8b01 (Ordine 62), H-8b02 (Ordine 63), H-8b05 (Ordine 64).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 62 | **H-8b01** `api/` loopback e hardening | Token, Host/Origin, CSRF, CSP, limite body | H-303, H-401 [P] |
| 63 | **H-8b02** Read model derivato | Cache rigenerabile, fonte unica (C27) | H-8b01 |
| 64 | **H-8b05** Gate solo da sessione umana | La UI non esegue build/test/git | H-8b01, H-403 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c scibetta/pk-25-api-loopback

# 3) Task H-8b01 - `api/` loopback e hardening  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-8b01: `api/` loopback e hardening"

# 3) Task H-8b02 - Read model derivato  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-8b02: Read model derivato"

# 3) Task H-8b05 - Gate solo da sessione umana  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-8b05: Gate solo da sessione umana"

# 4) Pubblica il branch
git push -u origin scibetta/pk-25-api-loopback

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d scibetta/pk-25-api-loopback
```

> **Note dal foglio:** Contatto: contratto API/OpenAPI di H-8b01 da congelare prima di PK-26.

---

### PK-17 · Ruoli di sviluppo

- **Branch:** `scibetta/pk-17-dev-roles`
- **Risorsa:** SCIBETTA · **Onda 4 (dopo GO)** · Step `S8`
- **Parti solo se in `dev` ci sono già:** PK-14, PK-15, PK-09
- **Puoi lavorare in parallelo con:** PK-16, PK-18
- **Percorsi che puoi toccare:** agents/plugin/agents/{analyst,developer,tester,integrator}.md
- **Test da far passare:** T-601, T-602, T-603, T-604

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-17** (`Risorsa` = SCIBETTA).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-17** → trovi: H-602 (Ordine 30), H-603 (Ordine 31), H-604 (Ordine 32), H-605 (Ordine 33).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 30 | **H-602** Ruolo `analyst` (opus) | Produrre `plan.json`; non inventa requisiti | gruppo A, H-402 |
| 31 | **H-603** Ruolo `developer` ×N (sonnet) | Un task per worktree/ramo, mai build/test/push | gruppo A, H-402 |
| 32 | **H-604** Ruolo `tester` ×M (sonnet) | Test dal TEST_PLAN senza vedere il diff; mai Graphify | gruppo A, H-402 |
| 33 | **H-605** Ruolo `integrator` (sonnet) | Integra su `integration/NNNN`; mai merge su `main` | gruppo A, H-402 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c scibetta/pk-17-dev-roles

# 3) Task H-602 - Ruolo `analyst` (opus)  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-602: Ruolo `analyst` (opus)"

# 3) Task H-603 - Ruolo `developer` ×N (sonnet)  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-603: Ruolo `developer` ×N (sonnet)"

# 3) Task H-604 - Ruolo `tester` ×M (sonnet)  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-604: Ruolo `tester` ×M (sonnet)"

# 3) Task H-605 - Ruolo `integrator` (sonnet)  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-605: Ruolo `integrator` (sonnet)"

# 4) Pubblica il branch
git push -u origin scibetta/pk-17-dev-roles

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d scibetta/pk-17-dev-roles
```

> **Note dal foglio:** Stessa cartella di PK-18 ma file distinti.

---

### PK-19 · Workflow core e permessi tool

- **Branch:** `scibetta/pk-19-workflow-core`
- **Risorsa:** SCIBETTA · **Onda 5 (dopo GO)** · Step `S9`
- **Parti solo se in `dev` ci sono già:** PK-14…PK-18, H-407
- **Puoi lavorare in parallelo con:** PK-20, PK-22
- **Percorsi che puoi toccare:** agents/plugin/ (limet-change.js, [N] file per onde/gate), agents/policy/
- **Test da far passare:** T-701, T-703, T-704, T-705, T-707, T-708, T-710

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-19** (`Risorsa` = SCIBETTA).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-19** → trovi: H-701 (Ordine 40), H-703 (Ordine 41), H-704 (Ordine 42), H-705 (Ordine 43), H-707 (Ordine 44), H-708 (Ordine 45), H-710 (Ordine 46).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 40 | **H-701** Workflow `limet-change.js` | Struttura, validazione input, stati, retry, stop-e-chiedi | gruppi A e C, H-407 |
| 41 | **H-703** Gate umano sul grafo | Conferma di task/onde/stima prima del lancio | gruppi A e C, H-407 |
| 42 | **H-704** Orchestrazione per onde | Worktree e rami dedicati, parallelismo entro limiti | gruppi A e C, H-407 |
| 43 | **H-705** Integrazione su SHA verde | Avanza solo con run runner sullo stesso SHA | gruppi A e C, H-407 |
| 44 | **H-707** «Fermati e chiedi» non globale | Blocca solo i dipendenti; `QUESTIONS.md`; maxAttempts 3 | gruppi A e C, H-407 |
| 45 | **H-708** Budget, checkpoint, ripresa | Stop controllato e `resumeFromRunId` | gruppi A e C, H-407 |
| 46 | **H-710** Permessi a livello di tool | `deny` su mvnw/push/merge, hook di log propri (estende H-404) | gruppi A e C, H-407 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c scibetta/pk-19-workflow-core

# 3) Task H-701 - Workflow `limet-change.js`  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-701: Workflow `limet-change.js`"

# 3) Task H-703 - Gate umano sul grafo  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-703: Gate umano sul grafo"

# 3) Task H-704 - Orchestrazione per onde  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-704: Orchestrazione per onde"

# 3) Task H-705 - Integrazione su SHA verde  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-705: Integrazione su SHA verde"

# 3) Task H-707 - «Fermati e chiedi» non globale  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-707: «Fermati e chiedi» non globale"

# 3) Task H-708 - Budget, checkpoint, ripresa  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-708: Budget, checkpoint, ripresa"

# 3) Task H-710 - Permessi a livello di tool  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-710: Permessi a livello di tool"

# 4) Pubblica il branch
git push -u origin scibetta/pk-19-workflow-core

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d scibetta/pk-19-workflow-core
```

> **Note dal foglio:** [N] se limet-change.js è un file unico va scritto da una sola persona (SCIBETTA).

---

### PK-19b · Modalità non presidiata: motore e allow-list dei ruoli

- **Branch:** `scibetta/pk-19b-unattended`
- **Risorsa:** SCIBETTA · **Onda 5+ (dopo GO)** · Step `S9b`
- **Parti solo se in `dev` ci sono già:** PK-19 (H-707, H-708), PK-20 (H-706), PK-21 (H-612)
- **Puoi lavorare in parallelo con:** PK-20b
- **Percorsi che puoi toccare:** agents/plugin/ (sezione night di limits.json, macchina a stati, kill switch, dead-man switch), agents/policy/ (allow-list ruoli)
- **Test da far passare:** T-713, T-1, T-2, T-715

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-19b** (`Risorsa` = SCIBETTA).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-19b** → trovi: H-713 (Ordine 50), H-715 (Ordine 51).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 50 | **H-713** Modalità non presidiata (UNATTENDED) | Sezione `night` in `limits.json`, macchina a stati del run, parcheggio dei dipendenti, kill switch, dead-man switch | H-706, H-707, H-708, H-710 |
| 51 | **H-715** Allow-list dei ruoli non presidiati | Solo `developer`, `tester`, `runner`, `test-reader`, `reviewer`, `metrics-observer`; al turno di un ruolo escluso il task si parcheggia con motivazione | H-713 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c scibetta/pk-19b-unattended

# 3) Task H-713 - Modalità non presidiata (UNATTENDED)  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-713: Modalità non presidiata (UNATTENDED)"

# 3) Task H-715 - Allow-list dei ruoli non presidiati  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-715: Allow-list dei ruoli non presidiati"

# 4) Pubblica il branch
git push -u origin scibetta/pk-19b-unattended

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d scibetta/pk-19b-unattended
```

> **Note dal foglio:** ADR 0003. Neutralità dei permessi: la modalità non amplia MAI i permessi (C02, C24, pavimento H-404). Stessi file di PK-19: stessa persona. Verifica: T-713, T-715, E2E-AUT-1, E2E-AUT-2.

---

### PK-24 · Knowledge base HLS_* e accesso agenti

- **Branch:** `scibetta/pk-24-kb-hls`
- **Risorsa:** SCIBETTA · **Onda 6 (dopo GO)** · Step `S11`
- **Parti solo se in `dev` ci sono già:** PK-03, PK-15
- **Puoi lavorare in parallelo con:** PK-21
- **Percorsi che puoi toccare:** [N] script KB / MCP in sola lettura
- **Test da far passare:** T-803, T-804

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-24** (`Risorsa` = SCIBETTA).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-24** → trovi: H-803 (Ordine 60), H-804 (Ordine 61).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 60 | **H-803** Setup KB `HLS_*` | Crea `HLS_*` solo con autorizzazione esplicita del SUPERVISORE | H-202, H-204 [P] |
| 61 | **H-804** Accesso agenti alla KB | Solo MCP/CLI in sola lettura, eventi registrati, degrado senza contesto | H-803, H-611 [P] |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c scibetta/pk-24-kb-hls

# 3) Task H-803 - Setup KB `HLS_*`  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-803: Setup KB `HLS_*`"

# 3) Task H-804 - Accesso agenti alla KB  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-804: Accesso agenti alla KB"

# 4) Pubblica il branch
git push -u origin scibetta/pk-24-kb-hls

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d scibetta/pk-24-kb-hls
```

> **Note dal foglio:** HLS_* creata solo con autorizzazione esplicita (Q9); mai su collection esistenti.

---

### PK-27 · Scenari E2E V1–V24

- **Branch:** `scibetta/pk-27-e2e`
- **Risorsa:** SCIBETTA · **Onda 7 (dopo GO)** · Step `S13`
- **Parti solo se in `dev` ci sono già:** Fasi 5–8
- **Puoi lavorare in parallelo con:** PK-28
- **Percorsi che puoi toccare:** E2E_VERIFICATION.md, [N] repo pilota test\

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-27** (`Risorsa` = SCIBETTA).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-27** → trovi: H-901 (Ordine 72).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 72 | **H-901** Scenari E2E ALA V1–V24 | Catena completa su 3 OS, progetto pilota | Fasi 5–8 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c scibetta/pk-27-e2e

# 3) Task H-901 - Scenari E2E ALA V1–V24  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-901: Scenari E2E ALA V1–V24"

# 4) Pubblica il branch
git push -u origin scibetta/pk-27-e2e

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d scibetta/pk-27-e2e
```

> **Note dal foglio:** Esecuzione su 3 OS a cura del SUPERVISORE.

---

### PK-29 · Hardening

- **Branch:** `scibetta/pk-29-hardening`
- **Risorsa:** SCIBETTA · **Onda 8 (dopo GO)** · Step `S14`
- **Parti solo se in `dev` ci sono già:** PK-27
- **Puoi lavorare in parallelo con:** PK-28
- **Percorsi che puoi toccare:** [N] log immutabili, sandbox, SBOM

**Come trovo questo pacchetto nell'Excel**

1. Foglio `Pacchetti` → riga **PK-29** (`Risorsa` = SCIBETTA).
2. Foglio `Post-GO` → filtra `Pacchetto` = **PK-29** → trovi: H-1001 (Ordine 75).
3. Lavorali in quest'ordine, uno alla volta:

| Ordine | Task | Cosa fare | Dipende da |
|---|---|---|---|
| 75 | **H-1001** Hardening | Log immutabili, sandbox, SBOM, audit | H-901 |

**Comandi**

```powershell
# 1) Parti da dev aggiornato
git switch dev
git pull origin dev

# 2) Crea il branch del pacchetto
git switch -c scibetta/pk-29-hardening

# 3) Task H-1001 - Hardening  (esegui questo blocco quando il task è finito)
git status        # devono comparire solo i percorsi del pacchetto
git add -A
git commit -m "H-1001: Hardening"

# 4) Pubblica il branch
git push -u origin scibetta/pk-29-hardening

# 5) Apri la Pull Request su GitHub: base = dev, compare = questo branch

# 6) Dopo il merge della PR
git switch dev
git pull origin dev
git branch -d scibetta/pk-29-hardening
```

> **Note dal foglio:** [N] nessun T-xxx in IN §6: verifica = audit.

---

## 6. Se qualcosa non torna

| Sintomo | Cosa fare |
|---|---|
| `git status` mostra file fuori dai percorsi del PK | **Non fare `git add`.** Annulla la modifica su quei file (`git restore <file>`) oppure chiedi al proprietario del percorso. |
| Non so se posso iniziare un PK | Controlla in `Pacchetti` la colonna `Dipende da (pacchetti)`: tutti devono essere *Fatto* e già mergiati in `dev`. |
| Ho committato sul branch sbagliato (es. `dev`) | Non fare push. Scrivi all'altra persona e al SUPERVISORE prima di toccare altro. |
| Il push è rifiutato | `git pull origin dev` non risolve: leggi il messaggio, non usare `--force`. |
| L'altra persona ha mergiato prima di me | Prima di aprire la PR: `git fetch origin` poi `git merge origin/dev`. |

## 7. Punti da decidere con il SUPERVISORE

1. **`api/` è di SCIBETTA, ma PK-26b e PK-26c (NARO) toccano `api/`** (gate di configurazione, avvio del run non presidiato). Proposta: NARO modifica solo i file di quel pacchetto in `api/`, con review obbligatoria di SCIBETTA, mai in parallelo a un branch di SCIBETTA su `api/`. In alternativa, SCIBETTA espone gli endpoint in PK-25 e NARO fa solo la parte `ui/`.
2. **Stessi file, stessa persona, in serie:** PK-05 → PK-05b → PK-07b (`ci.yml`, `.gitignore`) e PK-19 → PK-19b (`agents/plugin/`, `agents/policy/`). Un branch alla volta, mergiato prima di aprire il successivo.
3. **Contratti congelati:** dopo PK-01, `agents/contracts/` è modificabile solo da SCIBETTA. Suggerito un tag `contracts-v1` su `dev` a fine PK-01: `git tag contracts-v1` e `git push origin contracts-v1` (a cura di chi mergia).
4. **Convenzione dei branch** `<risorsa>/<pk>-<slug>`: proposta [P] da approvare.