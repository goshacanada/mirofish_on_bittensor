# HealthMesh — Decentralized Personal Health Consilium

**Vision:** Every person runs a permanent AI medical council on their own computer. Their complete health history lives in a local graph database. When a complex case needs broader input, the system consults a mesh of other instances — each running for other people — receiving medical opinions without ever sharing personal data. The result is a global network of AI specialists with knowledge from millions of cases, serving millions of individuals, where no one's data ever leaves their machine.

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

Over time the mesh itself becomes a source of `exploratory` and `inferred` knowledge:

```
1000 nodes each see: HRV declined before creatinine rose
→ Mesh coordinator observes this pattern across anonymized cases
→ Edge [HRV decline] ──PRECEDES──► [eGFR decline]
  upgraded from exploratory → inferred
  evidence_count: 847 cases
  confidence: 0.71
```

This is federated learning without sharing data — the *pattern* is the shared artifact, not the cases.

---

## Phase 1 — Local Health Graph

### 1.1 Graph Schema

The health graph is the foundation. Everything is a node or an edge.

**Core Node Types:**

| Node | Description | Example |
|------|-------------|---------|
| `Condition` | Diagnosed medical condition | Type 2 Diabetes, Hypertension |
| `Symptom` | Observed symptom | Fatigue, Shortness of breath |
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
| `Timeline` | Temporal anchor for events | — |

**Core Edge Types:**

| Edge | Connects | Meaning |
|------|----------|---------|
| `HAS_CONDITION` | Person → Condition | Diagnosed with |
| `HAS_SYMPTOM` | Person/Condition → Symptom | Experiences / caused by |
| `TAKES` | Person → Medication | Currently taking |
| `TREATS` | Medication → Condition | Medication for condition |
| `MEASURED_AT` | LabResult → Timeline | When measured |
| `RESULT_OF` | LabResult → LabPanel | Part of this panel |
| `INDICATES` | LabResult → Biomarker | Measures this biomarker |
| `SUGGESTS` | Biomarker → Condition | Abnormal value may indicate |
| `CORRELATES_WITH` | Biomarker ↔ Symptom | Statistical correlation |
| `MANAGED_BY` | Condition → Doctor | Under care of |
| `OCCURRED_AT` | Encounter → Timeline | When visit happened |
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

Every measurement is timestamped. The graph supports trend queries:

```cypher
-- Find all eGFR readings in the last 2 years, ordered by time
MATCH (b:Biomarker {name: "eGFR"})<-[:INDICATES]-(r:LabResult)-[:MEASURED_AT]->(t:Timeline)
WHERE t.date > date() - duration({years: 2})
RETURN r.value, t.date ORDER BY t.date
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

### 3.4 Collective Knowledge Without Sharing Data

Over time each node's agents become more knowledgeable because they have seen their own patient's full history. A node that has been running for 10 years for a diabetic patient has deeply informed nephrology, endocrinology, and cardiology agents — because those agents have tracked one real case longitudinally.

The mesh effect: when that node's agents give opinions to other nodes' queries, they draw on that deep case knowledge. Multiply by thousands of nodes, each with years of longitudinal data — the mesh becomes collectively as knowledgeable as a physician who has followed thousands of patients for years.

---

## Bittensor Integration — Doctor Validation Layer

This is the economic engine of HealthMesh. AI agents (miners) produce health opinions. Credentialed doctors (validators) grade those opinions and earn TAO rewards. The quality of a doctor's grading is itself scored over time, creating a self-regulating market for medical expertise.

### Roles in Bittensor Terms

```
Miners     = HealthMesh AI agent nodes
             → produce clinical opinions in response to anonymized queries
             → earn TAO proportional to how well doctors rate their opinions

Validators = Credentialed medical doctors
             → review anonymized cases + agent opinions
             → submit quality scores (weight vectors) to the chain
             → earn TAO proportional to their stake and consensus alignment

Subnet owner = HealthMesh protocol
             → defines the incentive mechanism
             → earns 18% of emissions
             → maintains the credentialing oracle and rubric library
```

### How Doctor Credentialing Works

Doctors cannot participate anonymously — their opinions are worth money only if they are actually doctors. But credentials must be verified without storing PII on-chain.

**Credentialing flow:**

```
1. Doctor submits license number + jurisdiction to a credentialing oracle
   (off-chain; run by HealthMesh or a trusted third party)

2. Oracle verifies against state/national medical board registries
   (automated API checks where available; manual review otherwise)

3. If valid: oracle issues a Soulbound Token (SBT) to the doctor's wallet
   - SBT encodes: specialty, jurisdiction, license status, expiry
   - SBT is non-transferable — cannot be sold or loaned
   - ZK proof attached: "this wallet holds a valid active MD license
     in cardiology, issued by a recognized authority" — no name or
     license number exposed on-chain

4. SBT is checked before any validator weight submission is accepted
   - No SBT = weights rejected by the subnet

5. License expiry monitoring: oracle re-checks annually; SBT revoked
   if license lapses or is disciplinary action taken
```

**Specialty matching:** The SBT encodes the doctor's specialty. The subnet only routes cases to doctors whose specialty matches the agent being validated. A nephrologist validates nephrology agent opinions; a cardiologist validates cardiology agent opinions. Specialists earn more for their specialty than out-of-specialty review.

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

The rubric is pre-generated (from HealthBench-style physician-authored criteria) and specialty-specific. Doctors mark each criterion: met / partially met / not met, optionally adding a note. This produces a structured score vector, not just a free-text opinion.

### How Doctor Scores Are Aggregated

The subnet validator code aggregates individual doctor ratings and produces weight vectors for Yuma Consensus:

```
For each AI agent (miner):

1. Collect all doctor ratings submitted this tempo
2. Compute weighted criterion scores (per-criterion, weighted by criterion importance)
3. Apply specialty weighting (in-specialty doctors weighted 2x out-of-specialty)
4. Apply doctor reputation multiplier (explained below)
5. Produce final score per agent: 0.0 → 1.0
6. Normalize across all agents → weight vector → submit to chain

Yuma Consensus then:
- Clips outlier validator scores toward the median (penalizes collusion)
- Distributes TAO emissions to miners (proportional to score)
  and validators/doctors (proportional to stake × consensus alignment)
```

### Doctor Reputation and Grading

Doctor ratings are themselves scored. A doctor who consistently rates well earns higher reputation — which multiplies their future TAO earnings and their influence on miner scores.

**How doctor quality is measured:**

| Signal | Timing | Weight |
|--------|--------|--------|
| Consensus with other credentialed doctors | Immediate | 40% |
| Golden set accuracy (known-correct cases injected) | Immediate | 30% |
| Proxy outcome alignment (did the recommended test confirm the hypothesis?) | Weeks–months | 20% |
| Long-term retrospective outcome (patient trajectory) | Months–years | 10% |

**Golden sets:** The HealthMesh subnet maintains a library of cases with physician-consensus ground truth (analogous to HealthBench's 48K rubric criteria). 10-20% of cases sent to doctors are golden sets — the doctor doesn't know which ones. This calibrates and audits every doctor in real time.

**Consensus scoring:** Inter-doctor agreement is the primary short-term signal. A doctor who consistently agrees with the credentialed specialist majority earns high reputation; a doctor whose scores are outliers gets investigated. (Yuma Consensus already does this at the validator level — consensus alignment is rewarded, deviation penalized.)

**The reputation score** gates earning potential:

```
Effective TAO earnings = base_earnings × reputation_multiplier

reputation_multiplier:
  0.0–0.4 reputation: 0.5× (half pay — probation)
  0.4–0.7 reputation: 1.0× (standard pay)
  0.7–0.9 reputation: 1.5× (senior validator bonus)
  0.9–1.0 reputation: 2.0× (expert validator bonus)
```

Doctors below 0.3 reputation for 4 consecutive tempos are suspended and their SBT flagged.

### The Delayed Ground Truth Problem

Not all medical opinions can be graded immediately — outcomes arrive weeks, months, or years later. The reward system is split into two tranches:

```
When doctor submits a rating:
  → 70% of reward paid immediately (based on consensus + golden set)
  → 30% held in time-locked escrow

When ground truth arrives (patient uploads follow-up, outcome confirmed):
  → Escrow is released, adjusted up or down based on retrospective accuracy
  → Doctor who said "this eGFR trend needs nephrology in 3 months" and was
    right (patient diagnosed with Stage 4 CKD 4 months later) gets full escrow
  → Doctor who said "this is normal variation" gets 0 escrow

Ground truth sources (patient-controlled, never mandatory):
  - Patient uploads subsequent lab results
  - Patient marks "outcome confirmed" on a consilium case
  - Patient's future agent opinions reference back to this case
```

If no ground truth ever arrives (patient doesn't upload follow-up), escrow is released at 12 months at the consensus rate — no penalty for unavailable outcomes.

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
| Doctor rates randomly to collect base pay | Golden set detection; low consensus → low reputation → lower earnings |
| Doctor copies other validators' scores | Commit-Reveal: scores committed (hashed) before submission, revealed after tempo; copying after reveal is too late |
| Doctor creates multiple accounts | SBT is non-transferable; one SBT per verified license; license oracle checks for duplicates |
| Sybil attack via fake licenses | Oracle verifies against real medical board registries; ZK proof tied to verified credential; license number is checked for uniqueness |
| Doctors collude to inflate a specific agent | Yuma Consensus clips outlier scores toward median; colluding validators lose consensus alignment → lose TAO |
| Agent operator bribes doctors | Stake slashing for detected coordination patterns; identity of which doctor rated which agent is randomized within specialty pool |
| Out-of-specialty doctor rates nephrology cases | SBT specialty field matched at routing; out-of-specialty ratings receive 0.5× weight and do not count toward miner's primary score |

### Incentive Flow Summary

```
Patient node (miner) produces opinion
    → Routed to 5–10 credentialed nephrologists (validators)
    → Each doctor reviews anonymized case + agent opinion against rubric
    → Scores submitted via Commit-Reveal to chain
    → Yuma Consensus aggregates → weight vector
    → TAO emission this tempo:
        41% → miner nodes (proportional to doctor scores)
        41% → doctor validators (proportional to stake × consensus alignment)
        18% → HealthMesh subnet (funds oracle, rubric library, development)
    → 30% of doctor TAO held in escrow pending outcome confirmation
```

### Why Doctors Will Participate

- **Revenue**: TAO rewards are real money. Senior validators (0.9+ reputation) earn 2× the base rate. A high-reputation specialist reviewing 20 cases/day at $50 equivalent per case = meaningful supplemental income.
- **Low friction**: reviewing a pre-structured rubric for an anonymized case takes 5–10 minutes, not a full chart review.
- **Compounding reputation**: reputation accrues over time; early participants in a growing subnet earn more as subnet emissions grow.
- **No liability**: doctors are rating AI agent opinions on anonymized synthetic contexts — they are not treating patients and carry no clinical liability.
- **Specialty impact**: cardiologists directly shape which cardiology agents survive and which die — the best agents for their specialty win, improving care globally.

---

## Phase 4 — Privacy Layer (Future)

*Designed from the start to support this — not required for local-only operation.*

- HMAC-based pseudonymous document IDs (different ID per document, linkable only by patient)
- TEE (Trusted Execution Environment) for mesh nodes — opinions computed in secure enclaves
- Differential privacy on query patterns — prevent inference from query frequency
- Zero-knowledge proofs for reputation scoring — prove a node gave good opinions without revealing what the opinion was about

---

## Tech Stack

| Component | Technology | Why |
|-----------|-----------|-----|
| Health graph | Neo4j (local) | Mature graph DB, excellent traversal, FHIR-compatible |
| Body knowledge graph | Hetionet + SPOKE (Neo4j native) + Uberon/FMA | Pre-loaded anatomy/physiology, confidence-tiered |
| Local LLM | Ollama + qwen3:14b | Runs on MacBook, no cloud, Metal GPU |
| Specialist agents | Python + LangGraph or custom | Stateful agent orchestration |
| Agent memory | Written back to Neo4j | Persistent, queryable, part of the graph |
| Data ingestion | Python (PyMuPDF, HL7, Apple Health parser) | PDF + standard medical formats |
| Wearable sync | Oura API, Apple HealthKit, FHIR | Official APIs |
| Mesh protocol | libp2p (same as IPFS) | Mature P2P networking, DHT built-in |
| Bittensor subnet | bittensor SDK + Yuma Consensus | Incentive layer for doctor validation |
| Doctor credentialing | Soulbound Tokens + ZK proofs + medical board oracle | On-chain credential without PII |
| Doctor review UI | React (doctor-facing web dashboard) | Rubric-based case review, score submission |
| Reward escrow | On-chain time-locked contract | Holds 30% of doctor rewards pending outcome confirmation |
| Prediction markets | Lightweight on-chain market per contested case | Calibrates probabilistic physician beliefs |
| Frontend | React + local web server | Patient dashboard on localhost |
| Privacy (Phase 4) | SGX / TDX TEE, ZK-SNARKs | Industry standard for confidential compute |

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

| System | Data location | Memory | Multi-specialist | Privacy |
|--------|--------------|--------|-----------------|---------|
| ChatGPT / Gemini | Cloud | None | No | None |
| Apple Health Intelligence | Apple's cloud | Limited | No | Partial |
| MDAgents | Cloud API | None | Yes | None |
| Your GP | Their EHR | Yes (fragmented) | Sometimes | HIPAA |
| **HealthMesh** | **Your machine** | **Complete, lifelong** | **Yes, permanent** | **By design** |
