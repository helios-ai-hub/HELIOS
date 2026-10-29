# ADR 0001 — HELIOS: decisione fondativa

- **Stato**: `Approvato`
- **Approvato da / data**: SUPERVISORE (@mcibetta) — 2026-10-29 (risposta «approvo adr»)
- **Data redazione**: 2026-10-29
- **Task**: H-101 (IN §5 Fase 1). Fonti: IN = `HELIOS_ANALISI_PER_LIMET.md`; R05 = `05_roadmap_e_rischi.md`; 01 = `01_analisi_ECC.md`.
- Etichette: **[F]** fatto · **[I]** ipotesi · **[P]** proposta · **[N]** non verificato.

## Contesto

LIMET è un metodo spec-driven per lavorare con agenti AI. HELIOS ne è l'evoluzione: una catena di agenti (analyst, developer, tester, reviewer, validator, ecc.) con runner confinato, gate umani meccanici e plugin Claude Code `limet-agents`. Come libreria di asset (skill, agenti, rules) si usa ECC, pinnato al commit `25278e7` (v2.2.3) **[F]** (01 §0–§7).

Il progetto richiede un'eccezione allo scope «niente framework di terzi» (ALA §2) e a FO1 (R05, C01).

## Decisione

1. **Autonomia (C01)**: HELIOS è un progetto autonomo con proprie regole (R05 §3, C01 = A).
2. **Titolarità (Q3)**: il SUPERVISORE è owner unico dell'eccezione di scope e degli ADR (IN §8 Q3).
3. **Struttura (Q7)**: monorepo; il plugin vive in `agents/plugin/` (IN §8 Q7).
4. **Perimetro ECC** (pin `25278e7`):
   - si riusano solo asset con licenza MIT verificata, registrati nel ledger (C07);
   - nessun hook ECC eseguito (C04), nessuna memoria ECC: CEREBRO è l'unica memoria (C08);
   - nessun flusso di pianificazione ECC come autore del piano; i planner ECC sono consulenti in sola lettura (C06; IN §3.5);
   - GitHub App ecc.tools non usata (C11); Plan Canvas e Control Pane non portati (C09);
   - gli asset portati hanno prefisso `ecc-` e nessun `Bash` libero (C02, C06).
5. **Verifica prima della costruzione (C14)**: il PoC go/no-go (Fase 4, `docs/poc-report.md`) è un punto di decisione del SUPERVISORE; le Fasi 5–12 partono solo con esito GO.
6. **Valutazione dei rischi dopo ogni task (C28)**: obbligatoria per chi sviluppa HELIOS (IN §2.3 n.7).
7. Le decisioni C01–C28 e Q1–Q9 sono vincolanti; ogni deviazione la decide solo il SUPERVISORE.

## Revoca

L'eccezione di scope è revocata se:
- il gate H-406 dà **NO-GO** e il SUPERVISORE non accetta il piano B (wrapper senza copia di ECC, 04 §5), oppure
- il SUPERVISORE la revoca con decisione scritta.

La revoca si registra in un nuovo ADR che sostituisce questo.
(Condizione confermata dal SUPERVISORE, 2026-10-29.)

## Alternative considerate

- **Rimanere con LIMET puro**: nessun rischio nuovo, ma nessun beneficio agentico; scartata (C01).
- **Wrapper senza copia di ECC (piano B)**: tenuta come ripiego dopo il PoC, non come scelta iniziale (04 §5).
- **Repo separati** (Q7 opzione non scelta): scartata a favore del monorepo.

## Conseguenze

- Positive: regole e asset sotto un unico controllo; auditabilità (ledger, hash, SBOM).
- Negative: costo di manutenzione del pin ECC e della deriva dalle versioni successive (R05, C12); progetto non ancora dimostrato fattibile (**[I]**, C14).
- Le installazioni LIMET/CEREBRO in uso per COSMO e mudeopen e le collection Qdrant esistenti restano intatte (C19, IN §8).

## Punti [N] collegati

- Remoto e storia git di CEREBRO (C21, Q-9).
- Parere legale sulle licenze (Q-10).
