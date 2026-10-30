# Piano di test — HELIOS (progetto completo, Fasi 1–12 + 8b; gate PoC in Fase 4)

> Etichette: **[F]** fatto · **[P]** proposta · **[N]** non verificato. Fonte dei test: IN §6 (`HELIOS_ANALISI_PER_LIMET.md`). **[N]** Tutti i file, classi e metodi sotto sono **proposti da IN §6 e non esistono ancora** (Q-11b; Q-11 è chiusa per framework e comandi da H-103 il 2026-10-29). I comandi sono eseguiti dal SUPERVISORE dalla radice del repo (`C:\Progetti\VSC\HELIOS-PLATFORM\HELIOS`), non dall'agente.

## Riferimenti

- **Piano**: `PLAN_helios.md` · **Task**: `TASK_LIST.md` · **E2E**: `E2E_VERIFICATION.md`

## Ambito della verifica

Garantire per i task del PoC: nessun segreto versionato (T-302), contratti validi (T-303), parità degli shim (T-304), confini tra moduli (T-305), CI su 3 OS (T-306), runner confinato e tracciato (T-401, T-402), ruoli senza Bash (T-403), gate sul PLAN (T-404), pavimento hard-deny (T-405), A/B (T-406), manifest del plugin (T-407). **Estensione**: tutti gli altri test di IN §6 (T-201…T-204, T-308, T-408, T-409, T-5xx, T-6xx, T-7xx, T-8xx, T-8bxx) sono nella sezione «Estensione a tutte le fasi» in fondo; i test delle Fasi 5–12 partono solo dopo GO del gate H-406.

## Unit test previsti

Framework **[P]**: `pytest` dalla radice (`python -m pytest`), Python ≥ 3.11 (IN §6, Q4).

| # | Cosa verifica | File/classe di test | Caso | Task |
| - | ------------- | ------------------- | ---- | ---- |
| T-302 | Nessun segreto nel repo; `cache/` e dati Qdrant ignorati | `TestSecrets` (`tests/test_secrets.py`) | contratto/regressione | H-302 |
| T-303 | Gli artefatti rispettano gli schemi dei contratti | `TestContracts` (`tests/test_contracts.py`) | happy + invalidi | H-303 |
| T-304 | Gli shim danno gli stessi esiti dei comandi legacy | `TestCompatParity` (`tests/test_compat_parity.py`) | parità | H-304 |
| T-305 | Nessun riferimento fuori dai contratti tra moduli | `TestModuleBoundaries` (`tests/test_module_boundaries.py`) | regressione | H-305 |
| T-306 | La CI definisce la matrice dei 3 OS con la stessa suite | `TestCiMatrix` (`tests/test_ci_matrix.py`) | contratto | H-306 |
| T-309 | Catena di precedenza, validazione semantica, esempio senza dati locali, file reale non versionabile | `TestLocalConfig` (`tests/test_local_config.py`) | contratto + negativi + precedenza | H-309 |
| T-401 | Il runner rifiuta operazioni fuori allowlist | `TestRunnerAllowlist` (`tests/test_runner_allowlist.py`) | negativo | H-401 |
| T-402 | Log con hash riproducibile; nessun commit/push né rete | `TestRunnerLog` (`tests/test_runner_log.py`) | riproducibilità | H-401 |
| T-403 | Nessun ruolo/asset portato ha `Bash` libero | `TestRolesNoBash` (`tests/test_roles_no_bash.py`) | regola | H-402 |
| T-404 | Il gate sul PLAN blocca senza approvazione umana | `TestGate` (`tests/test_gate.py`) | negativo | H-403 |
| T-405 | Il pavimento hard-deny non è rimovibile (neanche da UI/API) e la policy è auditata | `TestPolicyFloor` (`tests/test_policy_floor.py`) | negativo | H-404 |
| T-406 | L'asset portato non peggiora le metriche vs baseline | `TestAbMetrics` (`tests/test_ab_metrics.py`) | soglia Q-6 (recall ≥ baseline, falsi allarmi ≤ baseline, ≥ +1 su 10 difetti D1–D10 approvati 2026-10-29) | H-405 |
| T-407 | Manifest e struttura del plugin valide; registrazione e invocazione reale verificate **a mano** in Claude Code (E2E V13/V14) | `TestPluginManifest` (`tests/test_plugin_manifest.py`) | struttura | H-407 |
| T-504 | Integrità della documentazione di architettura: ogni citazione `file:riga` punta a una riga esistente, ogni modulo compare nella mappa, i diagrammi Mermaid si analizzano | `TestArchitectureDocs` (`tests/test_architecture_docs.py`) | contratto + negativi + regressione | H-504 |

### Autorevisione della qualità (manuale §5.1) — esito **preliminare**

I test non esistono ancora; l'autorevisione sotto è una **pre-revisione sul disegno**. Va **rifatta sul codice reale** dei test prima di dichiarare completo il lavoro di test (e riportata nella colonna Note del task).

- **Sabotaggio mentale** (alterazioni plausibili che oggi **non** farebbero fallire un test, quindi azioni di copertura da inserire quando si scrivono i test):
  1. T-401: aggiungere `git push` all'allowlist → deve fallire un caso che prova `git push` e `git commit` (non solo «un'operazione non ammessa» generica): **da includere**.
  2. T-401/T-402: il runner apre una connessione di rete senza che nulla lo rilevi → **[N]** il metodo `test_hash_riproducibile` non lo copre; serve un test esplicito «nessuna rete» (IN §6 lo cita nella descrizione di T-402 ma il metodo proposto è solo l'hash). **Gap segnalato**: aggiungere metodo dedicato (es. `test_nessuna_rete`), da confermare in H-103.
  3. T-405: rimuovere `./mvnw*` dal `floor.json` → `test_deny_mvnw_non_rimovibile` fallisce; ma altre voci del pavimento (`git push*`, `git merge*`, R19/H-710) sono scoperte: **da includere** un caso per ciascuna voce del pavimento o criterio di selezione scritto (insieme completo).
  4. T-403: usare un nuovo ruolo con `Bash` non elencato → il test deve scorrere **tutti** i file in `agents/roles/` (insieme completo, non campione).
  5. T-404: il gate approva con un approvatore non umano/vuoto → **da includere** casi: approvazione assente, vuota, di un agente.
  6. T-406: soglia go/no-go definita a posteriori → **[N]** Q-6 deve fissare metrica e baseline **prima** (valore atteso non derivato dall'oggetto sotto test).
- [ ] Ogni assert può fallire per il motivo dichiarato — da verificare sul codice reale
- [ ] Il valore atteso non è derivato dall'oggetto sotto test — da verificare (rischio maggiore: T-402 hash, T-406 soglia)
- [ ] Nessun campione arbitrario: T-403 e T-405 su insieme completo
- [ ] Contratto coperto (input assenti/vuoti/non validi): T-303 deve includere artefatti invalidi, T-401 operazioni vuote/ignote
- [ ] Ridondanze dichiarate: T-402 (log) e criterio 5 tracciamento sono sovrapposti; ammessa
- [x] Limite dichiarato: gli unit test non provano la registrazione reale del plugin né il comportamento su CI 3 OS → coperti da E2E V13/V14 e dalla CI (E2E-PoC-4)
- [ ] Nessuna mutazione di stato condiviso (log e policy su directory temporanee per test)

## Test e2e previsti

Vedi `E2E_VERIFICATION.md` (V8, V13–V19, E2E-PoC-1…6).

## Dati di test necessari

- Repo sandbox per H-407 (fuori dal repo HELIOS, **[N]** ubicazione da decidere) con change sandbox valida.
- Fixture di policy (floor) e di plan con/ senza approvazione.
- **[N]** Pilota «biblioteca» (Q-5) per T-406 e per costi (criterio 8).

## Casi esclusi dal test (e perché)

- Criterio 10 del PoC (sicurezza dashboard C23–C24): N/A motivato nel PoC (Q-12, SUPERVISORE, 2026-10-29); coperto da T-8b01…T-8b04, T-8b08 in Fase 8b.
- Task puramente documentali (H-101, H-102, H-103, H-203, H-307, H-406, H-800, H-902, H-1101, H-1201): nessun unit test, motivazione in TASK_LIST.

## Chi esegue

- **Scrittura test**: agente (quando approvato il piano), **senza eseguirli**.
- **Esecuzione**: SUPERVISORE; comunica l'esito all'agente che resta in standby.

## Comandi di test rieseguibili

Da eseguire dalla radice `C:\Progetti\VSC\HELIOS-PLATFORM\HELIOS` (**[N]** i file saranno disponibili dopo H-103/H-3xx/H-4xx).

### T-302 — Nessun segreto versionato
- **Pre-test**: `git ls-files cache` → nessun risultato; esistenza di `.gitignore` con `cache/`.
- **Classe**: `python -m pytest tests/test_secrets.py::TestSecrets`
- **Metodo**: `python -m pytest tests/test_secrets.py::TestSecrets::test_nessun_segreto_versionato`
- **Post-test**: invariato (test in sola lettura).

### T-303 — Contratti
- **Pre-test**: esistono gli schemi in `agents/contracts/*.schema.json`.
- **Classe**: `python -m pytest tests/test_contracts.py::TestContracts`
- **Metodo**: `python -m pytest tests/test_contracts.py::TestContracts::test_richiesta_runner_valida`
- **Post-test**: invariato.

### T-304 — Parità shim
- **Pre-test**: Qdrant :6333 e Ollama :11434 attivi (sola lettura sulle collection esistenti, che non vanno modificate).
- **Classe**: `python -m pytest tests/test_compat_parity.py::TestCompatParity`
- **Metodo**: `python -m pytest tests/test_compat_parity.py::TestCompatParity::test_query_qdrant_parita`
- **Post-test**: `CRB_csi_cosmo`, `CRB_mudeopenbtcet`, `CRB_mudeopensrvet`, `project_docs` con conteggio punti invariato (verifica: `python "C:\software\cerebro\scripts\query_qdrant.py" --project <slug> count` prima e dopo, **sola lettura**).

### T-305 — Confini moduli
- **Classe**: `python -m pytest tests/test_module_boundaries.py::TestModuleBoundaries`
- **Metodo**: `python -m pytest tests/test_module_boundaries.py::TestModuleBoundaries::test_cerebro_non_importa_helios`

### T-306 — Matrice CI
- **Classe**: `python -m pytest tests/test_ci_matrix.py::TestCiMatrix`
- **Metodo**: `python -m pytest tests/test_ci_matrix.py::TestCiMatrix::test_tre_os_stessa_suite`

### T-309 — Configurazione locale
- **Pre-test**: nessun `HELIOS_CONFIG` nell'ambiente; `config/helios.local.example.json` presente; il file reale **non** deve essere necessario (i test lavorano su directory temporanee, mai sulla configurazione dell'esecutore).
- **Classe**: `python -m pytest tests/test_local_config.py::TestLocalConfig`
- **Metodi** (uno per asserzione, tutti devono poter fallire per il motivo dichiarato):
  - `...::TestLocalConfig::test_file_assente_esito_incompleto` — nessun file in nessuna delle tre posizioni → esito `INCOMPLETO` con l'elenco dei campi obbligatori mancanti, **non** un'eccezione.
  - `...::TestLocalConfig::test_percorso_inesistente_esito_non_valido` — `paths.cerebro` punta a una cartella che non esiste → `NON VALIDO`, campo colpevole `paths.cerebro`.
  - `...::TestLocalConfig::test_percorso_esistente_ma_non_e_cerebro` — `paths.cerebro` esiste ma è una cartella vuota (manca `scripts/query_qdrant.py`) → `NON VALIDO`: distingue la validazione **semantica** dalla sola esistenza.
  - `...::TestLocalConfig::test_precedenza_env_vince_sul_repo` — con `HELIOS_CONFIG` e file nel repo entrambi presenti e **diversi**, i valori caricati sono quelli di `HELIOS_CONFIG`.
  - `...::TestLocalConfig::test_precedenza_repo_vince_su_utente` — file nel repo e file per utente entrambi presenti e diversi → vince quello nel repo.
  - `...::TestLocalConfig::test_config_version_ignota_errore_esplicito` — `configVersion` superiore a quella supportata → errore con messaggio che cita la versione trovata e quella attesa, mai caricamento silenzioso.
  - `...::TestLocalConfig::test_esempio_versionato_senza_dati_locali` — `config/helios.local.example.json` non contiene percorsi assoluti, nomi utente né valori non vuoti in `paths`.
  - `...::TestLocalConfig::test_file_reale_ignorato_da_git` — per ciascuna delle tre posizioni, il percorso nel repo risulta ignorato (`git check-ignore`); è il test che impedisce la regressione di C20.
  - `...::TestLocalConfig::test_autodiscovery_propone_percorsi_del_repo` — su un repo finto con `limet/` e `cerebro/`, l'autodiscovery propone quei percorsi senza scrivere nulla su disco.
  - `...::TestLocalConfig::test_validazione_non_apre_connessioni` — la validazione dei soli percorsi non effettua chiamate di rete (le sonde su Qdrant/Ollama sono esplicite e separate).
- **Post-test**: nessun file creato fuori dalla directory temporanea; `git status --porcelain` invariato; nessuna variabile d'ambiente lasciata modificata.

### T-401 — Allowlist runner
- **Pre-test**: nessun file nel workspace di prova fuori dalla directory temporanea.
- **Classe**: `python -m pytest tests/test_runner_allowlist.py::TestRunnerAllowlist`
- **Metodo**: `python -m pytest tests/test_runner_allowlist.py::TestRunnerAllowlist::test_rifiuta_operazione_non_ammessa`
- **Post-test**: `git status --porcelain` invariato; `git log -1` invariato (nessun commit).

### T-402 — Log e hash
- **Pre-test**: directory log vuota.
- **Classe**: `python -m pytest tests/test_runner_log.py::TestRunnerLog`
- **Metodo**: `python -m pytest tests/test_runner_log.py::TestRunnerLog::test_hash_riproducibile`
- **Post-test**: due esecuzioni consecutive producono lo stesso hash.

### T-403 — Ruoli senza Bash
- **Classe**: `python -m pytest tests/test_roles_no_bash.py::TestRolesNoBash`
- **Metodo**: `python -m pytest tests/test_roles_no_bash.py::TestRolesNoBash::test_nessun_bash`

### T-404 — Gate PLAN
- **Pre-test**: PLAN di prova in stato DRAFT.
- **Classe**: `python -m pytest tests/test_gate.py::TestGate`
- **Metodo**: `python -m pytest tests/test_gate.py::TestGate::test_blocca_senza_approvazione`
- **Post-test**: PLAN ancora DRAFT, nessun avanzamento.

### T-405 — Pavimento policy
- **Classe**: `python -m pytest tests/test_policy_floor.py::TestPolicyFloor`
- **Metodo**: `python -m pytest tests/test_policy_floor.py::TestPolicyFloor::test_deny_mvnw_non_rimovibile`
- **Post-test**: `agents/policy/floor.json` invariato (`git diff --stat agents/policy/floor.json` vuoto).

### T-406 — A/B
- **Pre-test**: baseline e metrica definite (**[N]** Q-6).
- **Classe**: `python -m pytest tests/test_ab_metrics.py::TestAbMetrics`
- **Metodo**: `python -m pytest tests/test_ab_metrics.py::TestAbMetrics::test_soglia_go_no_go`

### T-407 — Manifest plugin (parte automatica)
- **Classe**: `python -m pytest tests/test_plugin_manifest.py::TestPluginManifest`
- **Metodo**: `python -m pytest tests/test_plugin_manifest.py::TestPluginManifest::test_manifest_e_workflow_presenti`
- **Parte manuale**: E2E V13/V14 (`E2E_VERIFICATION.md`).

---

## Estensione a tutte le fasi (verifica di completezza 2026-10-29)

Le righe sotto coprono i **restanti** test di IN §6 (T-201…T-204, T-308, T-408, T-409, T-501…T-503, T-601…T-611, T-701…T-712, T-801…T-804, T-8b01…T-8b10) **più i test aggiunti dopo IN** (T-8b12, T-504, T-713, T-714, T-715, T-8b13). Con T-302…T-407 sopra — che includono **T-309**, anch'esso non presente in IN §6 — il totale è **66 test T-xxx**: i **58** derivati da IN §6 **[F]** (la riga «69» ipotizzata in precedenza era una stima sbagliata; il conteggio effettivo delle righe IN §6 è 58), più i **3** della configurazione locale (T-309, T-8b11, T-8b12), i **4** della modalità non presidiata (T-713, T-714, T-715, T-8b13) e **1** della documentazione viva (T-504), aggiunti su richiesta del SUPERVISORE del 2026-10-30. Tutti **[N]**: file/classi/metodi proposti, non esistenti fino a implementazione (Q-11b). I test delle fasi 5–12 si eseguono solo dopo il GO del gate H-406. **Pre/post-test comune**: test in sola lettura su directory temporanee; verificare `git status --porcelain` invariato fuori da `tests/`. I test su Qdrant (T-803, T-804) non devono alterare `CRB_csi_cosmo`, `CRB_mudeopenbtcet`, `CRB_mudeopensrvet`, `project_docs` (conteggio punti prima/dopo con `query_qdrant.py ... count`).

| # | Task | Comando classe | Comando metodo |
| - | ---- | -------------- | -------------- |
| T-201 | H-201 | `python -m pytest tests/test_inventory.py::TestInventory` | `python -m pytest tests/test_inventory.py::TestInventory::test_conteggi_completi` |
| T-202 | H-202 | `python -m pytest tests/test_ledger.py::TestLedger` | `python -m pytest tests/test_ledger.py::TestLedger::test_solo_mit_in_allowlist` |
| T-204 | H-204 | `python -m pytest tests/test_chain_matrix.py::TestChainMatrix` | `python -m pytest tests/test_chain_matrix.py::TestChainMatrix::test_nessun_da_classificare` |
| T-308 | H-308 | `python -m pytest tests/test_chain_contracts.py::TestChainContracts` | `python -m pytest tests/test_chain_contracts.py::TestChainContracts::test_verdict_senza_evidence_invalido` |
| T-408 | H-408 | `node --test agents/plugin/scheduler/limet-scheduler.test.js` | `node --test --test-name-pattern="budget" agents/plugin/scheduler/limet-scheduler.test.js` |
| T-409 | H-409 | `python -m pytest tests/test_runner_skill.py::TestRunnerSkill` | `python -m pytest tests/test_runner_skill.py::TestRunnerSkill::test_sha_diverso_rifiutato` |
| T-501 | H-501 | `python -m pytest tests/test_agnostic.py::TestAgnostic` | `python -m pytest tests/test_agnostic.py::TestAgnostic::test_asset_solo_prompt` |
| T-502 | H-502 | `python -m pytest tests/test_rules_per_role.py::TestRulesPerRole` | `python -m pytest tests/test_rules_per_role.py::TestRulesPerRole::test_quattro_common_assenti` |
| T-503 | H-503 | `python -m pytest tests/test_naming.py::TestNaming` | `python -m pytest tests/test_naming.py::TestNaming::test_nessun_file_di_piano_ecc` |
| T-504 | H-504 | `python -m pytest tests/test_architecture_docs.py::TestArchitectureDocs` | `python -m pytest tests/test_architecture_docs.py::TestArchitectureDocs::test_citazioni_file_riga_esistono` |
| T-601 | H-602 | `python -m pytest tests/test_role_analyst.py::TestAnalystRole` | `python -m pytest tests/test_role_analyst.py::TestAnalystRole::test_split_oltre_cap` |
| T-602 | H-603 | `python -m pytest tests/test_role_developer.py::TestDeveloperRole` | `python -m pytest tests/test_role_developer.py::TestDeveloperRole::test_git_solo_pattern_ammessi` |
| T-603 | H-604 | `python -m pytest tests/test_role_tester.py::TestTesterRole` | `python -m pytest tests/test_role_tester.py::TestTesterRole::test_input_senza_diff` |
| T-604 | H-605 | `python -m pytest tests/test_role_integrator.py::TestIntegratorRole` | `python -m pytest tests/test_role_integrator.py::TestIntegratorRole::test_conflitto_semantico_rimandato` |
| T-605 | H-606 | `python -m pytest tests/test_role_reader.py::TestReaderRole` | `python -m pytest tests/test_role_reader.py::TestReaderRole::test_estratto_reale_non_riassunto` |
| T-606 | H-607 | `python -m pytest tests/test_role_reviewer.py::TestReviewerRole` | `python -m pytest tests/test_role_reviewer.py::TestReviewerRole::test_modello_diverso_da_developer` |
| T-607 | H-608 | `python -m pytest tests/test_role_validator.py::TestValidatorRole` | `python -m pytest tests/test_role_validator.py::TestValidatorRole::test_conforme_richiede_evidenze` |
| T-608 | H-609 | `python -m pytest tests/test_metrics_observer.py::TestMetricsObserver` | `python -m pytest tests/test_metrics_observer.py::TestMetricsObserver::test_aggregazione_deterministica` |
| T-609 | H-610 | `python -m pytest tests/test_role_e2e.py::TestE2eRoles` | `python -m pytest tests/test_role_e2e.py::TestE2eRoles::test_nessun_passo_inventato` |
| T-610 | H-611 | `python -m pytest tests/test_roles_frontmatter.py::TestCommonAndFrontmatter` | `python -m pytest tests/test_roles_frontmatter.py::TestCommonAndFrontmatter::test_modelli_come_da_tabella` |
| T-611 | H-612 | `python -m pytest tests/test_role_risk_assessor.py::TestRiskAssessor` | `python -m pytest tests/test_role_risk_assessor.py::TestRiskAssessor::test_rischio_senza_opzioni_invalido` |
| T-701 | H-701 | `python -m pytest tests/test_workflow_states.py::TestWorkflowStates` | `python -m pytest tests/test_workflow_states.py::TestWorkflowStates::test_retry_poi_stop` |
| T-702 | H-702 | `python -m pytest tests/test_installer.py::TestInstaller` | `python -m pytest tests/test_installer.py::TestInstaller::test_idempotente_senza_sovrascrivere` |
| T-703 | H-703 | `python -m pytest tests/test_graph_gate.py::TestGraphGate` | `python -m pytest tests/test_graph_gate.py::TestGraphGate::test_nessun_lancio_senza_conferma` |
| T-704 | H-704 | `python -m pytest tests/test_waves.py::TestWaves` | `python -m pytest tests/test_waves.py::TestWaves::test_parallelismo_entro_limiti` |
| T-705 | H-705 | `python -m pytest tests/test_integration_flow.py::TestIntegrationFlow` | `python -m pytest tests/test_integration_flow.py::TestIntegrationFlow::test_avanza_solo_con_sha_verde` |
| T-706 | H-706 | `python -m pytest tests/test_state_events.py::TestStateEvents` | `python -m pytest tests/test_state_events.py::TestStateEvents::test_stale_dopo_merge` |
| T-707 | H-707 | `python -m pytest tests/test_ask_and_stop.py::TestAskAndStop` | `python -m pytest tests/test_ask_and_stop.py::TestAskAndStop::test_blocco_non_globale` |
| T-708 | H-708 | `python -m pytest tests/test_budget_resume.py::TestBudgetResume` | `python -m pytest tests/test_budget_resume.py::TestBudgetResume::test_ripresa_da_checkpoint` |
| T-709 | H-709 | `python -m pytest tests/test_reports.py::TestReports` | `python -m pytest tests/test_reports.py::TestReports::test_incompleto_mai_completato` |
| T-710 | H-710 | `python -m pytest tests/test_permissions_applied.py::TestPermissionsApplied` | `python -m pytest tests/test_permissions_applied.py::TestPermissionsApplied::test_deny_push_effettivo` |
| T-711 | H-711 | `python -m pytest tests/test_merge_gates.py::TestMergeGates` | `python -m pytest tests/test_merge_gates.py::TestMergeGates::test_regressione_fallisce_prima_del_fix` |
| T-712 | H-712 | `python -m pytest tests/test_limet_doc_changes.py::TestLimetDocChanges` | `python -m pytest tests/test_limet_doc_changes.py::TestLimetDocChanges::test_m1_m9_it_en` |
| T-713 | H-713 | `python -m pytest tests/test_unattended_mode.py::TestUnattendedMode` | `python -m pytest tests/test_unattended_mode.py::TestUnattendedMode::test_permessi_identici_a_modalita_presidiata` |
| T-714 | H-714 | `python -m pytest tests/test_autonomous_decisions_log.py::TestAutonomousDecisionsLog` | `python -m pytest tests/test_autonomous_decisions_log.py::TestAutonomousDecisionsLog::test_ogni_decisione_ha_evidenza_e_reversibilita` |
| T-715 | H-715 | `python -m pytest tests/test_unattended_roles.py::TestUnattendedRoles` | `python -m pytest tests/test_unattended_roles.py::TestUnattendedRoles::test_ruolo_ignoto_e_negato` |
| T-801 | H-801 | `python -m pytest tests/test_llm_registry.py::TestLlmRegistry` | `python -m pytest tests/test_llm_registry.py::TestLlmRegistry::test_chiamata_non_registrata_fallisce` |
| T-802 | H-802 | `python -m pytest tests/test_agentshield_wrapper.py::TestAgentShieldWrapper` | `python -m pytest tests/test_agentshield_wrapper.py::TestAgentShieldWrapper::test_senza_opus_e_senza_rete` |
| T-803 | H-803 | `python -m pytest tests/test_setup_hls_kb.py::TestSetupHlsKb` | `python -m pytest tests/test_setup_hls_kb.py::TestSetupHlsKb::test_non_tocca_collection_esistenti` |
| T-804 | H-804 | `python -m pytest tests/test_knowledge_access.py::TestKnowledgeAccess` | `python -m pytest tests/test_knowledge_access.py::TestKnowledgeAccess::test_tester_senza_graphify` |
| T-8b01 | H-8b01 | `python -m pytest tests/api/test_loopback.py::TestApiLoopback` | `python -m pytest tests/api/test_loopback.py::TestApiLoopback::test_bind_solo_127_0_0_1` |
| T-8b02 | H-8b01 | `python -m pytest tests/api/test_auth.py::TestApiAuth` | `python -m pytest tests/api/test_auth.py::TestApiAuth::test_origin_esterno_rifiutato` |
| T-8b03 | H-8b01 | `python -m pytest tests/api/test_hardening.py::TestApiHardening` | `python -m pytest tests/api/test_hardening.py::TestApiHardening::test_csp_restrittiva` |
| T-8b04 | H-8b01 | `python -m pytest tests/api/test_allowlist.py::TestApiAllowlist` | `python -m pytest tests/api/test_allowlist.py::TestApiAllowlist::test_nessuna_azione_git` |
| T-8b05 | H-8b02 | `python -m pytest tests/api/test_read_model.py::TestReadModel` | `python -m pytest tests/api/test_read_model.py::TestReadModel::test_cache_rigenerabile` |
| T-8b06 | H-8b03 | `npm --prefix ui test -- views.test.tsx` | `npm --prefix ui test -- views.test.tsx -t "Portfolio"` |
| T-8b07 | H-8b04 | `npm --prefix ui test -- permissions.test.tsx` | `npm --prefix ui test -- permissions.test.tsx -t "richiede conferma"` |
| T-8b08 | H-8b05 | `python -m pytest tests/api/test_gate_human_only.py::TestGateHumanOnly` | `python -m pytest tests/api/test_gate_human_only.py::TestGateHumanOnly::test_agente_non_approva` |
| T-8b09 | H-8b06 | `npm --prefix ui test -- a11y.test.tsx` | `npm --prefix ui test -- a11y.test.tsx -t "contrasto"` |
| T-8b10 | H-8b07 | `python -m pytest tests/test_frontend_deps.py::TestFrontendDeps` | `python -m pytest tests/test_frontend_deps.py::TestFrontendDeps::test_licenze_in_allowlist` |
| T-8b11 | H-8b09 | `python -m pytest tests/api/test_config_gate.py::TestConfigGate` | `python -m pytest tests/api/test_config_gate.py::TestConfigGate::test_nessuna_operazione_senza_config_valida` |
| T-8b12 | H-8b09 | `npm --prefix ui test -- config-dialog.test.tsx` | `npm --prefix ui test -- config-dialog.test.tsx -t "precompilata"` |
| T-8b13 | H-8b10 | `npm --prefix ui test -- unattended-panel.test.tsx` | `npm --prefix ui test -- unattended-panel.test.tsx -t "mostra il perimetro prima dell'avvio"` |

Nota: i file di test di T-8b01…T-8b05, T-8b08 e T-8b11 sono sotto `tests/api/`, quelli UI (T-8b06, T-8b07, T-8b09, T-8b12, T-8b13) sotto `ui/src/__tests__/` (IN §6; `--prefix ui` esegue da `ui/`). I file `.tsx` si indicano senza il percorso completo; **[N]** conferma in H-8b03.

### Copertura task → test e task senza test (elenco esplicito)

- Senza T-xxx per natura documentale: H-101, H-102, H-103, H-203, H-307, H-406, H-505, H-506, H-800, H-902, H-1101, H-1201.
- **[N]** Senza T-xxx in IN §6 e con verifica da definire: **H-800b** (un test per hook replicato), **H-1001** (audit). Da proporre al SUPERVISORE quando H-800 e H-1001 partono.
- Mappature: T-403 → H-402 e H-601; T-405 → H-404 e H-8b04; T-304 → H-304 e H-8b08; T-8b01…T-8b04 → H-8b01; T-8b11 e T-8b12 → H-8b09.
- **Aggiunti dopo IN §6** (richiesta SUPERVISORE 2026-10-30, configurazione locale): **T-309** → H-309; **T-8b11** (gate API) e **T-8b12** (dialog UI) → H-8b09. Totale 58 + 3 = **61**.
- **Aggiunti dopo IN §6** (richiesta SUPERVISORE 2026-10-30, modalità non presidiata, ADR 0003): **T-713** → H-713; **T-714** → H-714; **T-715** → H-715; **T-8b13** → H-8b10. Totale 61 + 4 = **65**.
- **Aggiunti dopo IN §6** (richiesta SUPERVISORE 2026-10-30, documentazione viva): **T-504** → H-504. Totale 65 + 1 = **66**. H-505 e H-506 restano senza T-xxx per natura documentale, con verifica a criteri osservabili dichiarata nella `TASK_LIST.md`.

### Dettaglio del test della documentazione viva (H-504)

Test di **Fase 5**, eseguibile solo dopo il GO. Stessa profondità di T-309.

#### T-504 — Integrità della documentazione di architettura (H-504)

- **Pre-test**: copia dei documenti in directory temporanea; l'albero dei moduli atteso è costruito **dal test** scorrendo il filesystem, mai letto da `module-map.md` (altrimenti il valore atteso deriverebbe dall'oggetto sotto esame).
- **Classe**: `python -m pytest tests/test_architecture_docs.py::TestArchitectureDocs`
- **Metodi**:
  - `...::test_citazioni_file_riga_esistono` — ogni citazione `percorso:riga` presente in `architecture.md` e `module-map.md` punta a un file esistente e a un numero di riga **compreso nel file**. È il controllo che intercetta il marciume più comune: il codice si sposta, la citazione resta.
  - `...::test_citazione_oltre_fine_file_fallisce` — caso negativo costruito apposta: una citazione a una riga oltre la fine del file deve far **fallire** il controllo. Senza, il metodo precedente potrebbe passare sempre.
  - `...::test_ogni_modulo_del_repo_compare_nella_mappa` — ogni directory di modulo trovata sul filesystem ha una voce in `module-map.md`.
  - `...::test_modulo_nuovo_non_documentato_fallisce` — creato un modulo fittizio nella copia temporanea, il controllo deve segnalarlo. È l'asserzione che rende il test utile a ogni fase successiva, non solo alla prima.
  - `...::test_voce_orfana_nella_mappa_fallisce` — una voce che punta a un modulo non più esistente viene segnalata. Copre la direzione opposta della precedente.
  - `...::test_diagrammi_mermaid_analizzabili` — ogni blocco ` ```mermaid ` si analizza senza errori di sintassi; un blocco malformato fa fallire.
  - `...::test_documenti_presenti_e_non_vuoti` — `architecture.md` e `module-map.md` esistono e hanno contenuto oltre all'intestazione del template. Contratto minimo: senza, tutti gli altri metodi passerebbero a vuoto su file inesistenti.
- **Post-test**: nessuna scrittura fuori dalla directory temporanea; `git status --porcelain` invariato; nessun documento reale modificato dal test.
- **Autorevisione (manuale §5.1)**: le alterazioni plausibili considerate — (1) spostare una funzione citata senza aggiornare la citazione → intercettata dal 1° metodo; (2) aggiungere un modulo senza documentarlo → 4°; (3) cancellare un modulo lasciando la voce → 5°; (4) rompere un diagramma → 6°; (5) svuotare il documento lasciando il titolo → 7°. Ogni metodo ha il proprio caso negativo, quindi nessuno può passare per vacuità.

### Dettaglio dei test della modalità non presidiata (ADR 0003)

Sono test di **Fase 7 e 8b**, eseguibili solo dopo il GO. Il livello di dettaglio è quello di
T-309: stato pre/post esplicito e un metodo per asserzione.

#### T-713 — Modalità non presidiata (H-713)

- **Pre-test**: nessun run in corso; orologio e registro eventi **iniettati** (il test non attende tempo reale); `limits.json` di prova in directory temporanea; nessun kill switch presente.
- **Classe**: `python -m pytest tests/test_unattended_mode.py::TestUnattendedMode`
- **Metodi**:
  - `...::test_permessi_identici_a_modalita_presidiata` — i permessi effettivi calcolati per uno stesso ruolo in presidiato e in non presidiato **coincidono**. È il test che protegge il principio dell'ADR 0003 §2: senza, la feature può diventare un bypass di C02/C24.
  - `...::test_config_night_non_puo_derogare_ai_deny` — un `limits.json` che prova ad ammettere `git push` in `night` viene **respinto**, non applicato parzialmente.
  - `...::test_domanda_bloccante_parcheggia_solo_i_dipendenti` — con `stopOnQuestions` = `false`, il task dubbio passa a `WAITING_SUPERVISOR`, i suoi dipendenti si bloccano e almeno un task **indipendente** avanza.
  - `...::test_stop_on_questions_true_ferma_tutto` — con `true`, nessun task avanza dopo la domanda e il run chiude `INCOMPLETO`. Copre il ramo non predefinito.
  - `...::test_durata_massima_ferma_il_run` — superati i 480 minuti simulati, il run chiude in modo controllato con stato `INCOMPLETO`, non viene ucciso a metà passo.
  - `...::test_budget_esaurito_ferma_il_run` — budget esaurito → stop controllato; distinto dal caso durata perché la causa registrata deve essere diversa.
  - `...::test_kill_switch_letto_a_ogni_passo` — il file di abort creato **dopo** l'avvio ha effetto entro il passo successivo; non basta leggerlo una volta all'avvio.
  - `...::test_dead_man_switch_interrompe_su_registro_fermo` — se il registro eventi non si aggiorna oltre la soglia, il run si interrompe da solo anche senza superare durata o budget.
  - `...::test_stato_sempre_ripartibile_da_checkpoint` — per **ognuna** delle cause di fine corsa, esiste un checkpoint che consente `resumeFromRunId`. È l'asserzione che rende accettabile la non reperibilità (Q-14).
  - `...::test_ruolo_escluso_non_viene_eseguito` — integrazione con H-715: al turno di un ruolo escluso il task si parcheggia e il run prosegue.
- **Post-test**: nessun file creato fuori dalla directory temporanea; `git status --porcelain` invariato; nessun commit, nessun push; nessun kill switch residuo.

#### T-714 — Registro delle decisioni autonome (H-714)

- **Pre-test**: registro vuoto; run simulato con decisioni note, prodotte dal test e non lette dall'oggetto sotto esame.
- **Classe**: `python -m pytest tests/test_autonomous_decisions_log.py::TestAutonomousDecisionsLog`
- **Metodi**:
  - `...::test_ogni_decisione_ha_evidenza_e_reversibilita` — ogni voce ha task, motivazione, evidenza e modo di annullarla; una voce incompleta fa fallire.
  - `...::test_run_senza_decisioni_produce_sezione_vuota_esplicita` — la sezione «decisioni autonome» esiste comunque e dichiara l'assenza: il silenzio non deve essere ambiguo.
  - `...::test_registro_append_only` — una riga già scritta non può essere modificata né rimossa.
  - `...::test_nessun_segreto_nel_registro` — valori riconducibili a segreti o token sono redatti.
  - `...::test_nessun_prompt_integrale_nel_registro` — il registro non contiene prompt completi né diff integrali.
  - `...::test_report_non_dichiara_completato_un_run_incompleto` — con task `WAITING_SUPERVISOR` o `UNDETERMINED`, il report non usa mai «completato» (R18, H-709).
  - `...::test_decisione_fuori_perimetro_non_e_registrata_ma_impedita` — un tentativo di decisione fuori perimetro (ADR 0003 §3) solleva il blocco **e** non lascia una voce «decisa»; distingue il registro da un log permissivo.
  - `...::test_sezione_presente_nel_report_notturno` — la sezione compare in `REPORT_NOTTURNO.md` nella posizione attesa, non solo nel registro interno.
- **Post-test**: registro e report restano nella directory temporanea; `git status --porcelain` invariato.

#### T-715 — Allow-list dei ruoli non presidiati (H-715)

- **Pre-test**: elenco dei ruoli di ALA §1.3 come dato del test, non ricavato dal modulo sotto esame (altrimenti l'asserzione non potrebbe fallire).
- **Classe**: `python -m pytest tests/test_unattended_roles.py::TestUnattendedRoles`
- **Metodi**:
  - `...::test_ruoli_ammessi_sono_esattamente_i_sei_previsti` — l'insieme ammesso è esattamente `developer`, `tester`, `runner`, `test-reader`, `reviewer`, `metrics-observer`.
  - `...::test_ruoli_esclusi_parcheggiano_il_task` — per **ciascuno** di `analyst`, `validator`, `integrator`, `risk-assessor`, `commit-commands` il task passa a `WAITING_SUPERVISOR` con motivazione non vuota.
  - `...::test_ruolo_ignoto_e_negato` — un ruolo non previsto è trattato come escluso: default negato, non default permesso.
  - `...::test_allow_list_non_ampliabile_da_configurazione` — un `limits.json` che prova ad aggiungere `integrator` ai ruoli notturni viene respinto.
  - `...::test_parcheggio_non_blocca_i_task_indipendenti` — il run prosegue sugli indipendenti anche dopo un parcheggio per ruolo escluso.
  - `...::test_developer_puo_committare_sul_ramo_del_task` — attua Q-18: il commit sul ramo del task è ammesso in non presidiato.
  - `...::test_developer_non_puo_pushare_ne_mergiare` — il contraltare del precedente: `push` e `merge` restano negati (R19, R21).
- **Post-test**: `git status --porcelain` invariato; nessun ramo creato fuori dalla directory temporanea.

#### T-8b13 — Pannello di avvio non presidiato (H-8b10)

- **Pre-test**: API simulata; nessun run in corso; configurazione locale valida (il gate H-8b09 non deve interferire).
- **Classe**: `npm --prefix ui test -- unattended-panel.test.tsx`
- **Metodi** (`-t "<nome>"`):
  - `-t "mostra il perimetro prima dell'avvio"` — perimetro di autonomia, ruoli ammessi e limiti sono visibili **prima** del pulsante di avvio, non dopo.
  - `-t "mostra i limiti effettivi"` — durata, budget e numero massimo di task mostrati coincidono con quelli che l'API applicherà, non con valori scritti nella UI.
  - `-t "avvio disabilitato senza sessione umana"` — senza sessione umana autenticata l'avvio non è attivabile (C24).
  - `-t "kill switch raggiungibile durante il run"` — a run avviato il comando di arresto è sempre presente e abilitato.
  - `-t "la UI non amplia i permessi"` — il pannello non espone alcun controllo che aggiunga permessi o rimuova un `deny`.
- **Post-test**: nessuna chiamata di rete verso destinazioni diverse dal loopback; nessuno stato residuo fra i test.

**Autorevisione §5.1 (preliminare, sul disegno)** — alterazione plausibile che *non* farebbe fallire
alcun test, e come è stata coperta: (1) leggere il kill switch solo all'avvio → coperta da
`test_kill_switch_letto_a_ogni_passo`; (2) consentire a `limits.json` di ampliare permessi o ruoli →
coperta da `test_config_night_non_puo_derogare_ai_deny` e `test_allow_list_non_ampliabile_da_configurazione`;
(3) trattare un ruolo ignoto come ammesso → coperta da `test_ruolo_ignoto_e_negato`; (4) fermare
tutto il run invece dei soli dipendenti → coperta da `test_domanda_bloccante_parcheggia_solo_i_dipendenti`;
(5) chiudere un run senza checkpoint ripartibile → coperta da `test_stato_sempre_ripartibile_da_checkpoint`.
Da riverificare sul codice reale quando H-713…H-715 e H-8b10 vengono implementati.
- **Autorevisione §5.1 per i test aggiunti (preliminare, sul disegno)**: alterazioni plausibili da coprire quando si scrivono i test: T-704 un cap `maxAgents` ignorato (serve caso con N+1 task); T-705 avanzamento con SHA diverso (caso negativo); T-707 stop globale invece di parziale; T-709 report «completato» con task `UNDETERMINED`; T-611 «nessun rischio» senza elenco controlli; T-803 creazione su nome esistente; T-804 tester con accesso a Graphify (già nel metodo) **e** a codice del developer; T-8b04 azioni `build/test/git` oltre a quella nominata (un caso per voce di allowlist). Da riverificare sul codice reale.

## Test E2E ALA V1–V24 (H-901)

Comandi e passi per scenario in `E2E_VERIFICATION.md` (con comando rieseguibile per scenario). **[N]** Per V1–V12 e V20–V24 i comandi dipendono dallo scaffold E2E di H-901 (nomi non in IN): sono marcati nel documento.
