# ADR 0001 — HELIOS come progetto autonomo: eccezione allo scope «niente framework di terzi»

- **Stato**: `PROPOSTO` → `APPROVATO` il [YYYY-MM-DD] da SUPERVISORE *(da compilare: il task H-101 è
  `done` solo con questa riga compilata)*
- **Data**: 2026-10-29
- **Autore**: Claude Code (LIMET «puro»), per conto del SUPERVISORE
- **Decisione di riferimento**: C01 (`limet-docs/05_roadmap_e_rischi.md` §2, «DECISO dal SUPERVISORE
  2026-10-28», opzione A ★)
- **Task LIMET**: H-101 (`limet/changes/0001-helios-roadmap-completa/TASK_LIST.md` #1)

## 1. Contesto

- **[F]** I documenti vincolanti del progetto `limet-agents` limitano lo scope a «LIMET + Claude Code
  nativo» ed escludono framework di terzi, altri strumenti AI e repository esterni (ALA §2 «Fuori»;
  FO1 §1). HELIOS importa invece asset di un progetto terzo: ECC (`affaan-m/ECC`, v2.2.3, commit
  `25278e7d7b6cf6e6a4e7a6c8e10f14ff22f0eb5b`, licenza MIT; `01_analisi_ECC.md` §0–§1).
- **[F]** Senza una decisione formale, HELIOS sarebbe in contraddizione con ALA/FO1 (criticità C01,
  `05` §2).
- **[F]** Il SUPERVISORE ha deciso (2026-10-28) che HELIOS è un **nuovo progetto autonomo** con
  proprie regole, che contiene al suo interno LIMET, CEREBRO ed ECC (asset in allowlist) e che
  eredita i vincoli non negoziabili (C01; `04_architettura_HELIOS.md` §1 n. 6).

## 2. Decisione

1. **HELIOS è un progetto autonomo.** Lo scope «niente framework di terzi» di ALA §2 e FO1 resta
   valido per `limet-agents` *come definito in quei documenti*, ma **non governa HELIOS**. ALA e FO1
   non vengono emendati né modificati: restano documenti vincolanti di riferimento per requisiti
   (R1–R27), scenari (V1–V24), stati, gate e scheletro del workflow.
2. **Le regole proprie di HELIOS** sono fissate nella costituzione (`docs/CONSTITUTION.md`, task
   H-102) e **ereditano i non-negoziabili** (`HELIOS_ANALISI_PER_LIMET.md` §2.3 n. 1–7): nessuna
   build/test/commit/push/merge autonomi; gate umani approvabili solo da sessione umana; Windows e
   Unix con la stessa suite; nessun invio a LLM salvo automazione approvata e registrata; nessun
   segreto nel repo e nessuna scrittura fuori dal workspace o in `~/.claude`; nessuna modifica a
   progetti esterni; valutazione dei rischi a fine di ogni task (C28).
3. **Owner dell'eccezione**: il **SUPERVISORE**, owner unico (Q3, `HELIOS_ANALISI_PER_LIMET.md` §8).
   Ogni deviazione dalle decisioni C01–C28 e Q1–Q9 è decisa solo da lui.
4. **Perimetro ECC ammesso**: esclusivamente gli **asset testuali** (agenti, skill, rules) presenti
   nell'**allowlist** del ledger `agents/assets/ecc-ledger.json` con licenza **MIT verificata**
   (C07-A), portati con prefisso `ecc-` (C06), frontmatter `tools:` senza `Bash` libero (C02),
   corpo invariato con hash e origine registrati, e superamento del test statico «LIMET-agnostico»
   (C17). Esclusi per costruzione: hook ECC (C04), memoria ECC (C08), flusso di pianificazione ECC
   come autore del piano (C06), Plan Canvas e Control Pane (C09), GitHub App (C11), installazioni
   globali in `~/.claude` (C05), asset con licenza non MIT verificata (C07), `--opus` di
   AgentShield (C10).
5. **Pin**: ECC è fissato al commit `25278e7` (C12). Nessun aggiornamento automatico. Un upgrade
   richiede una decisione scritta del SUPERVISORE, un nuovo ADR (o revisione di questo) e la suite
   di non regressione verde (Fasi 5–9).
6. **Durata**: l'eccezione vale **fino alla versione pin `25278e7`** o fino a diversa decisione
   scritta del SUPERVISORE (C01-A; confermato dal SUPERVISORE 2026-10-29).
7. **Criteri di revoca** (proposta dell'agente accettata dal SUPERVISORE 2026-10-29). L'eccezione
   decade, e HELIOS torna allo scope «solo LIMET + Claude Code nativo» (ALA §2) o all'alternativa
   clean-room (C01-B/C07-B), se si verifica uno dei seguenti casi:
   - (a) la verifica legale delle licenze (C07, `05` §6) fallisce e non è approvata un'alternativa
     clean-room;
   - (b) il PoC (Fase 4, C14) dà **no-go sulla fattibilità** (criteri 1–5, 7, 8 di `05` §5) e il
     piano B (`04` §5 Opzione 3, wrapper senza copia) non è approvato;
   - (c) viene adottato un upgrade oltre `25278e7` senza decisione scritta e senza test di non
     regressione (violazione di C12);
   - (d) il SUPERVISORE la revoca con decisione scritta.
   La revoca è registrata con un ADR successivo che indica la data, il motivo e il piano di
   dismissione degli asset `ecc-*` (rimozione dal ledger e dal plugin, test T-202/T-204 aggiornati).

## 3. Conseguenze

- **Positive**: il conflitto di scope con ALA/FO1 scompare perché lo scope è ridefinito dal nuovo
  progetto (C01-A); i requisiti, gli scenari e i gate di ALA/FO1 restano riusabili senza modifiche
  ai documenti originali; la tracciabilità per asset (ledger) rende verificabile il perimetro
  (T-202, T-204, T-501, T-503).
- **Negative / prezzo**: un ADR da mantenere; l'allowlist limita l'uso di ECC a ciò che è verificato
  (gli asset esclusi non entrano, `05` §6); ogni upgrade di ECC è un lavoro esplicito (C12).
- **Vincoli che discendono**: costituzione (H-102); ledger e `NOTICE` (H-202); matrice catena con
  decisione per ognuno dei 71 agenti e delle 311 skill (H-204); test statici in CI (H-501, H-503).

## 4. Alternative considerate (da `05` §2 C01)

- **B — Clean-room**: riscrivere da zero i contenuti ECC. Elimina la dipendenza da terzi, ma effort
  ×2–3 sul porting e nessun beneficio dagli aggiornamenti. Resta l'alternativa di ripiego (§2 n. 7).
- **C — HELIOS come progetto separato consumato da LIMET come fornitore esterno**: elimina il
  conflitto di scope ma richiede un secondo repository da governare. Scartata: il SUPERVISORE ha
  scelto il monorepo (Q7).

## 5. Dati di fatto del repository (annotazione per tracciabilità)

- **[F]** Repository di lavoro: `C:\Progetti\HELIOS`, remote `https://github.com/helios-ai-hub/HELIOS.git`
  (verificato 2026-10-29). **Nota**: Q5 (`HELIOS_ANALISI_PER_LIMET.md` §8) indicava `C:\Progetti\VSC\HELIOS`
  e l'account `odyssey2073`; il SUPERVISORE ha confermato (2026-10-29) che vale il repository reale.
  La cartella di studio `C:\Progetti\VSC\LIMET\HELIOS` resta separata, come previsto da Q5.
- **[F]** Le installazioni LIMET (`C:\Progetti\VSC\LIMET`) e CEREBRO (`C:\software\cerebro`) in uso
  per COSMO e mudeopen non vengono toccate (C19); HELIOS usa copie interne.

## 6. Riferimenti

`limet-docs/05_roadmap_e_rischi.md` §2 (C01, C06, C07, C12), §3 · `limet-docs/04_architettura_HELIOS.md`
§1, §5 · `limet-docs/HELIOS_ANALISI_PER_LIMET.md` §1.1–§1.4, §2.3, §8 (Q3, Q5, Q7) ·
`limet-docs/01_analisi_ECC.md` §0, §9 · ALA §2 · FO1 §1 · `limet/changes/0001-helios-roadmap-completa/PLAN.md` §4 n. 1, 4, 20.

---

### Verifica D-101 (checklist, `TEST_PLAN.md`)

- [x] Owner indicato (§2 n. 3)
- [x] Perimetro ECC = allowlist (§2 n. 4)
- [x] Pin `25278e7` (§2 n. 5)
- [x] Durata (§2 n. 6) e criteri di revoca (§2 n. 7)
- [ ] Riga «APPROVATO il … da SUPERVISORE» compilata (in attesa)
