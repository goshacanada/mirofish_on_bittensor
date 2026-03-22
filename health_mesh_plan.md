# HealthMesh — Decentralized Personal Health Consilium

**Vision:** Every person runs a permanent AI medical council on their own computer. Their complete health history lives in a local graph database. When a complex case needs broader input, the system consults a mesh of other instances — each running for other people — receiving medical opinions without ever sharing personal data. The result is a global network of AI specialists with knowledge from millions of cases, serving millions of individuals, where no one's data ever leaves their machine.

**Relationship to MiroFish:** HealthMesh is a **dedicated Bittensor subnet** — separate from the MiroFish opinion intelligence subnet (see `mirofish_bittensor_plan.md`). The two subnets share lineage: both extend the MiroFish/OASIS multi-agent simulation framework for persistent agent populations with graph-backed memory. But they are distinct networks with distinct incentive mechanisms, validators, and emission streams:

| | MiroFish Subnet | HealthMesh Subnet |
|---|---|---|
| Domain | General opinion intelligence (finance, geopolitics, tech) | Medical health intelligence |
| Miners | Agent populations running opinion simulations | Agent populations running clinical consiliums |
| Validators | Automated scoring (diversity, consistency, calibration) | Automated scoring + credentialed physician review |
| Graph | Topic-domain knowledge graph | Human body knowledge graph + personal health graph |
| Regulation | Minimal | FDA SaMD, HIPAA, international medical regulation |
| TAO registration | Separate subnet (own netuid) | Separate subnet (own netuid) |

The separation is necessary because: (1) medical AI requires credentialed human validation that general opinion subnets don't, (2) regulatory obligations (FDA, HIPAA) would burden the general subnet unnecessarily, and (3) the incentive mechanism is fundamentally different — health opinions are scored by doctors against clinical rubrics, not by automated diversity metrics.

---

## Core Principles

1. **Local first** — your health graph never leaves your device
2. **Opinions travel, not data** — the mesh shares reasoning, not records
3. **Graph-native** — all health knowledge is modeled as a graph, enabling holistic reasoning across conditions, time, and body systems
4. **Persistent consilium** — your specialist team remembers everything, forever
5. **Open mesh** — anyone can run a node; more nodes = richer collective knowledge

---

## System Overview

```
Your Machine
├── Health Graph (Neo4j)        ← your complete medical history
├── Specialist Agent Team       ← your permanent AI doctor council
├── Data Ingestor               ← pulls from wearables, labs, doctor notes
├── Local LLM (Ollama)          ← inference stays on your hardware
└── Mesh Gateway                ← sends anonymized questions, receives opinions

Mesh Network (other people's machines)
├── Node A: Alice's instance    ← runs her own consilium, answers anonymized queries
├── Node B: Bob's instance      ← same
├── Node C: ...                 ← thousands of nodes worldwide
└── Each node: learns from its own cases, shares only medical opinions
```

When your local agents face a hard case, the Mesh Gateway asks the network:
> *"Patient has elevated creatinine trending up over 8 months, concurrent metformin use, HbA1c 7.2, eGFR declining — what does your nephrology agent think?"*

No name. No age. No location. Just the medical facts needed for an opinion.

---

## Knowledge Foundation — Human Body Graph

Before personal health data is added, the system is pre-loaded with a **foundational body knowledge graph** — a map of organs, systems, biomarkers, and their relationships. Agents reason against this foundation to interpret personal data in physiological context.

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

## Phase 2 — Local Specialist Agent Team

### 2.1 Specialist Roles

Each person's local consilium includes a permanent team of specialist agents. They are instantiated as LLM personas with access to the health graph.

| Agent | Specialty | Watches for |
|-------|-----------|-------------|
| `GeneralistAgent` | Primary care, triage | Overall health, flags for specialists |
| `CardiologyAgent` | Heart, vascular | ECG anomalies, BP trends, cardiac risk |
| `NephrologyAgent` | Kidneys | eGFR decline, creatinine, electrolytes |
| `EndocrinologyAgent` | Hormones, metabolism | HbA1c, thyroid, glucose, insulin resistance |
| `NeurologyAgent` | Brain, nervous system | Sleep quality, HRV, cognitive markers |
| `HematologyAgent` | Blood | CBC trends, hemoglobin, inflammation markers |
| `PulmonologyAgent` | Lungs, respiratory | SpO2 trends, sleep apnea indicators |
| `GastroAgent` | Digestive system | Liver enzymes, gut microbiome markers |
| `PharmacologyAgent` | Drug interactions | Monitors all medications for conflicts |
| `PreventionAgent` | Risk modeling | Projects future risks from current trends |

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

### What Doctors Actually Review

Doctors receive **anonymized opinion packages** — never the patient's identity:

```json
{
  "case_id": "case_8f3a...",
  "specialty": "nephrology",
  "anonymized_context": {
    "conditions": ["Type 2 Diabetes (8 years)", "Hypertension"],
    "medications": ["Metformin 1000mg", "Lisinopril 10mg"],
    "labs": { "eGFR_trend": "68→61→58→55→52 over 20 months", "creatinine": "1.8 mg/dL", "Hgb": "10.2" },
    "wearables": { "avg_hrv": "38ms declining", "avg_sleep": "5.8h" }
  },
  "agent_opinion": "eGFR decline rate of ~4 pts/year in diabetic is concerning.
                    Anemia of CKD likely. Recommend: urine ACR, iron studies, PTH.
                    Metformin review at eGFR <45. Nephrology referral within 3 months.",
  "rubric": [
    { "criterion": "Correctly identified anemia of CKD as likely cause", "weight": 0.25 },
    { "criterion": "Cited appropriate eGFR threshold for metformin review (45 mL/min)", "weight": 0.20 },
    { "criterion": "Recommended appropriate next tests (ACR, iron, PTH)", "weight": 0.30 },
    { "criterion": "Urgency framing appropriate (non-urgent but timely)", "weight": 0.15 },
    { "criterion": "No unsafe recommendation included", "weight": 0.10 }
  ]
}
```

**Rubric generation — a key design challenge:**

Rubrics cannot be fully pre-built because every clinical case is unique. The system uses a **hybrid approach**:

1. **Template rubrics per specialty** (pre-authored by physician committees): generic criteria that apply to most cases in a specialty. Example for nephrology: "Correctly identified stage of CKD", "Recommended appropriate monitoring frequency", "No unsafe medication recommendation given renal function."

2. **Case-specific rubric augmentation** (auto-generated per case): an LLM generates 2–4 additional criteria specific to the case details. Example: "Recognized metformin dose adjustment needed at eGFR <45." These auto-generated criteria are flagged as machine-generated.

3. **Rubric validation loop**: every 100th case, a senior physician reviews the auto-generated rubric criteria and flags bad ones. Bad criteria are fed back to improve the generator. Over time, the auto-generated criteria converge toward physician quality.

4. **Doctor free-text override**: doctors can always add "criterion not covered by rubric" with their own assessment. These free-text additions are reviewed and potentially promoted to template criteria.

Doctors mark each criterion: met / partially met / not met, optionally adding a note. This produces a structured score vector, not just a free-text opinion.

### How Scores Flow From Doctors to Chain

Validators are the intermediary between human doctors and the Bittensor chain:

```
For each AI agent (miner), per tempo:

1. Validator selects cases for review and routes to matched doctors
2. Doctors submit ratings via the review portal (web UI)
3. Validator collects all doctor ratings received this tempo
4. Validator software aggregates:
   a. Compute weighted criterion scores (per-criterion, weighted by criterion importance)
   b. Apply specialty weighting (in-specialty doctors weighted 2x)
   c. Apply doctor reputation multiplier (explained below)
   d. Blend with automated scoring signals:
      - Guideline adherence check (automated, rule-based)
      - Drug interaction safety check (automated, database lookup)
      - Internal consistency check (does the opinion contradict itself?)
5. Produce final score per miner: 0.0 → 1.0
6. Normalize across all miners → weight vector → submit to chain

Yuma Consensus then:
- Clips outlier validator scores toward the median (penalizes collusion)
- Distributes TAO emissions:
  41% → miners (proportional to aggregated scores)
  41% → validators (proportional to stake × consensus alignment)
  18% → subnet owner (HealthMesh protocol)
- Validators allocate a portion of their 41% to pay doctors
```

**Automated vs. human scoring split:** Not every case needs a doctor. The validator runs automated checks on 100% of miner outputs (safety, guideline adherence, internal consistency). Only a sampled subset (10–30%) goes to human doctors for full rubric review. The sampling is biased toward: complex cases, cases where automated scores are ambiguous, and cases from miners with unstable score histories. This keeps doctor workload manageable while maintaining quality.

### Doctor Reputation and Grading

Doctor ratings are themselves scored by the validator. A doctor who consistently rates well earns higher reputation — which multiplies their per-case compensation and their weight in score aggregation.

**How doctor quality is measured:**

| Signal | Timing | Weight |
|--------|--------|--------|
| Consensus with other credentialed doctors reviewing the same case | Immediate | 40% |
| Golden set accuracy (known-correct cases injected blind) | Immediate | 30% |
| Proxy outcome alignment (did the recommended test confirm the hypothesis?) | Weeks–months | 20% |
| Long-term retrospective outcome (patient trajectory) | Months–years | 10% |

**Golden sets:** The HealthMesh protocol maintains a curated library of cases with physician-consensus ground truth (analogous to HealthBench's 48K rubric criteria). 10–20% of cases sent to doctors are golden sets — the doctor doesn't know which ones. This calibrates and audits every doctor in real time. Golden sets are stratified by specialty and difficulty.

**Consensus scoring:** Each case is sent to 3–5 doctors in the same specialty. Inter-doctor agreement is the primary short-term signal. A doctor who consistently agrees with the credentialed specialist majority earns high reputation; a doctor whose scores are outliers triggers review. Importantly, consensus is measured across validators — if Validator A's doctors and Validator B's doctors consistently agree, both validators gain consensus alignment in Yuma Consensus.

**The reputation score** gates per-case compensation:

```
Effective compensation = base_rate × reputation_multiplier

reputation_multiplier:
  0.0–0.4 reputation: 0.5× (half pay — probation period)
  0.4–0.7 reputation: 1.0× (standard rate)
  0.7–0.9 reputation: 1.5× (senior reviewer bonus)
  0.9–1.0 reputation: 2.0× (expert reviewer bonus)

base_rate: set by each validator independently (market-driven)
  estimated range: $10–50 per case review, depending on specialty and complexity
```

Doctors below 0.3 reputation for 30 consecutive days are suspended pending review.

### The Delayed Ground Truth Problem

Not all medical opinions can be graded immediately — outcomes arrive weeks, months, or years later. The system operates on **three time horizons**:

**Immediate (same-tempo) — 70% of validator reward allocation:**
- Automated scoring: safety checks, guideline adherence, internal consistency
- Doctor consensus: agreement across 3–5 reviewers on the same case
- Golden set accuracy: performance on known-correct cases

**Medium-term (weeks to months) — 20% held by validator:**
- Proxy outcome signals: did the recommended test confirm the hypothesis?
- Example: agent recommended urine ACR test → patient uploads ACR result 6 weeks later → agent's prediction of albuminuria was confirmed
- Validators that incorporated doctor ratings aligned with proxy outcomes earn retrospective reputation boost

**Long-term (months to years) — 10% reputation adjustment:**
- Retrospective outcome tracking: did the patient trajectory match the agent's prediction?
- This does NOT use escrow (too complex for a doctor payment system). Instead, it adjusts **doctor reputation scores** retrospectively, which affects future earning multipliers.

**Ground truth sources (patient-controlled, never mandatory):**
- Patient uploads subsequent lab results
- Patient marks "outcome confirmed" on a consilium case
- Patient's future agent opinions reference back to this case

**If no ground truth arrives:** medium-term allocation is released at the consensus rate after 6 months. No penalty for unavailable outcomes — the system accepts that most cases will never have confirmed outcomes.

### Prediction Market Layer (Advanced)

For complex or contested cases, the subnet runs a lightweight prediction market alongside standard validation:

```
Doctor A says: "eGFR will reach Stage 4 within 12 months" — stakes 10 TAO
Doctor B says: "eGFR will stabilize with current treatment" — stakes 10 TAO
Doctor C says: "Stage 4 within 18–24 months" — stakes 5 TAO

Outcome (patient uploads eGFR 13 months later: 42 → Stage 3b, not Stage 4):
  Doctor B partially right — recovered 8 TAO
  Doctor C closest — recovered 9 TAO
  Doctor A wrong — lost stake
```

Prediction markets elicit calibrated probabilistic beliefs rather than forced binary ratings. They are opt-in for doctors who want higher-stakes participation.

### Anti-Gaming Mechanisms

| Attack | Defense |
|--------|---------|
| Doctor rates randomly to collect base pay | Golden set detection; low consensus → low reputation → lower compensation |
| Doctor creates multiple accounts | One credential per verified license; NPI/license number is unique; oracle checks for duplicates |
| Sybil attack via fake licenses | Oracle verifies against real medical board registries; license number checked for uniqueness and active status |
| Doctors collude to inflate a specific agent | Cases are randomly assigned; doctors don't know which miner produced the opinion; cross-validator consensus penalizes coordinated outliers |
| Agent operator bribes doctors | Doctors don't know which miner produced the opinion they're reviewing; assignment is random per case |
| Validator fabricates doctor ratings | Cross-validator consensus: if Validator A's scores consistently diverge from Validators B, C, D (each using independent doctor pools), Yuma Consensus clips Validator A |
| Validator runs without real doctors (all automated) | Golden sets with known-tricky edge cases designed to fool automated scoring but not real physicians; validators whose golden set patterns look automated are flagged |
| Out-of-specialty doctor reviews nephrology cases | Credential specialty field matched at routing; out-of-specialty ratings receive 0.5× weight |

### Incentive Flow Summary

```
Miner node produces clinical opinion
    → Validator receives the opinion
    → Validator runs automated scoring (safety, guidelines, consistency)
    → Validator routes 10–30% of cases to credentialed doctors via review portal
    → Doctors review anonymized case + agent opinion against rubric (web UI)
    → Validator aggregates automated + doctor scores
    → Validator submits weight vector via Commit-Reveal to chain
    → Yuma Consensus runs across all validators
    → TAO emission this tempo:
        41% → miner nodes (proportional to aggregated scores)
        41% → validators (proportional to stake × consensus alignment)
            └─ validators pay doctors from this allocation (variable %)
        18% → HealthMesh subnet (funds oracle, rubric library, development)
```

**Validator economics:** A validator's main costs are (1) doctor compensation, (2) infrastructure, and (3) TAO stake opportunity cost. Validators compete for doctors by offering competitive per-case rates. Validators with higher-quality doctor pools produce weight vectors more aligned with consensus → earn more TAO → can afford to pay doctors more. This creates a virtuous cycle.

### Why Doctors Will Participate

- **Revenue**: $10–50 per case review (set by the validator market). A high-reputation specialist reviewing 15–20 cases/day = $150–1000/day in supplemental income. Paid in fiat via Stripe — no crypto knowledge needed.
- **CME credits**: partnership with CME accreditors means reviewing AI diagnostic opinions counts toward mandatory continuing education hours. Doctors already pay $500–2000/year for CME — HealthMesh makes it paid instead.
- **Low friction**: reviewing a pre-structured rubric for an anonymized case takes 5–10 minutes. No scheduling, no patient contact, fully asynchronous. Works on mobile.
- **No liability**: doctors are rating AI agent opinions on anonymized contexts — they are not treating patients and carry no clinical or malpractice liability.
- **Specialty impact**: cardiologists directly shape which cardiology agents survive and which die. The best agents for their specialty win, improving care globally. This is meaningful for physicians who care about AI safety in medicine.
- **Research opportunities**: the dataset of doctor-vs-AI agreement patterns is publishable. Academic physicians can co-author papers on AI diagnostic quality in their specialty.

---

## Phase 4 — Privacy Layer (Future)

*Designed from the start to support this — not required for local-only operation.*

- HMAC-based pseudonymous document IDs (different ID per document, linkable only by patient)
- TEE (Trusted Execution Environment) for mesh nodes — opinions computed in secure enclaves
- Differential privacy on query patterns — prevent inference from query frequency
- Zero-knowledge proofs for reputation scoring — prove a node gave good opinions without revealing what the opinion was about
- k-anonymity enforcement on mesh queries (see re-identification risk in Phase 3)

---

## Regulatory Considerations

**This section is critical. HealthMesh operates at the intersection of medical AI, decentralized networks, and personal health data — all heavily regulated domains.**

### FDA / Software as a Medical Device (SaMD)

HealthMesh agents provide clinical recommendations ("request urine albumin test", "consider nephrology referral within 3 months"). This likely makes it a **Software as a Medical Device (SaMD)** under FDA regulation and EU MDR.

**Mitigation strategies:**

| Strategy | Trade-off |
|----------|-----------|
| **Informational framing**: all agent outputs labeled "educational information, not medical advice" | Weakest defense; FDA has rejected this framing for AI tools that clearly influence clinical decisions |
| **Clinical decision support (CDS) exemption**: if the system presents supporting data but the physician makes the decision, it may qualify for CDS exemption under 21st Century Cures Act | Requires careful design — agents must present evidence and reasoning, not direct recommendations |
| **Wellness device classification**: if limited to wearable trend monitoring without diagnostic claims | Only works for Phase 1 (local health graph); breaks once agents make clinical assertions |
| **Staged regulatory path**: launch Phase 1–2 as a wellness/data-organization tool (no regulatory burden); pursue FDA 510(k) or De Novo pathway before launching agent recommendations | Most realistic; delays agent features but avoids enforcement risk |

**Recommendation:** Design Phase 1–3 to stay within the CDS exemption by presenting evidence and citations to the user's own physician, not direct treatment recommendations. Pursue formal FDA engagement (Pre-Submission meeting) before Phase 4+ mesh features.

### HIPAA and Health Data Privacy

- **Local-only data**: HIPAA applies to "covered entities" (providers, insurers) and their "business associates." A personal health tool running on a user's own device is generally NOT a covered entity. HealthMesh in local-only mode likely falls outside HIPAA.
- **Mesh queries**: If anonymized contexts transit the network, they may constitute de-identified health information under the HIPAA Safe Harbor standard (18 identifiers removed). The k-anonymity enforcement in Phase 3 strengthens this position.
- **Doctor review portal**: If doctors are reviewing clinical cases (even anonymized), the validator operating the portal may be a business associate depending on jurisdiction. Legal review needed.

### International Regulations

| Jurisdiction | Key regulation | Impact |
|-------------|---------------|--------|
| EU | MDR (Medical Device Regulation) + GDPR | MDR classification likely required for agent features; GDPR applies to any EU patient data even if processed locally |
| UK | MHRA AI as Medical Device guidance | Similar to FDA SaMD; UK has a more flexible regulatory sandbox |
| Canada | Health Canada SaMD guidance | Aligns with FDA framework |
| Australia | TGA | Tiered risk classification |

### Liability

**Who is liable when an agent gives bad advice?**

- The agent explicitly does NOT treat patients. It provides information to the patient's own consilium.
- The mesh opinions are explicitly labeled as "opinions from AI agents, not medical advice."
- Doctors reviewing AI outputs are rating quality, not treating patients — no doctor-patient relationship exists.
- **Open question:** If a patient follows an agent's recommendation and is harmed, is the subnet owner liable? Is the miner operator? Is the LLM provider? This is uncharted legal territory for decentralized medical AI. Legal review required before mainnet launch.

**Mitigation:** Prominent disclaimers, terms of service, and liability waivers. Design UI to direct patients to their own physicians rather than acting independently on agent recommendations.

---

## Economic Sustainability

TAO emissions fund the network initially, but long-term sustainability requires real revenue.

### Revenue Sources

| Source | Model | Phase |
|--------|-------|-------|
| **Patient subscriptions** | Free tier (local-only, no mesh, no doctor validation) + Pro tier ($20–50/month for mesh consultations + doctor-validated agent opinions) | Phase 3+ |
| **Enterprise API** | Hospitals, insurance companies, pharma companies pay for access to anonymized, aggregated health intelligence (population-level trends, not individual data) | Phase 5+ |
| **Research partnerships** | Pharmaceutical companies pay for anonymized cohort queries ("how many nodes have patients on Drug X with biomarker Y trend?") — federated analytics, no raw data shared | Phase 5+ |
| **CME accreditation fees** | CME accreditors pay HealthMesh (or vice versa) for the doctor review platform as a CME delivery mechanism | Phase 4+ |
| **Doctor review marketplace** | Validators compete for doctor talent; HealthMesh takes a platform fee on doctor compensation | Phase 4+ |

### Unit Economics (Estimated)

```
Per patient node per month:
  Infrastructure cost: ~$5 (Neo4j + Ollama on modest hardware)
  Mesh query cost:     ~$2 (10 queries × $0.20 per query in LLM inference)
  Doctor validation:   ~$10 (2 doctor-reviewed cases × $5 per case)
  Total cost:          ~$17/month

Revenue needed per patient: $20–50/month subscription covers costs + margin
Break-even at scale: ~1000 paying subscribers = $20K–50K/month revenue
```

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
| Bittensor subnet | bittensor SDK + Yuma Consensus | Incentive layer; validators run automated scoring + doctor portal |
| Doctor credentialing | Medical board oracle (NPPES/state APIs) + optional on-chain SBT | Verified credentials; doctor never needs crypto |
| Doctor review portal | React web app hosted by validators | Rubric-based case review, Stripe payments, mobile-friendly |
| Prediction markets | Lightweight on-chain market per contested case (Phase 12) | Calibrates probabilistic physician beliefs; opt-in for doctors |
| Patient frontend | React + local web server | Dashboard on localhost |
| Privacy (Phase 4) | SGX / TDX TEE, ZK-SNARKs, k-anonymity | Industry standard for confidential compute + re-identification prevention |

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
| **9** | Doctor credentialing oracle + SBT issuance | Verified doctors can join as validators |
| **10** | Doctor review UI + rubric engine | Doctors review cases and submit scores |
| **11** | Reward escrow + outcome confirmation | 30% of doctor rewards held until outcomes confirmed |
| **12** | Prediction market layer | High-stakes probabilistic doctor validation |
| **13** | Privacy layer — TEE, pseudonymous IDs, ZK proofs | Production-grade privacy |

---

## What This Looks Like for the User

You open the app and see:

```
Your Health Consilium — Last updated 2 hours ago

🟡 WATCH: Nephrology Agent
   "Your eGFR dropped to 52 (was 55 in October). At current trend,
   you'll reach Stage 4 CKD in ~18 months. I consulted 8 other
   nephrology agents on the mesh — 7/8 agree: request urine albumin
   test and consider nephrology referral. Your GP visit is next week —
   this should be on the agenda."

🟢 OK: Cardiology Agent
   "BP readings from Apple Watch stable. No ECG anomalies in 30 days.
   Lisinopril appears well-tolerated."

🟢 OK: Endocrinology Agent
   "HbA1c 7.2% — within target. Metformin dose appropriate.
   Note: NephrologyAgent flagged eGFR — I agree metformin should
   be reviewed if eGFR drops below 45."

📋 Ask your consilium anything:
   > "Is my fatigue related to my kidneys or something else?"
```

---

## Key Difference From Everything Else

| System | Data location | Memory | Multi-specialist | Privacy | Doctor-validated | Incentive-aligned |
|--------|--------------|--------|-----------------|---------|-----------------|-------------------|
| ChatGPT / Gemini | Cloud | None | No | None | No | No |
| Apple Health Intelligence | Apple's cloud | Limited | No | Partial | No | No |
| MDAgents (paper) | Cloud API | None | Yes | None | No | No |
| Your GP | Their EHR | Yes (fragmented) | Sometimes | HIPAA | Yes (1 doctor) | Fee-for-service |
| Safe Scan (SN76) | Cloud | Model weights | No (single task) | Partial | No | TAO |
| **HealthMesh** | **Your machine** | **Complete, lifelong** | **Yes, permanent** | **By design** | **Yes, multi-doctor consensus** | **TAO + fiat** |

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
| **Doctor cold start**: Without doctors, validators can't produce quality weight vectors. Without quality weights, miners don't improve. Without good miners, patients don't get value. Without patients, there's no network. | Critical | CME credit partnership is the best lever. Target 50 doctors across 5 specialties before subnet launch. Run a closed beta with 20 patients + 10 doctors for 3 months before opening the network. |
| **Patient data entry burden**: Most patients don't have their health data in digital form. Manual entry of lab results, conditions, and medications is tedious and error-prone. | High | Prioritize Apple Health and wearable integrations (automatic). For lab results, LLM-powered PDF parsing of lab reports (take a photo → structured data). Partner with patient health record platforms (e.g., Apple Health Records via FHIR) for automatic import. |
| **"AI doctor" perception**: Media and regulators may frame HealthMesh as "replacing doctors with AI" regardless of the actual design. | High | Frame consistently as "health data organization + AI-assisted monitoring that helps you prepare for doctor visits." Never claim diagnostic or treatment capability. Doctor validation layer actually reinforces the message: real doctors are in the loop. |

### Regulatory Risks

| Risk | Severity | Mitigation |
|------|----------|-----------|
| **FDA SaMD classification**: Agent recommendations may trigger medical device classification, requiring years of regulatory approval. | High | Stay within CDS exemption for initial launch; pursue FDA Pre-Submission before agent recommendation features. |
| **International medical licensing**: Doctors credentialed in one country rating AI opinions for patients in another country raises cross-border medical licensing questions. | Medium | Initial launch limited to US-licensed doctors reviewing anonymized cases (no patient-doctor relationship, no cross-border treatment). Legal review needed for international expansion. |
| **Bittensor regulatory uncertainty**: The TAO token and subnet emissions may face securities regulation scrutiny. | Medium | Not unique to HealthMesh — affects all Bittensor subnets. Monitor SEC/CFTC guidance on utility tokens. |

### Questions for Further Research

1. **Model benchmarking**: What is the minimum model size / capability threshold for clinical reasoning quality that doctors would rate as "acceptable"? Is qwen3:14b-Q4 sufficient, or does this require 70B+ models?
2. **Rubric scalability**: Can auto-generated rubric criteria achieve physician-level quality at scale, or will this always require human curation?
3. **Doctor retention**: What per-case rate is needed to retain doctors long-term? How does this compare to telehealth moonlighting rates ($40–100/hour)?
4. **Federated pattern detection**: Has anyone implemented the "pattern ballot + boolean attestation" protocol at scale? What are the failure modes?
5. **Graph import engineering**: How much work is the Hetionet + Uberon → unified schema merge? Is this weeks or months of data engineering?
6. **Patient willingness**: Will health-conscious consumers actually run Neo4j on their laptops? Or does this need a managed appliance / Raspberry Pi / home server product?
