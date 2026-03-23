# Consilium — Decentralized Personal Knowledge System

**Vision:** Every person — including domain experts themselves — runs a permanent AI advisory council on their own computer. Their personal knowledge — about any complex domain — lives in a local graph database. When the AI faces a hard question, it consults a mesh of other instances worldwide and taps a network of credentialed domain experts who earn micro-payments by answering targeted knowledge questions. No one knows everything: a cardiologist needs nephrology insight, a tax attorney needs immigration law context. The result is a global knowledge improvement system where experts and non-experts alike benefit from collective wisdom, and no one's data ever leaves their machine.

**This is an open-source project built for the common good.** The Consilium platform is free software that anyone can run, modify, and share. The Expert Portal — a company-operated service that routes micro-questions to credentialed experts — is the only paid component, funded by an optional Pro subscription (≤$20/month). The goal is to give every person — regardless of means — the ability to organize complex information and walk into professional appointments prepared. Domain experts, including retirees who want to stay active and contribute their lifetime of knowledge, earn income by improving the knowledge graphs that make this possible. If it helps people have better conversations with their doctors, advisors, and attorneys, that is the reward.

**Consilium is not a product in any regulated domain.** It is a general-purpose knowledge organization and educational entertainment system. It helps users organize complex information, explore connections, and prepare for informed conversations with real-world professionals (doctors, lawyers, financial advisors, engineers). It does not provide advice, diagnoses, recommendations, or professional services of any kind.

**Supported knowledge domains (at launch):**

| Domain | Knowledge graph | Expert pool | Example use |
|--------|----------------|-------------|-------------|
| **Wellness & Body Literacy** | Human body systems, biomarkers, physiology | Licensed physicians, researchers | Understand your lab results before talking to your doctor |
| **Personal Finance** | Markets, instruments, tax rules, macro indicators | CFAs, CPAs, financial analysts | Prepare questions for your financial advisor |
| **Legal Literacy** | Statutes, case law, procedures, rights | Licensed attorneys, paralegals | Understand your situation before meeting a lawyer |
| **Engineering & Technical** | Systems, specifications, failure modes, standards | Licensed engineers, domain specialists | Explore technical concepts for professional discussions |
| **Nutrition & Fitness** | Nutrients, metabolic pathways, exercise physiology | Registered dietitians, exercise scientists | Organize personal fitness data and explore patterns |

**The system is domain-agnostic by design.** The graph database, agent framework, mesh protocol, and expert micro-question network are identical across domains. Only the knowledge graph content and expert pool differ. Adding a new domain = importing a new foundational knowledge graph + onboarding domain-specific experts.

**Relationship to MiroFish:** Consilium extends the MiroFish/OASIS multi-agent simulation framework (see `mirofish_bittensor_plan.md`). MiroFish provides the agent orchestration engine — persistent agent populations with graph-backed memory. Consilium specializes this for personal knowledge management across multiple domains, with credentialed expert validation via micro-questions.

**Wellness & Body Literacy is the primary launch domain** because it has the richest available foundational knowledge graphs (Hetionet, Uberon, SPOKE), the largest addressable user base (everyone has health data), and the strongest expert pool (millions of licensed physicians worldwide). The rest of this document uses wellness examples throughout, but every technical component is domain-agnostic.

---

## Core Principles

1. **Open source, common good** — the platform is free and open-source. The Expert Portal is a company-operated service with a transparent fee. Infrastructure for people, built by people.
2. **Local first** — your personal knowledge graph never leaves your device
3. **Opinions travel, not data** — the mesh shares reasoning, not records
4. **Graph-native** — all knowledge is modeled as a graph, enabling holistic reasoning across topics, time, and interconnected domains
5. **Persistent consilium** — your advisory team remembers everything, forever
6. **Open mesh** — anyone can run a node; more nodes = richer collective knowledge
7. **Zero configuration for the user** — the user should never see a terminal, a database, or a config file. They add data and explore insights. Everything else is invisible.
8. **Educational entertainment** — Consilium helps users explore, learn, and prepare for real-world professional conversations. It is not a substitute for professional advice in any domain.
9. **Domain-agnostic** — the platform contains zero domain-specific code. All domain knowledge — agents, graphs, schemas — lives in external open-source repos and is loaded on-demand only when the user's data triggers it.
10. **User-driven domains** — the platform never suggests or pre-installs domain capabilities. The user loads their own data; the system classifies it and asks permission to install the relevant domain plugin. If a user never loads medical records, no medical code ever runs on their machine.
11. **Expert dignity** — domain experts, including retirees, earn income and stay engaged by contributing their knowledge. Retirement doesn't mean your expertise stops mattering.
12. **Experts are users too** — no one knows everything. A cardiologist needs nephrology insight. A tax attorney needs immigration law context. Domain experts use Consilium to tap into collective wisdom beyond their own specialty — through the mesh, external AI, and the Expert Portal. They are the system's most powerful users and its most valuable mesh nodes.

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

### Existing Foundations to Import

Rather than building from scratch, the system imports established biomedical knowledge graphs and maps them into the two-layer schema:

| Source | Content | Format | Confidence |
|--------|---------|--------|-----------|
| **Uberon** | Multi-species anatomy ontology — organs, tissues, part-of, connected-to, develops-from (~75k entities) | OWL/RDF | established |
| **FMA** (Foundational Model of Anatomy) | Detailed human anatomy spatial relationships — most comprehensive human-specific | OWL | established |
| **Hetionet** | 20+ biomedical databases merged — anatomy, disease, gene, drug relationships with evidence scores | Neo4j native | established → inferred |
| **SPOKE** (UCSF) | 4M+ nodes from 40+ sources — anatomy, protein, disease, compound | Neo4j native | mixed, tiered |
| **OpenBioLink** | Quality-filtered biomedical KG — explicitly separates high-confidence from low-confidence edges | TSV | explicitly tiered |

**Import strategy:** Hetionet and SPOKE are Neo4j-native and import directly. Uberon/FMA require OWL→Cypher conversion. OpenBioLink provides the confidence filtering logic. The import pipeline maps all into the unified schema with confidence tiers preserved.

### Research Paper Ingestion

The knowledge graph grows not just from foundational imports and expert answers, but from **research papers**. Users (especially domain experts) can feed research papers into Consilium to enrich their local knowledge graph.

**Ingestion pipeline:**

```
Research paper (PDF, DOI link, PubMed ID)
    │
    ▼
Credibility Assessment (automated)
    ├── Journal impact factor / ranking
    ├── Peer review status (preprint vs. published)
    ├── Citation count and citation network
    ├── Retraction database check (Retraction Watch)
    ├── Author credentials and h-index
    └── Assigns confidence tier: established / inferred / exploratory
    │
    ▼
Knowledge Extraction (LLM + NLP)
    ├── Entity recognition (conditions, biomarkers, drugs, mechanisms)
    ├── Relationship extraction (causes, correlates, treats, precedes)
    ├── Quantitative findings (effect sizes, p-values, sample sizes)
    └── Maps to graph schema with edges carrying source and confidence
    │
    ▼
Knowledge Graph Update
    ├── New entities and relationships added
    ├── Existing relationships reinforced (evidence count increases)
    ├── Confidence tiers adjusted based on accumulated evidence
    └── Provenance tracked — every edge links back to its source paper(s)
```

**Credibility signals by domain:**

| Domain | Key credibility signals |
|--------|----------------------|
| Wellness | PubMed indexed, peer-reviewed, RCT/meta-analysis, Cochrane inclusion |
| Finance | Published in recognized journal, regulatory filing, established institution |
| Legal | Published opinion, law review, official reporter, statutory source |
| Engineering | IEEE/ASME/standards body, peer-reviewed, field-validated |

**Users can also load papers manually** and override the automated credibility assessment — marking a paper as more or less credible based on their own domain expertise. This is especially valuable for domain experts who understand nuances the automated system may miss.

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
- Each person's folder is fully isolated — agents operating on Igor's data cannot access Elena's data
- The knowledge graph is shared across all folders (it's domain knowledge, not personal data)
- Agents are scoped per-person — when you select a person's folder, agents reason against that person's data + the shared knowledge graph
- Mesh queries never include any personal data from any folder

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

All edges carry: `confidence_tier`, `evidence_count`, `primary_source`, `last_reviewed_date`.

### Mesh Contributes to the Knowledge Graph

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

---

## Phase 1 — Local Knowledge Graph + Personal Data Store

### 1.1 Graph Schema

The knowledge graph captures **domain knowledge** — entities, relationships, mechanisms, and patterns. It is not a personal data store. Personal data (lab results, financial records, documents) lives in the **personal data store** (per-person folders on the local filesystem). Agents reason across both at query time.

**Knowledge Graph Node Types (domain knowledge — shared, not personal):**

| Node | Description | Example |
|------|-------------|---------|
| `Condition` | Medical condition (domain knowledge) | Type 2 Diabetes, Hypertension |
| `Symptom` | Known symptom type | Fatigue, Shortness of breath |
| `Medication` | Drug with known properties | Metformin, Lisinopril |
| `Biomarker` | Physiological metric definition | eGFR, HbA1c, Blood Pressure |
| `Procedure` | Medical procedure type | Colonoscopy, ECG, MRI |
| `Genomic` | Genetic variant with known effects | APOL1 G1/G2, CYP2D6 poor metabolizer |
| `AgentOpinion` | Reasoning output from a specialist agent | Nephrology agent analysis |
| `MeshOpinion` | Anonymous reasoning from another node | External cardiology opinion |
| `ResearchPaper` | Source paper with credibility metadata | DOI, journal, confidence tier |

**Personal Data Types (per-person folder on filesystem — private, auditable, deletable):**

| Data | Description | Example |
|------|-------------|---------|
| Lab results | Single measurement at a point in time | Creatinine 1.8 mg/dL on 2026-01-15 |
| Wearable readings | Metrics from wearable devices | HRV 42ms, Sleep score 73 |
| Allergies | Known allergies or adverse reactions | Penicillin allergy |
| Family history | Hereditary/familial conditions | Father: MI at 52 |
| Social factors | Lifestyle or environmental factors | Smoking 10 pack-years |
| Provider records | Treating physicians, encounters | Dr. Smith visit 2026-01-15 |
| Documents | Raw uploaded files | Lab report PDF, legal document |

Personal data is loaded into memory at query time, connected to knowledge graph entities by the agent, and never persisted in the graph database. The graph holds "Metformin TREATS Type 2 Diabetes" (knowledge). The personal data store holds "Igor takes Metformin 1000mg 2x daily" (personal fact).

**Knowledge Graph Edge Types (domain relationships):**

| Edge | Connects | Meaning |
|------|----------|---------|
| `TREATS` | Medication → Condition | Medication for condition |
| `SUGGESTS` | Biomarker → Condition | Abnormal value may indicate |
| `CORRELATES_WITH` | Biomarker ↔ Symptom | Statistical correlation |
| `CAUSES` | Condition → Symptom | Known causal relationship |
| `CONTRADICTS` | Medication → Medication | Drug interaction |
| `PRECEDES` | Biomarker → Condition | Temporal pattern |
| `TRENDING` | Biomarker → Biomarker | Biomarker trend relationship |
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

### 1.2 Temporal Modeling

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

### 1.3 Data Ingestion

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
    → Classifier (personal data vs. domain knowledge)
    │
    ├── Personal data path:
    │   → Store in per-person folder as structured JSON
    │   → Deduplicator (don't double-count the same result)
    │   → Change detector (flag new data for agent review)
    │
    └── Knowledge path:
        → Entity recognizer (map to knowledge graph node types)
        → Relationship extractor (identify domain relationships)
        → Knowledge graph writer (upsert nodes and edges in Neo4j)
        → Confidence tier assignment (based on source credibility)
```

---

## Phase 2 — Dynamic Agent Swarms

### 2.1 Agents Are Spawned by Data, Not Pre-Installed

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

### 2.2 Swarm Formation — Agents Find Each Other by Competence

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

### 2.3 Agent Memory via Graph

Unlike MDAgents (stateless), every agent opinion is written back to the graph:

```
[NephrologyAgent] analyzes eGFR pattern
    → creates [AgentOpinion] node
    → links to: [LabResult: eGFR=52], [LabResult: creatinine=1.8]
    → tags: competence=renal_function, date=2026-03-22, confidence=0.82
    → text: "eGFR shows a consistent downward pattern over 18 months"
```

Next time NephrologyAgent runs, it reads its own prior opinions and the subsequent data to see if its observations held up. Agents track their own accuracy over time.

### 2.4 Proactive Monitoring

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

---

## Phase 3 — Mesh Network (Federated Consultation)

### 3.1 How the Mesh Works — Dynamic Domain Discovery

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
    "values": {
      "eGFR_latest": 52,
      "creatinine": 1.8,
      "HbA1c": 7.2,
      "hemoglobin": 10.2
    }
  },
  "question": "What patterns do agents with renal_function competence see in this data?",
  "requesting_node": "node_hash_xyz"
}
```

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
- **k-anonymity enforcement**: before sending a mesh query, the local node checks whether the condition combination is shared by at least k patients in the body knowledge graph. If not, the query is generalized (e.g., "rare lysosomal storage disease" instead of "Fabry disease").
- **Differential privacy noise**: add controlled noise to lab values (within clinically meaningful ranges) to prevent exact-value matching.
- **Query rate limiting**: a node cannot send more than N mesh queries per day, preventing profile reconstruction from query patterns.
- **No longitudinal linking**: each mesh query uses a fresh pseudonymous ID. No way to determine that two queries came from the same patient.

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

### 3.2 What Nodes Share With Each Other

| Shared | Not shared |
|--------|-----------|
| Anonymized medical context | Name, age, location, dates |
| Clinical question | Any graph data |
| Agent opinion (text) | Lab PDFs or records |
| Confidence score | Identity of requester |
| "Based on N similar cases" | Details of those cases |

The responding node's agent draws on its *own patient's graph* to inform its opinion — it has seen similar cases. But it shares only the conclusion, not the underlying data.

### 3.3 Mesh Protocol

**Node discovery:** nodes advertise competence tags on a DHT (distributed hash table) — similar to BitTorrent. No central registry. No domain labels.

**Routing:** consultation requests are matched by competence tag overlap:
- Minimum 2 matching tags required for a node to be eligible
- More tag overlap = higher routing priority
- Data richness (how much relevant data the node has) is a secondary ranking signal
- Available compute and uptime are tertiary signals

**Remote swarm formation:** When a mesh query is published, matching nodes' agents form a temporary remote swarm. Each agent in the remote swarm analyzes the anonymized context independently and returns a perspective. The remote swarm dissolves after the query is answered.

**Reputation:** nodes whose perspectives are rated useful by the requesting node's swarm accumulate reputation score. Low-reputation nodes are queried less. Reputation is per-competence-tag, not global — a node can have high reputation for `renal_function` and low reputation for `hematology`.

**Volume:** a single consultation might query 5–20 nodes. Perspectives are aggregated by the local GeneralistAgent (or equivalent coordinator from the plugin).

### 3.4 Mesh Credit System — BitTorrent-Style Barter

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

### 3.5 External AI Escalation

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

### 3.6 Collective Context Without Sharing Data

**Important distinction:** agents don't "become smarter" from following one person's data — the underlying LLM's reasoning doesn't improve from longitudinal exposure. What improves is **context richness**. A node that has tracked someone's data for 10 years has a deeply populated graph: complete history, every trend, every agent opinion and whether it held up. When that node's agent answers a mesh query matching its competence tags, it draws on that deep longitudinal context — because it actually happened in its graph.

The mesh effect: each node contributes **data-grounded context**, not generic LLM reasoning. A single node's context is one person's data. A thousand nodes' aggregated responses across matching competence tags represent a thousand real longitudinal cases. The LLM is the same everywhere; the graph data is what differentiates nodes.

Node quality varies significantly. A node with 10 years of comprehensive data produces richer perspectives than a node with 6 months of sparse data. The mesh protocol accounts for this via the `data_richness` signal advertised on the DHT.

---

## Expert Portal — Company-Operated Knowledge Validation Service

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

## Phase 4 — Privacy Layer (Future)

*Designed from the start to support this — not required for local-only operation.*

- HMAC-based pseudonymous document IDs (different ID per document, linkable only by patient)
- TEE (Trusted Execution Environment) for mesh nodes — opinions computed in secure enclaves
- Differential privacy on query patterns — prevent inference from query frequency
- Zero-knowledge proofs for reputation scoring — prove a node gave good opinions without revealing what the opinion was about
- k-anonymity enforcement on mesh queries (see re-identification risk in Phase 3)

---

## Legal Strategy — Why Consilium Is Not a Regulated Product

**Consilium is a general-purpose knowledge organization and educational entertainment platform.** It is not a medical device, financial advisory service, legal practice tool, or professional service of any kind. This is not a disclaimer bolted onto a medical product — it is the fundamental architecture of the system.

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

### Phase 1 Target: MacBook (developer + early adopter)

The initial deployment target is a MacBook (Apple Silicon, 16GB+ RAM). All components — Neo4j, Ollama, the agent framework, and the dashboard — run locally via Docker Compose behind a native macOS app.

**Minimum specs:**
- Apple Silicon Mac (M1 or later)
- 16 GB RAM (24 GB+ recommended for qwen3:14b quantized)
- 50 GB free disk (Neo4j + body knowledge graph + model weights)
- macOS 13+

**User experience target:** The user downloads a `.dmg`, installs the app, and sees a dashboard. They never interact with Docker, Neo4j, Ollama, or a terminal. The app handles all infrastructure invisibly — similar to how the Docker Desktop app hides containers behind a GUI, or how Obsidian hides its data store behind a note-taking UI.

### Phase 2 Target: Consilium Appliance (DIY or pre-assembled)

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
