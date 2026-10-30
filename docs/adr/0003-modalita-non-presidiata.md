# ADR 0003 — Modalità non presidiata (UNATTENDED)

- **Stato**: Approvato dal SUPERVISORE il 2026-10-30
- **Task**: H-713, H-714, H-715, H-8b10 (Fase 7 e Fase 8b, **dopo il GO**)
- **Data**: 2026-10-30
- **Fonte**: `ANALISI_limet-agents_con_workflow_claude.md` R3, R8, R12, R17, R18, R19, R20, R21, §1.3, §9 V11, §13 q.9 e q.12 [F]; `HELIOS_ANALISI_PER_LIMET.md` §1, §2.3, §5 (H-612, H-706, H-707, H-708, H-709) [F]; `05_roadmap_e_rischi.md` §2 C06, C24, C28 [F]
- **Nota**: non introduce requisiti nuovi sul perimetro di autonomia; **formalizza** e chiude la questione aperta ALA §13 q.9, finora senza risposta.

## Contesto

Il SUPERVISORE ha chiesto (2026-10-30) se HELIOS possa funzionare in «auto-mode», cioè con
agenti che prendono decisioni autonome e portano a termine **tutti** i task creati da LIMET
senza intervento umano.

La verifica di fattibilità ha dato un esito netto e diviso in due.

**La forma assoluta non è fattibile e contraddice la definizione stessa del prodotto.** IN §1
dichiara **[F]** che HELIOS «non è *autonomo senza limiti*: si ferma e chiede in caso di dubbio,
errori ripetuti o costo oltre soglia» e prevede «**pause obbligatorie** in cui decide il
SUPERVISORE». I non-negoziabili di IN §2.3 vietano build/test/commit/push/merge autonomi e
impongono gate umani su PLAN e validazione finale (C24). ALA aggiunge il gate sul grafo dei task
(R3), il «fermati e chiedi» con `maxAttempts` = 3 (R8, R12), il `deny` su `git push`/`git merge`
(R19, R21) e l'esclusione esplicita di `commit-commands` dall'uso autonomo (§13 q.12). C28 e
H-612 impediscono al `risk-assessor` di correggere da solo. Un auto-mode totale richiederebbe di
riaprire almeno C02, C24, C28 e i non-negoziabili n.1 e n.2: non è stato proposto e non è stato
fatto.

**La forma limitata è fattibile ed era già metà progettata.** ALA prevede una sezione *notte* in
`limits.json` (R20), un `REPORT_NOTTURNO.md` con una sezione «decisioni autonome» (R18), lo
scenario V11 «Notte simulata», il checkpoint con ripresa (`resumeFromRunId`), il «fermati e
chiedi» non globale (R8 → H-707) e gli stati `WAITING_SUPERVISOR`, `BLOCKED`, `STALE`,
`INCOMPLETO` (R17 → H-706). Mancava il *contratto* che lega questi pezzi: chi autorizza la
tratta, quanto dura, che cosa può decidere l'agente dentro la tratta e come si dimostra a
posteriori che non ne è uscito. Questo ADR è quel contratto.

## Decisione

### 1. Nome e natura

La modalità si chiama **non presidiata (UNATTENDED)**, deliberatamente **non** «autonoma»: il
nome deve dire la verità su ciò che cambia. Cambia la **presenza**, non l'**autorità**.

### 2. Principio fondante — neutralità dei permessi

> La modalità non presidiata cambia la **supervisione**, mai l'**autorità**.
> Un agente in UNATTENDED ha **esattamente** gli stessi permessi che ha in modalità presidiata.

È il vincolo che rende la feature difendibile in audit: se la modalità potesse alzare i permessi,
diventerebbe un modo per aggirare C02, C24 e il pavimento hard-deny di H-404. Nessuna
configurazione, nessun flag e nessun parametro di avvio può ampliare i permessi di un agente in
UNATTENDED. Una violazione di questo principio è un difetto bloccante, non una regressione
minore.

### 3. Perimetro di autonomia (Q-15)

Dentro un task **già approvato** al gate R3, l'agente decide da solo **solo** su:

1. scelte implementative interne al task (nomi, struttura, refactor locale);
2. riprovare un'operazione fallita, entro `maxAttempts` = 3 (R12), mai un terzo tentativo cieco (R8);
3. ordine di esecuzione fra i task **pronti**, dentro il grafo già confermato (R3).

**Fuori perimetro sempre** — l'agente si ferma e parcheggia il task:

- qualunque dubbio su un requisito (C06, R8);
- qualunque rischio alto o critico (C28, H-612);
- merge su `main` e `git push` (R19, R21);
- approvazione del PLAN e validazione finale (C24);
- superamento di budget, durata massima o numero di task (R20);
- **split di un task**: il grafo confermato al gate non si modifica in assenza del SUPERVISORE (R3);
- **scrittura o modifica di unit test fuori dal TEST_PLAN approvato**.

### 4. Azioni di ruolo ≠ decisioni autonome (Q-18)

Il perimetro del §3 limita le **decisioni**, non le **azioni già definite dal ruolo** in ALA §1.3.
In particolare il `developer` **committa sul ramo del task** anche di notte, esattamente come di
giorno; restano vietati `push` e `merge` (R19, R21).

Motivazione: ALA §1.3 vieta al `runner` di operare «su codice non committato». Senza commit, la
catena `developer → tester → runner → test-reader → reviewer` si interromperebbe al primo anello
e il checkpoint perderebbe l'ancoraggio a uno SHA, rendendo impossibile la ripresa con
`resumeFromRunId`.

### 5. Ruoli ammessi (Q-16)

**Ammessi non presidiati**: `developer`, `tester`, `runner`, `test-reader`, `reviewer`,
`metrics-observer`.

**Esclusi**: `analyst`, `validator`, `integrator`, `risk-assessor`, e `commit-commands` (già
escluso da ALA §13 q.12).

L'elenco è una **allow-list applicata dal codice** (H-715), non una convenzione: al turno di un
ruolo escluso il task si parcheggia in `WAITING_SUPERVISOR` con motivazione esplicita.

Conseguenze accettate:

- senza `integrator`, i task completati di notte restano sui rispettivi rami; l'integrazione su
  `integration/NNNN` è lavoro presidiato del mattino;
- senza `risk-assessor`, un task che richiede valutazione di rischio si parcheggia invece di
  essere valutato: C28 non viene mai esercitato in assenza del SUPERVISORE;
- senza `validator`, nessuna dichiarazione di conformità può maturare di notte (coerente con C24).

### 6. Parametri della tratta — chiude ALA §13 q.9 (Q-14)

| Parametro | Valore deciso | Fonte |
|---|---|---|
| `night.stopOnQuestions` | **`false`** — il task va in `WAITING_SUPERVISOR`, i **dipendenti** si bloccano, gli **indipendenti** proseguono | R8 non globale, H-707 |
| `night.maxDurationMin` | **480** (8 ore); allo scadere il run si ferma in modo controllato → `INCOMPLETO` + report | R20 |
| Reperibilità | **nessuno** — il run è **auto-contenuto**: nessuna notifica attesa in corsa, tutto nel report del mattino | decisione SUPERVISORE |

La condotta alternativa (`stopOnQuestions` = `true`, ferma l'intero run) resta implementata come
opzione configurabile, ma **non è il default**.

**Conseguenza progettuale diretta della non reperibilità**: poiché nessuna risposta umana è
disponibile durante il run, il **limite di durata** e il **dead-man switch** non sono accessori
ma **requisiti di sicurezza primari**, e ogni blocco deve restare ripartibile il mattino dopo da
checkpoint (`resumeFromRunId`, R20 / H-708).

### 7. Fine corsa sempre garantita

Un run non presidiato termina **in ogni caso** per uno di questi motivi, tutti verificabili:

- esaurimento dei task pronti;
- superamento di `night.maxDurationMin`, del budget o del numero massimo di task (R20);
- **kill switch**: presenza di un file di abort, letto a ogni passo;
- **dead-man switch**: il registro eventi smette di aggiornarsi oltre una soglia → il run si
  interrompe da solo.

Nessuna di queste uscite può lasciare il repository in uno stato non ripartibile.

### 8. Prova a posteriori

Ogni decisione presa in autonomia finisce in un **registro delle decisioni autonome** (H-714),
con evidenza e reversibilità, e confluisce nella sezione «decisioni autonome» di
`REPORT_NOTTURNO.md` (R18). Il mattino dopo il SUPERVISORE deve poter rispondere a una sola
domanda: *«che cosa ha deciso da solo, e perché posso fidarmi?»*

### 9. Avvio

Il run non presidiato è **avviato da una sessione umana** dalla dashboard (H-8b10), che mostra
perimetro e limiti **prima** dell'avvio e tiene il kill switch raggiungibile. Non esiste avvio
automatico pianificato senza un umano che lo autorizzi (C24).

### 10. Collocazione (Q-17)

La modalità vale **solo dopo il GO** (Fase 7+). Il perimetro del PoC e i criteri del gate C14
**non cambiano**. Far girare non presidiato un sistema la cui fattibilità non è ancora dimostrata
sarebbe il contrario della prudenza che il gate C14 esiste per imporre.

## Alternative scartate

- **Auto-mode totale** (nessun gate umano): scartata — contraddice IN §1 e §2.3, richiederebbe di
  riaprire C02, C24, C28.
- **`stopOnQuestions` = `true` come default**: scartata — annullerebbe il valore della tratta
  notturna, fermando tutto al primo dubbio anche su task indipendenti.
- **Nessun commit di notte**: scartata — interrompe la catena al `runner` (ALA §1.3) e priva il
  checkpoint di un ancoraggio.
- **Ramo di quarantena `night/<task>`**: scartata dal SUPERVISORE — aggiunge un passo manuale di
  promozione senza ridurre un rischio che `deny` su push/merge già copre.
- **Ruoli `integrator` e `risk-assessor` ammessi di notte**: scartati — sono i due ruoli con la
  maggiore superficie decisionale.

## Conseguenze

- Chiude la questione aperta ALA §13 q.9.
- Introduce quattro task (H-713, H-714, H-715, H-8b10), quattro test (T-713, T-714, T-715,
  T-8b13) e due scenari E2E (E2E-AUT-1, E2E-AUT-2).
- La tratta percorribile di notte è **più corta** di quanto sarebbe con tutti i ruoli ammessi:
  è un costo accettato in cambio della non riapertura di C24 e C28.
- Il principio di neutralità dei permessi diventa un **controllo C28 ricorrente** su ogni task
  della modalità.

## Punti [N]

Nessuno aperto su questo tema: Q-14, Q-15, Q-16, Q-17 e Q-18 sono state decise dal SUPERVISORE
il 2026-10-30.

- Soglia esatta del dead-man switch (minuti di silenzio del registro eventi): da fissare in H-713
  con un valore di default e reso configurabile.

## Storico

| Data | Modifica |
|------|----------|
| 2026-10-30 | Creazione (Approvato). Verifica di fattibilità, chiusura di Q-14…Q-18 e di ALA §13 q.9 |
