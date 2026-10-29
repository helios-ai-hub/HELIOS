# Analisi — Progetto «Gestione biblioteca» (nuovo progetto da zero)

> **Natura del documento.** Analisi di input da passare a LIMET. **Non** è un piano né una
> specifica: LIMET, partendo da qui, genera i propri documenti (CONVENTIONS, ARCHITECTURE, PLAN,
> SPEC per capability, TASK_LIST, TEST_PLAN, E2E_VERIFICATION) con il consueto ciclo (manuale §3,
> App. B.2) e le domande contestuali (§1.2).
> **Fonte:** cap. 5 di `FEASIBILITY_O1_CLAUDE_CODE.html` v2.0 (scenario di riferimento).
> **Scopo del progetto:** applicazione classica e semplice, usata come **progetto pilota** per
> dimensionare costo/tempo dell'ecosistema `limet-agents` (vedi `ANALISI_ECOSISTEMA_LIMET_AGENTS.md`).
> Marcatori: `[FATTO]` · `[IPOTESI]` · `[DA VERIFICARE]`. Nulla è stato ancora implementato.

## 0. Istruzioni a LIMET

1. Percorso **A** (progetto da zero, App. B.2): generare prima CONVENTIONS e ARCHITECTURE iniziali,
   poi PLAN (scheletro), una SPEC per capability (§4), TASK_LIST con **task #0 = contract**
   (§8), TEST_PLAN e E2E_VERIFICATION.
2. Assegnare id stabili `R<n>` ai requisiti del §4; trasformare gli scenari in Dato/Quando/Allora
   con valori concreti (casi limite e d'errore inclusi).
3. Le decisioni del §3 sono già prese: non riaprirle salvo conflitto. Le **domande del §10** vanno
   poste all'utente una alla volta, non assunte.
4. **Il database è su Docker** (§5): vale per sviluppo, test di integrazione, e2e e controlli di
   stato pre/post.
5. Non eseguire build/test; nessun commit/push; re-index a cura dell'utente.

## 1. Obiettivo

Gestire il catalogo di una biblioteca e i prestiti ai lettori: CRUD libri/autori, ricerca,
registrazione lettori, prestito/restituzione con regole di business, elenco ritardi, e una SPA
molto semplice che usa le API REST.

Cartella progetto prevista: `C:\Progetti\biblioteca` `[IPOTESI]`.

## 2. Perimetro

**Dentro:** backend REST, database PostgreSQL su Docker con migrazioni, contratto OpenAPI, SPA
statica, unit/integrazione/e2e, convenzioni e invarianti.

**Fuori (esplicito):** autenticazione/autorizzazione, multiutenza/ruoli, notifiche/email, multe e
pagamenti, prenotazioni, import/export, framework frontend e build JS, deploy in produzione,
internazionalizzazione.

## 3. Decisioni di stack (già prese)

| Livello | Scelta | Motivo |
| --- | --- | --- |
| Backend | Java 17, Spring Boot 3 (Web, Data JPA, Validation), Flyway, Maven Wrapper | Stack Maven delle policy LIMET (`./mvnw test -Dtest=…`) |
| Qualità | Surefire, JaCoCo, PIT (mutation), SpotBugs/PMD come plugin Maven | Gate dell'ecosistema |
| **Database** | **PostgreSQL 16 in `docker-compose.yml`** | Mandato: DB su Docker |
| Test integrazione | Testcontainers (container PostgreSQL effimero per run) | Isolamento; richiede Docker attivo |
| Frontend | SPA statica HTML + JavaScript vanilla, servita da Spring (`/static`), fetch verso REST; nessun build | «Molto semplice» |
| Contratto | `openapi.yaml`, scritto nel task contract e validato nei test dei controller | Gate sui contratti |
| E2E | Playwright (Chromium) | Test da browser |
| Dominio | 4 entità: Libro, Autore, Lettore, Prestito; 6 casi d'uso | |

## 4. Requisiti candidati (da trasformare in SPEC)

| ID | Capability | Requisito | Scenari chiave (da espandere in Dato/Quando/Allora) |
| --- | --- | --- | --- |
| R1 | Catalogo | CRUD Libri (titolo, ISBN univoco, anno, copie totali) e Autori | ISBN duplicato → 409; copie < 0 → 400 |
| R2 | Catalogo | Ricerca libri per titolo/autore/ISBN con paginazione | Query vuota → prima pagina; nessun risultato → lista vuota, 200 |
| R3 | Lettori | Registrazione Lettori (email univoca) | Email duplicata → 409 |
| R4 | Prestiti | Prestito di una copia disponibile; max 3 prestiti attivi per lettore; durata 30 giorni | Nessuna copia → 409; quarto prestito → 409; le copie disponibili decrementano; scadenza = oggi + 30 |
| R5 | Prestiti | Restituzione: chiude il prestito, ripristina la copia, calcola giorni di ritardo | Restituzione doppia → 409; ritardo = max(0, oggi − scadenza) |
| R6 | Prestiti | Elenco prestiti in ritardo | Solo attivi con scadenza < oggi, ordinati per ritardo decrescente |
| R7 | SPA | Pagine Libri (lista, ricerca, nuovo), Lettori, Prestiti (nuovo, restituisci), Ritardi | Errori API mostrati all'utente; form validati lato client |
| R8 | NFR | Avvio con `docker compose up` + `./mvnw spring-boot:run`; migrazioni automatiche all'avvio; **nessuna query senza paginazione** (invariante); health-check 200 entro 60 s | |

Punti che la SPEC deve chiarire (vedi §10): prestito a lettore con prestiti in ritardo; fuso
orario/definizione di «oggi»; eliminazione di libro/autore/lettore con prestiti attivi;
cancellazione fisica o logica; ordinamento stabile della ricerca; formato errori API.

## 5. Database su Docker — vincoli e implicazioni

| Tema | Decisione / requisito |
| --- | --- |
| Servizio | `docker-compose.yml` con servizio `postgres:16`, volume nominato, health-check (`pg_isready`), porta host non standard configurabile per evitare conflitti con altri DB locali `[IPOTESI]` |
| Credenziali | Solo variabili d'ambiente / file `.env` **non versionato**; nessun segreto nel codice; `.env.example` versionato |
| Migrazioni | Flyway `V1__…` (4 tabelle: autore, libro, lettore, prestito, con vincoli di unicità ISBN/email e FK); eseguite all'avvio dell'app |
| Test di integrazione | Testcontainers: container PostgreSQL **effimero per run**, separato da quello di sviluppo → i test non toccano mai i dati di sviluppo |
| Stato pre/post | I test che toccano lo stato registrano query SQL **prima e dopo** (manuale §5.1/§5.2): es. `SELECT copie_disponibili FROM libro WHERE id = …`, conteggio prestiti attivi del lettore |
| E2E | Playwright contro app locale + DB Docker con **dati di seed dedicati e ripristinabili** (rollback/pulizia a fine test) |
| Accesso agenti | Eventuale canale DB per gli agenti **solo in lettura**: server MCP PostgreSQL in modalità read-only (candidati in `ANALISI_ECOSISTEMA_LIMET_AGENTS.md` §13.3: Postgres MCP Pro, MCP Toolbox) verso la porta del container, con utente DB dedicato a soli `SELECT`; nessuna scrittura/DDL da parte degli agenti |
| Risorse | Docker 29 presente `[FATTO]`; 8,09 GB di immagini, 1,86 GB recuperabili; disco libero 60,8 GB; RAM 15,7 GB (DB + build + Ollama da monitorare) |
| Prerequisito | Docker Desktop **avviato** prima di compose/Testcontainers/e2e; la TEST_PLAN deve dichiararlo come precondizione |
| Pulizia | `docker compose down -v` per azzerare; nessun `docker system prune` automatico |
| Operazioni distruttive | Drop/reset del DB di sviluppo solo su richiesta esplicita dell'utente |

## 6. Convenzioni e invarianti iniziali (per CONVENTIONS / `docs-limet/`)

- Package base `biblioteca.<dominio>` (es. `biblioteca.prestiti`); layering controller → service → repository.
- Errori API in formato uniforme (problem+json `[IPOTESI]`): 400 validazione, 404 non trovato, 409 conflitto di business.
- **Invarianti (verificabili meccanicamente dal runner):** nessuna query senza paginazione; nessun log di dati personali (email del lettore); nessun segreto versionato; migrazioni solo additive dopo V1; ogni endpoint presente in `openapi.yaml`.
- Test: nome `metodo_scenario_esito`; nessuno stato condiviso fra test; autorevisione §5.1 obbligatoria.
- Git: nessun commit/push/merge da parte degli agenti; branch `task/NNNN-Tn`, `test/NNNN-Tnt`, `integration/NNNN`.

## 7. Strategia di verifica candidata (per TEST_PLAN / E2E_VERIFICATION)

| Livello | Cosa | Note |
| --- | --- | --- |
| Unit | Service Libri/Autori/Prestiti: regole R1–R6, casi limite (confini 3/4 prestiti, 29/30/31 giorni, copie 0/1, restituzione doppia) | Test scritti dalla SPEC senza vedere l'implementazione; autorevisione §5.1 (es. cambiare 30 → 31 giorni deve far fallire un test) |
| Controller | Codici 200/400/404/409, validazione payload, conformità a `openapi.yaml` | |
| Integrazione | Flussi R4/R5 su PostgreSQL Testcontainers con pre/post stato SQL | Docker attivo |
| E2E (Playwright) | Nuovo libro; prestito; restituzione; ritardi; errori mostrati | DB Docker con seed; rollback/pulizia |
| Qualità | Copertura differenziale, mutation (PIT) sulle classi nuove, statica 0 errori, invarianti | Soglie da definire (§10) |

Esecuzione: build/test **non** eseguiti dall'agente; comandi rieseguibili forniti all'utente
(classe `-Dtest=ClasseTest` e metodo `-Dtest=ClasseTest#metodo`), con descrizione e classe.

## 8. Grafo dei task candidato (per la TASK_LIST)

| ID | Tipo | Titolo | Dipende da | Req. | Stima |
| --- | --- | --- | --- | --- | --- |
| T0 | contract | Scaffold: `pom.xml` (plugin qualità), `docker-compose.yml` (PostgreSQL), `.env.example`, Flyway V1 (4 tabelle), `openapi.yaml`, convenzioni, invarianti | — | R8 | M |
| T1 | dev | Entità + repository Libro/Autore | T0 | R1 | S |
| T2 | dev | Entità + repository Lettore, Prestito | T0 | R3, R4 | S |
| T3 | dev | Service Libri/Autori + ricerca paginata | T1 | R1, R2 | M |
| T4 | dev | Service Prestiti (max 3, 30 gg, disponibilità, restituzione, ritardi) | T1, T2 | R4–R6 | M |
| T5 | dev | Controller REST Libri/Autori/Ricerca + errori 409/400 | T3 | R1, R2 | M |
| T6 | dev | Controller REST Lettori/Prestiti/Ritardi | T4 | R3–R6 | M |
| T7 | dev | SPA: shell, client API, pagina Libri | T0 (OpenAPI) | R7 | M |
| T8 | dev | SPA: pagine Lettori, Prestiti, Ritardi | T7 | R7 | M |
| T9 | integration | Test Testcontainers (flussi R4/R5 con pre/post SQL) | T5, T6 | R4–R6 | M |
| T10 | e2e | Playwright: nuovo libro, prestito, restituzione, ritardi, errori | T8, T9 | R7 | M |
| T1t…T8t | test | Unit test per ciascun task dev (in parallelo al dev) | come il dev | come il dev | S/M |

Onde con 3 agenti: 1: T0 · 2: T1, T1t, T2 (poi T2t, T7, T7t) · 3: T3, T3t, T4 · 4: T4t, T5, T5t ·
5: T6, T6t, T8 · 6: T8t, T9 · 7: T10. Circa 20 unità di lavoro più review e validazione.
Dimensione attesa: ~25 classi Java, ~15 classi di test, ~8 file SPA, 2 config, ~3 spec Playwright.
Vincoli: T0 serializzato (contract); T5/T6 potrebbero toccare lo stesso gestore errori → serializzare.

## 9. Rapporto con l'ecosistema `limet-agents` (pilota)

Il progetto serve a validare gli scenari V1–V12 dell'analisi dell'ecosistema e a misurare:
costo API (stima 20–45 USD, ~25 USD con i modelli consigliati), tempo di parete (6–10 h),
supervisione umana (2–4 h), disco (4–9 GB), picco RAM (~8–9 GB su 15,7 GB). Il codice del
progetto è prodotto dagli agenti a partire dai documenti LIMET approvati; i documenti non sono
scritti dagli agenti. Prerequisiti ambiente: JDK 17+ (oggi JDK 11 in PATH `[DA VERIFICARE]`),
Playwright + Chrome `[DA FARE]`, Docker Desktop avviato.

Estensione utile al pilota (change successiva): «gestione prestiti» come nuova feature (App. B.4)
e un bug fix indotto (App. B.3, es. durata 31 giorni) per provare i tre casi d'uso.

## 10. Domande contestuali aperte (da porre all'utente, non assumere)

1. Percorso e nome del progetto (`C:\Progetti\biblioteca`?), nome del package base.
2. DB Docker: porta host, nome database/utente, volume persistente o effimero, seed iniziale?
3. Un lettore con prestiti in ritardo può prendere un nuovo prestito?
4. Definizione di «oggi» (fuso orario, `Clock` iniettabile per i test)?
5. Cancellazione di libri/autori/lettori con prestiti attivi o storici: vietata, a cascata, logica?
6. Un libro può avere più autori? Autore obbligatorio?
7. Formato errori API (problem+json o schema proprio)?
8. Soglie dei gate: copertura differenziale, mutation score, `taskCap`.
9. Lingua di messaggi di errore, UI e documenti del progetto?
10. E2E: Playwright contro il DB di sviluppo con seed dedicato o un compose separato per e2e?

## 11. Rischi

| Rischio | Impatto | Mitigazione |
| --- | --- | --- |
| Docker non avviato / RAM insufficiente (DB + Testcontainers + build) | Test bloccati | Precondizione esplicita; `maxBuilds 1`; un solo container di test alla volta |
| Conflitto di porta o dati sporchi nel DB di sviluppo | Falsi esiti | Testcontainers effimero per integrazione; seed e pulizia per e2e |
| Regole di business ambigue (ritardi, cancellazioni) | Divergenza tra SPEC, test e codice | Risolvere le domande §10 prima di approvare la SPEC |
| Test che confermano l'implementazione (es. 30 vs 31 giorni) | Copertura apparente | Autorevisione §5.1, mutation testing |
| JDK 11 in PATH vs Java 17 richiesto | Build fallita | Verificare/impostare JDK 17 come prerequisito |
| Conflitti su file condivisi (gestore errori, `openapi.yaml`) | Merge manuali | Contract in T0; serializzazione dei task che li toccano |
