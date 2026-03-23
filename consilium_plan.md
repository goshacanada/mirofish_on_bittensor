# Consilium — Decentralized Personal Knowledge System

**Vision:** Every person — including domain experts themselves — runs a permanent AI advisory council on their own computer. Their personal knowledge — about any complex domain — lives in a local knowledge graph that grows smarter every day as users discover, curate, and share research through a peer-to-peer mesh. When one user anywhere finds and verifies a paper, every interested node benefits. No one knows everything: a cardiologist needs nephrology insight, a tax attorney needs immigration law context. The result is a global knowledge dissemination and organization system — a living, community-curated knowledge network where experts and non-experts alike discover non-obvious connections, and no one's data ever leaves their machine.

**The patient advocacy problem:** With large patient-to-doctor ratios, a doctor's attention per patient is measured in minutes, not hours. They see today's labs, not the trend over 18 months. They see a medication list but don't have time to cross-check every interaction against every comorbidity across multiple specialists. Important information gets missed — not because doctors are careless, but because the system gives them too many patients and too little time. The patient is the only person who has ALL their data across ALL providers over ALL time. Consilium organizes that data so the patient can surface the information that matters — not opinions, not recommendations, but the facts, trends, and connections that help the doctor develop critical insights in the time they have. Patients must advocate for themselves. Consilium makes that advocacy informed.

**What Consilium gives the doctor — information, not opinion:** The preparation report is not "Consilium thinks you should do X." It is structured, factual data the doctor needs but may not have time to assemble: complete medication list with all prescribers, lab trends across multiple providers over months/years, drug interactions checked against current conditions, threshold violations flagged with guideline citations. The doctor brings the clinical judgment. Consilium brings the organized information. A 15-minute appointment where the doctor gets a complete picture is worth more than a 30-minute appointment where they're assembling the picture from scratch.

**This is an open-source project built for the common good.** The Consilium platform is free software that anyone can run, modify, and share. The Expert Portal — a company-operated service that routes micro-questions to credentialed experts — is the only paid component, funded by an optional Pro subscription (≤$20/month). The goal is to give every person — regardless of means — the ability to organize complex information and walk into professional appointments prepared. Domain experts, including retirees who want to stay active and contribute their lifetime of knowledge, earn income by improving the knowledge graphs that make this possible. If it helps people have better conversations with their doctors, advisors, and attorneys, that is the reward.

**Consilium is not a product in any regulated domain.** It is a general-purpose knowledge organization and educational entertainment system. It helps users organize complex information, explore connections, and prepare for informed conversations with real-world professionals (doctors, lawyers, financial advisors, engineers). It does not provide advice, diagnoses, recommendations, or professional services of any kind.

**Supported knowledge domains:**

| Domain | Knowledge graph | Expert pool | Example use | Phase |
|--------|----------------|-------------|-------------|-------|
| **Wellness & Body Literacy** | Human body systems, biomarkers, physiology | Licensed physicians, researchers | Understand your lab results before talking to your doctor | **Phase 0** |
| **Personal Finance** | Markets, instruments, tax rules, macro indicators | CFAs, CPAs, financial analysts | Prepare questions for your financial advisor | Phase 3 |
| **Legal Literacy** | Statutes, case law, procedures, rights | Licensed attorneys, paralegals | Understand your situation before meeting a lawyer | Phase 3 |
| **Engineering & Technical** | Systems, specifications, failure modes, standards | Licensed engineers, domain specialists | Explore technical concepts for professional discussions | Phase 3 |
| **Nutrition & Fitness** | Nutrients, metabolic pathways, exercise physiology | Registered dietitians, exercise scientists | Organize personal fitness data and explore patterns | Phase 3 |

**The system is domain-agnostic by design.** The graph database, agent framework, mesh protocol, and expert micro-question network are identical across domains. Only the knowledge graph content and expert pool differ. Adding a new domain = importing a new foundational knowledge graph + onboarding domain-specific experts.

**Relationship to MiroFish:** Consilium extends the MiroFish/OASIS multi-agent simulation framework (see `mirofish_bittensor_plan.md`). MiroFish provides the agent orchestration engine — persistent agent populations with graph-backed memory. Consilium specializes this for personal knowledge management across multiple domains, with credentialed expert validation via micro-questions.

**Wellness & Body Literacy is the only Phase 0 domain** because it has the richest available foundational knowledge graphs (Hetionet, Uberon, SPOKE), the largest addressable user base (everyone has health data), and the strongest expert pool (millions of licensed physicians worldwide). Additional domains are Phase 3. The rest of this document uses wellness examples throughout, but every technical component is domain-agnostic.

---

## Core Principles

1. **Open source, common good** — the platform is free and open-source. The Expert Portal is a company-operated service with a transparent fee. Infrastructure for people, built by people.
2. **Local first** — your personal knowledge graph never leaves your device
3. **Information, not opinion** — the system surfaces facts, trends, and connections. It does not tell the user or the doctor what to do. The doctor brings clinical judgment. Consilium brings the organized, complete picture. This is both a design principle and a legal requirement.
4. **Patient advocacy** — with high patient-to-doctor ratios, patients must advocate for themselves. Consilium makes that advocacy informed by organizing ALL their data across ALL providers over ALL time and extracting the key information that helps doctors develop critical insights in the minutes they have.
5. **Opinions travel, not data** — the mesh shares reasoning, not records
6. **Graph-native** — all knowledge is modeled as a graph, enabling holistic reasoning across topics, time, and interconnected domains
7. **Persistent consilium** — your advisory team remembers everything, forever
8. **Open mesh** — anyone can run a node; more nodes = richer collective knowledge
9. **Zero configuration for the user** — the user should never see a terminal, a database, or a config file. They add data and explore insights. Everything else is invisible.
10. **Educational entertainment** — Consilium helps users explore, learn, and prepare for real-world professional conversations. It is not a substitute for professional advice in any domain.
11. **Domain-agnostic** — the platform contains zero domain-specific code. All domain knowledge — agents, graphs, schemas — lives in external open-source repos and is loaded on-demand only when the user's data triggers it.
12. **User-driven domains** — the platform never suggests or pre-installs domain capabilities. The user loads their own data; the system classifies it and asks permission to install the relevant domain plugin. If a user never loads medical records, no medical code ever runs on their machine.
13. **Expert dignity** — domain experts, including retirees, earn income and stay engaged by contributing their knowledge. Retirement doesn't mean your expertise stops mattering.
14. **Experts are users too** — no one knows everything. A cardiologist needs nephrology insight. A tax attorney needs immigration law context. Domain experts use Consilium to tap into collective wisdom beyond their own specialty — through the mesh, external AI, and the Expert Portal. They are the system's most powerful users and its most valuable mesh nodes.

---

## Phasing Strategy — Build the Foundation, Then Expand

**The future is unknown. The system must be able to pivot.** This plan describes the full vision, but the build order matters. Each phase validates assumptions before the next phase adds complexity. Features are described in detail throughout this document for completeness, but only Phase 0 is the immediate build target.

### Phase 0: Foundation (Build This First)

The minimum system that delivers the core value proposition: **an individual navigating a complex domain outside their expertise walks into a professional appointment prepared.**

| Component | What's included | What's NOT included |
|-----------|----------------|---------------------|
| **Local data store** | Per-person folders, structured JSON, audit/delete | Backup integrations, FHIR export |
| **Data ingestion** | PDF upload (LLM extraction), Apple Health XML export | Oura/Whoop/CGM APIs, patient portal FHIR |
| **Knowledge graph** | Hand-curated wellness subset (CKD+Diabetes+Hypertension, ~200 nodes, ~500 edges) | Hetionet/SPOKE/OpenBioLink import |
| **Domain plugin** | Wellness plugin only, bundled (not pulled from GitHub) | Multi-domain, dynamic plugin discovery |
| **Agent** | Single generalist agent (not a swarm) | Multi-agent swarms, agent memory, proactive monitoring |
| **LLM** | Local Ollama only | External LLM escalation |
| **Threshold checking** | Deterministic rules in plugin YAML (metformin+eGFR, potassium, etc.) | Outcome-calibrated thresholds |
| **Drug interactions** | Drug-drug interaction graph (deterministic) | Drug-food, drug-supplement, pharmacogenomic |
| **Preparation report** | Severity-triaged (🔴🟡🔵) factual summary: lab trends, threshold violations, drug interactions, complete medication list — information to help doctor develop insights, not opinions | Clinician view, medication reconciliation |
| **Terminology** | SNOMED CT, RxNorm, LOINC on all nodes | UMLS crosswalk for bulk imports |
| **Data quality** | Plausibility checks, unit normalization, extraction confidence | Lab source tracking, cross-lab warnings |
| **Legal framing** | Dual framing, ToS, UI language rules | FDA pre-submission, international review |
| **UI** | Local web dashboard, single-user | QR code sharing, multi-device |

**Phase 0 validates:** Do users actually upload their data? Do they find the preparation report useful? Do they take it to their doctor? Is the single-agent + threshold approach sufficient, or do they need more?

### Phase 1: Validated Expansion (After Phase 0 Proves Value)

Build what users are asking for based on Phase 0 feedback.

| Component | What's added |
|-----------|-------------|
| **Multi-agent swarms** | Specialist agents spawned by data, swarm formation, agent memory |
| **Trend analysis** | Longitudinal monitoring, automated alerting on declining trends |
| **Medication reconciliation** | Compare new vs. previous medication list after appointments |
| **Alert severity triage** | 4-tier system with user-configurable density |
| **Patient + clinician view** | Two presentations of the same findings |
| **Research paper pipeline** | Basic: user uploads paper → LLM extracts edges → adds to graph |
| **External LLM escalation** | User's API key for harder questions |
| **Additional wearable integrations** | Only the ones users request |
| **Acute-on-chronic differentiation** | Dual comparison logic, KDIGO AKI staging |

### Phase 2: Network Effects (After Phase 1 Has Active Users)

| Component | What's added |
|-----------|-------------|
| **Mesh network** | P2P consultation, DHT discovery, barter credits, anonymized queries |
| **Expert Portal** | Micro-questions, reputation, golden sets, Stripe micropayments |
| **Knowledge decay** | Review intervals, supersession, mesh-assisted decay |
| **Outcome tracking** | Post-appointment recording, federated calibration |
| **Full KG import** | Hetionet/SPOKE via UMLS crosswalk, schema alignment |
| **Paper pipeline (advanced)** | Automated discovery, dual extraction, mesh consensus validation |

### Phase 3: Scale (After Network Effects Are Proven)

| Component | What's added |
|-----------|-------------|
| **Additional domains** | Finance, legal, engineering plugins |
| **Dynamic plugin discovery** | GitHub-based plugin registry, auto-detection, sandboxing |
| **Appliance hardware** | Open-source BOM, pre-configured ARM/x86 device |
| **Cohort query mode** | Professional cross-folder queries |
| **Emergency data card** | Lock screen, wallet card, QR, NFC, FHIR |
| **SDOH-aware reports** | Cost, transportation, access context in preparation reports |
| **Pharmacogenomics** | CYP2D6 and other gene-drug interactions |
| **Advanced privacy** | TEE, ZK proofs, differential privacy |

### Why This Order

1. **Phase 0 tests the core hypothesis** without building infrastructure nobody may want. If users don't upload data or don't find the report useful, the mesh and expert portal are irrelevant.
2. **Phase 1 adds depth** — the features that make the system genuinely better than "paste into ChatGPT" (trends, multi-agent reasoning, medication safety).
3. **Phase 2 adds network effects** — but only after there are users generating the demand for expert knowledge and mesh consultation.
4. **Phase 3 adds breadth** — new domains and hardware, but only after the wellness domain proves the model works.

**The domain-agnostic architecture is designed from day one** even though only wellness ships in Phase 0. The plugin interface, competence tag system, and graph schema are all domain-agnostic — but the first plugin is bundled, not dynamically discovered. This means pivoting to new domains later requires writing a new plugin, not rewriting the platform.

---

## System Overview

```
Your Machine
├── Consilium Platform (domain-agnostic core)
│   ├── Data Classifier (LLM)          ← detects what kind of data you loaded
│   ├── Knowledge Graph (Neo4j)         ← domain knowledge, not personal data
│   ├── Personal Data Store             ← per-person folders, auditable, deletable
│   ├── Research Paper Pipeline         ← ingests papers, assesses credibility, extracts knowledge
│   ├── Agent Framework                 ← runs agents from installed domain plugins
│   ├── Local LLM (Ollama)              ← inference stays on your hardware
│   ├── External LLM (optional)         ← user's API key for harder questions
│   └── Mesh Gateway                    ← sends anonymized questions, receives perspectives
│
├── Domain Plugins (installed on-demand from GitHub repos)
│   ├── [not installed until user loads relevant data]
│   ├── e.g. wellness plugin ← pulled only when user uploads lab results
│   ├── e.g. finance plugin  ← pulled only when user loads portfolio data
│   └── e.g. legal plugin    ← pulled only when user uploads legal documents
│
├── Personal Data (per-person folders, never leaves your machine)
│   ├── /people/igor/        ← household member's records
│   ├── /people/elena/       ← household member's records
│   ├── /clients/smith/      ← professional's client records
│   └── Fully auditable — view, export, delete any record at will
│
└── User-Configured Backup (optional, user's responsibility)
    ├── Google Drive, OneDrive, external drive, or custom
    └── Consilium provides export — user owns security and retention

Mesh Network (other people's machines)
├── Node A: runs wellness + finance plugins    ← her data drove those installs
├── Node B: runs only finance plugin           ← he only loaded financial data
├── Node C: doctor's node with rich KG         ← enriched by years of research papers
└── Mesh shares knowledge graph reasoning only — never personal data

Expert Portal (company-operated service)
├── Credentialed domain experts answer micro-questions
├── Earn micro-payments per answer ($0.05–0.50) funded by Pro subscriptions
└── Answers improve domain knowledge graphs for all nodes using that domain
```

---

## Domain Plugin Architecture — The Core Legal and Technical Design

**This is the most important architectural decision in Consilium.** The platform itself contains zero domain-specific code. All domain intelligence — specialist agents, knowledge graphs, schemas, data parsers — lives in **external open-source GitHub repositories** maintained by their respective communities. The platform is pure infrastructure.

### How It Works

```
User loads a document (PDF, CSV, photo, API link)
    │
    ▼
Data Classifier (local LLM) analyzes the content
    │
    ├── "This looks like a blood panel lab report"
    │   → Domain: wellness
    │   → Prompt: "Would you like to install the Wellness & Body Literacy plugin?"
    │   → User confirms → platform pulls github.com/community/consilium-wellness
    │
    ├── "This looks like a brokerage account statement"
    │   → Domain: finance
    │   → Prompt: "Would you like to install the Personal Finance plugin?"
    │   → User confirms → platform pulls github.com/community/consilium-finance
    │
    ├── "This looks like a lease agreement"
    │   → Domain: legal
    │   → Prompt: "Would you like to install the Legal Literacy plugin?"
    │   → User confirms → platform pulls github.com/community/consilium-legal
    │
    └── "I can't classify this document"
        → Stored as raw data in a general-purpose graph
        → User can manually assign a domain later
```

**The user must explicitly confirm every domain plugin installation.** The platform never auto-installs domain capabilities. This is critical for legal positioning: every domain-specific capability is user-initiated.

### What a Domain Plugin Contains

Each domain plugin is a self-contained GitHub repository with a standard structure:

```
consilium-wellness/                    ← example: wellness domain plugin
├── manifest.yaml                      ← plugin metadata, version, dependencies
├── knowledge_graph/
│   ├── import.py                      ← imports foundational KG into Neo4j
│   ├── schema.cypher                  ← graph schema (node types, edge types)
│   └── data/                          ← foundational KG data files
│       ├── hetionet_subset.json       ← pre-processed Hetionet data
│       └── uberon_anatomy.json        ← anatomy ontology
├── agents/
│   ├── agent_manifest.yaml            ← lists available specialist agents
│   ├── cardiology_agent.py            ← agent persona + graph query patterns
│   ├── nephrology_agent.py
│   ├── endocrinology_agent.py
│   └── generalist_agent.py
├── parsers/
│   ├── lab_pdf_parser.py              ← extracts structured data from lab PDFs
│   ├── apple_health_parser.py         ← parses Apple Health XML export
│   ├── fhir_parser.py                 ← imports FHIR-format records
│   └── oura_parser.py                 ← Oura Ring API client
├── questions/
│   ├── question_templates.yaml        ← templates for expert micro-questions
│   └── golden_sets.yaml               ← known-correct Q&A for expert calibration
├── ui/
│   ├── dashboard_widgets.json         ← domain-specific UI components
│   └── explore_prompts.yaml           ← suggested exploration topics
└── README.md                          ← domain documentation
```

**Anyone can create a domain plugin.** The plugin format is open-source and documented. A researcher could create a `consilium-genomics` plugin. A fitness community could create `consilium-athletics`. A car enthusiast could create `consilium-automotive-maintenance`. The platform doesn't control what domains exist — the community does.

### Existing Open-Source Projects as Plugins

Domain plugins can wrap existing open-source projects. The platform doesn't re-invent domain expertise — it provides a standardized interface for existing work:

| Domain | Existing project | What the plugin wraps |
|--------|-----------------|----------------------|
| Wellness | **MDAgents** (github.com/gersteinlab/MDAgents) | Multi-agent clinical reasoning framework — becomes the agent layer |
| Wellness | **Hetionet** (github.com/hetio/hetionet) | Biomedical knowledge graph — becomes the foundational KG |
| Wellness | **Uberon** (github.com/obophenotype/uberon) | Anatomy ontology — extends the KG |
| Finance | **FinGPT** (github.com/AI4Finance-Foundation/FinGPT) | Financial LLM framework — becomes the agent layer |
| Legal | **LegalBench** (github.com/HazyResearch/legalbench) | Legal reasoning benchmark — seeds the knowledge graph |
| Engineering | **OpenStax** textbooks | Engineering fundamentals — seeds the knowledge graph |

**The platform never ships with any of these.** They are pulled from GitHub only when the user's data triggers the relevant domain and the user confirms installation.

### Plugin Versioning and Security

Plugins are pulled from GitHub repos and run locally on the user's machine with access to their knowledge graph. This requires security safeguards:

```
Plugin installation flow (with safety checks):
1. User confirms plugin installation
2. Platform fetches the plugin repo at a specific tagged release (e.g., v1.2.0)
3. Platform verifies the release signature against the plugin registry's public key
4. Platform displays a manifest summary: what node types, edge types, agents,
   and parsers the plugin will install — user reviews and confirms
5. Plugin is installed in a sandboxed directory
6. Plugin code runs with access ONLY to its own graph namespace —
   a wellness plugin cannot read finance graph data

Plugin updates:
- Updates are checked periodically (configurable: daily, weekly, manual)
- Updates require user confirmation — never auto-applied
- Each update shows a diff of what changed (new agents, schema changes, etc.)
- User can pin a specific version and skip updates
- Rollback to previous version is always available

Security model:
- Plugins are open-source — anyone can audit the code before installing
- A community-maintained plugin registry lists reviewed plugins
  with security audit status (audited / unaudited / flagged)
- Unaudited plugins show a warning: "This plugin has not been reviewed
  by the community. Install at your own risk."
- Plugin sandboxing prevents cross-domain data access
- Plugins cannot make network requests except through the platform's
  Mesh Gateway (which enforces anonymization)
```

### Why This Avoids FDA Completely

```
Traditional medical AI product:
  Vendor ships code that processes health data → FDA says "that's SaMD"

Consilium:
  1. Open-source project ships a generic platform with ZERO health code
  2. User loads their own health data into the platform
  3. Platform (generic LLM) says "this looks like health data"
  4. User chooses to install an open-source community plugin (e.g. MDAgents)
  5. Plugin code runs locally on user's machine
  6. Platform never touches, distributes, or endorses health-specific code

Who is the "manufacturer" of the medical device?
  - Not Consilium (ships no health code, doesn't control plugins)
  - Not the plugin author (open-source, no commercial relationship)
  - Not the user (they're a consumer, not a manufacturer)
  → There is no manufacturer → There is no medical device
```

This is the same legal model as:
- **VS Code**: generic editor. Microsoft doesn't regulate Python extensions.
- **WordPress**: generic CMS. Automattic doesn't regulate health plugins.
- **Docker Hub**: generic container registry. Docker doesn't regulate medical containers.
- **npm/PyPI**: generic package registries. They don't regulate what packages do.

The platform is a **tool**, not a **product**. The domain knowledge is user-installed, community-maintained, open-source software. Consilium is the operating system; domain plugins are the apps.

### Data Classifier — How the Platform Detects Domains

The Data Classifier is a local LLM prompt (runs on the user's Ollama instance) that analyzes incoming data and produces a domain classification:

```python
# Simplified classifier logic (runs locally, never sends data anywhere)

CLASSIFIER_PROMPT = """
Classify this document into one of these categories:
- wellness: medical records, lab results, prescriptions, health readings
- finance: bank statements, portfolios, tax documents, invoices
- legal: contracts, agreements, court filings, regulations
- engineering: technical specs, schematics, maintenance logs
- unknown: cannot determine

Return ONLY the category name. Do not interpret the content.
"""

# User drops a PDF into Consilium
document_text = extract_text(uploaded_file)
domain = local_llm.classify(CLASSIFIER_PROMPT, document_text)

if domain != "unknown" and domain not in installed_plugins:
    # Ask user permission — NEVER auto-install
    user_response = prompt_user(
        f"This document appears to contain {domain} information. "
        f"Would you like to install the {domain} domain plugin "
        f"to help organize this data? (You can review the plugin "
        f"source code at github.com/community/consilium-{domain})"
    )
    if user_response == "yes":
        install_plugin(f"consilium-{domain}")
```

**Key constraints on the classifier:**
- Runs locally (never sends document content anywhere)
- Only classifies — does not interpret, analyze, or extract
- Outputs a single category label, not domain-specific insights
- The classifier prompt is domain-agnostic (the same prompt handles all domains)
- Classification happens BEFORE any domain plugin is loaded

When your local agents face a question beyond their confidence, the Mesh Gateway asks the network:
> *"Individual has elevated creatinine trending up over 8 months, concurrent metformin use, HbA1c 7.2, eGFR declining — what patterns does your wellness agent see?"*

No name. No age. No location. Just the data points needed for pattern recognition.

---

## Knowledge Foundation — Domain Knowledge Graphs

When a domain plugin is installed (user-initiated), it imports a **foundational knowledge graph** into the local Neo4j instance — a map of domain entities and their relationships. Agents from that plugin reason against this foundation to interpret user data in domain context.

**The platform ships with no knowledge graph.** The graph database is empty until the user loads data and installs a domain plugin. Each plugin brings its own graph schema, import scripts, and foundational data.

**Example: Wellness & Body Literacy domain plugin** (primary launch domain):

When installed, the wellness plugin imports a knowledge graph mapping organs, systems, biomarkers, and their relationships.

### The Two-Layer Architecture — Knowledge Graph + Personal Data Store

Consilium separates **domain knowledge** from **personal data**. These are fundamentally different things with different storage, sharing, and lifecycle rules.

```
Layer 1: Domain Knowledge Graph (Neo4j — shared, enriched over time)
├── Domain entities and relationships (organs, biomarkers, statutes, instruments)
├── Patterns and correlations (what creatinine rising means for kidneys)
├── Known mechanisms (how CKD progresses, how tax rules interact)
├── Research-derived knowledge (from papers, expert answers, mesh patterns)
└── Confidence tiers on every relationship (established → exploratory)
    ↑ This is what agents reason against
    ↑ This is what the mesh shares
    ↑ This is NOT personal data — it's domain knowledge

Layer 2: Personal Data Store (local filesystem — private, per-person, auditable)
├── Raw records organized in per-person folders
│   ├── /people/igor/            ← household member
│   ├── /people/elena/           ← household member
│   ├── /clients/smith-john/     ← financial advisor's client
│   └── /clients/doe-jane/       ← attorney's client
├── Lab results, financial statements, legal documents
├── Wearable data exports
├── Agent analysis reports
└── Fully auditable — user can view, export, and delete any record at will
```

**The knowledge graph captures knowledge, not personal data.** When you upload a lab report, the system:
1. Extracts domain knowledge (relationships, patterns, thresholds)
2. Updates the knowledge graph with any new connections
3. Stores the raw personal data in the per-person folder for your reference
4. Agents reason across both layers — the knowledge graph for domain context, personal data for your specific situation

**Personal data never enters the knowledge graph.** Your creatinine value of 1.8 stays in your personal data folder. The knowledge that "creatinine above 1.5 with declining eGFR suggests CKD stage 3" lives in the knowledge graph. Agents connect the two at query time.

**Personal data never leaves your machine.** The mesh shares reasoning from the knowledge graph only. No personal data is transmitted, shared, or accessible to anyone — including the Expert Portal.

Agents traverse both layers simultaneously. When NephrologyAgent sees your eGFR declining (personal data), it already knows (from the knowledge graph) that: kidney → filters creatinine → declining eGFR → tubular damage → anemia of CKD → fatigue. It connects your personal data to established domain knowledge automatically.

### Epistemic Confidence Tiers

Not all knowledge is equal. Every relationship in the body knowledge graph carries an explicit confidence tier:

| Tier | Label | Meaning | Source |
|------|-------|---------|--------|
| 1 | `established` | Textbook, high-quality RCTs, clinical consensus | Guidelines, meta-analyses |
| 2 | `inferred` | Well-replicated observational, epidemiological association | Large cohort studies |
| 3 | `exploratory` | Emerging research, mechanistic hypothesis, small studies | Preprints, pilot studies |
| 4 | `unknown` | Plausible connection, no good data yet | Reasoning from mechanism |

**Example — eGFR and fatigue:**
```
[Kidney] ──FILTERS──► [Creatinine]          confidence: established
[CKD]    ──CAUSES───► [Anemia]              confidence: established
[Anemia] ──CAUSES───► [Fatigue]             confidence: established
[CKD]    ──CAUSES───► [Sleep disruption]    confidence: inferred
[HRV decline] ──PRECEDES──► [CKD onset]    confidence: exploratory
[Gut microbiome] ──MODULATES──► [CKD progression]  confidence: exploratory
```

Agents cite confidence when giving opinions:
> *"Your fatigue is likely explained by anemia of CKD — this connection is well-established. There is also an emerging association between HRV decline and early renal stress, but this is based on limited studies."*

### Existing Foundations to Import *(Phase 2 — Phase 0 uses hand-curated subset)*

Rather than building from scratch, the system imports established biomedical knowledge graphs and maps them into the two-layer schema:

| Source | Content | Format | Confidence |
|--------|---------|--------|-----------|
| **Uberon** | Multi-species anatomy ontology — organs, tissues, part-of, connected-to, develops-from (~75k entities) | OWL/RDF | established |
| **FMA** (Foundational Model of Anatomy) | Detailed human anatomy spatial relationships — most comprehensive human-specific | OWL | established |
| **Hetionet** | 20+ biomedical databases merged — anatomy, disease, gene, drug relationships with evidence scores | Neo4j native | established → inferred |
| **SPOKE** (UCSF) | 4M+ nodes from 40+ sources — anatomy, protein, disease, compound | Neo4j native | mixed, tiered |
| **OpenBioLink** | Quality-filtered biomedical KG — explicitly separates high-confidence from low-confidence edges | TSV | explicitly tiered |

**Import strategy — schema alignment is a major engineering effort:**

Hetionet uses its own relationship types (e.g., `PALLIATES`, `ASSOCIATES`, `DOWNREGULATES`). SPOKE uses different types again. Our schema uses `TREATS`, `CAUSES`, `LOWERS`, etc. Mapping 2.25M edges from one schema to another while preserving semantics is not trivial.

**Approach:**
1. **Design the schema mapping FIRST.** Before finalizing Consilium's edge schema, map the 24 Hetionet metaedge types and SPOKE's edge types to our relationship types. Let the available data shape the schema — don't force 2.25M edges into a schema designed from 200 hand-curated edges.
2. **Import at `inferred` confidence.** Imported edges from bulk databases lack the qualified claims (population, effect size, confounders) that hand-curated edges have. Import them as `inferred` with `source: "hetionet_v1.0"` and upgrade as papers/experts enrich them with qualifiers.
3. **Entity resolution via standard terminologies.** Hetionet uses DOID (Disease Ontology), DrugBank IDs, and Entrez Gene IDs. SPOKE uses UMLS CUIs. Our schema uses SNOMED, RxNorm, and LOINC. The mapping layer converts between these using UMLS Metathesaurus as the crosswalk — UMLS maps between ~200 source vocabularies including all of the above.
4. **Accept incompleteness.** Not all Hetionet edges will map cleanly to our schema. Edges with no clear mapping are stored in a `RELATED_TO` catch-all type with the original Hetionet metaedge as a property, and refined later.
5. **Proof-of-concept first.** Import the renal + cardiovascular + endocrine subset (~5% of Hetionet) and validate that entity resolution, schema mapping, and confidence assignment work correctly before attempting full import.

Uberon/FMA require OWL→Cypher conversion. OpenBioLink provides the confidence filtering logic. The import pipeline maps all into the unified schema with confidence tiers preserved.

### Research Paper Pipeline — Discovery, Curation, and Mesh Knowledge Sharing *(Phase 1 basic; Phase 2 full pipeline with mesh)*

**Knowledge dissemination and organization is the core value of Consilium.** The knowledge graph is only as valuable as its edges. Research papers are how new edges get added — and the mesh is how those edges spread to everyone who needs them. This pipeline is not a nice-to-have feature; it is the engine that makes the system more valuable every day.

#### Step 1: Paper Discovery (automated)

The system proactively finds papers relevant to the user's knowledge graph. The graph itself drives discovery — topics, conditions, biomarkers, and medications already in the graph define what's relevant.

```
User's Knowledge Graph
    │
    ├── Topics of interest extracted from graph nodes
    │   (e.g., CKD, SGLT2 inhibitors, diabetic nephropathy)
    │
    ▼
Source Monitoring (configurable frequency — daily/weekly)
    ├── PubMed API — biomedical research
    ├── arXiv — preprints (medical, CS, engineering)
    ├── SSRN — finance, economics, law
    ├── Google Scholar alerts — cross-domain
    ├── Cochrane Library — systematic reviews
    ├── Domain-specific databases (IEEE, FINRA filings, court opinions)
    └── Mesh paper announcements (see Step 5)
    │
    ▼
Relevance Filtering (local LLM)
    ├── Scores relevance to user's graph topics (0–1)
    ├── Filters out duplicates (already ingested)
    ├── Limits suggestions to user-configured max per day/week
    └── Presents top candidates with relevance explanation
```

The user sees: "3 new papers found relevant to your CKD + SGLT2 inhibitor knowledge. Review?"

#### Step 2: Credibility Assessment (automated, user can override)

Every paper gets an automated credibility profile. The system presents the signals — it does NOT make a binary accept/reject decision. Users (especially domain experts) use their judgment.

**Automated credibility signals:**

| Signal | Source | Weight |
|--------|--------|--------|
| Peer review status | Journal metadata | High — published > preprint |
| Journal ranking / impact factor | Journal Citation Reports | Medium — proxy for review rigor |
| Citation count | Semantic Scholar API | Medium — lagging indicator, low for new papers |
| Citation network quality | Who cites it? Other high-quality papers? | Medium |
| Retraction status | Retraction Watch database | Critical — retracted = reject |
| Author h-index and affiliation | Semantic Scholar, ORCID | Low — reputation is a weak signal |
| Study type | LLM classification | High — RCT/meta-analysis > case report > opinion |
| Sample size | LLM extraction | Medium — larger studies, higher confidence |
| Conflict of interest | LLM extraction from disclosures | Low — flag but don't auto-reject |

**Composite credibility score:** Weighted combination of signals, mapped to confidence tiers:
- Score > 0.8 → suggest `established` tier
- Score 0.5–0.8 → suggest `inferred` tier
- Score 0.2–0.5 → suggest `exploratory` tier
- Score < 0.2 → warn user, suggest caution

**Expert override:** Domain experts can override the automated assessment. A nephrologist reading a preprint from a respected lab can mark it credible despite low citation count. A layperson should generally accept the automated assessment. Override decisions are tracked in provenance.

**Credibility signals by domain:**

| Domain | Key credibility signals |
|--------|----------------------|
| Wellness | PubMed indexed, peer-reviewed, RCT/meta-analysis, Cochrane inclusion |
| Finance | Published in recognized journal, regulatory filing, established institution |
| Legal | Published opinion, law review, official reporter, statutory source |
| Engineering | IEEE/ASME/standards body, peer-reviewed, field-validated |

#### Step 3: Knowledge Extraction (LLM)

The LLM reads the paper and extracts structured knowledge — entities, relationships, and findings.

```
Paper: "SGLT2 inhibitors reduce the risk of kidney failure in patients
        with type 2 diabetes regardless of baseline eGFR: EMPA-KIDNEY"
    │
    ▼
LLM Knowledge Extraction
    │
    ├── Entities found:
    │   [Empagliflozin] (Medication, SGLT2 Inhibitor)
    │   [CKD Progression] (Condition)
    │   [Type 2 Diabetes] (Condition)
    │   [Kidney Failure] (Condition)
    │
    ├── Relationships extracted:
    │   1. Empagliflozin ──REDUCES_RISK_OF──► Kidney Failure
    │      Source text: "...28% reduction in risk of kidney disease
    │      progression (HR 0.72, 95% CI 0.64–0.82, p<0.001)"
    │
    │   2. Empagliflozin ──PROTECTS──► Kidney
    │      Source text: "...benefit observed regardless of baseline eGFR,
    │      including eGFR 20–45 mL/min"
    │
    │   3. Empagliflozin ──EFFECTIVE_AT──► eGFR 20–45
    │      Source text: "...no significant interaction by baseline eGFR
    │      subgroup (p-interaction = 0.30)"
    │
    └── Quantitative findings:
        HR: 0.72 (0.64–0.82), p<0.001, n=6609, follow-up: 2 years
```

**Which model does paper extraction?** Research paper extraction — parsing 12-page RCTs with tables, extracting qualified relationships with effect sizes, detecting negation, handling conditional subgroup analyses — requires a frontier model. Local LLMs (Llama/Mistral on consumer hardware) cannot reliably do this today. Paper extraction uses the **External LLM** path (user's API key for OpenAI/Anthropic/Google). This is acceptable because research papers are public documents, not personal data. If no external API key is configured, the system still discovers and presents papers but marks extracted edges as `extraction_confidence: low` and prioritizes them for mesh validation.

**Extraction Validation Pipeline (critical for accuracy):**

LLM extraction is the single biggest accuracy risk in the system. Negation detection alone fails 5–15% of the time with current models. The system uses a multi-layer validation approach:

1. **Dual extraction:** Extract twice with different prompts (one structured, one free-form) and compare results. Disagreements are flagged for manual review or mesh validation.

2. **Quantitative cross-check:** For numerical findings (HR, OR, NNT, CI, p-values), run a regex/structured parser in parallel with the LLM. If the LLM says "HR 0.72" but the regex finds "HR 0.95" in the same sentence, flag for review.

3. **Negation stress test:** After extraction, run a dedicated negation-detection pass: "Does the source text for this edge contain any negation, non-significance, or null-result language?" Flag any edge where negation is detected but a positive relationship was extracted.

4. **Conditional extraction check:** Papers often contain subgroup-specific findings. The system checks: "Did this paper report different effects for different subgroups?" If yes, verify that the extraction produced MULTIPLE edges with DIFFERENT populations and effect sizes — not a single edge that averaged them.

5. **Extraction confidence score:** Every extracted edge gets an `extraction_confidence` (0–1) based on: prompt agreement (dual extraction), quantitative cross-check match, source text length and clarity. Edges below 0.7 are flagged.

6. **Accuracy tracking:** The system tracks what % of extractions are later corrected (by mesh contradiction, expert micro-question, or user report). This metric is reported to the user and used to calibrate trust in the extraction pipeline over time.

**Safeguards summary:**
- Negation detection is a core engineering challenge, not a checkbox — dedicated validation pass on every extraction
- Source text stored alongside every extracted relationship for auditability
- Contradictions with existing graph edges are flagged (see Step 4)
- Table/figure extraction is explicitly marked as lower confidence — key findings in supplementary materials may be missed

#### Step 4: Graph Self-Validation (no human review needed)

**The system does NOT ask users or experts to review papers.** Instead, it validates extracted relationships against the knowledge it already has — the existing graph, the paper's credibility score, and mesh consensus.

```
For each extracted relationship, the system checks:
    │
    ├── CONSISTENT — relationship already exists in the graph
    │   Example: "Empagliflozin PROTECTS Kidney" — already in graph
    │            from 3 other papers
    │   Action: AUTO-ACCEPT. Increase evidence count. No human needed.
    │
    ├── NOVEL + CONSISTENT — relationship is new but fits the graph structure
    │   Example: "Empagliflozin EFFECTIVE_AT eGFR 20–45" — new edge,
    │            but Empagliflozin and eGFR are known entities with
    │            existing connections
    │   Action: ADD at suggested confidence tier (based on paper credibility
    │           + study type + sample size). No human needed.
    │
    ├── NOVEL + NO CONTEXT — relationship involves entities or connections
    │   the graph has no opinion about
    │   Example: "Gut microbiome modulates CKD progression" — graph has
    │            no edges between gut microbiome and CKD
    │   Action: ADD at exploratory confidence. Flag for mesh consensus
    │           (Step 5). Generate targeted expert micro-question if the
    │           edge would change clinical reasoning (Step 6).
    │
    └── CONTRADICTORY — relationship contradicts an established edge
        Example: Paper claims "Metformin is safe at eGFR < 20" but
                 graph has "Metformin CONTRAINDICATED_WHEN eGFR < 30"
                 (established, FDA label, 12 evidence sources)
        Action: DO NOT ADD. Flag the contradiction. The system needs
                strong evidence to override established knowledge:
                - Paper credibility must be very high (> 0.9)
                - Study must be RCT or meta-analysis
                - Even then, add as competing edge with provenance,
                  don't remove the established edge
                - Generate expert micro-question to resolve (Step 6)
```

**Confidence tier assignment for novel edges (no human needed):**

A single study — no matter how large — is never `established` on its own. The standard for `established` is replication: multiple independent studies confirming the same effect, or clinical guideline adoption. A single RCT is strong evidence, but it's one data point.

| Paper credibility | Study type | Replication | Assigned tier |
|------------------|-----------|-------------|--------------|
| > 0.8 | Meta-analysis of multiple RCTs | By definition, replicated | established |
| > 0.8 | Single RCT, any sample size | Not yet replicated | inferred (max) |
| > 0.8 | Adopted by clinical guideline (KDIGO, ADA, etc.) | Guideline implies expert consensus on evidence | established |
| > 0.8 | RCT, confirmed by ≥ 1 independent RCT | Replicated | established candidate (upgrade via mesh consensus or expert micro-question) |
| 0.5–0.8 | Cohort/observational | > 500 participants | inferred |
| 0.5–0.8 | Case-control/small study | < 500 participants | exploratory |
| < 0.5 | Any | Any | exploratory (flagged) |
| Any | Case report / opinion | Any | exploratory |

Every edge carries a `replication_count` — how many independent studies confirm this relationship. A single EMPA-REG OUTCOME result starts at `inferred`. When CREDENCE and DAPA-CKD confirm the SGLT2i class effect (replication_count = 3), and KDIGO adopts it, the edge upgrades to `established`.

**The key insight: the graph validates against itself.** A graph with 10,000 edges can assess whether a new edge is consistent with established knowledge. The more the graph grows, the better it gets at self-validation. This is a virtuous cycle — more knowledge = better curation = more trustworthy knowledge.

#### Step 5: Mesh Consensus (distributed validation without human review)

When a node adds new edges from a paper, it announces them to the mesh. Other nodes validate the edges **automatically** — not by having their users review the paper, but by checking whether the edges are consistent with their own knowledge graphs.

```
Node A extracts and self-validates edges from a paper
    │
    ▼
Node A publishes to the mesh:
    {
      type: "paper_announcement",
      doi: "10.1056/NEJMoa2204233",
      domain_tags: ["nephrology", "sglt2", "ckd", "diabetes"],
      credibility_score: 0.92,
      study_type: "RCT",
      edges_extracted: [
        { from: "Empagliflozin", rel: "REDUCES_RISK_OF", to: "Kidney Failure",
          confidence: "established", effect_size: "HR 0.72" },
        { from: "Empagliflozin", rel: "PROTECTS", to: "Kidney",
          confidence: "established" },
        { from: "Empagliflozin", rel: "EFFECTIVE_AT", to: "eGFR 20-45",
          confidence: "established" }
      ]
    }
    │
    ▼
Other nodes with matching domain tags AUTOMATICALLY validate:
    │
    ├── Node B's graph: "Empagliflozin PROTECTS Kidney" — CONSISTENT
    │   (I already have this edge from 2 other papers)
    │   → auto-ingest new edges, increment consensus count
    │
    ├── Node C's graph: "Empagliflozin PROTECTS Kidney" — CONSISTENT
    │   (matches my graph structure)
    │   → auto-ingest, increment consensus count
    │
    ├── Node D's graph: "EFFECTIVE_AT eGFR 20-45" — NO CONTEXT
    │   (I have Empagliflozin but no eGFR threshold edges)
    │   → ingest at exploratory, wait for more consensus
    │
    └── Node E's graph: contradiction detected on edge 2
        → dispute flag raised, mesh tracks: 3 consistent vs 1 dispute
```

**Consensus thresholds (no human involvement):**

| Consensus level | Action |
|----------------|--------|
| ≥ 10 nodes consistent, 0 disputes | Confidence upgrade (exploratory → inferred) |
| ≥ 50 nodes consistent, < 5% dispute rate | Confidence upgrade (inferred → established) |
| Dispute rate > 20% | Flag edge as contested — do not upgrade |
| Retraction detected | Broadcast retraction alert → all nodes notified → edges flagged |

**Consensus is weighted by graph diversity, not just count.** Ten nodes that all imported the same foundational graph and ingested zero papers are worth less than two nodes with independently enriched graphs. The system weights consensus by:

| Weight factor | How it works | Why |
|---------------|-------------|-----|
| **Graph depth in relevant domain** | A nephrologist's node with 500 nephrology papers carries more weight than a general user | Expert-enriched graphs have more edges to validate against |
| **Source diversity** | Nodes that derived their knowledge from DIFFERENT papers/sources get higher weight | Prevents echo — 10 nodes that all read the same paper = 1 signal, not 10 |
| **Independent validation** | Nodes that validated the edge against their own unique edges (not just the same foundational graph) get higher weight | Foundational graph agreement is low-signal — everyone has it |
| **Provenance chain tracking** | The mesh tracks WHERE each node's matching edges originally came from (which paper DOIs) | If all agreeing nodes trace back to the same single source, consensus is shallow |

**Example — shallow vs. deep consensus:**
```
Shallow: 50 nodes agree on "Empagliflozin PROTECTS Kidney"
  BUT all 50 imported this from the same foundational Hetionet graph
  Effective consensus: 1 source, weight = LOW

Deep: 8 nodes agree on "Empagliflozin PROTECTS Kidney"
  Node A: from EMPA-REG OUTCOME paper ingestion
  Node B: from CREDENCE trial paper + independent analysis
  Node C: nephrologist who ingested DAPA-CKD + clinical observations
  Node D: from EMPA-KIDNEY paper ingestion
  Effective consensus: 4 independent sources, weight = HIGH
```

**What the mesh shares:** Extracted edges + DOI + credibility signals + consensus count + provenance DOIs (which papers each node's agreement traces to). **NOT** the paper content itself (copyright). The mesh shares knowledge, not documents.

#### Step 6: Expert Micro-Questions (only for specific gaps)

**Experts are NOT asked to review papers.** They answer targeted micro-questions generated when the system encounters specific uncertainties during curation. These are the same 10–30 second questions from the Expert Portal — not paper reviews.

**When micro-questions are generated:**

| Trigger | Example question | Why |
|---------|-----------------|-----|
| Novel edge with no graph context | "Is there evidence for gut microbiome affecting CKD progression? [Yes with strong evidence / Emerging research / No evidence / My area, but unsure]" | System found this in a paper but has no existing edges to validate against |
| Contradiction with established edge | "Recent large RCT suggests Metformin may be safe at eGFR 25–30. Does this align with your clinical experience? [Yes, practice is shifting / No, still contraindicated / Depends on patient factors]" | System can't resolve the conflict between paper and existing graph |
| Confidence tier uncertainty | "How would you rate the evidence for HRV decline preceding CKD progression? [Strong / Moderate / Weak / Not aware of evidence]" | System has the edge at exploratory but can't determine if it should be upgraded |
| Threshold refinement | "At what eGFR level would you consider reducing Metformin dose? [< 30 / < 45 / < 60 / Depends on other factors]" | Multiple papers suggest different thresholds |

**The key difference:** The system does all the work — finds papers, assesses credibility, extracts knowledge, validates against the graph, builds mesh consensus. Experts only answer specific, pointed questions that the system can't resolve on its own. Each answer makes the system better at future curation — it's not just adding one edge, it's calibrating the system's judgment.

**Expert answers improve curation, not just knowledge.** When an expert answers "Yes, practice is shifting on Metformin at eGFR 25–30," the system doesn't just add that edge — it learns that recent RCTs in this area may override older FDA guidance. This improves how the system handles similar contradictions in the future.

**The compound effect:** Over time, the mesh becomes a distributed knowledge curation network that operates almost entirely without human intervention. Papers are discovered, validated against the collective knowledge of thousands of graphs, and disseminated — all automatically. Expert micro-questions handle the edge cases where the system genuinely doesn't know. The more the system curates, the fewer questions it needs to ask.

**Knowledge dissemination is the core value of Consilium.** The graph is the structure. Papers are the fuel. The mesh is the distribution and validation network. Expert micro-questions handle the gaps. Together, they create a living knowledge system that curates itself.

### Multi-Person Data Model

Consilium supports **per-person folders** for organizing personal data. This serves two distinct use cases:

**Household use:** A family runs one Consilium instance with separate folders for each member.
```
/people/
├── igor/          ← dad's health records, financial data
├── elena/         ← mom's health records
├── alex/          ← child's health records
└── shared/        ← family-level documents (insurance, shared finances)
```

**Professional use:** A financial advisor, attorney, or physician uses Consilium to organize client data.
```
/clients/
├── smith-john/    ← client's financial records
├── doe-jane/      ← client's legal documents
└── templates/     ← reusable document templates
```

**Isolation rules:**
- Each person's folder is fully isolated by default — agents operating on Igor's data cannot access Elena's data
- The knowledge graph is shared across all folders (it's domain knowledge, not personal data)
- Agents are scoped per-person — when you select a person's folder, agents reason against that person's data + the shared knowledge graph
- Mesh queries never include any personal data from any folder

**Cohort query mode (professional use only) — Phase 3:**

For professionals managing multiple clients/patients, per-folder isolation is essential for privacy but prevents practice-level queries like "Which of my CKD patients haven't had potassium checked in 6 months?" or "Show me all clients with concentrated sector exposure."

Consilium supports a **cohort query mode** that the professional user can explicitly enable:

```
Cohort query mode:
  ├── Requires explicit authorization per query (not a persistent mode toggle)
  ├── Only available to the instance owner — not the mesh, not agents autonomously
  ├── Runs locally — cross-folder data never leaves the machine
  ├── Returns aggregate results — "4 of 12 CKD patients need potassium recheck"
  │   not individual records unless the user drills into a specific folder
  ├── Audit-logged — every cross-folder query is recorded with timestamp and scope
  └── Not available for household mode — cohort queries are for professional
      client management only (user configures which folders are "clients")
```

**Example cohort queries:**
| Query | Result |
|-------|--------|
| "Which clients on ACE inhibitors haven't had potassium checked in 6 months?" | List of client folders matching criteria |
| "Show me all clients with eGFR declining > 5 pts/year" | Aggregate count + individual folders if requested |
| "What % of my diabetic clients are on an SGLT2 inhibitor?" | Percentage + list |

This is not a population analytics tool — it's a practice management aid. All computation is local. No cross-folder data enters the mesh.

### Data Sovereignty — Audit, Deletion, and Backup

Users have full control over their personal data at all times.

**Audit:** Every piece of personal data has a clear provenance trail — when it was loaded, from what source, what knowledge was extracted from it. Users can browse their data store, see exactly what Consilium holds, and verify accuracy.

**Deletion:** Users can delete any record at any time. When personal data is deleted:
- The raw record is removed from the personal data store
- Knowledge already extracted into the graph remains (it's domain knowledge, not personal data)
- Any agent analysis reports referencing that data are also deleted
- Deletion is immediate and permanent — no soft-delete, no recovery period

**Backup:** Consilium provides export and sync mechanisms but **takes zero responsibility for backup security or retention**. The user configures their own backup solution:

| Backup target | How it works |
|---------------|-------------|
| Google Drive | Consilium exports encrypted archive → user's Drive folder syncs it |
| Microsoft OneDrive | Same — encrypted archive → user's OneDrive folder |
| Local external drive | Export to user-specified path |
| Custom solution | Export API — user scripts their own backup |

**What gets backed up:** Personal data store only. The knowledge graph is reproducible from foundational imports + research papers + expert answers — it doesn't need personal backup. Users can optionally back up their knowledge graph state for convenience (faster restore vs. re-import).

**Consilium's responsibility ends at export.** The system provides the mechanism to export data in standard formats. Security, retention, encryption-at-rest, and access control of backups are the user's responsibility. Consilium does not store backup credentials, does not monitor backup health, and does not guarantee data recovery.

### Outcome Tracking — Closing the Feedback Loop *(Phase 2)*

The system generates preparation reports and flags findings, but it never learns whether they were useful. Without outcome feedback, the system can't improve its clinical reasoning over time or calibrate whether its thresholds and alerts are well-tuned.

**After-appointment recording:** After a professional appointment, the user can optionally record outcomes:

```
Consilium flagged: "Metformin dose may need adjustment (eGFR 42, current dose 2000mg)"

User records outcome (simple multiple choice):
  ├── ✅ Doctor agreed — dose was reduced
  ├── ❌ Doctor said not needed — reason: [free text, optional]
  ├── ⏸ Doctor wants to monitor and revisit
  └── 🔄 Doctor ordered additional tests
```

**How outcomes improve the system:**

| Outcome | System action |
|---------|--------------|
| Doctor agreed with flag | Positive signal — threshold/rule is well-calibrated |
| Doctor dismissed flag | Investigate — was the flag wrong, or does the doctor disagree with the guideline? Store as data point, don't change threshold from a single dismissal |
| Doctor ordered more tests | The flag prompted useful action even if the finding wasn't confirmed yet |
| Doctor said not needed + provided reason | Most valuable — the reason may reveal a MODIFIED_BY factor the system missed (e.g., "patient has a single kidney, different thresholds apply") |

**Aggregated outcome calibration (via mesh, anonymized):**

Outcome data — stripped of all personal context — can be shared via mesh to calibrate the system's thresholds:
```
{
  "type": "outcome_signal",
  "rule": "metformin_dose_adjust_when_egfr_below_45",
  "outcome": "doctor_agreed",
  "count": 1
}
```

Aggregated across thousands of nodes: "This threshold was confirmed by doctors 87% of the time" vs "This threshold was dismissed 60% of the time — it may be too aggressive." This is federated outcome tracking — each node shares only the boolean result, never the personal context.

**Outcome recording is always optional.** Many users won't bother, and that's fine. The system works without it. But for users who do record outcomes, the system becomes personally calibrated over time — learning which flags their specific doctors find useful and which ones they consistently dismiss.

### Body Knowledge Graph Schema Extension

The foundational layer adds node types that the personal layer connects to:

**Additional Node Types (body knowledge layer):**

| Node | Description | Example |
|------|-------------|---------|
| `Organ` | Anatomical organ | Kidney, Heart, Liver |
| `System` | Body system | Renal system, Cardiovascular system |
| `Tissue` | Tissue type | Glomerular endothelium, Myocardium |
| `Pathway` | Biological pathway | Renin-angiotensin-aldosterone system |
| `Gene` | Gene with function | APOL1 (kidney disease risk) |
| `Protein` | Protein with role | Erythropoietin (EPO) |
| `Mechanism` | Known disease mechanism | Hyperfiltration injury in diabetic nephropathy |

**Additional Edge Types (body knowledge layer):**

| Edge | Connects | Confidence | Meaning |
|------|----------|-----------|---------|
| `PART_OF` | Organ → System | established | Anatomical containment |
| `FILTERS` | Organ → Biomarker | established | Kidney filters creatinine |
| `PRODUCES` | Organ → Protein | established | Kidney produces EPO |
| `REGULATES` | Pathway → Biomarker | established/inferred | RAAS regulates blood pressure |
| `DAMAGES` | Condition → Tissue | established/inferred | Diabetes damages glomeruli |
| `PRECEDES` | Biomarker → Condition | inferred/exploratory | HRV decline precedes CKD |
| `MODULATES` | Gene → Condition | inferred | APOL1 modulates CKD risk |
| `ASSOCIATED_WITH` | Biomarker ↔ Biomarker | inferred/exploratory | HRV ↔ eGFR correlation |

All edges carry: `confidence_tier`, `evidence_count`, `primary_source`, `last_reviewed_date`, `created_date`, `superseded_by` (if replaced by newer evidence).

**Additional edge properties for precision (see health_knowledge_graph.md for full schema):**

| Property category | Properties | Purpose |
|-------------------|-----------|---------|
| **Qualified claims** | `population`, `effect_size`, `adjusted_for`, `subgroups`, `confounders`, `limitations` | Prevent oversimplification — medical relationships are rarely universal |
| **Context** | `severity`, `temporal` (acute/chronic/transient), `phenotype`, `onset`, `reversibility`, `dose_dependent` | Describe WHEN and HOW the relationship applies |
| **Conflict tracking** | `CONFLICTS_WITH` edge type, `resolution_status` (resolved_by_population / unresolved / superseded) | Model legitimate scientific disagreements (e.g., SPRINT vs ACCORD) |
| **Patient modifiers** | `MODIFIED_BY` edges with `modifier_type` (contraindication / dose_adjustment / reduced_efficacy) | Capture how race, pregnancy, genetics, and comorbidities modify a relationship |
| **Knowledge decay** | `superseded_by`, `stale` flag, review intervals per edge type | Ensure the graph stays current as guidelines update and papers are retracted |
| **Interaction model** | `interaction_type` (drug-drug / drug-drug-disease / drug-food / pharmacogenomic), `disease_context`, `severity` | Handle complex multi-factor interactions, not just simple drug pairs |

### Mesh Contributes to the Knowledge Graph *(Phase 2)*

Over time the mesh itself becomes a source of `exploratory` and `inferred` knowledge — but without a central coordinator (which would contradict the decentralized design).

**Federated pattern detection (no coordinator):**

```
Each node independently computes:
  "In my patient's graph, HRV declined ≥15% in the 6 months before eGFR dropped ≥5 points"
  → True / False

Nodes publish anonymized pattern attestations to the DHT:
  { pattern: "HRV_decline_precedes_eGFR_decline", observed: true, confidence: 0.73 }

Any node can query the DHT and aggregate attestations:
  → 847 / 1000 nodes attested this pattern
  → Local node upgrades its body knowledge graph edge:
     [HRV decline] ──PRECEDES──► [eGFR decline]
     confidence_tier: exploratory → inferred
     evidence_count: 847
```

This is **federated analytics** — each node computes locally, shares only the boolean result, and aggregation happens client-side. No coordinator ever sees individual patient data. The protocol is similar to how federated learning works in Google's Gboard keyboard (local training, shared gradients only), but simpler: boolean pattern attestations instead of model weights.

**Limitations:** This only works for pre-defined pattern queries. The network publishes a periodic "pattern ballot" — a list of hypothesized relationships to test. Nodes that have relevant data attest; nodes without relevant data abstain. New pattern hypotheses can be proposed by any node and added to the ballot after community review.

### Knowledge Decay — Keeping the Graph Current *(Phase 2)*

Medical guidelines update. Trials get superseded. Papers get retracted. The knowledge graph must age gracefully — edges that were correct when added may become outdated. Consilium handles this systematically:

**Review intervals by edge type:**

| Edge type | Review interval | Rationale |
|-----------|----------------|-----------|
| Drug interactions | 12 months | New drugs and interactions discovered regularly |
| Clinical guidelines | 24 months | Major guidelines update every 2–5 years |
| Pathophysiology | 60 months | Basic mechanisms change slowly |
| Drug thresholds | 12 months | Regulatory updates, new trial data |
| Exploratory/inferred edges | 6 months | Weak evidence needs frequent re-evaluation |

**Automated staleness workflow:**
1. System detects edges past their review interval
2. Searches for newer papers on the same topic (automated via Paper Discovery pipeline)
3. If newer evidence found → extract, validate, update or supersede the edge
4. If retraction found → broadcast to mesh, downgrade confidence, flag for user
5. If guideline updated → auto-supersede with new guideline edge

**Supersession model:** Old edges are never deleted — they're marked `superseded_by` pointing to the replacement edge. This preserves the full provenance chain: *why* the knowledge changed, *when*, and *what replaced it*.

**Mesh-assisted decay:** When one node discovers a retraction or guideline update, it broadcasts a `knowledge_update` to the mesh. All nodes with matching edges auto-update. One node's vigilance benefits the entire network.

See `health_knowledge_graph.md` section 8 for the full knowledge decay specification with examples.

---

## Phase 0 — Local Knowledge Graph + Personal Data Store *(Immediate Build Target)*

### 0.1 Graph Schema *(Phase 0 — immediate build target)*

The knowledge graph captures **domain knowledge** — entities, relationships, mechanisms, and patterns. It is not a personal data store. Personal data (lab results, financial records, documents) lives in the **personal data store** (per-person folders on the local filesystem). Agents reason across both at query time.

**Knowledge Graph Node Types (domain knowledge — shared, not personal):**

| Node | Standard Codes | Description | Example |
|------|---------------|-------------|---------|
| `Condition` | ICD-10, SNOMED CT, OMOP | Medical condition (domain knowledge) | Type 2 Diabetes (SNOMED: 44054006, ICD-10: E11) |
| `Symptom` | SNOMED CT | Known symptom type | Fatigue (SNOMED: 84229001) |
| `Medication` | RxNorm CUI, ATC | Drug with known properties | Metformin (RxNorm: 6809, ATC: A10BA02) |
| `Biomarker` | LOINC | Physiological metric definition | eGFR (LOINC: 48642-3), Creatinine (LOINC: 2160-0) |
| `Procedure` | CPT/HCPCS | Medical procedure type | Comprehensive metabolic panel (CPT: 80053) |
| `Genomic` | — | Genetic variant with known effects | APOL1 G1/G2, CYP2D6 poor metabolizer |
| `AgentOpinion` | — | Reasoning output from a specialist agent | Nephrology agent analysis |
| `MeshOpinion` | — | Anonymous reasoning from another node | External cardiology opinion |
| `ResearchPaper` | DOI | Source paper with credibility metadata | DOI, journal, confidence tier, replication_count |

**Standard terminology mapping is required for all clinical nodes.** Without SNOMED CT, RxNorm, and LOINC, entity resolution across imported knowledge bases (Hetionet, SPOKE, DrugBank), research papers, and user-uploaded data becomes unreliable. "Type 2 DM" = "T2DM" = "NIDDM" = SNOMED 44054006. "Glucophage" = "Metformin" = RxNorm 6809. "Serum creatinine" ≠ "Urine creatinine" — LOINC disambiguates. This mapping is done at ingestion time (see Data Quality Layer) and is the single most important prerequisite for scaling the knowledge graph beyond hand-curated edges.

**Personal Data Types (per-person folder on filesystem — private, auditable, deletable):**

| Data | Description | Example |
|------|-------------|---------|
| Lab results | Single measurement at a point in time | Creatinine 1.8 mg/dL on 2026-01-15 |
| Wearable readings | Metrics from wearable devices | HRV 42ms, Sleep score 73 |
| Allergies | Known allergies or adverse reactions | Penicillin allergy |
| Family history | Hereditary/familial conditions | Father: MI at 52 |
| Social factors | Lifestyle or environmental factors | Smoking 10 pack-years |
| Social determinants | Factors that affect access to care and outcomes | Insurance status, transportation barriers, food security |
| Provider records | Treating physicians, encounters | Dr. Smith visit 2026-01-15 |
| Documents | Raw uploaded files | Lab report PDF, legal document |

**Social determinants of health (SDOH):** Research estimates social determinants drive 50–80% of health outcomes. A preparation report that says "ask about SGLT2 inhibitors" is useless if the patient can't afford them ($500/month without insurance). Consilium tracks SDOH as personal data (optional, user-entered):

| Factor | Example | How agents use preparation reports |
|--------|---------|-----------------------------------|
| Insurance type | Medicare, Medicaid, uninsured, commercial | "Ask about Empagliflozin AND whether there's a patient assistance program or generic alternative" (instead of just "ask about Empagliflozin" — which is $500/month without insurance) |
| Transportation | No car, relies on public transit | "Nephrology referral recommended — ask if telehealth is available" (instead of assuming the patient can drive to a specialist) |
| Food security | Limited access to fresh food | "Low-potassium diet recommended — ask your doctor about what's practical given your food access" (instead of a standard dietary restriction that requires expensive specialty foods) |
| Health literacy | Self-reported comfort with medical info | Adjust language complexity of preparation reports. Clinician view is unaffected. |
| Medication affordability | Monthly medication budget | Flag cost-prohibitive medications before the appointment so the patient can discuss alternatives proactively, not discover the cost at the pharmacy |
| Lives alone | No caregiver present | Flag if a condition requires monitoring that depends on someone else being present (e.g., post-procedure observation, hypoglycemia monitoring) |

**How SDOH changes the preparation report:**

Without SDOH: "Consider SGLT2 inhibitor (e.g., Empagliflozin) — proven kidney and heart protection."
With SDOH (uninsured): "Consider SGLT2 inhibitor — proven kidney and heart protection. Note: Empagliflozin costs ~$500/month without insurance. Ask about: (1) manufacturer patient assistance programs, (2) generic dapagliflozin availability, (3) whether your clinic has samples."

The difference between a clinically correct suggestion and a practically actionable one is often the SDOH context. A perfect treatment plan that the patient can't execute is fiction.

SDOH data is never shared via mesh, never enters the knowledge graph, and is entirely optional. Agents use it to make preparation reports more realistic — not just clinically accurate, but practically actionable for the specific person's situation.

Personal data is loaded into memory at query time, connected to knowledge graph entities by the agent, and never persisted in the graph database. The graph holds "Metformin TREATS Type 2 Diabetes" (knowledge). The personal data store holds "Igor takes Metformin 1000mg 2x daily" (personal fact).

**Knowledge Graph Edge Types (domain relationships):**

| Edge | Connects | Meaning |
|------|----------|---------|
| `TREATS` | Medication → Condition | Medication for condition |
| `SUGGESTS` | Biomarker → Condition | Abnormal value may indicate |
| `CORRELATES_WITH` | Biomarker ↔ Symptom | Statistical correlation |
| `CAUSES` | Condition → Symptom | Known causal relationship |
| `CONTRADICTS` | Medication → Medication | Drug interaction |
| `INTERACTS_WITH` | Medication → Medication/Food/Supplement | Drug interaction (typed: drug-drug, drug-food, pharmacogenomic) |
| `PRECEDES` | Biomarker → Condition | Temporal pattern |
| `TRENDING` | Biomarker → Biomarker | Biomarker trend relationship |
| `CONFLICTS_WITH` | Edge → Edge | Two edges with contradictory evidence (e.g., SPRINT vs ACCORD) |
| `MODIFIED_BY` | Edge → PatientFactor | Relationship modified by pregnancy, genetics, comorbidity, etc. |
| `SUPERSEDED_BY` | Edge → Edge | Newer evidence replaces older (knowledge decay) |
| `EVIDENCED_BY` | Edge → ResearchPaper | Source paper for this relationship |

**How agents connect personal data to the knowledge graph at query time:**

```
Knowledge Graph (Neo4j — domain knowledge)              Personal Data Store (filesystem)
┌─────────────────────────────────────────┐            ┌──────────────────────────────────┐
│ [Metformin] ──TREATS──► [Type 2 DM]    │            │ /people/igor/                    │
│ [Creatinine] ──SUGGESTS──► [CKD]       │  ◄─agents─►│   labs/2026-01-15-cmp.json       │
│ [CKD] ──CAUSES──► [Anemia]             │  connect   │     creatinine: 1.8, eGFR: 52   │
│ [Anemia] ──CAUSES──► [Fatigue]         │  at query  │   medications/current.json        │
│ [HRV decline] ──PRECEDES──► [CKD]      │  time      │     metformin 1000mg 2x daily    │
│ [Metformin] ──CONTRADICTS──► [...]      │            │   wearables/hrv-march.json       │
└─────────────────────────────────────────┘            │     HRV: 38ms avg               │
                                                       └──────────────────────────────────┘
```

An agent querying "why is this person fatigued?" loads Igor's personal data into memory, traverses the knowledge graph for domain context, and finds: declining eGFR (personal) + CKD causes anemia (knowledge) + anemia causes fatigue (knowledge) + low HRV (personal) + HRV decline precedes CKD (knowledge). It reasons across both layers simultaneously — but the graph itself contains only domain knowledge.

### 0.2 Temporal Modeling *(Phase 0 — store timestamped records; Phase 1 — automated trend analysis)*

Personal measurements are stored in the per-person data folder as timestamped JSON records. Agents load these into memory and compute trends at query time:

```
/people/igor/labs/
├── 2025-07-15-cmp.json    → { "eGFR": 61, "creatinine": 1.4, ... }
├── 2025-10-20-cmp.json    → { "eGFR": 58, "creatinine": 1.5, ... }
├── 2026-01-15-cmp.json    → { "eGFR": 55, "creatinine": 1.6, ... }
└── 2026-03-10-cmp.json    → { "eGFR": 52, "creatinine": 1.8, ... }
```

Agents compute trends from the personal data and reason against the knowledge graph:
- *"eGFR declining at 4.5 points/year — at this rate, Stage 4 CKD in 18 months"* (trend from personal data + CKD staging thresholds from knowledge graph)
- *"HRV trending down 3 months before creatinine rose — early stress signal"* (temporal pattern from personal data + HRV-CKD correlation from knowledge graph)

### 0.3 Data Ingestion *(Phase 0 — PDF + Apple Health; Phase 1 — additional sources)*

**Wearables:**

| Source | Metrics | Method |
|--------|---------|--------|
| Apple Watch | Heart rate, ECG, SpO2, activity, sleep | Apple Health Export (XML) or HealthKit API |
| Oura Ring | HRV, sleep stages, temperature, readiness | Oura API (personal token) |
| Whoop | Strain, recovery, HRV | Whoop API |
| Continuous Glucose Monitor | Blood glucose, time-in-range | LibreView API / NightScout |

**Lab Results:**
- PDF parsing (most lab reports are PDFs) using LLM extraction
- HL7 FHIR import (standard medical data format)
- Manual entry via structured form
- Direct integration with Quest, LabCorp APIs (where available)

**Doctor Notes / Medical Records:**
- Upload PDF/text → LLM extracts structured data
- User controls what gets ingested — nothing automatic

**Research Papers:**
- Upload PDF, paste DOI or PubMed ID
- Credibility assessment (journal, peer review, citations, retraction check)
- Knowledge extraction into graph (entities, relationships, findings)
- See "Research Paper Ingestion" section above for full pipeline

**Ingestor pipeline:**
```
Raw data (PDF, API, XML, research paper)
    → Parser (extract structured fields)
    → Data Quality Layer (see below)
    → Classifier (personal data vs. domain knowledge)
    │
    ├── Personal data path:
    │   → Store in per-person folder as structured JSON
    │   → Deduplicator (don't double-count the same result)
    │   → Change detector (flag new data for agent review)
    │   → Lab source tracker (tag which lab/assay produced this result)
    │
    └── Knowledge path:
        → Entity resolver (map to SNOMED/RxNorm/LOINC — see terminology mapping)
        → Relationship extractor (identify domain relationships)
        → Knowledge graph writer (upsert nodes and edges in Neo4j)
        → Confidence tier assignment (based on source credibility)
```

### Data Quality Layer

The hardest problem isn't reasoning over data — it's trusting the data. Lab reports from different sources, PDF extraction errors, unit confusion, and physiologically impossible values can all produce wrong conclusions. The data quality layer catches these before they enter the personal data store.

**Plausibility checks (reject or flag physiologically impossible values):**

| Biomarker | Reject if | Flag for review if | Rationale |
|-----------|----------|-------------------|-----------|
| eGFR | < 0 or > 200 | < 5 or > 150 | Physiologically impossible outside range |
| Creatinine | < 0 or > 30 mg/dL | > 10 mg/dL | Values > 10 almost always mean dialysis |
| Potassium | < 1.0 or > 10.0 mEq/L | < 2.5 or > 6.5 | Outside range = likely lab error or critical emergency |
| Hemoglobin | < 1.0 or > 25 g/dL | < 5 or > 20 | Below 5 = near death, above 20 = polycythemia or error |
| HbA1c | < 2% or > 20% | > 15% | Values > 15 are almost always lab errors |

**Unit normalization:** Labs report values in different units. The system normalizes on ingestion:

| Biomarker | Accepted units | Normalized to | Conversion |
|-----------|---------------|---------------|-----------|
| Creatinine | mg/dL, μmol/L | mg/dL | μmol/L ÷ 88.4 |
| Potassium | mEq/L, mmol/L | mEq/L | 1:1 (equivalent) |
| Glucose | mg/dL, mmol/L | mg/dL | mmol/L × 18.018 |
| Hemoglobin | g/dL, g/L | g/dL | g/L ÷ 10 |

If the unit is ambiguous or missing, the system flags the value for user confirmation rather than guessing.

**Lab source tracking:** Every measurement records WHERE it came from:

```json
{
  "value": 1.8,
  "biomarker": "creatinine",
  "loinc_code": "2160-0",
  "unit": "mg/dL",
  "date": "2026-03-20",
  "source": {
    "lab_name": "Quest Diagnostics",
    "report_id": "QD-2026-0320-4571",
    "extraction_method": "pdf_llm",
    "extraction_confidence": 0.95,
    "original_text": "Creatinine    1.8   mg/dL   H   (0.7-1.3)"
  }
}
```

**Why this matters for trend analysis:** The same blood sample can produce different creatinine values at different labs (different assays, different calibration). If a user switches labs between measurements, a trend line may show a false jump. The system warns: *"Note: this measurement is from Lab B; previous measurements were from Lab A. Trend comparison may be affected by assay differences."*

**Extraction confidence and user verification:** When a value is parsed from a PDF, the system stores the `extraction_confidence` and the `original_text` it read. If an extracted value triggers a clinical threshold (e.g., eGFR < 45 = metformin dose adjustment), the system shows the user the extracted value alongside the original text and asks: *"We read your eGFR as 42 from this report. Is this correct?"* Safety-critical thresholds require confirmation before generating alerts.

**FHIR terminology mapping on ingestion:** Every ingested biomarker, condition, and medication is mapped to standard terminologies at parse time:
- Biomarkers → LOINC code (resolves "Creatinine" vs "Creatine" vs "Creatinine Clearance")
- Conditions → SNOMED CT + ICD-10 (resolves "Type 2 DM" vs "T2DM" vs "Diabetes, non-insulin dependent")
- Medications → RxNorm CUI (resolves "Glucophage" vs "Metformin" vs "Metformin HCl 500mg")

Entity resolution happens at ingestion — not at query time. This ensures consistent matching across all data sources.

---

## Phase 1 — Dynamic Agent Swarms *(After Phase 0 Validation)*

### 1.1 Agents Are Spawned by Data, Not Pre-Installed

The platform does not come with a fixed roster of agents. **Agents are spawned dynamically based on what data the user loads and what domain plugins are installed.** The plugin provides agent templates; the platform instantiates only the agents relevant to the user's actual data.

```
User loads a lab panel containing: creatinine, eGFR, HbA1c, hemoglobin
    │
    ▼
Platform detects: wellness domain data (via classifier)
User confirms: install wellness plugin
    │
    ▼
Plugin examines the graph and spawns ONLY relevant agents:
    ├── NephrologyAgent  ← because creatinine + eGFR are present
    ├── EndocrinologyAgent ← because HbA1c is present
    ├── HematologyAgent  ← because hemoglobin is present
    └── GeneralistAgent  ← always spawned as coordinator

NOT spawned (no relevant data in graph):
    ├── CardiologyAgent  ← no ECG or BP data loaded yet
    ├── PulmonologyAgent ← no SpO2 data loaded yet
    └── NeurologyAgent   ← no sleep/HRV data loaded yet

Later, user connects Apple Watch → BP + ECG data flows in
    → CardiologyAgent is spawned automatically
    → CardiologyAgent joins the existing consilium
```

**Agents are identified by competence tags, not hardcoded roles.** Each agent declares what graph node types and edge patterns it can reason about:

```yaml
# From wellness plugin: agents/nephrology_agent.yaml
competence_tags:
  - renal_function
  - creatinine
  - eGFR
  - electrolytes
  - kidney_disease
  - dialysis
watches_node_types: [LabResult, Biomarker]
watches_patterns:
  - "Biomarker.name IN ['eGFR', 'creatinine', 'BUN', 'potassium']"
```

The platform uses these tags for swarm formation, mesh routing, and expert question targeting — without understanding what they mean.

### 1.2 Swarm Formation — Agents Find Each Other by Competence

When a consilium is triggered (new data, user question, scheduled review), agents don't follow a pre-defined workflow. They **self-organize into a swarm** based on competence overlap with the data involved.

```
Trigger: new lab panel uploaded containing eGFR, HbA1c, hemoglobin

Platform identifies which graph nodes were created/updated:
  → [LabResult: eGFR=52], [LabResult: HbA1c=7.2], [LabResult: Hgb=10.2]

Platform queries: "which agents have competence tags matching these nodes?"
  → NephrologyAgent: matches eGFR         ← invited to swarm
  → EndocrinologyAgent: matches HbA1c     ← invited to swarm
  → HematologyAgent: matches hemoglobin   ← invited to swarm
  → PharmacologyAgent: matches (cross-cuts all medications) ← invited
  → GeneralistAgent: always invited       ← moderator

Swarm forms: { Nephro, Endo, Hemato, Pharma, Generalist }
  → Round 1: each agent analyzes independently, writes preliminary opinion
  → Round 2: agents read each other's opinions, comment, agree/disagree
  → Round 3: GeneralistAgent synthesizes, dissenters note objections
  → Result: written to graph as linked [AgentOpinion] nodes
```

**Cross-domain swarms:** If a user has both wellness and finance plugins installed, and loads data that touches both (e.g., medical expenses in a financial plan), agents from different domains can be invited to the same swarm. A FinanceAgent and a WellnessAgent might both have opinions on a pattern — the platform doesn't care what domain they come from, only that their competence tags match the data.

### 1.3 Agent Memory via Graph

Unlike MDAgents (stateless), every agent opinion is written back to the graph:

```
[NephrologyAgent] analyzes eGFR pattern
    → creates [AgentOpinion] node
    → links to: [LabResult: eGFR=52], [LabResult: creatinine=1.8]
    → tags: competence=renal_function, date=2026-03-22, confidence=0.82
    → text: "eGFR shows a consistent downward pattern over 18 months"
```

Next time NephrologyAgent runs, it reads its own prior opinions and the subsequent data to see if its observations held up. Agents track their own accuracy over time.

### 1.4 Proactive Monitoring

Agents don't only respond to triggers — they watch for patterns in the background:

```yaml
# Each agent defines its own watch rules (from plugin config)
NephrologyAgent:
  schedule: weekly
  watch_query: "eGFR trend over 12 months"
  alert_if: "slope < -3 points/year"
  escalate_if: "slope < -6 points/year → request mesh consultation"

PortfolioAgent:
  schedule: daily
  watch_query: "sector concentration in portfolio"
  alert_if: "any sector > 40% of total allocation"
```

Agents that find nothing noteworthy write a brief "all clear" note to the graph so there's a record of monitoring. Watch rules are defined by plugins, not by the platform.

### 1.5 Alert Severity Triage

**Alert fatigue kills clinical decision support tools.** Every EHR fires dozens of alerts per patient; clinicians override 95% of them and miss the 5% that matter. Consilium prevents this with a mandatory severity classification on every alert.

**Every finding must be classified into exactly one severity tier:**

| Tier | Label | Criteria | Presentation | Example |
|------|-------|----------|-------------|---------|
| 🔴 | **Critical** | Immediate danger if not addressed. Exceeds a safety threshold. | Red banner, top of report. Cannot be scrolled past without acknowledgment. | Metformin 2000mg with eGFR 42 (exceeds FDA threshold) |
| 🟡 | **Urgent** | Should be addressed at next appointment. Trending toward danger. | Yellow highlight, prominent position. | eGFR declining 16 pts/year, K+ 5.2 trending up |
| 🔵 | **Monitor** | Worth tracking but not actionable now. | Listed in report, standard formatting. | Phosphorus 4.8 (slightly above 4.5) |
| ⚪ | **Informational** | Educational context, no action needed. | Available on request, collapsed by default. Not in summary view. | "Here's how CKD and diabetes are connected" |

**Severity is deterministic, not LLM-judged.** Plugins define severity rules alongside threshold rules:

```yaml
# From wellness plugin: rules/metformin_egfr.yaml
rule: "metformin_dose_exceeds_safe_limit"
trigger: "on_metformin AND current_dose > 1000 AND eGFR < 45"
severity: critical      # deterministic — not LLM opinion
message_patient: "Your Metformin dose exceeds safe limits for your kidney function."
message_clinician: "Metformin {dose}mg/d — eGFR {egfr} (< 45 threshold). FDA/KDIGO: max 1000mg/d."
source: "FDA label, KDIGO 2024"
```

**User-configurable alert density:** Users choose how many alerts they want to see:

| Mode | What's shown | Who it's for |
|------|-------------|-------------|
| **Critical only** | Only 🔴 critical alerts | Clinicians glancing at patient's phone, busy users |
| **Critical + Urgent** | 🔴 + 🟡 | Default for most users |
| **Full report** | 🔴 + 🟡 + 🔵 + educational context | Patients preparing for appointments |
| **Clinician view** | All tiers, compact format, no educational text, raw values + evidence sources | Healthcare providers reviewing patient data |

### 1.6 Patient View vs. Clinician View

The same findings, presented two different ways:

**Patient view** (default): Educational explanations, plain language, "questions to ask your doctor" framing. This is the preparation report.

**Clinician view**: Raw findings with standard medical abbreviations, ICD-10 codes, guideline citations, and trend data. Designed to be scanned in 30 seconds. No educational framing, no "your kidneys are like a filter" explanations. Just: `eGFR 58→52→42 in 12mo (-16/yr). On metformin 2000mg — exceeds FDA threshold at eGFR < 45. KDIGO 2024.`

**How to access clinician view:**
- Patient shows their phone to the doctor → doctor taps "Clinician View" button
- Exports to PDF in clinician format for printing or faxing
- QR code on patient's screen → doctor scans → opens clinician view on their device (data transmitted via local network, never via internet)

The clinician view is NOT the "educational entertainment" product. It is a data summary tool — like a patient bringing an organized spreadsheet of their labs. The clinician view never says "Consilium recommends" or "you should" — it presents findings and citations.

### 1.7 Medication Reconciliation at Transitions of Care

**The #1 cause of preventable harm in hospitals is medication errors during transitions of care** — ER admission, hospital discharge, specialist referral, nursing home transfer. A patient on 12 medications gets admitted, half get changed, they go home with a new list that doesn't match what they were taking before, and nobody catches that the cardiologist added Drug X which interacts with the nephrologist's Drug Y from 6 months ago.

Consilium is perfectly positioned to catch this because it has the complete medication history.

**Transition of care mode — triggered when the user updates their medication list:**

```
User photographs discharge summary or enters new medication list
    │
    ▼
System compares NEW list against PREVIOUS list
    │
    ├── ADDED medications:
    │   Check each new med against ALL existing meds + conditions for interactions
    │   "New: Spironolactone 25mg. WARNING: you are on Lisinopril (ACE inhibitor)
    │    and your potassium is 5.2. Triple risk: ACEi + K-sparing diuretic + CKD."
    │
    ├── REMOVED medications:
    │   Flag if removal seems unintentional (was it discussed at the appointment?)
    │   "Removed: Amlodipine 5mg. Was this intentional? Your blood pressure was
    │    142/88 at last reading — removing a BP medication may need monitoring."
    │
    ├── DOSE CHANGES:
    │   Check if new dose is appropriate for current labs
    │   "Changed: Metformin 2000mg → 1000mg. This is appropriate — your eGFR is 42."
    │
    └── UNCHANGED but should have changed:
        Flag medications that should have been adjusted but weren't
        "Unchanged: Metformin 2000mg. WARNING: your eGFR is 42.
         Current guidelines recommend max 1000mg/day at eGFR < 45.
         Was a dose reduction discussed?"
```

**This is a patient safety feature that saves lives.** It should be prominently accessible after every appointment or hospital stay: "Just had an appointment? Update your medications and Consilium will check for issues."

### 1.8 Acute-on-Chronic Differentiation

Agents must distinguish between acute changes (developing over hours/days) and chronic trends (developing over months). The same lab value means completely different things depending on the timeframe.

**When new labs arrive, agents compute TWO comparisons:**

```
New creatinine: 2.4    Date: 2026-03-23

Comparison 1 — vs. most recent value:
  Previous: 1.8 (3 days ago)
  Delta: +0.6 in 3 days → ACUTE CHANGE

Comparison 2 — vs. stable baseline:
  Baseline: average of last 3 readings over 6 months = 1.8
  Chronic trend: stable at 1.8 (CKD Stage 3b)

Assessment: ACUTE kidney injury superimposed on chronic kidney disease.
  "Your creatinine rose from 1.8 to 2.4 in 3 days. This is a SUDDEN change
   on top of your existing kidney disease, not a continuation of your CKD
   trend. This may require urgent evaluation."
  Severity: 🔴 CRITICAL (acute rise ≥ 0.3 mg/dL in 48h = KDIGO AKI Stage 1)
```

**KDIGO AKI staging criteria (built into the knowledge graph):**

| AKI Stage | Definition | Severity |
|-----------|-----------|----------|
| Stage 1 | Creatinine ≥ 1.5x baseline within 7 days, or ≥ 0.3 mg/dL rise within 48h | 🟡 Urgent |
| Stage 2 | Creatinine ≥ 2.0x baseline | 🔴 Critical |
| Stage 3 | Creatinine ≥ 3.0x baseline, or creatinine ≥ 4.0 mg/dL, or initiated on dialysis | 🔴 Critical — emergency |

Without acute-on-chronic differentiation, a creatinine of 2.4 in a CKD patient with a baseline of 1.8 looks like "gradual worsening." With it, the system recognizes an acute 33% rise in 3 days and flags an emergency. This distinction saves lives.

### 1.9 Emergency Data Card *(Phase 3)*

**If a patient is unconscious in the ER, their medication list, allergies, and conditions could save their life.** But Consilium's local-first design means the data is on their device, which the ER doctor may not be able to access.

**The Emergency Data Card is a pre-generated minimal summary designed for emergency access:**

```
Contents (automatically generated, updated when data changes):
  ├── Current medications (name, dose, frequency)
  ├── Allergies and adverse reactions
  ├── Active conditions (with ICD-10 codes)
  ├── Most recent critical lab values (eGFR, K+, glucose, INR if on anticoagulant)
  ├── Emergency contact
  └── Last updated date
```

**Access methods (user configures which they want):**

| Method | How it works | When it's useful |
|--------|-------------|-----------------|
| **Phone lock screen** | Exports to Apple Health Medical ID or Android emergency info | ER staff check phones routinely |
| **Printed wallet card** | PDF formatted for credit-card-size printing | Phone dead or unavailable |
| **QR code** | Scannable code on phone lock screen or printed card → opens a local web page with the summary | Fast access for any healthcare provider |
| **NFC tag** | Programmable NFC sticker on phone case or medical bracelet → tap to read | Fastest — tap and read |
| **FHIR export** | Emergency data in standard HL7 FHIR format for EHR import | Hospital systems can ingest directly |

**What the Emergency Data Card is NOT:**
- It is NOT the full knowledge graph or personal data store
- It is NOT shared via mesh or internet — it's local to the user's devices
- It does NOT contain trend data, agent opinions, or preparation reports
- It IS the minimum information an ER doctor needs in the first 5 minutes

**Auto-update:** The card regenerates automatically whenever medications, allergies, or conditions change. The user is prompted: "Your medication list changed. Update your Emergency Data Card?" The card shows a "last updated" date so clinicians can judge currency.

---

## Phase 2 — Mesh Network (Federated Consultation) *(After Phase 1 Has Active Users)*

### 2.1 How the Mesh Works — Dynamic Domain Discovery

When your local consilium faces a question beyond its confidence threshold, it reaches out to the mesh — other instances running on other people's machines. **The mesh does not use hardcoded domains or specialties.** It routes by competence tags — the same tags agents use locally to form swarms.

**How mesh routing works:**

```
1. Local agent swarm debates a pattern and reaches low consensus
   → e.g. NephrologyAgent is 60% confident, wants more perspectives

2. The swarm generates a mesh query with competence tags:
   → required_tags: [renal_function, eGFR, diabetes_comorbidity]
   → These tags were derived from the data, not pre-defined

3. Query is published to the DHT (distributed hash table)
   → Other nodes advertise which competence tags their agents cover
   → Node A has: [renal_function, eGFR, cardiology, electrolytes]  ← MATCH
   → Node B has: [portfolio, tax, macro_economics]                  ← no match
   → Node C has: [renal_function, hematology]                       ← partial match

4. Matched nodes' agents form a REMOTE SWARM for this query
   → Node A's NephrologyAgent + Node C's HematologyAgent
   → They each analyze the anonymized context independently
   → Each returns a perspective

5. Local consilium integrates remote perspectives into its swarm debate
```

**What gets sent (the consultation request):**
```json
{
  "query_id": "q_a3f7...",
  "competence_tags": ["renal_function", "eGFR", "diabetes_comorbidity"],
  "anonymized_context": {
    "data_patterns": ["declining_trend:eGFR:18_months", "elevated:creatinine", "stable:HbA1c"],
    "co_occurring_tags": ["diabetes", "hypertension", "anemia"],
    "binned_values": {
      "eGFR_range": "30-59",
      "creatinine_range": "1.5-2.0",
      "HbA1c_range": "7.0-8.0",
      "hemoglobin_range": "10.0-11.0"
    }
  },
  "question": "What patterns do agents with renal_function competence see in this data?",
  "requesting_node": "node_hash_xyz"
}
```

**Values are sent as clinically meaningful bins, not exact numbers.** Exact values (eGFR 52, creatinine 1.8) combined with co-occurring conditions form quasi-identifiers in small populations. Binned ranges (eGFR 30–59, creatinine 1.5–2.0) match thousands of people, making re-identification impractical. The bins are aligned with clinical staging (CKD stages, diabetes control bands, anemia severity) so responding nodes can still reason effectively — a nephrologist doesn't need eGFR 52 vs 54, they need "Stage 3" vs "Stage 4".

No domain labels. No specialty names. No medical terminology in the protocol layer. Just competence tags and anonymized data patterns. The protocol is domain-agnostic — the same mesh message format works for renal function queries, portfolio allocation queries, and contract clause queries. The mesh doesn't know what domain it's routing.

**Nodes advertise competence, not identity:**

```
Each node publishes to the DHT (updated whenever plugins change):
{
  "node_hash": "abc123...",
  "competence_tags": ["renal_function", "eGFR", "cardiology", "electrolytes",
                       "hematology", "pharmacology"],
  "data_richness": 847,    // number of data points in graph
  "agent_count": 6,
  "uptime_hours": 4320,
  "reputation": 0.78
}

No domain name. No "I'm a medical node." Just tags and stats.
```

**Re-identification risk (honest assessment):** Removing demographics is necessary but not sufficient. Rare condition combinations can be uniquely identifying — a patient with Fabry disease + pheochromocytoma + a specific medication list may be one of a handful of people worldwide. Mitigation strategies:
- **Binned values, not exact values**: mesh queries send clinically meaningful ranges (eGFR "30–59") instead of exact numbers (eGFR 52). Bins match thousands of people; exact values narrow to individuals. See query format above.
- **k-anonymity enforcement**: before sending a mesh query, the local node checks whether the condition combination is shared by at least k patients in the body knowledge graph. If not, the query is generalized (e.g., "rare lysosomal storage disease" instead of "Fabry disease").
- **Temporal linkability protection**: even with fresh pseudonymous IDs, sequential queries from the same node about "declining eGFR" every 3 months with progressively worse values create a re-identifiable pattern. Mitigations: (1) randomize query timing within a window (±2 weeks), (2) batch multiple queries into single compound requests, (3) vary the bin width between queries so the same person's data appears in different bins over time, (4) rate-limit per-topic queries (same competence tags) to max 2 per 6-month period per person.
- **Query rate limiting**: a node cannot send more than N mesh queries per day, preventing profile reconstruction from query patterns.
- **No longitudinal linking**: each mesh query uses a fresh pseudonymous ID. No way to determine that two queries came from the same patient. Combined with binned values and temporal randomization, reconstructing a progression timeline from mesh queries is computationally infeasible.

**What comes back:**
```json
{
  "query_id": "q_a3f7...",
  "responding_node": "node_hash_abc",
  "matched_tags": ["renal_function", "eGFR", "diabetes_comorbidity"],
  "perspective": "eGFR decline of ~4 points/year in a diabetic on metformin warrants attention.
               Metformin should be reviewed at eGFR < 45 (you're approaching this).
               The concurrent anemia is significant — likely anemia of CKD.
               Recommend: urine albumin/creatinine ratio, iron studies, PTH level.
               Urgency: non-urgent but schedule nephrology referral within 3 months.",
  "confidence": 0.78,
  "based_on": "local case history includes 12 similar presentations"
}
```

### 2.2 What Nodes Share With Each Other

| Shared | Not shared |
|--------|-----------|
| Anonymized medical context | Name, age, location, dates |
| Clinical question | Any graph data |
| Agent opinion (text) | Lab PDFs or records |
| Confidence score | Identity of requester |
| "Based on N similar cases" | Details of those cases |

The responding node's agent draws on its *own patient's graph* to inform its opinion — it has seen similar cases. But it shares only the conclusion, not the underlying data.

### 2.3 Mesh Protocol

**Node discovery:** nodes advertise competence tags on a DHT (distributed hash table) — similar to BitTorrent. No central registry. No domain labels.

**Routing:** consultation requests are matched by competence tag overlap:
- Minimum 2 matching tags required for a node to be eligible
- More tag overlap = higher routing priority
- Data richness (how much relevant data the node has) is a secondary ranking signal
- Available compute and uptime are tertiary signals

**Remote swarm formation:** When a mesh query is published, matching nodes' agents form a temporary remote swarm. Each agent in the remote swarm analyzes the anonymized context independently and returns a perspective. The remote swarm dissolves after the query is answered.

**Reputation:** nodes whose perspectives are rated useful by the requesting node's swarm accumulate reputation score. Low-reputation nodes are queried less. Reputation is per-competence-tag, not global — a node can have high reputation for `renal_function` and low reputation for `hematology`.

**Volume:** a single consultation might query 5–20 nodes. Perspectives are aggregated by the local GeneralistAgent (or equivalent coordinator from the plugin).

### 2.4 Mesh Credit System — BitTorrent-Style Barter

Mesh queries are free — no money changes hands. Instead, the mesh uses a **credit-based barter system** inspired by BitTorrent's tit-for-tat protocol:

```
You serve queries to other nodes  → earn query credits
You send queries to other nodes   → spend query credits
You configure your capacity       → "serve up to N queries/day"
```

**How it works:**
- Each node tracks its own balance with each peer (peer-to-peer, no central ledger)
- Serving a query for another node earns 1 credit with that peer
- Sending a query to another node costs 1 credit with that peer
- New nodes start with 10 free credits to bootstrap (enough for a few initial consultations)
- Free credits are one-time — not replenished. After that, you earn by serving.
- Node identity is tied to a cryptographic keypair generated on first install — creating new nodes to farm free credits requires a fresh installation with empty data, which makes the node useless for serving (no data = no valuable perspectives = no one queries it)
- Nodes that only consume and never serve eventually run out of credits and can't query the mesh — the system self-balances

**Configurable capacity:** Users configure how many mesh queries their node will serve per day. A user with a powerful machine and rich data might serve 100 queries/day and accumulate credits quickly. A user with a laptop might serve 10 queries/day. The more you give, the more you can ask.

**No money, no crypto, no tokens.** The mesh is pure barter. Credits are tracked locally between peers — no blockchain, no central accounting, no financial infrastructure. This is how BitTorrent has scaled to billions of users without any payment system.

**Relationship to the Expert Portal:** The mesh (free, barter-based) and the Expert Portal (paid, company-operated) are completely separate systems. A user with zero money can still use the mesh to query other nodes' agents. The Expert Portal is only needed when the user wants to fund expert micro-questions to fill specific knowledge gaps.

### 2.5 External AI Escalation *(available from Phase 1 for paper extraction; mesh escalation in Phase 2)*

If the user configures an external LLM API key (OpenAI, Anthropic, Google, etc.), the system can escalate to more powerful AI models when the local LLM and mesh perspectives aren't sufficient. This is entirely user-controlled:

```
Reasoning escalation chain:
1. Local LLM (Ollama, free, private)        ← default, handles most queries
2. Mesh consultation (credit-based, free)    ← other nodes' agents weigh in
3. External AI API (user's API key, paid)    ← more powerful model for hard questions
4. Expert Portal (Pro subscription, paid)     ← human expert for knowledge gaps
```

**How it works:**
- User adds an API key in settings (e.g., OpenAI, Anthropic, OpenRouter)
- When an agent's confidence on a reasoning chain falls below a configurable threshold and mesh perspectives don't resolve it, the system can send the anonymized question context to the external API
- The same privacy rules apply — no personal identifiers, only anonymized patterns and knowledge graph context
- The user controls: which API to use, spending limits, which confidence threshold triggers escalation, and whether escalation is automatic or requires confirmation

**Why this matters:** A local qwen3:14b quantized model may struggle with complex multi-system reasoning that GPT-4 or Claude handles well. Rather than compromising on reasoning quality, the user can opt into more powerful models for the hardest questions — at their own cost, under their own API key. The local system remains the default; external AI is a power-user option.

### 2.6 Collective Context Without Sharing Data

**Important distinction:** agents don't "become smarter" from following one person's data — the underlying LLM's reasoning doesn't improve from longitudinal exposure. What improves is **context richness**. A node that has tracked someone's data for 10 years has a deeply populated graph: complete history, every trend, every agent opinion and whether it held up. When that node's agent answers a mesh query matching its competence tags, it draws on that deep longitudinal context — because it actually happened in its graph.

The mesh effect: each node contributes **data-grounded context**, not generic LLM reasoning. A single node's context is one person's data. A thousand nodes' aggregated responses across matching competence tags represent a thousand real longitudinal cases. The LLM is the same everywhere; the graph data is what differentiates nodes.

Node quality varies significantly. A node with 10 years of comprehensive data produces richer perspectives than a node with 6 months of sparse data. The mesh protocol accounts for this via the `data_richness` signal advertised on the DHT.

### 2.7 Mesh Knowledge Sharing — Distributed Paper Curation

Beyond answering queries, the mesh serves as a **distributed knowledge curation network**. When any node discovers and verifies a research paper, it announces the extracted knowledge to the mesh so all interested nodes can benefit.

**This is the primary mechanism for knowledge dissemination across the network.** It transforms the mesh from a Q&A system into a living, community-curated knowledge base.

**How it works:**

1. User A finds a paper (proactively suggested by the system or manually loaded), reviews the LLM-extracted relationships, and confirms them into their graph (see Research Paper Pipeline in Knowledge Foundation section).
2. User A's node publishes a paper announcement to the DHT with: DOI, domain tags, credibility score, extracted edges, and whether the confirmer is a credentialed expert.
3. Other nodes subscribed to matching domain tags see the announcement.
4. Each node decides: auto-ingest (if credibility is high and expert-confirmed), manual review, or ignore.
5. If multiple nodes independently confirm the same edges, the confidence score increases across the network.
6. If a retraction is detected later, the mesh broadcasts a retraction alert and all nodes that ingested are notified.

**The compound effect:** A community of 1000 nephrology-interested users means 1000x the paper discovery rate compared to a single user. Every paper anyone finds enriches everyone's knowledge graph. Over time, this creates a collectively curated knowledge base that no individual, institution, or company could build alone.

**Mesh paper sharing uses the same credit system as mesh queries** — sharing a paper announcement earns credits, just like serving a query response.

See the Research Paper Pipeline section for the full discovery → credibility → extraction → confirmation → sharing workflow.

---

## Expert Portal — Company-Operated Knowledge Validation Service *(Phase 2)*

The Expert Portal is the economic engine of Consilium. It connects users who need knowledge graph improvements with credentialed domain experts who answer micro-questions. The portal is **operated by Consilium as a company** — domain-agnostic, handling routing, payments, quality control, and reporting. The company earns revenue through a transparent service fee on each transaction.

**The company does not differentiate by domain.** It does not market itself as providing health experts, financial experts, or legal experts. It operates a domain-agnostic expert routing platform — like Uber doesn't differentiate between "airport drivers" and "grocery drivers." Experts self-declare their competence tags. The system routes questions to matched experts based on tags and reputation. The company never hires or fires experts — it routes work to them based on quality, like Uber rewards high-rated drivers with more rides.

### How It Works

```
User's local system detects a knowledge gap
    → Question Engine generates a micro-question
    → Question sent to Expert Portal with competence tags + payment authorization
    → Portal routes question to matched, high-rated experts
    → Expert answers on phone (10–30 seconds)
    → Portal validates answer (golden sets, consensus, pattern checks)
    → Answer flows back to update the shared knowledge graph
    → Expert paid from Pro subscription pool

Money flow:
    Pro user pays ≤$20/month subscription (Stripe)
    → User's system generates micro-question from knowledge gap
    → Question sent to Expert Portal
    → Expert answers → validated
    → Expert paid from subscription pool (e.g., $0.10 per answer)
    → Consilium retains service fee for operations
    → Expert payments batched weekly to minimize Stripe transaction fees
```

### Expert Onboarding — The Uber Model

Experts sign up, verify credentials, and start answering. No interview, no hiring process, no employment relationship. The platform routes questions to them based on competence tags and reputation score.

**Onboarding flow:**

```
1. Expert signs up on the Consilium Expert Portal (standard web registration)
   → provides: credential type, license/certification number, jurisdiction
   → self-declares competence tags (e.g., renal_function, eGFR, electrolytes)

2. Credential verification against public registries
   → Wellness: NPPES (NPI lookup), state medical board APIs
   → Finance: FINRA BrokerCheck, CPA license databases
   → Legal: state bar association records
   → Engineering: PE license databases
   → Automated where APIs exist; manual review otherwise
   → Estimated verification time: 24–72 hours

3. Expert starts in "calibration mode"
   → First 50 questions are heavily weighted toward golden sets
   → Establishes baseline accuracy before earning full pay
   → Expert sees this as normal — they don't know which are golden sets

4. Expert account activated at full pay rate
   → Questions routed based on competence tags + reputation
   → License expiry monitoring: re-check every 6 months
   → Credential revoked if license lapses or disciplinary action taken
```

**Competence matching:** The credential encodes the expert's competence areas (mapped to the same tag system agents use). A nephrologist is tagged `[renal_function, eGFR, electrolytes, dialysis]`. A CFA is tagged `[portfolio, asset_allocation, risk_management]`. The portal routes micro-questions to experts whose competence tags match the question's topic tags. This is fully dynamic — no hardcoded domain routing. The portal doesn't know or care what "nephrology" means — it matches tags.

### Expert Reputation — The Uber Rating System

Every expert answer is graded. High-rated experts get more questions and higher pay. Low-rated experts get fewer questions and eventually stop receiving them — the system phases them out naturally, without anyone being "fired."

**How answer quality is measured:**

| Signal | Timing | Weight |
|--------|--------|--------|
| Agreement with other experts on the same question | Immediate | 50% |
| Golden set accuracy (questions with known textbook answers) | Immediate | 30% |
| Downstream knowledge graph improvement (did the updated edge improve agent reasoning?) | Weeks | 20% |

**Golden sets are easy with micro-questions:** "What is the normal range for serum creatinine in adult males?" has a definitive answer. 15–20% of questions are golden sets — the expert never knows which ones.

**Reputation gates question volume and pay multiplier:**

```
reputation_score → routing priority + pay multiplier:
  0.0–0.3: no questions routed (effectively phased out)
  0.3–0.5: 0.5× pay, limited question volume (probation — chance to improve)
  0.5–0.7: 1.0× pay, standard volume
  0.7–0.9: 1.5× pay, priority routing, access to higher-value questions
  0.9–1.0: 2.0× pay, free-text questions unlocked, influence on question generation
```

**Nobody is fired.** Low-reputation experts simply receive fewer and fewer questions — the system naturally routes work to better experts. If their accuracy improves, their reputation recovers and questions resume. Exactly like Uber: low-rated drivers don't get deactivated immediately, they just get fewer ride requests.

### What Experts Actually Do — Micro-Questions, Not Full Diagnoses

**Key design principle:** Experts are never asked to review a full case or provide a diagnosis. Instead, the system asks them **small, targeted questions** — each answerable in 10–30 seconds. Experts are paid per answer at a nominal rate, like paying for tokens. The system uses an LLM to generate questions that will maximally improve the knowledge graph.

**Why micro-questions instead of full case review:**
- A full case review takes 5–10 minutes, costs $10–50, and requires understanding context
- A micro-question takes 10–30 seconds, costs $0.05–0.50, and requires only domain knowledge
- Micro-questions scale: 1000 questions/day across 50 experts = massive knowledge acquisition
- Experts can answer between appointments, in an elevator, on a phone — zero context switching
- No risk of the expert accidentally providing case-specific advice (regulatory/liability win)
- Easier to golden-set test — simple questions have clearer right/wrong answers

### The Question Engine

The heart of the system. An LLM examines the knowledge graph and agent reasoning chains, identifies **where uncertainty lives**, and generates targeted questions to resolve it.

**What runs where:**

| Component | Runs on | Why |
|-----------|---------|-----|
| Question Engine (gap detection + question generation) | User's machine (local LLM) | Needs access to the user's graph and agent reasoning chains — this data never leaves the device |
| Question routing + expert matching | Expert Portal (company servers) | Needs access to the full expert pool, reputation scores, and competence tag index |
| Golden set management + injection | Expert Portal | Golden sets must be secret and centrally managed to prevent gaming |
| Answer validation + consensus computation | Expert Portal | Requires answers from multiple experts across the network |
| Knowledge graph diff generation | Expert Portal | Aggregates validated consensus into signed graph updates |

**How questions are generated:**

```
1. Agent produces an opinion based on user's data
   → e.g. "eGFR declining at 4pts/year — pattern worth discussing with your doctor"

2. The Question Engine LLM analyzes the agent's reasoning chain:
   → What edges in the knowledge graph did the agent traverse?
   → Which edges have low confidence or missing evidence?
   → Where did the agent make an assumption vs. follow established knowledge?
   → Where do different agents in the consilium disagree?

3. The LLM generates 2–5 micro-questions targeting the weakest links:

   Question types:

   MULTIPLE CHOICE (most common, fastest to answer):
   "For a diabetic patient with eGFR 52 on metformin 1000mg, what is
    the most commonly recommended next step?
    a) Continue current dose
    b) Reduce to 500mg
    c) Switch to alternative
    d) Depends on other factors (which?)"

   THRESHOLD (numeric, calibrates decision boundaries):
   "At what eGFR would you typically refer a diabetic patient to nephrology?
    [slider: 30 ─── 45 ─── 60]"

   YES/NO WITH CONFIDENCE (fast, binary signal):
   "Does concurrent anemia increase urgency of nephrology referral
    in CKD stage 3a?
    ○ Yes  ○ No  ○ Uncertain
    Confidence: [Low ─── Medium ─── High]"

   RANKING (prioritization):
   "Rank the next tests for declining eGFR + anemia in a diabetic:
    [ ] Urine albumin/creatinine ratio
    [ ] Iron studies
    [ ] PTH level
    [ ] Reticulocyte count
    [ ] Vitamin B12"

   FREE TEXT (rare, high-value, higher pay):
   "What diagnosis would you consider if eGFR is declining at 4pts/year
    but urine albumin is normal? (one sentence)"
```

**Question targeting — active learning for the knowledge graph:**

The Question Engine doesn't generate random trivia. It specifically targets **edges in the knowledge graph that would change agent behavior if updated**. This is active learning — the system identifies what it doesn't know and asks the most informative question to fill the gap.

```
Example: improving the knowledge graph

Current state:
  [eGFR < 45] ──REQUIRES──► [Metformin dose review]
  confidence_tier: inferred
  evidence_count: 12

Question Engine identifies:
  - This edge drives a key agent observation
  - Confidence is "inferred" (not established)
  - The threshold (45) is uncertain — some guidelines say 30, others say 45
  - If the threshold is wrong, agent reasoning is miscalibrated

Generated question:
  "At what eGFR would you begin reducing metformin dose?
   [slider: 20 ─── 30 ─── 45 ─── 60]"
   Sent to: 20 experts tagged [renal_function, endocrinology]

Results: median answer = 30, IQR = 25–45
  → Edge updated: [eGFR < 30] ──REQUIRES──► [Metformin discontinuation]
                  [eGFR 30–45] ──REQUIRES──► [Metformin dose reduction]
  → confidence_tier upgraded: inferred → established
  → evidence_count: 40 (expert responses)
  → All agents using this edge now have better calibration
```

### How Expert Answers Improve the Knowledge Graph

```
Question Engine generates questions from agent reasoning gaps
    → Questions routed to 3–10 matched experts via Expert Portal
    → Each question sent to experts with matching competence tags
    → Experts answer on phone/web (10–30 seconds each)
    → Portal collects answers, computes consensus:
        - Multiple choice: majority vote + agreement %
        - Threshold: median + IQR
        - Yes/No: proportion + confidence-weighted average
        - Ranking: Borda count or similar aggregation
    → Consensus answers fed back into knowledge graph:
        - Update edge confidence tiers
        - Adjust decision thresholds
        - Add new edges if expert answers reveal missing relationships
    → Updated knowledge graph distributed to all nodes (see below)
```

### How Knowledge Graph Updates Reach All Nodes

When expert answers improve the knowledge graph, those improvements must flow back to every user's local instance. This is handled through the **plugin update mechanism**:

```
Expert consensus establishes a new edge or updates confidence
    → Expert Portal publishes the update as a signed diff:
       { edge: "[eGFR < 30] ──REQUIRES──► [Metformin discontinuation]",
         confidence: "established", evidence_count: 40, timestamp: ... }
    → Diff is committed to the domain plugin's GitHub repo
       (e.g., consilium-wellness/knowledge_graph/updates/)
    → Each user's local instance periodically checks for plugin updates
       (configurable: hourly, daily, or manual)
    → User's system applies the diff to their local Neo4j
    → Agents immediately reason against the improved graph

Update format:
    - Signed JSON diffs (not full graph dumps)
    - Each diff is small (one edge update, ~1KB)
    - Append-only log — updates never delete existing edges, only add or re-tier
    - Diffs are cryptographically signed by the Expert Portal
      to prevent tampering in the GitHub repo
    - User can review pending updates before applying (opt-in)
```

**Why GitHub repos:** Simple, auditable, forkable. Anyone can inspect the update history. If the Expert Portal disappears, the last published state of the knowledge graph remains in the repo. No proprietary distribution channel.

**The key insight:** Experts don't score agents directly. They answer knowledge questions. The system uses those answers to update the knowledge graph, and then agents reason against the improved graph automatically. This separates the expert's contribution (domain knowledge) from the agent improvement mechanism (automated, deterministic).

### Expert Micro-Payments — Knowledge as Tokens

Experts are paid per answer, not per case. The payment model mirrors LLM token pricing — small, frequent, zero-friction:

```
Question type         Estimated time    Pay per answer
─────────────────────────────────────────────────────
Multiple choice       10 sec            $0.05–0.10
Yes/No + confidence   10 sec            $0.05–0.10
Threshold (slider)    15 sec            $0.10–0.20
Ranking               20 sec            $0.15–0.25
Free text             30–60 sec         $0.25–0.50
```

**What this means for experts:**
- An expert answering questions during downtime: 100 questions/day × $0.10 avg = $10/day
- A focused 30-min session: 60–100 questions = $6–10/session
- Not life-changing money, but **zero-friction** — no scheduling, no context, no liability
- For retired professionals: meaningful engagement with their field, supplemental income, and the knowledge that their expertise is helping people navigate complex decisions
- For active professionals: CME credits (wellness domain) + shaping the knowledge graphs their future patients/clients will benefit from

**Funded by Pro subscriptions:**
- Pro users pay ≤$20/month — expert micro-questions are included, no per-question billing
- Consilium allocates subscription revenue to expert payouts + operations
- At scale: 1,000 Pro users × $20 = $20K/month; ~$12K to experts, ~$8K to operations
- Free users still get the full platform — they benefit from knowledge graph improvements funded by Pro users

### Anti-Gaming for Micro-Questions

| Attack | Defense |
|--------|---------|
| Expert answers randomly to farm volume | Golden sets catch this immediately; random answers on multiple-choice have ~25% accuracy, golden set threshold is 70% |
| Expert always picks the same answer (e.g., always "a") | Pattern detection; questions are randomized in option order |
| Bot answers questions (no real expert) | Golden sets + CAPTCHA on suspicious patterns + credential re-verification |
| Expert creates multiple accounts | One credential per license number; duplicate detection across registries |
| Experts collude on answers | Questions randomized across experts; answer order shuffled; same question sent to experts on different schedules |
| User generates excessive questions to consume quota | Rate limiting per user; Question Engine prioritizes high-information-gain questions |
| Portal operator inflates costs | Service fee is published and auditable; transaction log is append-only and public (amounts + question IDs, no content) |

### User and Expert Reporting

**User dashboard (Pro tier):**

```
Your Consilium Pro
├── Plan: Pro ($20/month)
├── This month: 23 expert questions answered
├── Knowledge gaps filled: 12 (renal function: 5, metabolic: 4, cardiology: 3)
├── Mesh queries: 47 sent, 89 served (credit balance: +42)
└── Expert question log: [expandable, exportable]
```

**Expert dashboard:**

```
Your Expert Profile
├── Earnings this month: $47.50 (312 questions answered)
├── Reputation score: 94/100
├── Accuracy on calibration questions: 96%
├── Peer agreement rate: 91%
├── Payout schedule: Weekly via Stripe
└── Answer history: [expandable, exportable]
```

**Company transparency report (published monthly):**

```
Consilium Expert Portal — March 2027
├── Total questions routed: 31,400
├── Total expert payouts: $3,140
├── Total service fees collected: $471
├── Active experts: 127 (across all domains)
├── Average expert reputation: 0.74
├── Average response time: 14 seconds
└── Golden set accuracy (network-wide): 89%
```

### Incentive Flow Summary

```
User loads data → agent reasons against knowledge graph
    → Question Engine identifies uncertainty in agent's reasoning
    → Generates 2–5 targeted micro-questions
    → Questions sent to Expert Portal with payment authorization
    → Portal routes to 3–10 matched experts (by competence tags + reputation)
    → Experts answer (10–30 sec each, $0.05–0.50 per answer)
    → Portal validates answers (golden sets, consensus, pattern checks)
    → Consensus updates shared knowledge graph
    → Updated graph distributed to all nodes via plugin updates
    → Expert paid from Pro subscription pool; service fee retained
```

### Why Experts Will Participate

- **Zero friction**: answer questions on your phone while waiting for coffee. 10 seconds per question. No case review, no context loading, no documentation.
- **Paid per answer**: like a domain trivia game that pays. $0.05–0.50 per answer, accumulating passively throughout the day.
- **Retired professionals**: stay active, contribute your lifetime of expertise, earn supplemental income. Self-worth matters — retirement doesn't mean your knowledge stops being valuable.
- **CME credits**: for wellness domain — partnership with CME accreditors. Answering clinical micro-questions counts toward continuing education hours.
- **No liability**: experts answer general domain knowledge questions, not case-specific queries. No professional-client relationship. No malpractice exposure.
- **Shape the knowledge**: high-reputation experts directly influence the knowledge graph that millions of agents reason against. For professionals who care about AI accuracy in their field, this is meaningful work.
- **Research value**: aggregate question-answer patterns across domains are publishable. Academic professionals can co-author papers on knowledge consensus patterns.

### Expert Cold-Start Strategy

Getting the first 50 experts is the hardest problem. Concrete strategies:

| Strategy | Target | How |
|----------|--------|-----|
| CME credits | Licensed physicians | Partner with a CME accreditor (ACCME). Answering clinical knowledge questions counts toward continuing education. Doctors currently pay for CME — Consilium provides it as a benefit. |
| Retired professionals | Retirees across all domains | Position as meaningful post-retirement engagement. "Your 30 years of expertise shouldn't retire when you do." Zero-friction, phone-based, own-schedule. |
| Academic partnerships | Residents, fellows, students | Offer as a teaching/training tool. Answer questions and learn from the feedback loop. Partner initially with 2–3 academic institutions. |
| Professional society outreach | Board-certified specialists | Present at society meetings. Frame as "shape the AI knowledge graph in your specialty." |
| Research collaborators | Academic professionals | Offer co-authorship on publications analyzing expert consensus patterns. Academic currency. |

**Target: 50 credentialed experts across 3+ domains before launch. 3-month closed beta.**

### Experts as Users — Personal Knowledge Assistant and Cross-Specialty Wisdom

**No one knows everything.** A cardiologist doesn't know nephrology. A tax attorney doesn't know immigration law. A structural engineer doesn't know electrical systems. Domain experts face the same problem as everyone else — complex decisions that cross specialty boundaries — but with higher stakes and less tolerance for superficial answers.

**Consilium is a personal knowledge assistant for domain experts.** The primary value for experts is not earning money through the Expert Portal — it's using the system to **capture, organize, and build on their own professional knowledge over their entire career.**

| Expert user | How they use Consilium | What they gain |
|-------------|----------------------|----------------|
| Cardiologist | Feeds research papers, clinical observations over years | A personal knowledge graph crystallizing decades of cardiology expertise + cross-specialty mesh access for nephrology, endocrinology |
| Tax attorney | Loads case law, tax code changes, client patterns | A living knowledge base of tax reasoning + immigration/corporate law context from the mesh |
| Structural engineer | Ingests building codes, failure analyses, specifications | Accumulated engineering knowledge + electrical/mechanical insights from other specialists' nodes |
| Retired internist | Continues feeding new research papers post-retirement | Stays current on oncology, immunology developments through mesh + contributes lifetime of clinical reasoning |
| Financial advisor | Organizes client data in per-client folders, loads market research | Cross-domain insight (legal, tax) for complex client situations |

**How it works for expert users:**

1. **They capture their professional knowledge over time** — every research paper they read, every clinical pattern they observe, every case outcome they note gets processed into their knowledge graph. Over years, this becomes an invaluable personal knowledge base — a crystallized version of their professional expertise.
2. **Per-person folders organize client/patient data** — a doctor creates folders per patient, a financial advisor per client. Personal data stays isolated; the knowledge graph grows from patterns across all cases.
3. **Their knowledge graph is dramatically richer** — enriched by research papers, professional observations, and years of domain-specific data. Agents working against this deep graph produce more sophisticated analysis.
4. **The mesh is especially powerful for them** — when their consilium queries the mesh, it can reach nodes run by experts in other specialties. An expert node's mesh responses are higher quality because its knowledge graph is deeper.
5. **Cross-specialty consultation is the core value** — a cardiologist tapping nephrology insight through the mesh, a tax attorney getting immigration law context. This is what professionals need and can't get from any other tool.
6. **They make the mesh better for everyone** — expert nodes are the most valuable mesh participants. More experts using the system = dramatically richer mesh for all users.

**This creates a virtuous cycle:** experts join because Consilium is the best personal knowledge assistant for their work → their rich knowledge graphs make the mesh better for everyone → more experts join → the mesh becomes even more valuable. The system doesn't just serve experts — it gets better because experts use it.

**Expert Portal participation is optional and secondary.** Experts can also earn income by answering micro-questions in their specialty through the Expert Portal — but this is a bonus, not the primary value proposition. The primary value is having a personal knowledge system that grows with your career and connects you to collective wisdom across specialties.

---

## Phase 3 — Privacy Layer & Scale *(Future)*

*Designed from the start to support this — not required for local-only operation.*

- HMAC-based pseudonymous document IDs (different ID per document, linkable only by patient)
- TEE (Trusted Execution Environment) for mesh nodes — opinions computed in secure enclaves
- Differential privacy on query patterns — prevent inference from query frequency
- Zero-knowledge proofs for reputation scoring — prove a node gave good opinions without revealing what the opinion was about
- k-anonymity enforcement on mesh queries (see re-identification risk in Phase 3)

---

## Legal Strategy — Why Consilium Is Not a Regulated Product

**Consilium is a general-purpose knowledge organization and educational entertainment platform.** It is not a medical device, financial advisory service, legal practice tool, or professional service of any kind. This is not a disclaimer bolted onto a medical product — it is the fundamental architecture of the system.

### Dual Framing — Same System, Two Audiences

Consilium requires different framing for different audiences. The system is the same — the emphasis changes.

| Audience | Framing | Emphasis |
|----------|---------|----------|
| **Regulators / Legal** | "Educational entertainment and knowledge exploration tool" | Not a medical device. Not professional advice. No fiduciary relationship. Generic platform + user-installed community plugins. |
| **Expert recruitment** | "Professional knowledge management system" | Career-long knowledge capture. Cross-specialty consultation via mesh. Client data organization in per-person folders. Research paper ingestion into personal knowledge graph. The best knowledge tool you've ever had — and you can earn income on the side. |
| **Clinicians reviewing patient data** | "Patient-organized data summary" | Clinician view: raw findings, standard abbreviations, ICD-10 codes, guideline citations, no educational framing. Like a patient bringing an organized spreadsheet — not a "product" that tells doctors what to do. |
| **General users** | "Personal knowledge organization with AI agents" | Organize your data, see connections, prepare for professional appointments. Your data stays on your device. |

**Why this isn't contradictory:** "Educational entertainment" describes the legal classification (what the system IS NOT — not a professional service). "Professional knowledge management" describes the user experience (what the system IS — a powerful tool for organizing and connecting knowledge). A spreadsheet used by a doctor to organize patient data is both "general-purpose productivity software" (legally) and "a clinical tool" (practically). Same principle.

**The key rule:** All external communications, legal documents, and regulatory filings use the "educational entertainment" framing. Marketing to experts, product documentation, and feature descriptions use the "professional knowledge management" framing. Never mix them in the same document.

### Why This Is Legally Defensible

**1. The system never provides professional advice.**

Consilium agents do not diagnose, treat, prescribe, recommend, or advise. They organize user-provided data into a knowledge graph, identify patterns and connections, and present educational information. Every output is framed as:

> *"Here are patterns in your data that you may want to discuss with your doctor/advisor/lawyer."*

Not: *"You should do X."*

This is the same legal framing as:
- **WebMD / Mayo Clinic symptom checker**: educational health information, not medical advice
- **TurboTax**: tax preparation software, not a CPA practice
- **LegalZoom**: document preparation, not legal advice
- **Wolfram Alpha**: computational knowledge, not professional consulting
- **Fitbit / Apple Health**: wellness data display, not medical diagnosis

**2. The platform ships with zero domain-specific code.**

FDA regulates Software as a Medical Device (SaMD). Consilium's core platform contains no health-specific logic — no medical knowledge graph, no clinical agents, no lab result parsers, no health-related code of any kind. It is a graph database + agent framework + mesh protocol. Like VS Code without extensions.

All domain intelligence lives in **external open-source GitHub repositories** (e.g., MDAgents, Hetionet) that are pulled only when the user loads domain-relevant data and explicitly confirms installation. The platform operator does not author, control, endorse, or distribute these plugins. They are community-maintained open-source software.

**There is no medical device to classify.** The platform is a generic tool. The health-related code is open-source community software installed by the user. This is the same model as Docker (doesn't regulate what containers run), VS Code (doesn't regulate extensions), or WordPress (doesn't regulate plugins).

**3. Experts answer general knowledge questions, not case-specific queries.**

Domain experts on the micro-question network answer **general domain knowledge questions**, not patient/client-specific cases:

| What experts see | What experts DON'T see |
|-----------------|----------------------|
| "At what eGFR level would nephrology referral typically be considered?" | Any user's actual eGFR, name, or data |
| "What is the standard monitoring frequency for metformin users?" | Whether any specific user takes metformin |
| "Rank these lab tests by diagnostic yield for anemia workup" | Any user's lab results |

Experts contribute to a **general knowledge graph** — like writing a textbook entry or answering a medical school exam question. No expert-client relationship. No case-specific opinion. No malpractice exposure.

**4. All data stays on the user's device.**

HIPAA applies to "covered entities" (healthcare providers, insurers) and their "business associates." Consilium is neither. It is personal software running on a user's own device. The user's data never leaves their machine. The system is not a healthcare provider, does not bill insurance, and does not create medical records. It is a personal data organization tool — like a spreadsheet or a personal journal.

Mesh queries transmit anonymized, de-identified pattern descriptions — not health records. Under HIPAA Safe Harbor, information with all 18 identifiers removed is not Protected Health Information (PHI).

**5. Experts are not providing professional services.**

Key legal distinctions:

| Consilium Expert | Traditional Professional |
|-----------------|------------------------|
| Answers general knowledge questions | Provides client-specific advice |
| Contributes to a shared knowledge graph | Creates a fiduciary relationship |
| Never sees user data | Reviews case materials |
| Paid per micro-answer ($0.05–0.50) | Paid per consultation ($200–500) |
| No professional-client relationship | Duty of care exists |
| Like being a textbook contributor or exam question writer | Like being a treating physician or retained attorney |

### Legal Safeguards (Implemented in Product)

**Terms of Service:**
- User explicitly acknowledges: "Consilium is an educational entertainment and knowledge exploration tool. It does not provide medical, financial, legal, or professional advice of any kind."
- User agrees: "I will consult qualified professionals before making any decisions based on information from Consilium."
- Liability waiver: "Consilium, its operators, and expert contributors bear no liability for decisions made based on information displayed in the system."

**UI/UX enforcement (every output includes these elements):**
- Every agent output prefixed with: "For educational exploration only — discuss with your [doctor/advisor/lawyer]"
- No imperative language ("you should", "you must", "we recommend") — only observational language ("your data shows", "research suggests", "you may want to discuss")
- "Prepare for your appointment" feature generates a **question list**, not a recommendation list: "Questions to ask your doctor" not "What your doctor should do"
- Domain experts are labeled "knowledge contributors" not "consultants" or "advisors"

**Expert Terms:**
- Experts acknowledge: "I am contributing general domain knowledge to an educational knowledge graph. I am not providing professional services, case-specific advice, or establishing any professional relationship with end users."
- Experts never see user identity or user-specific data
- Expert contributions are anonymous and aggregated — no single expert's answer is attributable in the user-facing product

### Precedent Analysis

| Product | What it does | Regulatory status | Why it works |
|---------|-------------|-------------------|-------------|
| **VS Code** | Generic editor + community extensions (Python, medical, etc.) | Not regulated | Platform doesn't control extensions |
| **Docker Hub** | Generic container registry — anyone can publish containers | Not regulated | Platform doesn't regulate what containers do |
| **WordPress** | Generic CMS + 60,000 plugins (including health plugins) | Not regulated | Platform doesn't control plugin content |
| **npm / PyPI** | Package registries hosting millions of packages | Not regulated | Registry doesn't endorse or control packages |
| **WebMD** | Displays health information, symptom checkers | Not FDA-regulated | Educational information, not diagnosis |
| **Apple Health** | Displays health data from wearables | Not FDA-regulated (data display) | Data organization, not clinical interpretation |
| **TurboTax** | Prepares tax returns using user data | Not SEC/IRS regulated as financial advice | Software tool, not a CPA practice |
| **Consilium** | Generic knowledge platform + user-installed domain plugins | Not regulated (target) | Platform ships zero domain code; all domain intelligence is user-installed open-source community software |

### Remaining Legal Tasks

1. **Engage healthcare regulatory attorney** before launch to review the framing, ToS, and UI language. Estimated cost: $10K–25K for initial review.
2. **File for formal FDA guidance** (optional, defensive): submit a Pre-Submission to FDA describing the system and requesting written confirmation that it does not meet SaMD criteria. This creates a paper trail of good-faith regulatory engagement.
3. **Review international regulatory landscape** for wellness/education tools (EU, UK, Canada) — generally lighter touch for non-diagnostic tools.
4. **Insurance**: obtain errors & omissions (E&O) insurance for the company operating the Expert Portal. Not because liability is expected, but as a prudent measure.

---

## Funding Model

The Consilium platform (mesh software, agent framework, domain plugins) is free and open-source. The Expert Portal is a company-operated service that earns revenue through a transparent service fee.

**Two tiers:**

| Tier | What you get | Price |
|------|-------------|-------|
| **Free** | Local consilium + mesh consultations + base knowledge graphs from plugins | $0 |
| **Pro** | Everything in Free + expert-validated knowledge (micro-questions routed to credentialed human experts) | ≤$20/month |

### Why $20/Month or Less

The price ceiling is set by what people already pay for AI services:

| Service | Price | What you get |
|---------|-------|-------------|
| ChatGPT Plus | $20/month | AI chat — no your data, no memory, no knowledge graph, no human experts |
| Claude Pro | $20/month | Same — stateless, cloud-only, no domain expertise |
| Gemini Advanced | $20/month | Same |
| **Consilium Pro** | **≤$20/month** | AI agents on **your** data + persistent memory + knowledge graph + mesh consultation + **human expert** knowledge validation |

Consilium does strictly more than any AI chat subscription — and costs the same or less. This is possible because:

1. **The heavy compute happens on the user's machine.** ChatGPT runs massive GPU clusters for every inference. Consilium's local LLM runs on the user's hardware. The company only operates a lightweight routing service (Expert Portal), not an AI inference fleet.
2. **Knowledge graph improvements are shared.** When one Pro user funds a question about eGFR thresholds, the improved edge benefits every user — free and paid. The per-user cost of knowledge improvement decreases as the network grows.
3. **Expert micro-questions are cheap.** At $0.10 average per question, $20/month funds ~170 expert answers (after service fee). Most users' systems won't generate that many — knowledge gaps are filled once and stay filled.

**Pro question budget:** Each Pro subscriber gets up to 200 expert questions/month. The Question Engine prioritizes questions by **information gain** — it asks the questions most likely to improve the knowledge graph first. In practice, most users generate far fewer than 200 questions because:
- Knowledge gaps are filled once and stay filled (the graph improves permanently)
- Many gaps are filled by other Pro users' questions before yours are needed
- The Question Engine deduplicates — if 50 users trigger the same knowledge gap, one question is generated, not 50

If a user's system generates more questions than their monthly budget, excess questions are queued for the next month. The budget exists to make costs predictable, not to restrict value.

**Revenue model:** The Pro subscription covers expert micro-payments + service fee + portal operations. At scale:
- 1,000 Pro subscribers × $20/month = $20K/month revenue
- Expert payouts: ~$12K (assuming 60% goes to experts)
- Service fee + operations: ~$8K
- As the knowledge graph matures, fewer new questions are needed per user → margins improve

**Users on the free tier** still get a genuinely useful product — local agents, mesh consultations, base knowledge graphs. They benefit from knowledge improvements funded by Pro users. The free tier is not crippled — it's the full platform minus expert escalation.

**Why retired experts are uniquely valuable:** A retired physician, attorney, or engineer has decades of pattern recognition and judgment. Micro-questions let them contribute that expertise in 10-second increments from their phone — no commute, no scheduling, no liability. It's meaningful work that keeps their knowledge active and benefits people. The income is supplemental; the sense of purpose is the real draw.

---

## Tech Stack

| Component | Technology | Why |
|-----------|-----------|-----|
| Knowledge graph | Neo4j (local, Community Edition) | Mature graph DB, excellent traversal, supports multiple domain schemas |
| Domain knowledge graphs | Installed by plugins. E.g., wellness: Hetionet (~47K nodes) + Uberon/FMA (OWL→Cypher) | Pre-loaded domain knowledge, confidence-tiered. Note: SPOKE requires UCSF access — evaluate feasibility. Combined graph can be large; may need lazy-loading for local deployments. |
| Local LLM | Ollama + qwen3:14b (Q4_K_M quantization) | Runs on MacBook with 24GB RAM in quantized form. Full precision requires 32GB+. Performance impact of quantization on clinical reasoning needs benchmarking. |
| External LLM (optional) | OpenAI / Anthropic / Google / OpenRouter via user's API key | Escalation for hard reasoning tasks; user-controlled, user-funded |
| Specialist agents | Python + LangGraph or custom (MiroFish agent framework) | Stateful agent orchestration, extends MiroFish/OASIS multi-agent architecture |
| Agent memory | Written back to Neo4j | Persistent, queryable, part of the graph |
| Data ingestion | Python (PyMuPDF, HL7, Apple Health parser) | PDF + standard medical formats |
| Wearable sync | Oura API, Apple HealthKit, FHIR | Official APIs |
| Mesh protocol | libp2p (same as IPFS) | Mature P2P networking, DHT built-in |
| Expert credentialing | Domain-specific credential verification (NPPES for wellness, FINRA for finance, state bars for legal) | Verified credentials via public registries |
| Question Engine | LLM analyzing agent reasoning chains → generates targeted micro-questions | Active learning for knowledge graph; identifies and fills knowledge gaps |
| Expert Portal | React PWA (mobile-first), company-operated | Micro-question routing, Stripe Connect escrow, reputation system |
| User frontend | React + local web server | Dashboard on localhost |
| Privacy (Phase 4) | SGX / TDX TEE, ZK-SNARKs, k-anonymity | Industry standard for confidential compute + re-identification prevention |

---

## Hardware & Deployment Strategy

### Phase 0 Target: MacBook (developer + early adopter)

The initial deployment target is a MacBook (Apple Silicon, 16GB+ RAM). All components — Neo4j, Ollama, the agent framework, and the dashboard — run locally via Docker Compose behind a native macOS app.

**Minimum specs:**
- Apple Silicon Mac (M1 or later)
- 16 GB RAM (24 GB+ recommended for qwen3:14b quantized)
- 50 GB free disk (Neo4j + body knowledge graph + model weights)
- macOS 13+

**User experience target:** The user downloads a `.dmg`, installs the app, and sees a dashboard. They never interact with Docker, Neo4j, Ollama, or a terminal. The app handles all infrastructure invisibly — similar to how the Docker Desktop app hides containers behind a GUI, or how Obsidian hides its data store behind a note-taking UI.

### Phase 3 Target: Consilium Appliance (DIY or pre-assembled)

A pre-configured hardware appliance that plugs into the home network. Open-source hardware design — anyone can build one from the published BOM, or community members may offer pre-assembled units. The user connects it to power and Wi-Fi, opens a web dashboard on their phone or laptop, and starts adding data.

```
Consilium Home Unit
├── Hardware: ARM SBC (Raspberry Pi 5 / Orange Pi 5+) or Intel NUC
│   - 8 GB+ RAM, 256 GB SSD, Wi-Fi + Ethernet
│   - Estimated BOM cost: $150–250 (community can self-build or buy pre-assembled)
│
├── Pre-installed software:
│   - Neo4j Community Edition (knowledge graph database)
│   - Ollama + quantized medical LLM (pre-downloaded)
│   - Agent framework + all specialist agents
│   - Consilium dashboard (web UI on local network)
│   - Mesh gateway (P2P query serving, configurable capacity)
│   - Auto-update service (pulls updates from Consilium repo)
│
├── User experience:
│   - Plug in, connect to Wi-Fi (via phone app or display)
│   - Open consilium.local in any browser on home network
│   - Link medical accounts (Apple Health, Oura, patient portals)
│   - Upload lab PDFs (drag-and-drop or phone camera)
│   - View consilium dashboard
│   - No terminal, no config, no Docker, no accounts to create
│
└── Privacy:
    - All data stays on the box in the user's home
    - Encrypted SSD — if stolen, data is inaccessible without passcode
    - Mesh queries go through the box's network connection (anonymized)
    - User can physically unplug the box to go fully offline
```

**Why an appliance, not just an app:**
- Not everyone has a MacBook with 24GB RAM
- A dedicated device can run 24/7 (proactive monitoring, mesh queries, background agent checks) without draining a laptop battery
- Physical device in the home reinforces the "your data stays with you" message
- Simpler support model — known hardware, known OS, known software versions
- Potential for dedicated medical-grade hardware in later versions (TPM for key storage, cellular backup for mesh connectivity)

### Data Input — The User's Actual Workflow

The user's primary interaction is **adding links and uploading documents**. The system does the rest.

```
How a user adds data:

1. LINK a health account
   → "Connect Apple Health" → authorize in Apple Health app → done
   → "Connect Oura Ring" → OAuth flow → done
   → "Connect patient portal" → enter portal URL + credentials
     → system uses FHIR/Smart-on-FHIR to pull records automatically
     → supported: Epic MyChart, Cerner, Allscripts (cover ~80% of US patients)

2. UPLOAD a document
   → Drag-and-drop a lab PDF into the dashboard
   → Or: take a photo of a lab printout with phone → upload
   → AI extracts structured data → shows preview → user confirms
   → "We found: Creatinine 1.8 mg/dL, eGFR 52, HbA1c 7.2% — correct?"

3. DOWNLOAD information from the consilium
   → Dashboard shows agent opinions, trends, alerts
   → "Prepare for doctor visit" → generates a 1-page summary PDF
     with flagged items, trends, and questions to ask
   → Export full health history as FHIR JSON or PDF at any time
```

The user never sees a graph, a database query, or an agent prompt. They see: "Your wellness agent noticed your eGFR has been trending down. Here are some questions you might want to explore with your doctor."

---

## Implementation Phases

| Phase | What | Outcome |
|-------|------|---------|
| **1** | Knowledge graph schema + Neo4j setup + manual data entry | Graph populated with your data |
| **2** | Domain knowledge graph import (e.g., Hetionet/SPOKE for wellness) + confidence tiers | Agents have domain context to reason against |
| **3** | Data ingestors — Apple Health, Oura, PDF labs | Graph auto-updated from real sources |
| **4** | Local specialist agents — query graph, write opinions | Working local consilium |
| **5** | Consilium workflow — multi-round debate, synthesis | Agents consult each other on your data |
| **6** | Proactive monitoring — scheduled agent checks, alerts | Agents watch your data continuously |
| **7** | Mesh protocol — P2P consultation requests | Your node asks others for opinions |
| **8** | Expert Portal MVP — credential verification, question routing, Stripe Connect, reputation system, mobile PWA | Experts can sign up, answer micro-questions on phone, and get paid |
| **9** | Question Engine — LLM identifies knowledge gaps, generates targeted micro-questions | System automatically generates the right questions from agent reasoning |
| **10** | Knowledge graph feedback loop — expert answers update edge confidence, agents re-reason | Expert answers improve agent reasoning automatically |
| **11** | macOS native app — one-click install, no terminal | Non-technical MacBook users can onboard |
| **12** | Consilium Appliance — open-source hardware design, build guides, community assembly | Plug-and-play device anyone can build |
| **13** | Privacy layer — TEE, pseudonymous IDs, ZK proofs | Production-grade privacy |

---

## What This Looks Like for the User

You open the app and see:

```
Your Consilium — Last updated 2 hours ago

  FOR EDUCATIONAL EXPLORATION ONLY — Discuss with qualified professionals

Active agent swarms: Renal · Metabolic · Hematology
Installed plugins: Wellness & Body Literacy · Personal Finance

━━━ WELLNESS ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🟡 EXPLORE: Renal Function Patterns
   "Your eGFR readings show a downward trend (55 → 52 since October).
   A swarm of 3 local agents (renal, metabolic, hematology) and
   8 mesh nodes with matching competence tags explored this pattern:
   • What does an eGFR trend like this typically indicate?
   • Is a urine albumin test commonly ordered in similar situations?
   • At what point do people typically discuss nephrology referrals?"

🟢 STABLE: Metabolic Patterns
   "HbA1c reading of 7.2% — within commonly cited target ranges.
   The knowledge graph connects kidney trends with metformin
   management — a topic for your next visit."

━━━ FINANCE ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🟢 STABLE: Portfolio Allocation
   "Sector exposure is diversified. No concentration alerts."

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📋 Explore a topic with your consilium:
   > "What connections exist between fatigue and kidney function?"

  ℹ️ Consilium helps you explore and learn. It does not provide
     professional advice. Always consult qualified professionals.
```

---

## Key Difference From Everything Else

| System | Data location | Memory | Multi-agent | Privacy | Expert-validated knowledge | Incentive-aligned |
|--------|--------------|--------|------------|---------|--------------------------|-------------------|
| ChatGPT / Gemini | Cloud | None | No | None | No | No |
| Apple Health / Fitbit | Apple's cloud | Limited | No | Partial | No | No |
| WebMD / Mayo Clinic | N/A (static) | None | No | N/A | Editorial only | No |
| Your professional (doctor, CPA, lawyer) | Their systems | Yes (fragmented) | Sometimes | Regulated | Yes (1 person) | Fee-for-service |
| **Consilium** | **Your machine** | **Complete, lifelong** | **Yes, permanent** | **By design** | **Yes, expert micro-question consensus** | **Free + Pro ≤$20/mo** |

---

## Open Questions and Risks

Issues that need resolution before launch. Honest assessment of what's unsolved.

### Technical Risks

| Risk | Severity | Mitigation |
|------|----------|-----------|
| **LLM reasoning quality**: Current open-source models (qwen3:14b quantized) may not match GPT-4-class reasoning. If agent opinions are consistently poor, experts will flag low quality and the improvement loop breaks. | High | Benchmark agent quality before launch. If quality is below threshold, users can escalate to external AI APIs for hard reasoning while keeping data local. The local graph provides context the LLM wouldn't otherwise have — this may compensate for model size. |
| **Body knowledge graph size**: Combined Hetionet + Uberon + FMA could be 100M+ edges. Running this on a consumer laptop alongside personal data may be impractical. | Medium | Lazy-load strategy: only import subgraphs relevant to the patient's conditions. A patient with CKD + diabetes loads renal + endocrine subgraphs, not the entire human anatomy. |
| **Neo4j Community Edition limits**: No clustering, no role-based access control. Adequate for single-user local deployment. | Low | Local deployment uses Community Edition. Expert Portal backend uses a separate database for aggregation. |

### Adoption Risks

| Risk | Severity | Mitigation |
|------|----------|-----------|
| **Expert cold start**: Without experts answering micro-questions, the knowledge graph doesn't improve. Without a good knowledge graph, agents are mediocre. Without good agents, users don't get value. | Critical | CME credit partnership for wellness domain is the best lever. Retired professionals as a target segment. Micro-questions are much lower friction than case reviews — target 50 experts across 3 domains before launch. Run a closed beta with 20 users + 10 experts for 3 months. |
| **User data entry burden**: Most people don't have their data in digital form. Manual entry is tedious and error-prone. | High | Prioritize automatic integrations (Apple Health, wearables, FHIR patient portals). For documents, LLM-powered PDF/photo parsing. Make data entry the smallest possible barrier. |
| **"AI doctor/lawyer/advisor" perception**: Media may frame Consilium as "replacing professionals with AI" regardless of the actual design. | High | Frame consistently as "knowledge exploration and educational entertainment." Never use words like diagnose, advise, recommend. The domain-agnostic framing is the strongest defense — it's clearly a general knowledge tool, not a professional service. |

### Regulatory Risks

| Risk | Severity | Mitigation |
|------|----------|-----------|
| **FDA attempts SaMD classification despite general-purpose framing**: unlikely but possible if wellness domain usage dominates and outputs look clinical. | Medium | Domain-agnostic architecture is the primary defense. UI language enforcement (no imperatives, no recommendations). Optional: proactive FDA Pre-Submission for written confirmation. Engage healthcare regulatory attorney ($10–25K). |
| **Expert licensing concerns**: Experts answering general knowledge questions across borders could raise cross-jurisdictional licensing questions. | Low | Experts answer general knowledge questions, not case-specific queries. No professional-client relationship. Same legal framing as Stack Overflow, Quora, or medical textbook authorship. |
| **Expert Portal as single point of failure**: If the company-operated portal goes down, no expert questions can be answered. | Medium | Portal code is open-source — if the company fails, community can fork and operate. User funds in Stripe Connect escrow are protected. |

### Questions for Further Research

1. **Model benchmarking**: What is the minimum model size / capability threshold for knowledge graph reasoning that experts would validate as "acceptable"? Is qwen3:14b-Q4 sufficient, or does this require 70B+ models?
2. **Expert engagement curve**: Will experts sustain interest in answering micro-questions long-term, or does novelty wear off? Need progression mechanics (unlock harder/higher-paying question types as reputation grows).
3. **Question Engine quality**: Can the LLM reliably identify the most informative questions to ask? Bad questions waste expert time and budget. Need to measure information gain per question and optimize the generator.
4. **Federated pattern detection**: Has anyone implemented the "pattern ballot + boolean attestation" protocol at scale? What are the failure modes?
5. **Graph import engineering**: How much work is the Hetionet + Uberon → unified schema merge? Is this weeks or months of data engineering?
6. **Appliance hardware selection**: Raspberry Pi 5 (4/8GB) vs Intel NUC vs custom ARM board? Need to benchmark Neo4j + Ollama + quantized LLM on candidate hardware. Can a Pi 5 with 8GB run qwen3:8b quantized with acceptable latency for agent reasoning?
7. **Multi-domain knowledge graphs**: What are the best foundational KGs for finance, legal, and engineering domains? Do equivalents of Hetionet exist for non-medical fields?
8. **Legal review**: Engage a regulatory attorney to stress-test the "educational entertainment" framing specifically. What language triggers concern? What language is safe?
