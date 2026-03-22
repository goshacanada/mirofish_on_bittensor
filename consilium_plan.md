# Consilium — Decentralized Personal Knowledge System

**Vision:** Every person runs a permanent AI advisory council on their own computer. Their personal knowledge — about any complex domain — lives in a local graph database. When the AI faces a hard question, it consults a mesh of other instances worldwide and taps a marketplace of credentialed domain experts who earn money by answering targeted micro-questions. The result is a global knowledge improvement system where no one's data ever leaves their machine.

**Consilium is not a product in any regulated domain.** It is a general-purpose knowledge organization and educational entertainment system. It helps users organize complex information, explore connections, and prepare for informed conversations with real-world professionals (doctors, lawyers, financial advisors, engineers). It does not provide advice, diagnoses, recommendations, or professional services of any kind.

**Supported knowledge domains (at launch):**

| Domain | Knowledge graph | Expert pool | Example use |
|--------|----------------|-------------|-------------|
| **Wellness & Body Literacy** | Human body systems, biomarkers, physiology | Licensed physicians, researchers | Understand your lab results before talking to your doctor |
| **Personal Finance** | Markets, instruments, tax rules, macro indicators | CFAs, CPAs, financial analysts | Prepare questions for your financial advisor |
| **Legal Literacy** | Statutes, case law, procedures, rights | Licensed attorneys, paralegals | Understand your situation before meeting a lawyer |
| **Engineering & Technical** | Systems, specifications, failure modes, standards | Licensed engineers, domain specialists | Explore technical concepts for professional discussions |
| **Nutrition & Fitness** | Nutrients, metabolic pathways, exercise physiology | Registered dietitians, exercise scientists | Organize personal fitness data and explore patterns |

**The system is domain-agnostic by design.** The graph database, agent framework, mesh protocol, expert micro-question marketplace, and Bittensor incentive layer are identical across domains. Only the knowledge graph content and expert pool differ. Adding a new domain = importing a new foundational knowledge graph + onboarding domain-specific experts.

**Relationship to MiroFish:** Consilium is a **single Bittensor subnet** that extends the MiroFish/OASIS multi-agent simulation framework (see `mirofish_bittensor_plan.md`). MiroFish provides the agent orchestration engine — persistent agent populations with graph-backed memory. Consilium specializes this for personal knowledge management across multiple domains, with credentialed expert validation via micro-questions.

**Wellness & Body Literacy is the primary launch domain** because it has the richest available foundational knowledge graphs (Hetionet, Uberon, SPOKE), the largest addressable user base (everyone has health data), and the strongest expert pool (millions of licensed physicians worldwide). The rest of this document uses wellness examples throughout, but every technical component is domain-agnostic.

---

## Core Principles

1. **Local first** — your personal knowledge graph never leaves your device
2. **Opinions travel, not data** — the mesh shares reasoning, not records
3. **Graph-native** — all knowledge is modeled as a graph, enabling holistic reasoning across topics, time, and interconnected domains
4. **Persistent consilium** — your advisory team remembers everything, forever
5. **Open mesh** — anyone can run a node; more nodes = richer collective knowledge
6. **Zero configuration for the user** — the user should never see a terminal, a database, or a config file. They add data and explore insights. Everything else is invisible.
7. **Educational entertainment** — Consilium helps users explore, learn, and prepare for real-world professional conversations. It is not a substitute for professional advice in any domain.
8. **Domain-agnostic** — the platform contains zero domain-specific code. All domain knowledge — agents, graphs, schemas — lives in external open-source repos and is loaded on-demand only when the user's data triggers it.
9. **User-driven domains** — the platform never suggests or pre-installs domain capabilities. The user loads their own data; the system classifies it and asks permission to install the relevant domain plugin. If a user never loads medical records, no medical code ever runs on their machine.

---

## System Overview

```
Your Machine
├── Consilium Platform (domain-agnostic core)
│   ├── Data Classifier (LLM)          ← detects what kind of data you loaded
│   ├── Graph Database (Neo4j)          ← empty until you add data
│   ├── Agent Framework                 ← runs agents from installed domain plugins
│   ├── Local LLM (Ollama)              ← inference stays on your hardware
│   └── Mesh Gateway                    ← sends anonymized questions, receives perspectives
│
├── Domain Plugins (installed on-demand from GitHub repos)
│   ├── [not installed until user loads relevant data]
│   ├── e.g. wellness plugin ← pulled only when user uploads lab results
│   ├── e.g. finance plugin  ← pulled only when user loads portfolio data
│   └── e.g. legal plugin    ← pulled only when user uploads legal documents
│
└── Your Data (drives everything)
    ├── Whatever you load → classified → routed to the right graph
    └── You control what domains are active on your machine

Mesh Network (other people's machines)
├── Node A: runs wellness + finance plugins    ← her data drove those installs
├── Node B: runs only finance plugin           ← he only loaded financial data
├── Node C: runs wellness + legal plugins      ← her data drove those installs
└── Each node is different — shaped by its owner's data

Expert Marketplace (Bittensor subnet)
├── Credentialed domain experts answer micro-questions
├── Paid per answer ($0.05–0.50) — like token pricing
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

### Why This Avoids FDA Completely

```
Traditional medical AI product:
  Company ships code that processes health data → FDA says "that's SaMD"

Consilium:
  1. Company ships a generic platform with ZERO health code
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

### The Two-Layer Graph

```
Layer 1: Body Knowledge Graph (shared, pre-loaded, updated from research)
├── Organs, tissues, systems (anatomy)
├── Physiological relationships (how things connect)
├── Biomarker meanings (what creatinine rising means for kidneys)
├── Known disease mechanisms (how CKD progresses)
└── Drug effects and interactions (known mechanism of action)

Layer 2: Personal Health Graph (private, per-person)
├── Your lab results, conditions, symptoms
├── Your medications and doses
├── Your wearable data
└── Agent opinions about your case
```

Agents traverse both layers simultaneously. When NephrologyAgent sees your eGFR declining, it already knows (from Layer 1) that: kidney → filters creatinine → declining eGFR → tubular damage → anemia of CKD → fatigue. It connects your personal data to established physiology automatically.

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

**Limitations:** This only works for pre-defined pattern queries. The subnet owner publishes a periodic "pattern ballot" — a list of hypothesized relationships to test. Nodes that have relevant data attest; nodes without relevant data abstain. New pattern hypotheses can be proposed by any node and added to the ballot by the subnet owner after review.

---

## Phase 1 — Local Health Graph

### 1.1 Graph Schema

The health graph is the foundation. Everything is a node or an edge.

**Core Node Types:**

| Node | Description | Example |
|------|-------------|---------|
| `Condition` | Diagnosed medical condition | Type 2 Diabetes, Hypertension |
| `Symptom` | Observed symptom | Fatigue, Shortness of breath |
| `Allergy` | Known allergy or adverse reaction | Penicillin allergy, Sulfa sensitivity |
| `FamilyHistory` | Hereditary / familial condition | Father: MI at 52, Mother: Type 2 DM |
| `SocialFactor` | Lifestyle or environmental factor | Smoking 10 pack-years, Sedentary, Alcohol 14 units/wk |
| `Medication` | Drug with dose and schedule | Metformin 1000mg 2x daily |
| `LabResult` | Single lab measurement at a point in time | Creatinine 1.8 mg/dL on 2026-01-15 |
| `LabPanel` | Group of related lab results | Complete Metabolic Panel |
| `WearableReading` | Metric from a wearable device | HRV 42ms, Sleep score 73 |
| `Biomarker` | Tracked physiological metric | eGFR, HbA1c, Blood Pressure |
| `Doctor` | Treating physician with specialty | Dr. Smith, Nephrologist |
| `Encounter` | Medical visit or event | ER visit 2025-11-03, Annual checkup |
| `Procedure` | Medical procedure | Colonoscopy, ECG, MRI |
| `AgentOpinion` | Opinion from a specialist agent | Nephrology agent analysis 2026-03-01 |
| `MeshOpinion` | Anonymous opinion from another node | External cardiology opinion |
| `Genomic` | Genetic variant or pharmacogenomic result | APOL1 G1/G2, CYP2D6 poor metabolizer |

**Core Edge Types:**

| Edge | Connects | Meaning |
|------|----------|---------|
| `HAS_CONDITION` | Person → Condition | Diagnosed with |
| `HAS_SYMPTOM` | Person/Condition → Symptom | Experiences / caused by |
| `TAKES` | Person → Medication | Currently taking |
| `TREATS` | Medication → Condition | Medication for condition |
| `MEASURED_AT` | LabResult → date property | When measured (stored as datetime on the edge or node, not a separate Timeline node) |
| `RESULT_OF` | LabResult → LabPanel | Part of this panel |
| `INDICATES` | LabResult → Biomarker | Measures this biomarker |
| `SUGGESTS` | Biomarker → Condition | Abnormal value may indicate |
| `CORRELATES_WITH` | Biomarker ↔ Symptom | Statistical correlation |
| `MANAGED_BY` | Condition → Doctor | Under care of |
| `ALLERGIC_TO` | Person → Allergy | Known allergy |
| `HAS_FAMILY_HISTORY` | Person → FamilyHistory | Hereditary risk factor |
| `HAS_SOCIAL_FACTOR` | Person → SocialFactor | Lifestyle / environmental factor |
| `HAS_VARIANT` | Person → Genomic | Genetic variant carried |
| `OCCURRED_AT` | Encounter → date property | When visit happened |
| `LED_TO` | Encounter → Condition/Procedure | Visit resulted in |
| `TRENDING` | Biomarker → Biomarker | Biomarker trend relationship |
| `CONTRADICTS` | Medication → Medication | Drug interaction |
| `GENERATED_BY` | AgentOpinion → Condition | Opinion about this condition |
| `INFORMED_BY` | AgentOpinion → LabResult | Opinion used this data |

**Example subgraph for a patient:**

```
[Person]
  ├─HAS_CONDITION──► [Type 2 Diabetes] ──MANAGED_BY──► [Dr. Chen, Endocrinologist]
  │                        │
  │                   TREATED_BY
  │                        ▼
  ├─TAKES──────────► [Metformin 1000mg]
  │
  ├─HAS_CONDITION──► [Hypertension] ──MANAGED_BY──► [Dr. Smith, GP]
  │
  ├─HAS_SYMPTOM───► [Fatigue] ◄──CAUSED_BY?──── [Anemia] ← SUGGESTS ← [Hemoglobin 10.2]
  │
  ├─LAB_RESULT────► [Creatinine 1.8] ──MEASURED_AT──► [2026-01-15]
  │                       │
  │               INDICATES▼
  │                   [eGFR: 52] ──SUGGESTS──► [Early CKD Stage 3]
  │                       │
  │               TRENDING▼
  │              [eGFR: 61 → 58 → 55 → 52]  ← 18-month decline
  │
  └─WEARABLE──────► [HRV: 38ms avg] ──CORRELATES_WITH──► [Fatigue]
                    [Sleep: 5.8h avg]
                    [Resting HR: 72bpm]
```

This is what gives agents *holistic* context. An agent querying "why is this patient fatigued?" traverses the graph and finds: declining eGFR + low hemoglobin + poor sleep + metformin use — and reasons across all of them simultaneously.

### 1.2 Temporal Modeling

Every measurement carries a `measured_at` datetime property directly on the node (not in a separate Timeline node — that would add unnecessary indirection and multiply edge count). The graph supports trend queries natively:

```cypher
-- Find all eGFR readings in the last 2 years, ordered by time
MATCH (b:Biomarker {name: "eGFR"})<-[:INDICATES]-(r:LabResult)
WHERE r.measured_at > date() - duration({years: 2})
RETURN r.value, r.measured_at ORDER BY r.measured_at
```

Agents use trends, not just snapshots:
- *"eGFR declining at 4.5 points/year — at this rate, Stage 4 CKD in 18 months"*
- *"HRV trending down 3 months before creatinine rose — early stress signal"*

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
- Upload PDF/text → LLM extracts structured data → mapped to graph nodes
- Patient controls what gets ingested — nothing automatic

**Ingestor pipeline:**
```
Raw data (PDF, API, XML)
    → Parser (extract structured fields)
    → Entity recognizer (map to graph node types)
    → Deduplicator (don't double-count the same result)
    → Graph writer (upsert nodes and edges in Neo4j)
    → Change detector (flag new results for agent review)
```

---

## Phase 2 — Local Agent Team (Plugin-Provided)

### 2.1 Agent Roles Are Defined by Domain Plugins

The platform provides the agent framework — the ability to run persistent LLM agents with graph access. **Which agents exist is determined by the installed domain plugins**, not by the platform.

**Example: Wellness plugin agents** (from `consilium-wellness/agents/`):

| Agent | Specialty | Watches for |
|-------|-----------|-------------|
| `GeneralistAgent` | Primary care patterns | Overall trends, flags for specialists |
| `CardiologyAgent` | Heart, vascular | ECG anomalies, BP trends |
| `NephrologyAgent` | Kidneys | eGFR patterns, creatinine, electrolytes |
| `EndocrinologyAgent` | Hormones, metabolism | HbA1c, thyroid, glucose patterns |
| `NeurologyAgent` | Brain, nervous system | Sleep quality, HRV |
| `HematologyAgent` | Blood | CBC trends, hemoglobin |
| `PharmacologyAgent` | Drug interactions | Monitors all medications for known conflicts |

**Example: Finance plugin agents** (from `consilium-finance/agents/`):

| Agent | Specialty | Watches for |
|-------|-----------|-------------|
| `PortfolioAgent` | Asset allocation | Concentration risk, sector exposure |
| `TaxAgent` | Tax implications | Unrealized gains, wash sale patterns |
| `MacroAgent` | Economic trends | Rate sensitivity, inflation exposure |

**The platform doesn't know what these agents do.** It just runs them against the graph and collects their outputs. The domain-specific intelligence lives entirely in the plugin code — which is open-source, community-maintained, and user-installed.

### 2.2 Agent Memory via Graph

Unlike MDAgents (stateless), every agent opinion is written back to the graph:

```
[CardiologyAgent] analyzes ECG + BP trends
    → creates [AgentOpinion] node
    → links to: [ECG result], [BP readings], [Condition: Hypertension]
    → tags: specialty=cardiology, date=2026-03-22, confidence=0.82
    → conclusion: "BP control adequate, recommend reducing lisinopril dose"
```

Next time CardiologyAgent runs, it reads its own prior opinions and the subsequent data to see if its predictions were right. Agents learn from their own track record.

### 2.3 Consilium Workflow

**Triggered by:** new lab result, wearable anomaly, scheduled review (weekly), or user question.

```
1. Trigger detected (e.g. new lab panel uploaded)

2. GeneralistAgent reads new data + full graph context
   → determines which specialists are relevant
   → complexity assessment: simple / moderate / complex

3. Relevant agents are activated
   → each queries the graph independently
   → each writes a preliminary opinion

4. Consilium round:
   → agents see each other's preliminary opinions
   → can agree, disagree, or request more data
   → GeneralistAgent moderates disagreements
   → up to 3 rounds of debate

5. Final synthesis:
   → GeneralistAgent writes consolidated summary
   → Each specialist confirms or notes dissent
   → Written to graph as [AgentOpinion] nodes

6. Patient notification:
   → Summary presented in plain language
   → Flagged items: urgent / watch / informational
   → "Your nephrologist agent noticed your eGFR dropped another 3 points.
      Combined with your fatigue and low hemoglobin, it recommends
      requesting an anemia workup at your next GP visit."
```

### 2.4 Proactive Monitoring

Agents don't only respond to queries — they watch for patterns:

```python
# Example: NephrologyAgent scheduled check
- Runs weekly
- Queries: eGFR trend over 12 months
- If slope < -3 points/year: flag for consilium
- If slope < -6 points/year: urgent alert + mesh consultation
```

Agents that find nothing noteworthy write a brief "all clear" note to the graph so there's a record of monitoring.

---

## Phase 3 — Mesh Network (Federated Consultation)

### 3.1 How the Mesh Works

When your local consilium faces a case beyond its confidence threshold, it reaches out to the mesh — other instances running on other people's machines.

**What gets sent (the consultation request):**
```json
{
  "query_id": "q_a3f7...",
  "specialty": "nephrology",
  "anonymized_context": {
    "conditions": ["Type 2 Diabetes (8 years)", "Hypertension"],
    "medications": ["Metformin 1000mg", "Lisinopril 10mg"],
    "trend": "eGFR: 68→61→58→55→52 over 20 months",
    "labs": {
      "creatinine": "1.8 mg/dL",
      "hemoglobin": "10.2 g/dL",
      "HbA1c": "7.2%"
    },
    "wearables": {
      "avg_hrv": "38ms (declining)",
      "avg_sleep": "5.8h"
    }
  },
  "question": "Is this eGFR decline rate concerning given the full picture? What would you watch next?",
  "requesting_node": "node_hash_xyz"
}
```

No name. No age. No location. No dates. Just the medical picture.

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
  "specialty": "nephrology",
  "opinion": "eGFR decline of ~4 points/year in a diabetic on metformin warrants attention.
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

**Node discovery:** nodes advertise available specialties on a DHT (distributed hash table) — similar to BitTorrent. No central registry.

**Routing:** consultation requests are routed to nodes that have:
- The requested specialty agent
- Relevant case history (e.g. nodes with diabetic nephrology experience)
- Available compute

**Reputation:** nodes that give useful opinions (judged by whether local agents found them helpful) accumulate reputation score. Low-reputation nodes are queried less.

**Volume:** a single consultation might query 5-20 nodes. Opinions are aggregated by the local GeneralistAgent.

### 3.4 Collective Context Without Sharing Data

**Important distinction:** agents don't "become smarter" from following one patient — the underlying LLM's medical reasoning doesn't improve from longitudinal exposure. What improves is **context richness**. A node that has tracked a diabetic patient for 10 years has a deeply populated graph: complete medication history, every lab trend, every wearable anomaly, every agent opinion and whether it proved correct. When that node's agent answers a mesh query about diabetic nephropathy, it can say *"in a case with a similar trajectory, eGFR stabilized after switching from metformin to an SGLT2 inhibitor at eGFR 48"* — because that actually happened in its graph.

The mesh effect: each node contributes **case-grounded context**, not generic LLM reasoning. A single node's context is one patient. A thousand nodes' aggregated responses represent a thousand real longitudinal cases — approaching the experience base of a specialist who has managed thousands of patients over decades. The LLM is the same everywhere; the graph data is what differentiates nodes.

This also means node quality varies significantly. A node with 10 years of comprehensive data (labs, wearables, genomics, imaging) produces richer opinions than a node with 6 months of Apple Watch data. The validator scoring mechanism (see Bittensor Integration) must account for this.

---

## Bittensor Integration — Doctor Validation Layer

This is the economic engine of HealthMesh. AI agents (miners) produce health opinions. Validators run infrastructure that routes cases to credentialed doctors, collects their ratings, and submits weight vectors on-chain. Doctors are a **new role** — human raters compensated from validator emissions — not validators themselves.

### Roles in Bittensor Terms

```
Miners       = HealthMesh AI agent nodes
               → produce clinical opinions in response to anonymized queries
               → earn TAO proportional to quality scores from validators

Validators   = Infrastructure operators (similar to Tensorplex Dojo model)
               → run automated scoring pipeline + doctor review portal
               → route anonymized cases to credentialed doctors
               → aggregate doctor ratings into weight vectors
               → submit weights to chain via Yuma Consensus
               → earn TAO proportional to stake × consensus alignment
               → pay doctors from their validator emissions

Doctors      = Human expert raters (NOT Bittensor validators)
               → log into validator's review portal (web UI, no crypto needed)
               → review anonymized cases + agent opinions against rubrics
               → submit structured scores
               → compensated in fiat (Stripe) or TAO (opt-in)
               → never need to stake TAO, run nodes, or touch wallets

Subnet owner = HealthMesh protocol
               → defines the incentive mechanism and rubric standards
               → earns 18% of emissions
               → maintains the credentialing oracle and rubric library
```

**Why doctors cannot be validators directly:** Bittensor validators must stake TAO (capital barrier), run infrastructure 24/7 (operational barrier), and submit weight vectors every tempo (~72 min) (continuous commitment). Doctors don't do any of this. Asking a nephrologist to buy crypto, run a node, and submit on-chain transactions every 72 minutes is a product-killing friction. Instead, validators are infrastructure operators who run the review portal as a service, and doctors interact with a simple web UI — just like reviewing cases on UpToDate or a CME platform.

This mirrors the Tensorplex Dojo (SN52) architecture: validators generate tasks, route them to human raters, and aggregate human feedback into weight vectors. The innovation here is that the human raters are **credentialed medical professionals**, not general crowdworkers.

### How Doctor Credentialing Works

Doctors' opinions are only valuable if they are actually doctors. Credentials must be verified, but the system should not require doctors to interact with crypto at all.

**Credentialing flow:**

```
1. Doctor signs up on the HealthMesh Review Portal (standard web registration)
   → provides: license number, jurisdiction, specialty, NPI (US) or equivalent

2. HealthMesh credentialing oracle verifies against medical board registries
   → US: NPPES (NPI lookup), state medical board APIs
   → EU: national medical registers
   → Automated where APIs exist; manual review otherwise
   → Estimated verification time: 24–72 hours

3. If valid: oracle records a credential attestation on-chain
   → Option A (SBT): Soulbound Token to a validator-managed wallet
     (doctor never touches a wallet — validator holds the SBT on their behalf)
   → Option B (off-chain): credential stored in the oracle database,
     referenced by validator when submitting weights (simpler, less decentralized)
   → Attestation encodes: specialty, jurisdiction, license status, expiry
   → ZK proof option: validator can prove "this weight vector includes
     ratings from N verified nephrologists" without revealing identities

4. Doctor's portal account is activated — they can start reviewing cases
   → No wallet, no TAO, no crypto needed from the doctor's side

5. License expiry monitoring: oracle re-checks every 6 months; credential
   revoked if license lapses or disciplinary action taken
```

**Specialty matching:** The credential encodes the doctor's specialty. The validator portal routes cases only to doctors whose specialty matches the agent being reviewed. A nephrologist reviews nephrology agent opinions; a cardiologist reviews cardiology agent opinions. In-specialty reviews are weighted 2x in the score aggregation.

**Doctor onboarding — solving the cold start:**

Getting the first 50 doctors is the hardest problem. Concrete strategies:

| Strategy | Target | How |
|----------|--------|-----|
| CME credits | All doctors | Partner with a CME accreditor (ACCME). Reviewing AI diagnostic opinions qualifies as continuing medical education. Doctors already spend money on CME — HealthMesh makes it paid instead. |
| Medical school partnerships | Residents, fellows | Offer as a teaching tool. Residents review AI opinions and learn from the rubric feedback loop. Partner initially with 2–3 academic medical centers. |
| Telehealth moonlighting | Early-career physicians | Position as flexible, asynchronous work. 5–10 min per case, no patient liability, no scheduling constraints. Compete with telehealth moonlighting platforms on flexibility. |
| Specialty society outreach | Board-certified specialists | Present at society meetings (ASN for nephrology, ACC for cardiology). Frame as "shape the AI that will assist your specialty." |
| Research collaborators | Academic physicians | Offer co-authorship on publications analyzing doctor-vs-AI agreement patterns. Academic currency. |

**Target: 50 credentialed doctors across 5 specialties before subnet launch.**

### What Doctors Actually Do — Micro-Questions, Not Full Diagnoses

**Key design principle:** Doctors are never asked to review a full case or provide a diagnosis. Instead, the system asks them **small, targeted questions** — each answerable in 10–30 seconds. Doctors are paid per answer at a nominal rate, like paying for tokens. The system uses an LLM to generate questions that will maximally improve the body knowledge graph and agent reasoning.

**Why micro-questions instead of full case review:**
- A full case review takes 5–10 minutes, costs $10–50, and requires understanding context
- A micro-question takes 10–30 seconds, costs $0.05–0.50, and requires only specialty knowledge
- Micro-questions scale: 1000 questions/day across 50 doctors = massive knowledge acquisition
- Doctors can answer between patients, in an elevator, on a phone — zero context switching
- No risk of the doctor accidentally providing a diagnosis (regulatory/liability win)
- Easier to golden-set test — simple questions have clearer right/wrong answers

### The Question Engine

The heart of the system. An LLM examines the body knowledge graph and agent reasoning chains, identifies **where uncertainty lives**, and generates targeted questions to resolve it.

**How questions are generated:**

```
1. Agent produces an opinion on a patient case
   → e.g. "eGFR declining at 4pts/year, recommend nephrology referral"

2. The Question Engine LLM analyzes the agent's reasoning chain:
   → What edges in the body knowledge graph did the agent traverse?
   → Which edges have low confidence or missing evidence?
   → Where did the agent make an assumption vs. follow established knowledge?
   → Where do different agents in the consilium disagree?

3. The LLM generates 2–5 micro-questions targeting the weakest links:

   Question types:

   MULTIPLE CHOICE (most common, fastest to answer):
   "For a diabetic patient with eGFR 52 on metformin 1000mg:
    a) Continue current dose
    b) Reduce to 500mg
    c) Switch to alternative
    d) Depends on other factors (which?)"

   THRESHOLD (numeric, calibrates decision boundaries):
   "At what eGFR would you refer a diabetic patient to nephrology?
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

The Question Engine doesn't generate random medical trivia. It specifically targets **edges in the body knowledge graph that would change agent behavior if updated**. This is active learning — the system identifies what it doesn't know and asks the most informative question to fill the gap.

```
Example: improving the decision graph

Current state in body knowledge graph:
  [eGFR < 45] ──REQUIRES──► [Metformin dose review]
  confidence_tier: inferred
  evidence_count: 12

Question Engine identifies:
  - This edge drives a key agent recommendation
  - Confidence is "inferred" (not established)
  - The threshold (45) is uncertain — some guidelines say 30, others say 45
  - If the threshold is wrong, thousands of agent opinions are miscalibrated

Generated question:
  "At what eGFR would you begin reducing metformin dose?
   [slider: 20 ─── 30 ─── 45 ─── 60]"
   Sent to: 20 nephrologists + 20 endocrinologists

Results: median answer = 30, IQR = 25–45
  → Edge updated: [eGFR < 30] ──REQUIRES──► [Metformin discontinuation]
                  [eGFR 30–45] ──REQUIRES──► [Metformin dose reduction]
  → confidence_tier upgraded: inferred → established
  → evidence_count: 40 (physician responses)
  → All agents using this edge now have better calibration
```

### How Doctor Answers Flow to the Chain

```
Question Engine generates questions from agent reasoning gaps
    → Questions routed to matched doctors via validator portals
    → Each question sent to 3–10 doctors in the relevant specialty
    → Doctors answer on phone/web (10–30 seconds each)
    → Validator collects answers, computes consensus:
        - Multiple choice: majority vote + agreement %
        - Threshold: median + IQR
        - Yes/No: proportion + confidence-weighted average
        - Ranking: Borda count or similar aggregation
    → Consensus answers fed back into body knowledge graph:
        - Update edge confidence tiers
        - Adjust decision thresholds
        - Add new edges if doctor answers reveal missing relationships
    → Validator scores each miner's agent opinions against
       the updated knowledge graph:
        - Agents whose reasoning aligned with doctor consensus → higher score
        - Agents whose reasoning contradicted doctor consensus → lower score
    → Weight vector submitted to chain via Commit-Reveal
    → Yuma Consensus distributes emissions
```

**The key insight:** Doctors don't score agents directly. They answer knowledge questions. The system uses those answers to update the knowledge graph, and then agents are scored automatically against the improved graph. This separates the doctor's contribution (medical knowledge) from the scoring mechanism (automated, deterministic).

### Pricing — Doctors as Tokens

Doctors are paid per answer, not per case. The pricing model mirrors LLM token pricing:

```
Question type         Estimated time    Pay per answer
─────────────────────────────────────────────────────
Multiple choice       10 sec            $0.05–0.10
Yes/No + confidence   10 sec            $0.05–0.10
Threshold (slider)    15 sec            $0.10–0.20
Ranking               20 sec            $0.15–0.25
Free text             30–60 sec         $0.25–0.50
```

**At scale:**
- A doctor answering questions during downtime: 100 questions/day × $0.10 avg = $10/day
- A doctor doing a focused 30-min session: 60–100 questions = $6–10/session
- A motivated doctor doing 2 sessions/day: $12–20/day supplemental income
- Not life-changing money, but **zero-friction** — no scheduling, no context, no liability
- The real draw for early adopters: CME credits + shaping the AI that will define their specialty

**Cost to the network:**
- 1000 questions/day × $0.10 avg = $100/day = $3K/month in doctor payments
- This is paid from validator emissions (41% of subnet TAO)
- At current TAO prices and moderate subnet emissions, this is sustainable
- Compare to: one traditional medical consultation costs $200–500

### Doctor Reputation — Graded on Answer Quality

Every doctor answer is graded. Doctors who answer well see more questions and higher pay. Doctors who answer poorly or randomly get fewer questions.

**How answer quality is measured:**

| Signal | Timing | Weight |
|--------|--------|--------|
| Agreement with other doctors on the same question | Immediate | 50% |
| Golden set accuracy (questions with known textbook answers) | Immediate | 30% |
| Downstream agent improvement (did the updated edge improve agent performance?) | Weeks | 20% |

**Golden sets are easy with micro-questions:** "What is the normal range for serum creatinine in adult males?" has a definitive answer. 15–20% of questions are golden sets — the doctor never knows which ones.

**Reputation gates question volume and pay multiplier:**

```
reputation_multiplier:
  0.0–0.3: suspended (too many wrong answers)
  0.3–0.5: 0.5× pay, limited question volume (probation)
  0.5–0.7: 1.0× pay, standard volume
  0.7–0.9: 1.5× pay, priority access to high-value questions
  0.9–1.0: 2.0× pay, free-text questions unlocked, influence on question generation
```

### Anti-Gaming for Micro-Questions

| Attack | Defense |
|--------|---------|
| Doctor answers randomly to farm volume | Golden sets catch this immediately; random answers on multiple-choice have ~25% accuracy, golden set threshold is 70% |
| Doctor always picks the same answer (e.g., always "a") | Pattern detection; questions are randomized in option order |
| Bot answers questions (no real doctor) | Golden sets + CAPTCHA on suspicious patterns + credential re-verification |
| Doctor creates multiple accounts | One credential per NPI/license; duplicate detection |
| Doctors collude on answers | Questions are randomized across doctors; answer order shuffled; same question sent to doctors across different validators |

### Incentive Flow Summary

```
Agent produces clinical opinion
    → Question Engine LLM identifies uncertainty in agent's reasoning
    → Generates 2–5 targeted micro-questions
    → Questions routed to 3–10 matched doctors via validator portals
    → Doctors answer (10–30 sec each, $0.05–0.50 per answer)
    → Validator aggregates answers → consensus
    → Consensus updates body knowledge graph (edge confidence, thresholds)
    → Agents re-scored against improved knowledge graph
    → Validator submits weight vector to chain
    → TAO emissions:
        41% → miners (agents aligned with doctor-validated knowledge earn more)
        41% → validators (consensus-aligned validators earn more)
            └─ validators pay doctors per answer from their allocation
        18% → HealthMesh subnet (funds Question Engine, oracle, development)
```

### Why Doctors Will Participate

- **Zero friction**: answer questions on your phone while waiting for coffee. 10 seconds per question. No case review, no context loading, no documentation.
- **Paid per answer**: like a medical trivia game that pays. $0.05–0.50 per answer, accumulating passively throughout the day.
- **CME credits**: partnership with CME accreditors. Answering clinical micro-questions counts toward continuing education hours. Doctors already pay for CME — this pays them instead.
- **No liability**: doctors answer general medical knowledge questions, not patient-specific cases. No doctor-patient relationship. No diagnosis. No malpractice exposure.
- **Shape the AI**: high-reputation doctors directly influence which knowledge edges the AI relies on. For physicians who care about AI safety in medicine, this is a way to ensure the AI in their specialty gets it right.
- **Research value**: aggregate question-answer patterns across specialties are publishable. Academic physicians can co-author papers on clinical knowledge consensus.

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

Domain experts on the micro-question marketplace answer **general domain knowledge questions**, not patient/client-specific cases:

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
- Liability waiver: "Consilium, its operators, miners, validators, and expert contributors bear no liability for decisions made based on information displayed in the system."

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
4. **Insurance**: obtain errors & omissions (E&O) insurance for the subnet operator. Not because liability is expected, but as a prudent business measure.

---

## Economic Sustainability

TAO emissions fund the network initially, but long-term sustainability requires real revenue.

### Revenue Sources

| Source | Model | Phase |
|--------|-------|-------|
| **User subscriptions** | Free tier (local-only, no mesh, no expert validation) + Pro tier ($29/month for mesh consultations + expert-validated knowledge) | Phase 3+ |
| **Enterprise API** | Organizations pay for access to anonymized, aggregated knowledge intelligence (population-level patterns, not individual data) | Phase 5+ |
| **Research partnerships** | Organizations pay for anonymized federated analytics queries — no raw data shared | Phase 5+ |
| **Expert marketplace fees** | Validators compete for expert talent; Consilium takes a platform fee on expert compensation | Phase 4+ |
| **Appliance sales** | Consilium Home Unit ($399–499) with 1 year Pro subscription included | Phase 6+ |

### Unit Economics (Estimated)

```
Per patient node per month:
  Infrastructure cost: ~$5 (Neo4j + Ollama on modest hardware)
  Mesh query cost:     ~$2 (10 queries × $0.20 per query in LLM inference)
  Doctor micro-questions: ~$1 (20 questions generated × $0.05 avg per answer)
  Question Engine LLM:  ~$0.50 (generating questions from reasoning gaps)
  Total cost:          ~$8.50/month

Revenue needed per patient: $29/month subscription covers costs with healthy margin
Break-even at scale: ~500 paying subscribers = ~$14.5K/month revenue
```

**Doctor costs are dramatically lower with micro-questions:**
- Old model: $10–50 per full case review → ~$10/patient/month
- New model: $0.05–0.50 per micro-question → ~$1/patient/month
- 10× cheaper per unit of doctor knowledge acquired

---

## Tech Stack

| Component | Technology | Why |
|-----------|-----------|-----|
| Health graph | Neo4j (local, Community Edition) | Mature graph DB, excellent traversal, FHIR-compatible |
| Body knowledge graph | Hetionet (Neo4j native, ~47K nodes) + Uberon/FMA (OWL→Cypher) | Pre-loaded anatomy/physiology, confidence-tiered. Note: SPOKE requires UCSF access — evaluate feasibility. Combined graph is large; may need separate Neo4j instance or lazy-loading for local deployments. |
| Local LLM | Ollama + qwen3:14b (Q4_K_M quantization) | Runs on MacBook with 24GB RAM in quantized form. Full precision requires 32GB+. Performance impact of quantization on clinical reasoning needs benchmarking. |
| Specialist agents | Python + LangGraph or custom (MiroFish agent framework) | Stateful agent orchestration, extends MiroFish/OASIS multi-agent architecture |
| Agent memory | Written back to Neo4j | Persistent, queryable, part of the graph |
| Data ingestion | Python (PyMuPDF, HL7, Apple Health parser) | PDF + standard medical formats |
| Wearable sync | Oura API, Apple HealthKit, FHIR | Official APIs |
| Mesh protocol | libp2p (same as IPFS) | Mature P2P networking, DHT built-in |
| Bittensor subnet | bittensor SDK + Yuma Consensus | Incentive layer; validators run automated scoring + expert portal |
| Expert credentialing | Domain-specific credential oracles (NPPES for wellness, FINRA for finance, state bars for legal) + optional on-chain SBT | Verified credentials; expert never needs crypto |
| Question Engine | LLM analyzing agent reasoning chains → generates targeted micro-questions | Active learning for knowledge graph; identifies and fills knowledge gaps |
| Expert question portal | React PWA (mobile-first) hosted by validators | Micro-question interface, Stripe payments, 10-sec answer flow |
| User frontend | React + local web server | Dashboard on localhost |
| Privacy (Phase 4) | SGX / TDX TEE, ZK-SNARKs, k-anonymity | Industry standard for confidential compute + re-identification prevention |

---

## Hardware & Deployment Strategy

### Phase 1 Target: MacBook (developer + early adopter)

The initial deployment target is a MacBook (Apple Silicon, 16GB+ RAM). All components — Neo4j, Ollama, the agent framework, and the patient dashboard — run locally via Docker Compose behind a native macOS app.

**Minimum specs:**
- Apple Silicon Mac (M1 or later)
- 16 GB RAM (24 GB+ recommended for qwen3:14b quantized)
- 50 GB free disk (Neo4j + body knowledge graph + model weights)
- macOS 13+

**User experience target:** The user downloads a `.dmg`, installs the app, and sees a dashboard. They never interact with Docker, Neo4j, Ollama, or a terminal. The app handles all infrastructure invisibly — similar to how the Docker Desktop app hides containers behind a GUI, or how Obsidian hides its data store behind a note-taking UI.

### Phase 2 Target: Consilium Appliance (mass market)

A pre-configured hardware appliance that plugs into the home network. The user connects it to power and Wi-Fi, opens a web dashboard on their phone or laptop, and starts adding data.

```
Consilium Home Unit
├── Hardware: ARM SBC (Raspberry Pi 5 / Orange Pi 5+) or Intel NUC
│   - 8 GB+ RAM, 256 GB SSD, Wi-Fi + Ethernet
│   - Estimated BOM cost: $150–250
│   - Retail price: $399–499 (includes 1 year of Pro subscription)
│
├── Pre-installed software:
│   - Neo4j Community Edition (health graph + body knowledge graph)
│   - Ollama + quantized medical LLM (pre-downloaded)
│   - Agent framework + all specialist agents
│   - Patient dashboard (web UI on local network)
│   - Mesh gateway + Bittensor miner (opt-in)
│   - Auto-update service (pulls updates from HealthMesh repo)
│
├── User experience:
│   - Plug in, connect to Wi-Fi (via phone app or display)
│   - Open healthmesh.local in any browser on home network
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
| **1** | Health graph schema + Neo4j setup + manual data entry | Graph populated with your health data |
| **2** | Body knowledge graph import — Hetionet/SPOKE + confidence tiers | Agents have physiological context to reason against |
| **3** | Data ingestors — Apple Health, Oura, PDF labs | Graph auto-updated from real sources |
| **4** | Local specialist agents — query graph, write opinions | Working local consilium |
| **5** | Consilium workflow — multi-round debate, synthesis | Agents consult each other on your data |
| **6** | Proactive monitoring — scheduled agent checks, alerts | Agents watch your data continuously |
| **7** | Mesh protocol — P2P consultation requests | Your node asks others for opinions |
| **8** | Bittensor subnet registration + miner interface | Agent nodes earn TAO for quality opinions |
| **9** | Expert credentialing oracle (multi-domain) + SBT issuance | Verified experts can join as knowledge contributors |
| **10** | Question Engine + expert question portal (mobile PWA) | LLM generates micro-questions, experts answer on phone |
| **11** | Knowledge graph feedback loop — answers update edge confidence | Expert answers improve agent reasoning automatically |
| **12** | Expert reputation system + pay-per-answer billing | Quality control on expert answers, Stripe payouts |
| **13** | macOS native app — one-click install, no terminal | Non-technical MacBook users can onboard |
| **14** | Consilium Appliance — hardware design, manufacturing, distribution | Mass-market plug-and-play device |
| **15** | Privacy layer — TEE, pseudonymous IDs, ZK proofs | Production-grade privacy |

---

## What This Looks Like for the User

You open the app and see:

```
Your Consilium — Wellness Domain — Last updated 2 hours ago

  FOR EDUCATIONAL EXPLORATION ONLY — Discuss with your doctor

🟡 EXPLORE: Kidney & Renal Patterns
   "Your eGFR readings show a downward trend (55 → 52 since October).
   The knowledge graph shows several connections worth exploring.
   8 other mesh nodes with similar patterns highlighted these as
   common discussion topics with healthcare providers:
   • What does my eGFR trend mean long-term?
   • Should I ask about a urine albumin test?
   • Is a nephrology referral worth discussing?"

🟢 STABLE: Cardiovascular Patterns
   "Your BP readings from Apple Watch have been consistent.
   No unusual ECG patterns detected in 30 days."

🟢 STABLE: Metabolic Patterns
   "HbA1c reading of 7.2% — within commonly cited target ranges.
   Worth noting: the knowledge graph connects kidney trends
   with metformin management — a topic for your next visit."

📋 Explore a topic with your consilium:
   > "What connections exist between fatigue and kidney function?"

  ℹ️ Consilium helps you explore and learn. It does not provide
     medical advice. Always consult your healthcare provider.
```

---

## Key Difference From Everything Else

| System | Data location | Memory | Multi-agent | Privacy | Expert-validated knowledge | Incentive-aligned |
|--------|--------------|--------|------------|---------|--------------------------|-------------------|
| ChatGPT / Gemini | Cloud | None | No | None | No | No |
| Apple Health / Fitbit | Apple's cloud | Limited | No | Partial | No | No |
| WebMD / Mayo Clinic | N/A (static) | None | No | N/A | Editorial only | No |
| Your professional (doctor, CPA, lawyer) | Their systems | Yes (fragmented) | Sometimes | Regulated | Yes (1 person) | Fee-for-service |
| Safe Scan (SN76) | Cloud | Model weights | No (single task) | Partial | No | TAO |
| **Consilium** | **Your machine** | **Complete, lifelong** | **Yes, permanent** | **By design** | **Yes, expert micro-question consensus** | **TAO + fiat** |

---

## Open Questions and Risks

Issues that need resolution before mainnet launch. Honest assessment of what's unsolved.

### Technical Risks

| Risk | Severity | Mitigation |
|------|----------|-----------|
| **LLM clinical reasoning quality**: Current open-source models (qwen3:14b quantized) may not match GPT-4-class clinical reasoning. If agent opinions are consistently poor, doctors will rate everything low and the incentive loop breaks. | High | Benchmark agent quality against HealthBench rubrics before launch. If quality is below threshold, consider using a larger model via API (Chutes/OpenRouter) for clinical reasoning while keeping data local. The local graph provides context the LLM wouldn't otherwise have — this may compensate for model size. |
| **Body knowledge graph size**: Combined Hetionet + Uberon + FMA could be 100M+ edges. Running this on a consumer laptop alongside personal data may be impractical. | Medium | Lazy-load strategy: only import subgraphs relevant to the patient's conditions. A patient with CKD + diabetes loads renal + endocrine subgraphs, not the entire human anatomy. |
| **Neo4j Community Edition limits**: No clustering, no role-based access control. Adequate for single-user local deployment but may need Enterprise for validator-scale operations. | Low | Local deployment uses Community Edition. Validators use Enterprise or switch to a different graph DB for aggregation. |

### Product Risks

| Risk | Severity | Mitigation |
|------|----------|-----------|
| **Expert cold start**: Without experts answering micro-questions, the knowledge graph doesn't improve. Without a good knowledge graph, agents are mediocre. Without good agents, users don't get value. | Critical | CME credit partnership for wellness domain is the best lever. Micro-questions are much lower friction than case reviews — target 50 experts across 3 domains before subnet launch. Run a closed beta with 20 users + 10 experts for 3 months. |
| **User data entry burden**: Most people don't have their data in digital form. Manual entry is tedious and error-prone. | High | Prioritize automatic integrations (Apple Health, wearables, FHIR patient portals). For documents, LLM-powered PDF/photo parsing. Make data entry the smallest possible barrier. |
| **"AI doctor/lawyer/advisor" perception**: Media may frame Consilium as "replacing professionals with AI" regardless of the actual design. | High | Frame consistently as "knowledge exploration and educational entertainment." Never use words like diagnose, advise, recommend. The domain-agnostic framing is the strongest defense — it's clearly a general knowledge tool, not a professional service. |

### Regulatory Risks

| Risk | Severity | Mitigation |
|------|----------|-----------|
| **FDA attempts SaMD classification despite general-purpose framing**: unlikely but possible if wellness domain usage dominates and outputs look clinical. | Medium | Domain-agnostic architecture is the primary defense. UI language enforcement (no imperatives, no recommendations). Optional: proactive FDA Pre-Submission for written confirmation. Engage healthcare regulatory attorney ($10–25K). |
| **Expert licensing concerns**: Experts answering general knowledge questions across borders could raise cross-jurisdictional licensing questions. | Low | Experts answer general knowledge questions, not case-specific queries. No professional-client relationship. Same legal framing as Stack Overflow, Quora, or medical textbook authorship. |
| **Bittensor regulatory uncertainty**: TAO token and subnet emissions may face securities regulation scrutiny. | Medium | Not unique to Consilium — affects all Bittensor subnets. Monitor SEC/CFTC guidance on utility tokens. |

### Questions for Further Research

1. **Model benchmarking**: What is the minimum model size / capability threshold for knowledge graph reasoning that experts would validate as "acceptable"? Is qwen3:14b-Q4 sufficient, or does this require 70B+ models?
2. **Expert engagement curve**: Will experts sustain interest in answering micro-questions long-term, or does novelty wear off? Need progression mechanics (unlock harder/higher-paying question types as reputation grows).
3. **Question Engine quality**: Can the LLM reliably identify the most informative questions to ask? Bad questions waste expert time and budget. Need to measure information gain per question and optimize the generator.
4. **Federated pattern detection**: Has anyone implemented the "pattern ballot + boolean attestation" protocol at scale? What are the failure modes?
5. **Graph import engineering**: How much work is the Hetionet + Uberon → unified schema merge? Is this weeks or months of data engineering?
6. **Appliance hardware selection**: Raspberry Pi 5 (4/8GB) vs Intel NUC vs custom ARM board? Need to benchmark Neo4j + Ollama + quantized LLM on candidate hardware. Can a Pi 5 with 8GB run qwen3:8b quantized with acceptable latency for agent reasoning?
7. **Multi-domain knowledge graphs**: What are the best foundational KGs for finance, legal, and engineering domains? Do equivalents of Hetionet exist for non-medical fields?
8. **Legal review**: Engage a regulatory attorney to stress-test the "educational entertainment" framing specifically. What language triggers concern? What language is safe?
