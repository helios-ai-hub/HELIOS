# Appendice — Registro completo skill e agenti ECC @25278e7

Generato da inventario scriptato (100% di `skills/` e `agents/`). **[F]** elenco, licenza dichiarata, strumenti, script, segnali di rischio (euristica a regex, non verifica semantica). **[P]** colonna *Decisione*: tutte «da classificare» in Fase 2 (H-201); l'obiettivo del SUPERVISORE è usarne il più possibile, escluse solo quelle non sicure/incompatibili (C03–C07).

Legenda segnali: net=rete, build_test=build/test, git_write=commit/push, destructive=operazioni distruttive, llm_send=invio a LLM, secrets=riferimenti a segreti, hooks_mem=hook/memoria ECC, plan_flow=flusso di pianificazione ECC.

Totali: 311 skill (29 con script), 71 agenti (55 con Bash, 28 con Write/Edit).

## Skills

| # | Skill | Descrizione | Licenza dichiarata | File | Script | Segnali | Decisione |
|---|---|---|---|---|---|---|---|
| 1 | `accessibility` | Design, implement, and audit accessible UI to WCAG 2.2 Level AA across Web, iOS, and Andro | (assente) | 1 |  | net | da classificare |
| 2 | `agent-architecture-audit` | Full-stack diagnostic for agent and LLM applications. Audits the 12-layer agent stack for  | (assente) | 1 |  |  | da classificare |
| 3 | `agent-eval` | Head-to-head comparison of coding agents (Claude Code, Aider, Codex, etc.) on custom tasks | MIT | 1 |  | net,build_test | da classificare |
| 4 | `agent-harness-construction` | Design and optimize AI agent action spaces, tool definitions, and observation formatting f | (assente) | 1 |  | secrets | da classificare |
| 5 | `agent-introspection-debugging` | Structured self-debugging workflow for AI agent failures using capture, diagnosis, contain | (assente) | 1 |  | secrets,hooks_mem | da classificare |
| 6 | `agent-payment-x402` | Add x402 payment execution to AI agents with per-task budgets, spending controls, and non- | (assente) | 1 |  | net,secrets | da classificare |
| 7 | `agent-runtime-gateway-smoke-test` | Verify a local agent API, temporary gateway tunnel, and remote sandbox callback with a too | (assente) | 1 |  | net,secrets | da classificare |
| 8 | `agent-security-hardening` | Security hardening guidance for AI agent frameworks that process untrusted content, invoke | (assente) | 1 |  | secrets | da classificare |
| 9 | `agent-self-evaluation` | Use after completing any non-trivial task. The agent self-rates its output on 5 axes — acc | (assente) | 7 | .py | net,build_test,hooks_mem | da classificare |
| 10 | `agent-sort` | Build an evidence-backed ECC install plan for a specific repo by sorting skills, commands, | (assente) | 1 |  |  | da classificare |
| 11 | `agentic-engineering` | Operate as an agentic engineer using eval-first execution, decomposition, and cost-aware m | (assente) | 1 |  | secrets | da classificare |
| 12 | `agentic-os` | Build persistent multi-agent operating systems on Claude Code. Covers kernel architecture, | (assente) | 1 |  | secrets,llm_send | da classificare |
| 13 | `ai-first-engineering` | Engineering operating model for teams where AI agents generate a large share of implementa | (assente) | 1 |  |  | da classificare |
| 14 | `ai-regression-testing` | Regression testing strategies for AI-assisted development. Sandbox-mode API testing withou | (assente) | 1 |  | net,build_test | da classificare |
| 15 | `android-clean-architecture` | Clean Architecture patterns for Android and Kotlin Multiplatform projects — module structu | (assente) | 1 |  | net,build_test | da classificare |
| 16 | `angular-developer` | Generates Angular code and provides architectural guidance. Trigger when creating projects | (assente) | 36 |  | net,secrets | da classificare |
| 17 | `api-connector-builder` | Build a new API connector or provider by matching the target repo's existing integration p | (assente) | 1 |  |  | da classificare |
| 18 | `api-design` | REST API design patterns including resource naming, status codes, pagination, filtering, e | (assente) | 1 |  | secrets | da classificare |
| 19 | `architecture-decision-records` | Capture architectural decisions as numbered ADR markdown files in docs/adr/ with context,  | (assente) | 1 |  | secrets | da classificare |
| 20 | `article-writing` | Write articles, guides, blog posts, tutorials, newsletter issues, and other long-form cont | (assente) | 1 |  |  | da classificare |
| 21 | `automation-audit-ops` | Evidence-first automation inventory and overlap audit workflow for ECC. Use when the user  | (assente) | 1 |  |  | da classificare |
| 22 | `autonomous-agent-harness` | Transform Claude Code into a fully autonomous agent system with persistent memory, schedul | (assente) | 1 |  | net,llm_send | da classificare |
| 23 | `autonomous-loops` | Patterns and architectures for autonomous Claude Code loops — from simple sequential pipel | (assente) | 1 |  | git_write,build_test,secrets,llm_send | da classificare |
| 24 | `backend-patterns` | Backend architecture patterns, API design, database optimization, and server-side best pra | (assente) | 1 |  | secrets | da classificare |
| 25 | `benchmark` | Measure performance baselines and detect regressions across browser Core Web Vitals (LCP,  | MIT | 1 |  |  | da classificare |
| 26 | `benchmark-methodology` | Score a scoped competitor set into comparable profile cards: nine weighted dimensions (pos | MIT | 1 |  |  | da classificare |
| 27 | `benchmark-optimization-loop` | Convert 'make it faster' requests into a bounded measured optimization loop — baseline fir | MIT | 1 |  |  | da classificare |
| 28 | `bengali-nlp` | Bengali (Bangla) text processing patterns including Unicode normalization, script detectio | (assente) | 1 |  | secrets | da classificare |
| 29 | `blender-motion-state-inspection` | Use this skill when inspecting Blender characters, rigs, poses, animation retargeting, gro | (assente) | 1 |  |  | da classificare |
| 30 | `blueprint` | Turn a one-line objective into a step-by-step construction plan for multi-session, multi-a | (assente) | 1 |  | llm_send,plan_flow | da classificare |
| 31 | `brand-discovery` | Run a structured, resumable multi-session brand identity interview across 8 modules (purpo | (assente) | 9 |  |  | da classificare |
| 32 | `brand-voice` | Build a source-derived writing style profile from real posts, essays, launch notes, docs,  | (assente) | 2 |  |  | da classificare |
| 33 | `brownfield-retrospective` | Run the ECC engineering process retroactively against an existing project slice. Audit cur | (assente) | 1 |  | hooks_mem | da classificare |
| 34 | `browser-qa` | Run automated post-deploy UI verification with a browser automation MCP (claude-in-chrome, | (assente) | 1 |  |  | da classificare |
| 35 | `bun-runtime` | Bun as runtime, package manager, bundler, and test runner. When to choose Bun vs Node, mig | (assente) | 1 |  | net | da classificare |
| 36 | `canary-watch` | Use this skill to monitor and verify a deployed URL after releases — checks HTTP endpoints | (assente) | 1 |  | net,git_write,hooks_mem | da classificare |
| 37 | `carrier-relationship-management` | Manage truckload, LTL, and intermodal carrier portfolios: sourcing and FMCSA vetting, frei | Apache-2.0 | 1 |  |  | da classificare |
| 38 | `change-impact` | >- | (assente) | 1 |  | plan_flow | da classificare |
| 39 | `cisco-ios-patterns` | Cisco IOS and IOS-XE review patterns for show commands, config hierarchy, wildcard masks,  | (assente) | 1 |  |  | da classificare |
| 40 | `ck` | Persistent per-project memory for Claude Code (Context Keeper) driven by deterministic Nod | (assente) | 10 | .mjs | net | da classificare |
| 41 | `claude-devfleet` | Orchestrate multi-agent coding tasks via Claude DevFleet — plan projects, dispatch paralle | (assente) | 1 |  | net | da classificare |
| 42 | `click-path-audit` | Trace every user-facing button/touchpoint through its full state change sequence to find b | (assente) | 1 |  |  | da classificare |
| 43 | `clickhouse-io` | ClickHouse database patterns, query optimization, analytics, and data engineering best pra | (assente) | 1 |  | net,secrets | da classificare |
| 44 | `code-tour` | Create CodeTour `.tour` files — persona-targeted, step-by-step walkthroughs with real file | (assente) | 1 |  | net | da classificare |
| 45 | `codebase-onboarding` | Analyze an unfamiliar codebase and generate a structured onboarding guide with architectur | (assente) | 1 |  | build_test | da classificare |
| 46 | `codehealth-mcp` | Real-time structural Code Health via CodeScene MCP — review before edits, verify score del | (assente) | 1 |  | net,secrets | da classificare |
| 47 | `coding-standards` | Baseline cross-project coding conventions for naming, readability, immutability, and code- | (assente) | 1 |  | net,llm_send | da classificare |
| 48 | `competitive-platform-analysis` | Use when scoping a competitive landscape — identifying, categorising, and score-filtering  | (assente) | 1 |  |  | da classificare |
| 49 | `competitive-report-structure` | Assemble scored competitor profile cards (from benchmark-methodology) into a decision-grad | (assente) | 1 |  |  | da classificare |
| 50 | `compose-multiplatform-patterns` | Compose Multiplatform and Jetpack Compose patterns for KMP projects — state management, na | (assente) | 1 |  |  | da classificare |
| 51 | `config-gc` | Garbage collection for your Claude Code configuration. Periodically scans ~/.claude (skill | (assente) | 1 |  | git_write,hooks_mem | da classificare |
| 52 | `configure-ecc` | Run the conversational ECC setup wizard inside the current harness: inventory the install, | (assente) | 1 |  |  | da classificare |
| 53 | `connections-optimizer` | Reorganize the user's X and LinkedIn network with review-first pruning, add/follow recomme | (assente) | 1 |  |  | da classificare |
| 54 | `content-engine` | Create platform-native content systems for X, LinkedIn, TikTok, YouTube, newsletters, and  | (assente) | 1 |  |  | da classificare |
| 55 | `content-hash-cache-pattern` | Cache expensive file processing results using SHA-256 content hashes — path-independent, a | (assente) | 1 |  |  | da classificare |
| 56 | `context-and-decisions` | >- | (assente) | 2 |  |  | da classificare |
| 57 | `context-budget` | Routes live context checks to the native Codex or Claude Code meter without scanning files | (assente) | 1 |  | net,secrets,llm_send | da classificare |
| 58 | `continuous-agent-loop` | Patterns for continuous autonomous agent loops with quality gates, evals, and recovery con | (assente) | 1 |  |  | da classificare |
| 59 | `continuous-learning` | [DEPRECATED - use continuous-learning-v2] Legacy v1 stop-hook skill extractor. v2 is a str | (assente) | 3 | .json,.sh | net,hooks_mem | da classificare |
| 60 | `continuous-learning-v2` | Instinct-based learning system that observes sessions via hooks, creates atomic instincts  | (assente) | 12 | .json,.py,.sh | net,destructive,build_test,secrets,hooks_mem | da classificare |
| 61 | `contract-first` | Coordinate frontend/backend or service-to-service work through one authoritative machine-c | (assente) | 1 |  | secrets | da classificare |
| 62 | `conventional-commit-review` | Reviews staged changes and drafts a Conventional Commits-compliant commit message with the | (assente) | 1 |  | git_write,secrets | da classificare |
| 63 | `cost-aware-llm-pipeline` | Cost optimization patterns for LLM API usage — model routing by task complexity, budget tr | (assente) | 1 |  | llm_send | da classificare |
| 64 | `cost-tracking` | Track and report Claude Code token usage, spending, and budgets from the local ECC cost-tr | (assente) | 1 |  | secrets | da classificare |
| 65 | `council` | Convene a four-voice council for ambiguous decisions, tradeoffs, and go/no-go calls. Use w | (assente) | 1 |  |  | da classificare |
| 66 | `council-multi-model` | Add one optional external Codex critique after the existing council has produced a decisio | (assente) | 2 | .js | llm_send | da classificare |
| 67 | `counterparty-channel-discipline` | Per-channel strict prompts, mention gating, silent observation, and a communication autono | (assente) | 3 | .yaml |  | da classificare |
| 68 | `cpp-coding-standards` | C++ coding standards based on the C++ Core Guidelines (isocpp.github.io). Use when writing | (assente) | 1 |  | net,secrets | da classificare |
| 69 | `cpp-testing` | Use only when writing/updating/fixing C++ tests, configuring GoogleTest/CTest, diagnosing  | (assente) | 1 |  | net | da classificare |
| 70 | `crosspost` | Multi-platform content distribution across X, LinkedIn, Threads, and Bluesky. Adapts conte | (assente) | 1 |  |  | da classificare |
| 71 | `csharp-testing` | C# and .NET testing patterns with xUnit, FluentAssertions, mocking, integration tests, and | (assente) | 1 |  | build_test,secrets | da classificare |
| 72 | `customer-billing-ops` | Operate customer billing workflows such as subscriptions, refunds, churn triage, billing-p | (assente) | 1 |  | secrets | da classificare |
| 73 | `customs-trade-compliance` | Codified customs and trade compliance expertise — HS/HTS tariff classification with GRI ru | Apache-2.0 | 1 |  |  | da classificare |
| 74 | `dart-flutter-patterns` | Production-ready Dart and Flutter patterns covering null safety, immutable state with Free | (assente) | 1 |  | net,secrets | da classificare |
| 75 | `dashboard-builder` | Build monitoring dashboards that answer real operator questions for Grafana, SigNoz, and s | (assente) | 1 |  |  | da classificare |
| 76 | `data-scraper-agent` | Build a fully automated AI-powered data collection agent for any public source — job board | (assente) | 1 |  | net,git_write,secrets | da classificare |
| 77 | `data-throughput-accelerator` | Diagnose and accelerate large data movement — ingestion, backfill, export, ETL, warehouse  | MIT | 1 |  |  | da classificare |
| 78 | `database-migrations` | Safe, reversible database migration patterns: forward-only production changes, expand-cont | (assente) | 1 |  |  | da classificare |
| 79 | `deep-research` | Produce cited research reports from multiple web sources using firecrawl and exa MCP tools | (assente) | 1 |  |  | da classificare |
| 80 | `deepseek-harness-setup` | Install and operate Everything Claude Code (ECC) on the DeepSeek Harness (DSH): native ski | (assente) | 1 |  | hooks_mem | da classificare |
| 81 | `defi-amm-security` | Security checklist for Solidity AMM contracts, liquidity pools, and swap flows. Covers ree | (assente) | 1 |  | secrets | da classificare |
| 82 | `delivery-gate` | Stop hook that blocks Claude from finishing until quality checks pass. Detects rationaliza | (assente) | 2 | .py | hooks_mem | da classificare |
| 83 | `deployment-patterns` | Deployment workflows, CI/CD pipeline patterns, Docker containerization, health checks, rol | (assente) | 1 |  | net,build_test,secrets | da classificare |
| 84 | `design-system` | Generate a design system from an existing codebase or audit one for visual consistency: ex | (assente) | 1 |  | net,secrets | da classificare |
| 85 | `dev-team` | Simulate a collaborative dev team session where multiple role-based personas (PM, Architec | (assente) | 1 |  | secrets,plan_flow | da classificare |
| 86 | `django-celery` | Django + Celery async task patterns — configuration, task design, beat scheduling, retries | (assente) | 1 |  | build_test,secrets | da classificare |
| 87 | `django-patterns` | Django architecture patterns, REST API design with DRF, ORM best practices, caching, signa | (assente) | 1 |  | secrets | da classificare |
| 88 | `django-security` | Django security best practices, authentication, authorization, CSRF protection, SQL inject | (assente) | 1 |  | net,secrets | da classificare |
| 89 | `django-tdd` | Django testing strategies with pytest-django, TDD methodology, factory_boy, mocking, cover | (assente) | 1 |  | build_test,secrets | da classificare |
| 90 | `django-verification` | Run the full Django verification loop — environment check, mypy/ruff/black linting, migrat | (assente) | 1 |  | net,build_test,secrets | da classificare |
| 91 | `dmux-workflows` | Multi-agent orchestration using dmux (tmux pane manager for AI agents). Patterns for paral | (assente) | 1 |  | net,git_write,secrets,llm_send,plan_flow | da classificare |
| 92 | `docker-patterns` | Docker and Docker Compose patterns for local development, hardened CLI installer harnesses | (assente) | 1 |  | net,destructive,build_test,secrets,llm_send | da classificare |
| 93 | `docs-governance` | Route broad documentation-governance requests to existing ECC skills and run an opt-in, re | (assente) | 4 | .py | net,secrets | da classificare |
| 94 | `documentation-lookup` | Use up-to-date library and framework docs via Context7 MCP instead of training data. Activ | (assente) | 1 |  |  | da classificare |
| 95 | `dotnet-patterns` | Idiomatic C# and .NET patterns, conventions, dependency injection, async/await, and best p | (assente) | 1 |  | secrets | da classificare |
| 96 | `dynamic-workflow-mode` | Design task-local harnesses, eval gates, and reusable skill extraction for Claude dynamic  | (assente) | 1 |  | secrets | da classificare |
| 97 | `e2e-testing` | Playwright E2E testing patterns, Page Object Model, configuration, CI/CD integration, arti | (assente) | 1 |  | net | da classificare |
| 98 | `ecc-guide` | Answer questions about ECC by reading the live repo surface — agents, skills, commands, ho | (assente) | 1 |  | build_test,hooks_mem | da classificare |
| 99 | `ecc-recipes` | Map a described workflow to the right ECC command group with run-order and stop condition, | (assente) | 1 |  | hooks_mem,plan_flow | da classificare |
| 100 | `ecc-tools-cost-audit` | Evidence-first ECC Tools burn and billing audit workflow. Use when investigating runaway P | (assente) | 1 |  | llm_send | da classificare |
| 101 | `email-ops` | Evidence-first mailbox triage, drafting, send verification, and sent-mail-safe follow-up w | (assente) | 1 |  |  | da classificare |
| 102 | `energy-procurement` | Procure electricity and natural gas for commercial and industrial facilities: tariff and r | Apache-2.0 | 1 |  |  | da classificare |
| 103 | `enterprise-agent-ops` | Operational controls for long-lived or cloud-hosted agent systems — runtime lifecycle (sta | (assente) | 1 |  | secrets | da classificare |
| 104 | `error-handling` | Patterns for robust error handling across TypeScript, Python, and Go. Covers typed errors, | (assente) | 1 |  | net | da classificare |
| 105 | `esign-field-placement` | Deterministic method for placing signature, date, and text fields in a web e-signature com | (assente) | 2 |  |  | da classificare |
| 106 | `eval-harness` | Eval-driven development (EDD) framework for AI coding sessions — define capability and reg | (assente) | 1 |  | build_test,secrets | da classificare |
| 107 | `evm-token-decimals` | Prevent silent decimal mismatch bugs across EVM chains. Covers runtime decimal lookup, cha | (assente) | 1 |  | secrets | da classificare |
| 108 | `exa-search` | Neural search via Exa MCP for web, code, and company research. Use when the user needs web | (assente) | 1 |  | net,secrets,llm_send | da classificare |
| 109 | `fal-ai-media` | Unified media generation via fal.ai MCP — image, video, and audio. Covers text-to-image (N | (assente) | 1 |  | net,secrets | da classificare |
| 110 | `fastapi-patterns` | FastAPI best practices covering project structure, Pydantic v2 schemas, dependency injecti | (assente) | 1 |  | net,build_test,secrets | da classificare |
| 111 | `finance-billing-ops` | Evidence-first revenue, pricing, refunds, team-billing, and billing-model truth workflow f | (assente) | 1 |  |  | da classificare |
| 112 | `flox-environments` | Create reproducible, cross-platform (macOS/Linux) development environments with Flox, a de | (assente) | 1 |  | net,git_write,destructive,build_test,secrets | da classificare |
| 113 | `flutter-dart-code-review` | Library-agnostic Flutter/Dart code review checklist covering widget best practices, state  | (assente) | 1 |  | net,secrets | da classificare |
| 114 | `foundation-models-on-device` | Apple FoundationModels framework for on-device LLM — text generation, guided generation wi | (assente) | 1 |  | secrets | da classificare |
| 115 | `frontend-a11y` | Accessibility patterns for React and Next.js — semantic HTML, ARIA attributes, form labeli | (assente) | 1 |  | net,secrets | da classificare |
| 116 | `frontend-design-direction` | Set an ECC-specific frontend design direction for production UI work. Use when building or | (assente) | 2 | .js | net,llm_send | da classificare |
| 117 | `frontend-patterns` | Frontend development patterns for React, Next.js, state management, performance optimizati | (assente) | 1 |  | net | da classificare |
| 118 | `frontend-slides` | Create stunning, animation-rich HTML presentations from scratch or by converting PowerPoin | (assente) | 7 | .css,.py,.sh | net,destructive | da classificare |
| 119 | `fsharp-testing` | F# testing patterns with xUnit, FsUnit, Unquote, FsCheck property-based testing, integrati | (assente) | 1 |  | build_test,secrets | da classificare |
| 120 | `gan-style-harness` | GAN-inspired Generator-Evaluator agent harness for building high-quality applications auto | (assente) | 1 |  | net,llm_send | da classificare |
| 121 | `gateguard` | PreToolUse fact-forcing gate that denies the first Edit/Write/Bash (including MultiEdit an | (assente) | 1 |  | net,git_write,destructive,hooks_mem | da classificare |
| 122 | `generating-python-installer` | Commercial-grade Python installer expert for Windows: Nuitka extreme compilation, dist sli | (assente) | 1 |  | net,build_test | da classificare |
| 123 | `git-workflow` | Git workflow patterns including branching strategies, commit conventions, keeping history  | (assente) | 1 |  | net,git_write,destructive,build_test,secrets | da classificare |
| 124 | `github-ops` | GitHub repository operations, automation, and management. Issue triage, PR management, CI/ | (assente) | 2 |  | net,git_write,secrets | da classificare |
| 125 | `golang-patterns` | Idiomatic Go patterns, best practices, and conventions for building robust, efficient, and | (assente) | 1 |  | net,build_test | da classificare |
| 126 | `golang-testing` | Go testing patterns including table-driven tests, subtests, benchmarks, fuzzing, and test  | (assente) | 1 |  | build_test | da classificare |
| 127 | `google-workspace-ops` | Operate across Google Drive, Docs, Sheets, and Slides as one workflow surface for plans, t | (assente) | 1 |  |  | da classificare |
| 128 | `growth-log` | Write growth log entries that extract reusable patterns from completed work — root cause,  | (assente) | 1 |  | git_write,secrets | da classificare |
| 129 | `healthcare-cdss-patterns` | Clinical Decision Support System (CDSS) development patterns. Drug interaction checking, d | (assente) | 1 |  |  | da classificare |
| 130 | `healthcare-emr-patterns` | EMR/EHR development patterns for healthcare applications. Clinical safety, encounter workf | (assente) | 1 |  |  | da classificare |
| 131 | `healthcare-eval-harness` | Patient safety evaluation harness for healthcare application deployments. Automated test s | (assente) | 1 |  | build_test | da classificare |
| 132 | `healthcare-phi-compliance` | Protected Health Information (PHI) and PII compliance patterns for healthcare applications | (assente) | 1 |  |  | da classificare |
| 133 | `hermes-imports` | Convert local Hermes operator workflows into sanitized ECC skills and release-pack artifac | (assente) | 1 |  | secrets | da classificare |
| 134 | `hexagonal-architecture` | Design, implement, and refactor Ports & Adapters systems with clear domain boundaries, dep | (assente) | 1 |  |  | da classificare |
| 135 | `hipaa-compliance` | HIPAA-specific entrypoint for healthcare privacy and security work. Use when a task is exp | (assente) | 1 |  |  | da classificare |
| 136 | `homelab-network-readiness` | Readiness checklist for homelab VLAN segmentation, local DNS filtering (Pi-hole, AdGuard H | (assente) | 1 |  |  | da classificare |
| 137 | `homelab-network-setup` | Practical home and homelab network planning for gateways, switches, access points, IP rang | (assente) | 1 |  |  | da classificare |
| 138 | `homelab-pihole-dns` | Pi-hole installation, blocklist management, DNS-over-HTTPS setup, DHCP integration, local  | (assente) | 1 |  | net,destructive,secrets | da classificare |
| 139 | `homelab-vlan-segmentation` | Segmenting home networks into VLANs for IoT, guest, trusted, and server traffic using UniF | (assente) | 1 |  | secrets | da classificare |
| 140 | `homelab-wireguard-vpn` | WireGuard VPN server setup, peer configuration, key generation, split tunneling vs full tu | (assente) | 1 |  | net,destructive,secrets | da classificare |
| 141 | `hookify-rules` | Create and configure hookify rules — markdown files with YAML frontmatter that match bash, | (assente) | 1 |  | destructive,secrets | da classificare |
| 142 | `i18n-sync` | Translate and synchronize application JSON locale files using source-key usage, project te | (assente) | 1 |  | net | da classificare |
| 143 | `inherit-legacy-style` | Prevent AI style drift on legacy projects by scanning the codebase for implicit convention | (assente) | 1 |  | hooks_mem | da classificare |
| 144 | `intent-driven-development` | Turn ambiguous or high-impact product and engineering changes into scoped, verifiable acce | (assente) | 1 |  | secrets | da classificare |
| 145 | `inventory-demand-planning` | Codified demand planning expertise for multi-location retailers: demand forecasting method | Apache-2.0 | 1 |  |  | da classificare |
| 146 | `investor-materials` | Create and update pitch decks, one-pagers, investor memos, accelerator applications, finan | (assente) | 1 |  |  | da classificare |
| 147 | `investor-outreach` | Draft cold emails, warm intro blurbs, follow-ups, update emails, and investor communicatio | (assente) | 1 |  |  | da classificare |
| 148 | `ios-icon-gen` | Generate iOS app icons as PNG imagesets for Xcode asset catalogs from SF Symbols (5000+ Ap | (assente) | 3 | .sh,.swift | net | da classificare |
| 149 | `iterative-retrieval` | Pattern for progressively refining context retrieval to solve the subagent context problem | (assente) | 1 |  | net,secrets,hooks_mem | da classificare |
| 150 | `ito-baskets` | Read-only Itô basket and prediction-market data skill. Index the live basket catalog, comp | (assente) | 3 | .js,.yaml | net,secrets | da classificare |
| 151 | `ito-compute` | Query live GPU inventory, submit an authenticated Itô fixed-rate RFQ, inspect RFQ or procu | (assente) | 2 | .yaml | net,secrets | da classificare |
| 152 | `ito-inference` | Inspect the availability of model serving on a completed Itô compute booking and, when the | (assente) | 1 |  | secrets,llm_send | da classificare |
| 153 | `ito-training` | Inspect the availability of ML training on a completed Itô compute booking and, when the c | (assente) | 1 |  | secrets | da classificare |
| 154 | `java-coding-standards` | Java coding standards for Spring Boot and Quarkus services: naming, immutability, Optional | (assente) | 1 |  | build_test | da classificare |
| 155 | `jira-integration` | Use this skill when retrieving Jira tickets, analyzing requirements, updating ticket statu | (assente) | 1 |  | net,secrets | da classificare |
| 156 | `jpa-patterns` | JPA/Hibernate patterns for entity design, relationships, query optimization, transactions, | (assente) | 1 |  |  | da classificare |
| 157 | `knowledge-ops` | Knowledge base management, ingestion, sync, and retrieval across multiple storage layers ( | (assente) | 1 |  |  | da classificare |
| 158 | `kotlin-coroutines-flows` | Kotlin Coroutines and Flow patterns for Android and KMP — structured concurrency, Flow ope | (assente) | 1 |  | net | da classificare |
| 159 | `kotlin-exposed-patterns` | JetBrains Exposed ORM patterns including DSL queries, DAO pattern, transactions, HikariCP  | (assente) | 1 |  | build_test,secrets | da classificare |
| 160 | `kotlin-ktor-patterns` | Ktor server patterns including routing DSL, plugins, authentication, Koin DI, kotlinx.seri | (assente) | 1 |  | net,secrets | da classificare |
| 161 | `kotlin-patterns` | Idiomatic Kotlin patterns, best practices, and conventions for building robust, efficient, | (assente) | 1 |  | net,build_test | da classificare |
| 162 | `kotlin-testing` | Kotlin testing patterns with Kotest, MockK, coroutine testing, property-based testing, and | (assente) | 1 |  | build_test,secrets | da classificare |
| 163 | `kubernetes-patterns` | Kubernetes workload patterns, resource management, RBAC, probes, autoscaling, ConfigMap/Se | (assente) | 1 |  | net,secrets | da classificare |
| 164 | `laravel-patterns` | Laravel architecture patterns, routing/controllers, Eloquent ORM, service layers, queues,  | (assente) | 1 |  | plan_flow | da classificare |
| 165 | `laravel-plugin-discovery` | Discover and evaluate Laravel packages via LaraPlugins.io MCP. Use when the user wants to  | (assente) | 1 |  | net | da classificare |
| 166 | `laravel-security` | Laravel security best practices — authentication, authorization, Eloquent safety, CSRF, XS | (assente) | 1 |  | net,secrets | da classificare |
| 167 | `laravel-tdd` | Laravel testing strategies with PHPUnit, Pest, model factories, HTTP tests, Sanctum authen | (assente) | 1 |  | net,secrets | da classificare |
| 168 | `laravel-verification` | Verification loop for Laravel projects: env checks, linting, static analysis, tests with c | (assente) | 1 |  |  | da classificare |
| 169 | `latency-critical-systems` | Optimize and verify latency-sensitive systems — realtime dashboards, market data feeds, st | MIT | 1 |  |  | da classificare |
| 170 | `lead-intelligence` | AI-native lead intelligence and outreach pipeline. Replaces Apollo, Clay, and ZoomInfo wit | (assente) | 5 |  | net,secrets | da classificare |
| 171 | `liquid-glass-design` | iOS 26 Liquid Glass design system — dynamic glass material with blur, reflection, and inte | (assente) | 1 |  |  | da classificare |
| 172 | `living-docs-governance` | Keep a long-lived project's documentation from rotting by assigning existing project docs  | (assente) | 1 |  | secrets | da classificare |
| 173 | `llm-trading-agent-security` | Security patterns for autonomous trading agents with wallet or transaction authority. Cove | (assente) | 1 |  | net,secrets | da classificare |
| 174 | `logistics-exception-management` | Codified freight-exception handling expertise for shipment delays, damages, losses, shorta | Apache-2.0 | 1 |  |  | da classificare |
| 175 | `loop-design-check` | Design a goal-oriented agent loop or review one for failure modes: spinning, Goodhart-gami | (assente) | 1 |  | build_test,secrets | da classificare |
| 176 | `mailtrap-email-integration` | Guides agents through integrating transactional email sending via Mailtrap's Email API, in | (assente) | 1 |  | net,secrets | da classificare |
| 177 | `make-interfaces-feel-better` | Apply concrete design-engineering details that make interfaces feel polished. Use when rev | (assente) | 1 |  |  | da classificare |
| 178 | `manim-video` | Build reusable Manim explainers for technical concepts, graphs, system diagrams, and produ | (assente) | 2 | .py |  | da classificare |
| 179 | `market-research` | Conduct market research, competitive analysis, investor due diligence, and industry intell | (assente) | 1 |  |  | da classificare |
| 180 | `marketing-campaign` | End-to-end marketing campaign planning and execution. Covers audience research, positionin | (assente) | 1 |  |  | da classificare |
| 181 | `master-agreement-generator` | Generate review drafts of counterparty master agreements from one template plus a JSON spe | (assente) | 4 | .js,.json |  | da classificare |
| 182 | `mcp-dependency-review` | Statically review MCP configuration for mutable package references before approval or CI,  | (assente) | 1 |  | net,secrets | da classificare |
| 183 | `mcp-server-patterns` | Build MCP servers with Node/TypeScript SDK — tools, resources, prompts, Zod validation, st | (assente) | 1 |  | net | da classificare |
| 184 | `messages-ops` | Evidence-first live messaging workflow for ECC. Use when the user wants to read texts or D | (assente) | 1 |  |  | da classificare |
| 185 | `ml-adoption-playbook` | End-to-end methodology for AI agents and software engineers to add machine learning algori | (assente) | 1 |  | plan_flow | da classificare |
| 186 | `mle-workflow` | Production machine-learning engineering workflow for data contracts, reproducible training | MIT | 1 |  | build_test,secrets,plan_flow | da classificare |
| 187 | `module-regression` | >- | (assente) | 2 |  | build_test | da classificare |
| 188 | `motion-advanced` | Advanced motion patterns for React / Next.js — drag & drop, gestures, text animations, SVG | (assente) | 1 |  | secrets | da classificare |
| 189 | `motion-foundations` | Motion tokens, spring presets, performance rules, device adaptation, accessibility enforce | (assente) | 1 |  | secrets | da classificare |
| 190 | `motion-patterns` | Production-ready animation patterns for React / Next.js — button, modal, toast, stagger, p | (assente) | 1 |  | secrets | da classificare |
| 191 | `mybatis-patterns` | MyBatis and MyBatis-Spring patterns for mapper design, XML and annotation SQL, result mapp | (assente) | 1 |  | net,secrets | da classificare |
| 192 | `mysql-patterns` | MySQL and MariaDB schema, query, indexing, transaction, replication, and connection-pool p | (assente) | 1 |  | secrets | da classificare |
| 193 | `nanoclaw-repl` | Operate and extend NanoClaw, ECC's zero-dependency session-aware REPL, with persistent mar | (assente) | 1 |  |  | da classificare |
| 194 | `nasiko-control-plane` | Manage the experimental Nasiko CLI lifecycle through ECC — read-only status checks, consen | (assente) | 2 | .yaml | net | da classificare |
| 195 | `nestjs-patterns` | NestJS architecture patterns for modules, controllers, providers, DTO validation, guards,  | (assente) | 1 |  | secrets | da classificare |
| 196 | `netmiko-ssh-automation` | Safe Python Netmiko patterns for read-only collection, bounded batch SSH, TextFSM parsing, | (assente) | 1 |  | secrets | da classificare |
| 197 | `network-bgp-diagnostics` | Diagnostics-only BGP troubleshooting patterns for neighbor state, route exchange, prefix p | (assente) | 1 |  | secrets | da classificare |
| 198 | `network-config-validation` | Pre-deployment checks for router and switch configuration, including dangerous commands, d | (assente) | 1 |  | secrets | da classificare |
| 199 | `network-interface-health` | Diagnose interface errors, drops, CRCs, duplex mismatches, flapping, speed negotiation iss | (assente) | 1 |  |  | da classificare |
| 200 | `nextjs-turbopack` | Next.js 16+ and Turbopack guidance — incremental Rust bundling, file-system caching, faste | (assente) | 1 |  | net | da classificare |
| 201 | `nodejs-keccak256` | Prevent Ethereum hashing bugs in JavaScript and TypeScript. Node's sha3-256 is NIST SHA3,  | (assente) | 1 |  |  | da classificare |
| 202 | `nutrient-document-processing` | Process, convert, OCR, extract, redact, sign, and fill documents using the Nutrient DWS AP | (assente) | 1 |  | net,secrets | da classificare |
| 203 | `nuxt4-patterns` | Nuxt 4 app patterns for hydration safety, performance, route rules, lazy loading, and SSR- | (assente) | 1 |  | net | da classificare |
| 204 | `openclaw-persona-forge` | 为 OpenClaw AI Agent 锻造完整的龙虾灵魂方案。根据用户偏好或随机抽卡， 输出身份定位、灵魂描述(SOUL.md)、角色化底线规则、名字和头像生图提示词。 如当前环 | (assente) | 9 | .py,.sh |  | da classificare |
| 205 | `opensource-pipeline` | Open-source pipeline: fork, sanitize, and package private projects for safe public release | (assente) | 1 |  | git_write,secrets | da classificare |
| 206 | `operator-approval-loop` | Operator approval contract with internal filing notices for agent-drafted outbound message | (assente) | 3 | .py,.sql | secrets | da classificare |
| 207 | `orch-add-feature` | Orchestrate building a brand-new feature end to end — research, plan, TDD implementation,  | (assente) | 1 |  | plan_flow | da classificare |
| 208 | `orch-build-mvp` | Orchestrate bootstrapping a working MVP from a design or spec document — ingest the SDD/PR | (assente) | 1 |  | plan_flow | da classificare |
| 209 | `orch-change-feature` | Orchestrate altering an existing, working feature to new desired behavior — update its tes | (assente) | 1 |  | plan_flow | da classificare |
| 210 | `orch-fix-defect` | Orchestrate fixing a bug — reproduce it as a failing regression test, fix to green, review | (assente) | 1 |  | plan_flow | da classificare |
| 211 | `orch-pipeline` | Shared orchestration engine behind the orch-* skill family — the gated Research-Plan-TDD-R | (assente) | 1 |  | plan_flow | da classificare |
| 212 | `orch-refine-code` | Orchestrate a behavior-preserving refactor — confirm tests are green, restructure without  | (assente) | 1 |  | plan_flow | da classificare |
| 213 | `osint-investigation` | Sparse-clue OSINT investigation methodology for extracting overlooked leads, connecting fr | CC-BY-SA-4.0 | 2 | .txt | net | da classificare |
| 214 | `parallel-execution-optimizer` | Speed up a task by turning it into a dependency graph of parallel lanes with a lane matrix | MIT | 1 |  |  | da classificare |
| 215 | `perl-patterns` | Modern Perl 5.36+ idioms, best practices, and conventions for building robust, maintainabl | (assente) | 1 |  |  | da classificare |
| 216 | `perl-security` | Comprehensive Perl security covering taint mode, input validation, safe process execution, | (assente) | 1 |  | destructive,secrets | da classificare |
| 217 | `perl-testing` | Perl testing patterns using Test2::V0, Test::More, prove runner, mocking, coverage with De | (assente) | 1 |  | secrets | da classificare |
| 218 | `plan-canvas` | Open plans and HTML artifacts in a local browser canvas where the human annotates elements | (assente) | 1 |  | net,plan_flow | da classificare |
| 219 | `plan-orchestrate` | Read a plan document, decompose it into steps, classify each step, and emit the ready-to-p | (assente) | 1 |  | secrets,plan_flow | da classificare |
| 220 | `plankton-code-quality` | Write-time code quality enforcement using Plankton — auto-formatting, linting, and Claude- | (assente) | 1 |  | net,llm_send,hooks_mem | da classificare |
| 221 | `postgres-patterns` | PostgreSQL database patterns for query optimization, schema design, indexing, and security | (assente) | 1 |  |  | da classificare |
| 222 | `prediction-market-oracle-research` | Research prediction markets as data sources or oracle signals for products, agents, dashbo | (assente) | 1 |  |  | da classificare |
| 223 | `prediction-market-risk-review` | Review prediction-market, basket, oracle, and trading-agent workflows for compliance, safe | (assente) | 1 |  | secrets | da classificare |
| 224 | `prisma-patterns` | Prisma ORM patterns for TypeScript backends — schema design, query optimization, transacti | (assente) | 1 |  |  | da classificare |
| 225 | `product-capability` | Translate PRD intent, roadmap asks, or product discussions into an implementation-ready ca | (assente) | 1 |  |  | da classificare |
| 226 | `product-lens` | Validate the why before building through four product diagnostics — a YC-style product dia | (assente) | 1 |  |  | da classificare |
| 227 | `production-audit` | Local-evidence production readiness audit for shipped apps, pre-launch reviews, post-merge | (assente) | 1 |  |  | da classificare |
| 228 | `production-scheduling` | Codified expertise for production scheduling, job sequencing, line balancing, changeover o | Apache-2.0 | 1 |  |  | da classificare |
| 229 | `project-flow-ops` | Operate execution flow across GitHub and Linear by triaging issues and pull requests, link | (assente) | 1 |  |  | da classificare |
| 230 | `prompt-caching-strategy` | >- | (assente) | 1 |  | net,secrets,llm_send | da classificare |
| 231 | `prompt-optimizer` | Analyze draft prompts, detect intent and missing context, match ECC commands, skills, and  | (assente) | 1 |  | build_test,secrets,plan_flow | da classificare |
| 232 | `python-patterns` | Pythonic idioms, PEP 8 standards, type hints, and best practices for building robust, effi | (assente) | 1 |  | build_test | da classificare |
| 233 | `python-testing` | Python testing strategies using pytest, TDD methodology, fixtures, mocking, parametrizatio | (assente) | 1 |  | net,build_test,secrets | da classificare |
| 234 | `pytorch-patterns` | PyTorch deep learning patterns and best practices for building robust, efficient, and repr | (assente) | 1 |  | plan_flow | da classificare |
| 235 | `quality-nonconformance` | Quality control and non-conformance management for regulated manufacturing (FDA 21 CFR 820 | Apache-2.0 | 1 |  |  | da classificare |
| 236 | `quarkus-patterns` | Quarkus 3.x LTS architecture patterns with Camel for messaging, RESTful API design, CDI se | (assente) | 1 |  | secrets | da classificare |
| 237 | `quarkus-security` | Quarkus security implementation patterns: JWT and OIDC authentication, @RolesAllowed RBAC  | (assente) | 1 |  | net,build_test,secrets | da classificare |
| 238 | `quarkus-tdd` | Test-driven development for Quarkus 3.x LTS using JUnit 5, Mockito, REST Assured, Camel te | (assente) | 1 |  | build_test | da classificare |
| 239 | `quarkus-verification` | Verification loop for Quarkus projects: build, static analysis (Checkstyle, PMD, SpotBugs) | (assente) | 1 |  | net,build_test,secrets | da classificare |
| 240 | `rails-patterns` | Ruby on Rails framework patterns for Rails 7.1+ and 8.x apps. Covers the directory contrac | (assente) | 1 |  | net,secrets | da classificare |
| 241 | `ralphinho-rfc-pipeline` | Split an RFC into a multi-agent execution DAG — decompose into work units with dependencie | (assente) | 1 |  | net | da classificare |
| 242 | `react-native-patterns` | React Native and Expo app patterns — Expo Router navigation, state separation (server/clie | (assente) | 1 |  | secrets | da classificare |
| 243 | `react-patterns` | React 18/19 patterns including hooks discipline, server/client component boundaries, Suspe | (assente) | 1 |  | net | da classificare |
| 244 | `react-performance` | React and Next.js performance optimization patterns adapted from Vercel Engineering's Reac | (assente) | 1 |  | net | da classificare |
| 245 | `react-testing` | React component testing with React Testing Library, Vitest/Jest, MSW for network mocking,  | (assente) | 1 |  |  | da classificare |
| 246 | `recsys-pipeline-architect` | Design composable recommendation, ranking, and feed pipelines using the six-stage Source→H | (assente) | 1 |  | net,build_test | da classificare |
| 247 | `recursive-decision-ledger` | Run repeated rollouts ("Prime Gauss" style recursive prompting) while keeping an append-on | MIT | 1 |  |  | da classificare |
| 248 | `redis-patterns` | Redis data structure patterns, caching strategies, distributed locks, rate limiting, pub/s | (assente) | 1 |  | secrets | da classificare |
| 249 | `regex-vs-llm-structured-text` | Decision framework for parsing structured text (quizzes, forms, invoices, receipts, tables | (assente) | 1 |  |  | da classificare |
| 250 | `remotion-video-creation` | Best practices for Remotion - Video creation in React. 29 domain-specific rules covering 3 | (assente) | 32 | .tsx | net,secrets,llm_send | da classificare |
| 251 | `repo-scan` | Bootstrap pointer that installs the external repo-scan skill from a pinned, reviewable com | (assente) | 1 |  | net,destructive,secrets | da classificare |
| 252 | `research-ops` | Evidence-first current-state research workflow for ECC. Use when the user wants fresh fact | (assente) | 1 |  |  | da classificare |
| 253 | `returns-reverse-logistics` | Codified expertise for returns authorization, receipt and inspection, disposition decision | Apache-2.0 | 1 |  |  | da classificare |
| 254 | `rules-distill` | Scan skills to extract cross-cutting principles and distill them into rules — append, revi | (assente) | 3 | .sh | destructive | da classificare |
| 255 | `rust-patterns` | Idiomatic Rust patterns, ownership, error handling, traits, concurrency, and best practice | (assente) | 1 |  | build_test,secrets | da classificare |
| 256 | `rust-testing` | Rust testing patterns including unit tests, integration tests, async testing, property-bas | (assente) | 1 |  | build_test | da classificare |
| 257 | `safety-guard` | Guard against destructive operations with three modes: Careful intercepts dangerous comman | (assente) | 1 |  | net,git_write,destructive,hooks_mem | da classificare |
| 258 | `santa-method` | Multi-agent adversarial verification: two independent reviewers with the same rubric must  | (assente) | 1 |  | secrets | da classificare |
| 259 | `scientific-db-pubmed-database` | Direct PubMed and NCBI E-utilities search workflows for biomedical literature, MeSH querie | (assente) | 1 |  | net,secrets | da classificare |
| 260 | `scientific-db-uspto-database` | USPTO patent and trademark data workflow for official record lookup, PatentSearch queries, | (assente) | 1 |  | net,secrets | da classificare |
| 261 | `scientific-pkg-gget` | gget CLI and Python workflow for quick genomic database queries, sequence lookup, BLAST-st | (assente) | 1 |  | net | da classificare |
| 262 | `scientific-thinking-literature-review` | Systematic literature-review workflow for academic, biomedical, technical, and scientific  | (assente) | 1 |  |  | da classificare |
| 263 | `scientific-thinking-scholar-evaluation` | Structured scholarly-work evaluation for papers, proposals, literature reviews, methods se | (assente) | 1 |  |  | da classificare |
| 264 | `search-first` | Research-before-coding workflow: search npm/PyPI, MCP servers, skills, and GitHub for exis | (assente) | 1 |  | build_test | da classificare |
| 265 | `security-bounty-hunter` | Hunt for exploitable, bounty-worthy security issues in repositories. Focuses on remotely r | (assente) | 1 |  |  | da classificare |
| 266 | `security-review` | Use this skill when adding authentication, handling user input, working with secrets, crea | (assente) | 2 |  | net,secrets,llm_send | da classificare |
| 267 | `security-scan` | Scan your Claude Code configuration (.claude/ directory) for security vulnerabilities, mis | (assente) | 1 |  | net,secrets,llm_send,hooks_mem | da classificare |
| 268 | `seo` | Audit, plan, and implement SEO improvements across technical SEO, on-page optimization, st | (assente) | 1 |  | net | da classificare |
| 269 | `skill-comply` | Visualize whether skills, rules, and agent definitions are actually followed — auto-genera | (assente) | 23 | .jsonl,.py,.toml,.yaml | build_test,secrets,llm_send | da classificare |
| 270 | `skill-host-compat` | Lint ECC skills for Codex/Cursor-safe frontmatter and Claude-only substitutions before a s | (assente) | 1 |  | build_test,llm_send | da classificare |
| 271 | `skill-scout` | Search existing local, marketplace, GitHub, and web skill sources before creating a new sk | (assente) | 1 |  |  | da classificare |
| 272 | `skill-stocktake` | Use when auditing Claude skills and commands for quality. Supports Quick Scan (changed ski | (assente) | 4 | .sh | net,destructive,hooks_mem | da classificare |
| 273 | `social-graph-ranker` | Weighted social-graph ranking for warm intro discovery, bridge scoring, and network gap an | (assente) | 1 |  |  | da classificare |
| 274 | `social-publisher` | Agent-driven scheduling and publishing of social media posts across 13 platforms via Socia | (assente) | 1 |  | net,secrets | da classificare |
| 275 | `springboot-patterns` | Spring Boot architecture patterns, REST API design, layered services, data access, caching | (assente) | 1 |  |  | da classificare |
| 276 | `springboot-security` | Spring Security best practices for authn/authz, validation, CSRF, secrets, headers, rate l | (assente) | 1 |  | net,secrets | da classificare |
| 277 | `springboot-tdd` | Test-driven development for Spring Boot using JUnit 5, Mockito, MockMvc, Testcontainers, a | (assente) | 1 |  | build_test | da classificare |
| 278 | `springboot-verification` | Run the full Spring Boot verification loop — Maven or Gradle build, SpotBugs, PMD, and Che | (assente) | 1 |  | build_test,secrets | da classificare |
| 279 | `story-lifecycle` | Manage a file-based epic → story → sprint development loop without requiring an external p | (assente) | 2 | .js | plan_flow | da classificare |
| 280 | `strategic-compact` | Suggests manual context compaction at logical intervals to preserve context through task p | (assente) | 1 |  | net,secrets,hooks_mem | da classificare |
| 281 | `swift-actor-persistence` | Thread-safe data persistence in Swift using actors — in-memory cache with file-backed stor | (assente) | 1 |  |  | da classificare |
| 282 | `swift-concurrency-6-2` | Swift 6.2 Approachable Concurrency — single-threaded by default, @concurrent for explicit  | (assente) | 1 |  |  | da classificare |
| 283 | `swift-protocol-di-testing` | Protocol-based dependency injection for testable Swift code — mock file system, network, a | (assente) | 1 |  |  | da classificare |
| 284 | `swiftui-patterns` | SwiftUI architecture patterns, state management with @Observable, view composition, naviga | (assente) | 1 |  |  | da classificare |
| 285 | `taste` | Creative-direction layer for music videos and short-form edits in the angelcore / cloud-tr | (assente) | 2 |  | plan_flow | da classificare |
| 286 | `taste-application` | Generate new video against a distilled style pack and cut it into a finished piece - plan  | (assente) | 81 | .edl,.json,.py,.toml,.txt | net,secrets,llm_send | da classificare |
| 287 | `taste-distillation` | Measure a set of reference videos into a reusable style pack - colour grade as a 3D LUT, c | (assente) | 15 | .py,.txt | net,secrets,llm_send | da classificare |
| 288 | `tasteforge-video` | Use for file-driven multimodal image, video, and 3D-asset discovery; taste interviews; dis | (assente) | 1 |  |  | da classificare |
| 289 | `tdd-workflow` | Test-driven development workflow: write a failing test first, watch it fail, implement the | (assente) | 1 |  | net,build_test,secrets,llm_send,plan_flow | da classificare |
| 290 | `team-agent-orchestration` | Run team-based orchestration for agent squads: work items with owners and scope, agent Kan | (assente) | 1 |  |  | da classificare |
| 291 | `team-builder` | Interactive picker that discovers available agent personas via the claude agents command a | (assente) | 1 |  | secrets | da classificare |
| 292 | `terminal-opener` | Open an executable and its argument array in a visible terminal window through a reusable, | (assente) | 3 | .js,.yaml | secrets | da classificare |
| 293 | `terminal-ops` | Evidence-first repo execution workflow for ECC. Use when the user wants a command run, a r | (assente) | 1 |  |  | da classificare |
| 294 | `test-collaboration` | Inventory and govern a project's test assets. Connect requirements, business rules, risks, | (assente) | 2 |  | build_test | da classificare |
| 295 | `tinystruct-patterns` | Expert guidance for developing with the tinystruct Java framework. Use when working on the | (assente) | 7 |  | net,build_test,secrets | da classificare |
| 296 | `token-budget-advisor` | Offer a choice of response depth (25%/50%/75%/100%) with token estimates before answering, | (assente) | 1 |  | net,secrets | da classificare |
| 297 | `token-card` | Render a token-usage stat card from local Claude Code, Codex, Gemini CLI, and OpenCode ses | (assente) | 1 |  | net,secrets | da classificare |
| 298 | `ui-demo` | Record polished UI demo videos using Playwright. Use when the user asks to create a demo,  | (assente) | 1 |  | net | da classificare |
| 299 | `ui-to-vue` | Use when the user has UI screenshots or design exports that need batch conversion into Vue | (assente) | 1 |  | secrets | da classificare |
| 300 | `uncloud` | Use when managing an Uncloud cluster — deploying services, configuring Caddy ingress, addi | (assente) | 1 |  | net,git_write,secrets | da classificare |
| 301 | `unified-memory` | Share durable, inspectable context and handoffs between Claude, Codex, Hermes, Cursor, Ope | (assente) | 1 |  | secrets,hooks_mem | da classificare |
| 302 | `unified-notifications-ops` | Operate notifications as one ECC-native workflow across GitHub, Linear, desktop alerts, ho | (assente) | 1 |  |  | da classificare |
| 303 | `verification-loop` | Run a six-phase verification of a Claude Code session's work — build, type check, lint, te | MIT | 1 |  | build_test,secrets,hooks_mem | da classificare |
| 304 | `video-editing` | AI-assisted video editing workflows for cutting, structuring, and augmenting real footage. | (assente) | 13 | .json,.lua,.setting | net,secrets | da classificare |
| 305 | `videodb` | Ingest, index, search, edit, and monitor video and audio with the VideoDB Python SDK — upl | (assente) | 12 | .py | net,secrets | da classificare |
| 306 | `visa-doc-translate` | Translate visa document images (bank deposit, employment, income, and retirement certifica | (assente) | 2 |  |  | da classificare |
| 307 | `vite-patterns` | Vite build tool patterns including config, plugins, HMR, env variables, proxy setup, SSR,  | (assente) | 1 |  | net,secrets | da classificare |
| 308 | `vue-patterns` | Vue.js 3 Composition API patterns, component architecture, reactivity best practices, Pini | (assente) | 1 |  | net,secrets | da classificare |
| 309 | `windows-desktop-e2e` | E2E testing for Windows native desktop apps (WPF, WinForms, Win32/MFC, Qt) using pywinauto | (assente) | 1 |  | net,build_test,secrets | da classificare |
| 310 | `workspace-surface-audit` | Audit the active repo, MCP servers, plugins, connectors, env surfaces, and harness setup,  | (assente) | 1 |  | secrets | da classificare |
| 311 | `x-api` | X/Twitter API integration for posting tweets, threads, reading timelines, search, and anal | (assente) | 1 |  | net,secrets | da classificare |

## Agenti

| # | Agente | Descrizione | Strumenti | Modello | Segnali | Decisione |
|---|---|---|---|---|---|---|
| 1 | `a11y-architect` | Accessibility Architect specializing in WCAG 2.2 compliance for Web and Native platforms.  | Read, Write, Edit, Grep, Glob | sonnet | secrets | da classificare |
| 2 | `agent-evaluator` | Evaluates agent output against 5-axis quality rubric (accuracy, completeness, clarity, act | Read, Grep, Glob, Bash | sonnet | net,git_write,build_test,secrets | da classificare |
| 3 | `architect` | Software architecture specialist for system design, scalability, and technical decision-ma | Read, Grep, Glob | opus | secrets | da classificare |
| 4 | `bengali-reviewer` | Reviews code handling Bengali (Bangla) text for Unicode correctness, proper normalization, | Read, Grep, Glob | sonnet | secrets | da classificare |
| 5 | `build-error-resolver` | Build and TypeScript error resolution specialist. Use PROACTIVELY when build fails or type | Read, Write, Edit, Bash, Grep, Glob | sonnet | destructive,build_test,secrets | da classificare |
| 6 | `chief-of-staff` | Personal communication chief of staff that triages email, Slack, LINE, and Messenger. Clas | Read, Grep, Glob, Bash, Edit, Write | sonnet | net,git_write,secrets,llm_send,hooks_mem | da classificare |
| 7 | `code-architect` | Designs feature architectures by analyzing existing codebase patterns and conventions, the | Read, Grep, Glob, Bash | sonnet | secrets,plan_flow | da classificare |
| 8 | `code-explorer` | Deeply analyzes existing codebase features by tracing execution paths, mapping architectur | Read, Grep, Glob | sonnet | secrets | da classificare |
| 9 | `code-reviewer` | Expert code review specialist. Proactively reviews code for quality, security, and maintai | Read, Grep, Glob, Bash | sonnet | secrets | da classificare |
| 10 | `code-simplifier` | Simplifies and refines code for clarity, consistency, and maintainability while preserving | Read, Write, Edit, Bash, Grep, Glob | sonnet | secrets | da classificare |
| 11 | `comment-analyzer` | Analyze code comments for accuracy, completeness, maintainability, and comment rot risk. | Read, Grep, Glob | haiku | secrets | da classificare |
| 12 | `commit-reviewer` | Reviews a staged diff and drafts a Conventional Commits-compliant message matching the rep | Read, Grep, Glob, Bash | sonnet | git_write | da classificare |
| 13 | `conversation-analyzer` | Use this agent when analyzing conversation transcripts to find behaviors worth preventing  | Read, Grep | haiku | secrets | da classificare |
| 14 | `cpp-build-resolver` | C++ build, CMake, and compilation error resolution specialist. Fixes build errors, linker  | Read, Write, Edit, Bash, Grep, Glob | sonnet | secrets | da classificare |
| 15 | `cpp-reviewer` | Expert C++ code reviewer specializing in memory safety, modern C++ idioms, concurrency, an | Read, Grep, Glob, Bash | sonnet | secrets | da classificare |
| 16 | `csharp-reviewer` | Expert C# code reviewer specializing in .NET conventions, async patterns, security, nullab | Read, Grep, Glob, Bash | sonnet | build_test,secrets | da classificare |
| 17 | `dart-build-resolver` | Dart/Flutter build, analysis, and dependency error resolution specialist. Fixes `dart anal | Read, Write, Edit, Bash, Grep, Glob | sonnet | build_test,secrets | da classificare |
| 18 | `database-reviewer` | PostgreSQL database specialist for query optimization, schema design, security, and perfor | Read, Grep, Glob, Bash | sonnet | secrets | da classificare |
| 19 | `django-build-resolver` | Django/Python build, migration, and dependency error resolution specialist. Fixes pip/Poet | Read, Write, Edit, Bash, Grep, Glob | sonnet | destructive,secrets | da classificare |
| 20 | `django-reviewer` | Expert Django code reviewer specializing in ORM correctness, DRF patterns, migration safet | Read, Grep, Glob, Bash | sonnet | build_test,secrets | da classificare |
| 21 | `doc-updater` | Documentation and codemap specialist. Use PROACTIVELY for updating codemaps and documentat | Read, Write, Edit, Bash, Grep, Glob | haiku | secrets | da classificare |
| 22 | `docs-lookup` | When the user asks how to use a library, framework, or API or needs up-to-date code exampl | Read, Grep, mcp__context7__resolve-library-id, mcp__context7__query-docs | haiku | secrets | da classificare |
| 23 | `e2e-runner` | End-to-end testing specialist using Vercel Agent Browser (preferred) with Playwright fallb | Read, Write, Edit, Bash, Grep, Glob | sonnet | net,secrets | da classificare |
| 24 | `fastapi-reviewer` | Reviews FastAPI applications for async correctness, dependency injection, Pydantic schemas | Read, Grep, Glob, Bash | sonnet | build_test,secrets | da classificare |
| 25 | `flutter-reviewer` | Flutter and Dart code reviewer. Reviews Flutter code for widget best practices, state mana | Read, Grep, Glob, Bash | sonnet | secrets | da classificare |
| 26 | `fsharp-reviewer` | Expert F# code reviewer specializing in functional idioms, type safety, pattern matching,  | Read, Grep, Glob, Bash | sonnet | build_test,secrets | da classificare |
| 27 | `gan-evaluator` | GAN Harness — Evaluator agent. Tests the live running application via Playwright, scores a | Read, Write, Bash, Grep, Glob, mcp__playwright__browser_navigate, mcp__playwright__browser_click, mcp__playwright__browser_take_screenshot, mcp__playwright__browser_snapshot, mcp__playwright__browser_type, mcp__playwright__browser_fill_form, mcp__playwright__browser_resize, mcp__playwright__browser_press_key | sonnet | net,build_test,secrets,llm_send | da classificare |
| 28 | `gan-generator` | GAN Harness — Generator agent. Implements features according to the spec, reads evaluator  | Read, Write, Edit, Bash, Grep, Glob | sonnet | net,git_write,secrets,llm_send | da classificare |
| 29 | `gan-planner` | GAN Harness — Planner agent. Expands a one-line prompt into a full product specification w | Read, Write, Grep, Glob | sonnet | secrets,llm_send | da classificare |
| 30 | `go-build-resolver` | Go build, vet, and compilation error resolution specialist. Fixes build errors, go vet iss | Read, Write, Edit, Bash, Grep, Glob | sonnet | build_test,secrets | da classificare |
| 31 | `go-reviewer` | Expert Go code reviewer specializing in idiomatic Go, concurrency patterns, error handling | Read, Grep, Glob, Bash | sonnet | build_test,secrets | da classificare |
| 32 | `harmonyos-app-resolver` | HarmonyOS application development expert specializing in ArkTS and ArkUI. Reviews code for | Read, Write, Edit, Bash, Grep, Glob | sonnet | secrets | da classificare |
| 33 | `harness-optimizer` | Improve local agent-harness configuration reliability and cost using eval-driven grading ( | Read, Grep, Glob, Bash, Edit | sonnet | secrets,hooks_mem | da classificare |
| 34 | `healthcare-reviewer` | Reviews healthcare application code for clinical safety, CDSS accuracy, PHI compliance, an | Read, Grep, Glob | opus | secrets | da classificare |
| 35 | `homelab-architect` | Designs home and small-lab network plans from hardware inventory, goals, and operator expe | Read, Grep | sonnet | secrets | da classificare |
| 36 | `java-build-resolver` | Java/Maven/Gradle build, compilation, and dependency error resolution specialist. Automati | Read, Write, Edit, Bash, Grep, Glob | sonnet | destructive,build_test,secrets | da classificare |
| 37 | `java-reviewer` | Expert Java code reviewer for Spring Boot and Quarkus projects. Automatically detects the  | Read, Grep, Glob, Bash | sonnet | build_test,secrets | da classificare |
| 38 | `kotlin-build-resolver` | Kotlin/Gradle build, compilation, and dependency error resolution specialist. Fixes build  | Read, Write, Edit, Bash, Grep, Glob | sonnet | destructive,build_test,secrets | da classificare |
| 39 | `kotlin-reviewer` | Kotlin and Android/KMP code reviewer. Reviews Kotlin code for idiomatic patterns, coroutin | Read, Grep, Glob, Bash | sonnet | build_test,secrets | da classificare |
| 40 | `loop-operator` | Operate autonomous agent loops, monitor progress, and intervene safely when loops stall. | Read, Grep, Glob, Bash, Edit | sonnet | secrets | da classificare |
| 41 | `marketing-agent` | Marketing strategist and copywriter for campaign planning, audience research, positioning, | Read, Grep, Glob, WebSearch, WebFetch | sonnet | net,secrets | da classificare |
| 42 | `mle-reviewer` | Production machine-learning engineering reviewer for data contracts, feature pipelines, tr | Read, Grep, Glob, Bash | sonnet | build_test,secrets,plan_flow | da classificare |
| 43 | `network-architect` | Designs enterprise or multi-site network architecture from requirements, using existing ne | Read, Grep | sonnet | secrets | da classificare |
| 44 | `network-config-reviewer` | Reviews router and switch configurations for security, correctness, stale references, risk | Read, Grep | sonnet | secrets | da classificare |
| 45 | `network-troubleshooter` | Diagnoses network connectivity, routing, DNS, interface, and policy symptoms with a read-o | Read, Bash, Grep | sonnet | secrets | da classificare |
| 46 | `opensource-forker` | Fork any project for open-sourcing. Copies files, strips secrets and credentials (20+ patt | Read, Write, Edit, Bash, Grep, Glob | haiku | net,git_write,secrets | da classificare |
| 47 | `opensource-packager` | Generate complete open-source packaging for a sanitized project. Produces CLAUDE.md, setup | Read, Write, Edit, Bash, Grep, Glob | haiku | net,build_test,secrets | da classificare |
| 48 | `opensource-sanitizer` | Verify an open-source fork is fully sanitized before release. Scans for leaked secrets, PI | Read, Grep, Glob, Bash | sonnet | net,secrets | da classificare |
| 49 | `performance-optimizer` | Performance analysis and optimization specialist. Use PROACTIVELY for identifying bottlene | Read, Write, Edit, Bash, Grep, Glob | sonnet | net,secrets | da classificare |
| 50 | `php-reviewer` | Expert PHP code reviewer specializing in PSR-12 compliance, PHP type system, Eloquent ORM  | Read, Grep, Glob, Bash | sonnet | secrets | da classificare |
| 51 | `planner` | Expert planning specialist for complex features and refactoring. Use PROACTIVELY when user | Read, Grep, Glob | opus | secrets | da classificare |
| 52 | `pr-test-analyzer` | Review pull request test coverage quality and completeness, with emphasis on behavioral co | Read, Grep, Glob, Bash | sonnet | secrets | da classificare |
| 53 | `python-reviewer` | Expert Python code reviewer specializing in PEP 8 compliance, Pythonic idioms, type hints, | Read, Grep, Glob, Bash | sonnet | build_test,secrets | da classificare |
| 54 | `pytorch-build-resolver` | PyTorch runtime, CUDA, and training error resolution specialist. Fixes tensor shape mismat | Read, Write, Edit, Bash, Grep, Glob | sonnet | net,secrets,plan_flow | da classificare |
| 55 | `rag-pipeline-reviewer` | Reviews RAG (Retrieval-Augmented Generation) pipelines for retrieval quality, chunking str | Read, Grep, Glob, Bash | sonnet | secrets | da classificare |
| 56 | `react-build-resolver` | Diagnose and fix React build failures across Vite, webpack, Next.js, CRA, Parcel, esbuild, | Read, Write, Edit, Bash, Grep, Glob | sonnet | build_test,secrets | da classificare |
| 57 | `react-reviewer` | Expert React/JSX code reviewer specializing in hook correctness, render performance, serve | Read, Grep, Glob, Bash | sonnet | secrets | da classificare |
| 58 | `refactor-cleaner` | Dead code cleanup and consolidation specialist. Use PROACTIVELY for removing unused code,  | Read, Write, Edit, Bash, Grep, Glob | sonnet | secrets | da classificare |
| 59 | `ruby-reviewer` | Expert Ruby and Rails code reviewer specializing in idiomatic Ruby, Active Record query sa | Read, Grep, Glob, Bash | sonnet | git_write,secrets | da classificare |
| 60 | `rust-build-resolver` | Rust build, compilation, and dependency error resolution specialist. Fixes cargo build err | Read, Write, Edit, Bash, Grep, Glob | sonnet | build_test,secrets | da classificare |
| 61 | `rust-reviewer` | Expert Rust code reviewer specializing in ownership, lifetimes, error handling, unsafe usa | Read, Grep, Glob, Bash | sonnet | build_test,secrets | da classificare |
| 62 | `security-reviewer` | Security vulnerability detection and remediation specialist. Use PROACTIVELY after writing | Read, Grep, Glob, Bash | sonnet | net,secrets | da classificare |
| 63 | `seo-specialist` | SEO specialist for technical SEO audits, on-page optimization, structured data, Core Web V | Read, Grep, Glob, WebSearch, WebFetch | sonnet | net,secrets | da classificare |
| 64 | `silent-failure-hunter` | Review code for silent failures, swallowed errors, bad fallbacks, and missing error propag | Read, Grep, Glob, Bash | sonnet | secrets | da classificare |
| 65 | `spec-miner` | Extracts behavioral specs from existing codebases for OpenSpec. Produces flat Requirement  | Read, Grep, Glob, Bash, Write | opus | git_write,secrets | da classificare |
| 66 | `swift-build-resolver` | Swift/Xcode build, compilation, and dependency error resolution specialist. Fixes swift bu | Read, Write, Edit, Bash, Grep, Glob | sonnet | secrets | da classificare |
| 67 | `swift-reviewer` | Expert Swift code reviewer specializing in protocol-oriented design, value semantics, ARC  | Read, Grep, Glob, Bash | sonnet | secrets | da classificare |
| 68 | `tdd-guide` | Test-Driven Development specialist enforcing write-tests-first methodology. Use PROACTIVEL | Read, Write, Edit, Bash, Grep | sonnet | build_test,secrets,llm_send | da classificare |
| 69 | `type-design-analyzer` | Analyze type design for encapsulation, invariant expression, usefulness, and enforcement. | Read, Grep, Glob | sonnet | secrets | da classificare |
| 70 | `typescript-reviewer` | Expert TypeScript/JavaScript code reviewer specializing in type safety, async correctness, | Read, Grep, Glob, Bash | sonnet | secrets | da classificare |
| 71 | `vue-reviewer` | Expert Vue.js code reviewer specializing in Composition API correctness, reactivity pitfal | Read, Grep, Glob, Bash | sonnet | secrets | da classificare |
