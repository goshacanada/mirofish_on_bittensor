# Health Domain Knowledge Graph — Design & Validation

**Purpose:** Validate the Consilium knowledge graph concept with real medical knowledge. This document defines a concrete graph schema, populates it with real clinical relationships, and demonstrates how agents would reason across it to produce useful insights.

**Scope:** Chronic Kidney Disease (CKD) + Type 2 Diabetes Mellitus (T2DM) + Hypertension — three conditions that frequently co-occur, involve multiple organ systems, and require cross-system reasoning. This triad affects ~15% of adults over 60 and is the most common reason for nephrology referrals.

**This is domain knowledge, not personal data.** Everything in this graph is textbook medicine — relationships between conditions, biomarkers, medications, and mechanisms. No patient-specific data lives here.

---

## 1. Schema — Node Types

### 1.1 Anatomical Nodes

| Node Label | Properties | Description |
|-----------|-----------|-------------|
| `Organ` | name, system | A discrete anatomical organ |
| `System` | name | A body system (renal, cardiovascular, etc.) |
| `Tissue` | name, organ | A specific tissue type within an organ |
| `Structure` | name, organ | A functional anatomical structure |

### 1.2 Clinical Nodes

| Node Label | Properties | Description |
|-----------|-----------|-------------|
| `Condition` | name, icd10, chronic (bool), stage_system | A disease or clinical state |
| `Symptom` | name, specificity (high/medium/low) | An observable symptom |
| `Mechanism` | name, description | A pathophysiological process |

### 1.3 Measurement Nodes

| Node Label | Properties | Description |
|-----------|-----------|-------------|
| `Biomarker` | name, unit, normal_range_low, normal_range_high, source | A measurable physiological value |
| `BiomarkerThreshold` | biomarker, operator, value, clinical_meaning | A clinically meaningful cutoff |

### 1.4 Treatment Nodes

| Node Label | Properties | Description |
|-----------|-----------|-------------|
| `Medication` | name, class, mechanism_of_action | A drug |
| `MedicationClass` | name, mechanism | A drug class (ACE inhibitors, etc.) |
| `Procedure` | name, type (diagnostic/therapeutic) | A medical procedure |

### 1.5 Knowledge Source Nodes

| Node Label | Properties | Description |
|-----------|-----------|-------------|
| `Guideline` | name, organization, year, url | A clinical practice guideline |
| `ResearchPaper` | doi, title, journal, year, impact_factor, citation_count, peer_reviewed, retracted | A source paper |

---

## 2. Schema — Edge Types

Every edge carries:
- `confidence`: established / inferred / exploratory / unknown
- `evidence_source`: guideline name, paper DOI, or "textbook"
- `evidence_count`: number of supporting sources
- `last_reviewed`: date

### 2.1 Anatomical Relationships

| Edge | From → To | Meaning | Example |
|------|-----------|---------|---------|
| `PART_OF` | Organ → System | Organ belongs to system | Kidney PART_OF Renal System |
| `CONTAINS` | Organ → Structure | Organ contains structure | Kidney CONTAINS Glomerulus |
| `COMPOSED_OF` | Structure → Tissue | Structure made of tissue | Glomerulus COMPOSED_OF Endothelium |
| `CONNECTED_TO` | Organ → Organ | Functional connection | Kidney CONNECTED_TO Heart (via renal artery) |

### 2.2 Pathophysiology Relationships

| Edge | From → To | Meaning | Example |
|------|-----------|---------|---------|
| `CAUSES` | Condition → Condition | Direct causal chain | Diabetes CAUSES Diabetic Nephropathy |
| `RISK_FACTOR_FOR` | Condition → Condition | Increases risk | Hypertension RISK_FACTOR_FOR CKD |
| `ACCELERATES` | Condition → Condition | Worsens progression | Hypertension ACCELERATES CKD |
| `DRIVEN_BY` | Condition → Mechanism | Underlying mechanism | Diabetic Nephropathy DRIVEN_BY Hyperfiltration |
| `PRODUCES` | Organ → Biomarker | Organ produces substance | Kidney PRODUCES Erythropoietin |
| `FILTERS` | Organ → Biomarker | Organ clears substance | Kidney FILTERS Creatinine |
| `MANIFESTS_AS` | Condition → Symptom | Condition causes symptom | Anemia MANIFESTS_AS Fatigue |
| `DISRUPTS` | Condition → Organ | Condition damages organ | CKD DISRUPTS Kidney |
| `IMPAIRS` | Condition → Mechanism | Condition impairs process | CKD IMPAIRS EPO Production |

### 2.3 Diagnostic Relationships

| Edge | From → To | Meaning | Example |
|------|-----------|---------|---------|
| `MEASURED_BY` | Biomarker → Procedure | How biomarker is measured | Creatinine MEASURED_BY Blood Test |
| `INDICATES` | Biomarker → Condition | Abnormal value suggests | Elevated Creatinine INDICATES CKD |
| `STAGES` | BiomarkerThreshold → Condition | Defines disease stage | eGFR < 60 STAGES CKD Stage 3 |
| `CALCULATED_FROM` | Biomarker → Biomarker | Derived measurement | eGFR CALCULATED_FROM Creatinine |
| `CORRELATES_WITH` | Biomarker → Biomarker | Statistical association | HbA1c CORRELATES_WITH Blood Glucose |

### 2.4 Treatment Relationships

| Edge | From → To | Meaning | Example |
|------|-----------|---------|---------|
| `TREATS` | Medication → Condition | First-line or standard treatment | Metformin TREATS Type 2 Diabetes |
| `PROTECTS` | Medication → Organ | Organ-protective effect | Lisinopril PROTECTS Kidney |
| `LOWERS` | Medication → Biomarker | Reduces biomarker level | Lisinopril LOWERS Blood Pressure |
| `RAISES` | Medication → Biomarker | Increases biomarker level | Lisinopril RAISES Potassium |
| `CONTRAINDICATED_WHEN` | Medication → BiomarkerThreshold | Must not use when | Metformin CONTRAINDICATED_WHEN eGFR < 30 |
| `DOSE_ADJUST_WHEN` | Medication → BiomarkerThreshold | Reduce dose when | Metformin DOSE_ADJUST_WHEN eGFR < 45 |
| `INTERACTS_WITH` | Medication → Medication | Drug interaction | ACE Inhibitor INTERACTS_WITH Potassium-Sparing Diuretic |
| `BELONGS_TO` | Medication → MedicationClass | Drug classification | Lisinopril BELONGS_TO ACE Inhibitors |

### 2.5 Evidence Relationships

| Edge | From → To | Meaning | Example |
|------|-----------|---------|---------|
| `EVIDENCED_BY` | (any edge) → ResearchPaper | Source for relationship | (Diabetes CAUSES DN) EVIDENCED_BY doi:10.1056/NEJ... |
| `RECOMMENDED_BY` | (treatment edge) → Guideline | Guideline recommendation | (Lisinopril PROTECTS Kidney) RECOMMENDED_BY KDIGO 2024 |

---

## 3. Populated Graph — CKD + Diabetes + Hypertension

This section populates the schema with real medical knowledge. Every relationship is grounded in established medicine.

### 3.1 Anatomical Foundation

```
Renal System
├── Kidney
│   ├── Glomerulus (filtering unit — ~1M per kidney)
│   │   ├── Glomerular Basement Membrane
│   │   └── Podocytes (filtration barrier)
│   ├── Tubules (reabsorption)
│   ├── Juxtaglomerular Apparatus (RAAS regulation)
│   └── Peritubular Capillaries (EPO production)
│
Cardiovascular System
├── Heart
├── Arteries (including renal artery)
└── Capillary beds

Endocrine System
├── Pancreas
│   ├── Beta Cells (insulin production)
│   └── Alpha Cells (glucagon production)
└── Adrenal Glands (aldosterone)
```

**Key cross-system connections:**
```
[Kidney] ──CONNECTED_TO──► [Heart]          via renal artery — blood flow
[Kidney] ──CONNECTED_TO──► [Adrenal Gland]  via RAAS — hormonal regulation
[Pancreas] ──CONNECTED_TO──► [Kidney]       via blood glucose — metabolic load
```

### 3.2 Biomarkers — The Measurable Facts

| Biomarker | Unit | Normal Range | What It Measures | Source |
|-----------|------|-------------|-----------------|--------|
| **Creatinine** | mg/dL | 0.7 – 1.3 (male), 0.6 – 1.1 (female) | Muscle metabolism waste product filtered by kidneys | Blood test |
| **eGFR** | mL/min/1.73m² | > 90 | Estimated kidney filtration rate (calculated from creatinine, age, sex, race) | Calculated |
| **BUN** (Blood Urea Nitrogen) | mg/dL | 7 – 20 | Protein metabolism waste filtered by kidneys | Blood test |
| **Potassium** | mEq/L | 3.5 – 5.0 | Electrolyte regulated by kidneys | Blood test |
| **Hemoglobin** | g/dL | 13.5 – 17.5 (male), 12.0 – 16.0 (female) | Oxygen-carrying protein in red blood cells | CBC |
| **HbA1c** | % | < 5.7 (normal), 5.7–6.4 (prediabetes), ≥ 6.5 (diabetes) | 3-month average blood glucose | Blood test |
| **Fasting Glucose** | mg/dL | 70 – 100 | Current blood sugar level | Blood test |
| **Blood Pressure** | mmHg | < 120/80 | Force of blood against artery walls | Sphygmomanometer |
| **Urine Albumin-to-Creatinine Ratio (UACR)** | mg/g | < 30 | Protein in urine — early kidney damage marker | Urine test |
| **Erythropoietin (EPO)** | mIU/mL | 4 – 24 | Hormone stimulating red blood cell production | Blood test |
| **Phosphorus** | mg/dL | 2.5 – 4.5 | Mineral regulated by kidneys | Blood test |
| **Parathyroid Hormone (PTH)** | pg/mL | 15 – 65 | Calcium/phosphorus regulation hormone | Blood test |
| **HRV** (Heart Rate Variability) | ms | 20 – 70+ (age-dependent) | Autonomic nervous system function | Wearable |
| **Resting Heart Rate** | bpm | 60 – 100 | Cardiac function baseline | Wearable |

### 3.3 Clinically Meaningful Thresholds

These are the critical decision points that make the knowledge graph actionable.

**CKD Staging (KDIGO 2024):**

| Stage | eGFR Range | Clinical Meaning | Action |
|-------|-----------|-----------------|--------|
| G1 | ≥ 90 | Normal or high | Monitor if other risk factors |
| G2 | 60 – 89 | Mildly decreased | Monitor, manage risk factors |
| G3a | 45 – 59 | Mildly to moderately decreased | Nephrology referral, adjust meds |
| G3b | 30 – 44 | Moderately to severely decreased | Dose-adjust metformin, monitor closely |
| G4 | 15 – 29 | Severely decreased | Prepare for renal replacement |
| G5 | < 15 | Kidney failure | Dialysis or transplant |

**Albuminuria Categories (KDIGO):**

| Category | UACR | Meaning |
|----------|------|---------|
| A1 | < 30 mg/g | Normal to mildly increased |
| A2 | 30 – 300 mg/g | Moderately increased (microalbuminuria) |
| A3 | > 300 mg/g | Severely increased (macroalbuminuria) |

**Diabetes Control:**

| HbA1c | Meaning | Estimated Average Glucose |
|-------|---------|--------------------------|
| < 5.7% | Normal | < 117 mg/dL |
| 5.7 – 6.4% | Prediabetes | 117 – 137 mg/dL |
| 6.5 – 7.0% | Well-controlled diabetes | 140 – 154 mg/dL |
| 7.0 – 8.0% | Suboptimal control | 154 – 183 mg/dL |
| > 9.0% | Poor control | > 212 mg/dL |

**Hypertension Thresholds (AHA/ACC 2017):**

| Category | Systolic | Diastolic |
|----------|----------|-----------|
| Normal | < 120 | < 80 |
| Elevated | 120 – 129 | < 80 |
| Stage 1 Hypertension | 130 – 139 | 80 – 89 |
| Stage 2 Hypertension | ≥ 140 | ≥ 90 |

**Critical Action Thresholds:**

| Biomarker | Threshold | Action | Confidence |
|-----------|----------|--------|-----------|
| eGFR | < 60 mL/min for > 3 months | Diagnose CKD Stage 3+ | established |
| eGFR | < 45 | Dose-adjust metformin (reduce to 1000mg/day max) | established |
| eGFR | < 30 | Discontinue metformin | established |
| eGFR | < 30 | Consider nephrology referral if not already | established |
| UACR | > 30 mg/g (persistent) | Start ACE inhibitor or ARB | established |
| Potassium | > 5.5 mEq/L | Review potassium-raising medications | established |
| Potassium | > 6.0 mEq/L | Urgent — risk of cardiac arrhythmia | established |
| HbA1c | > 9.0% | Intensify glucose management | established |
| Hemoglobin | < 10.0 g/dL in CKD | Evaluate for anemia of CKD, consider EPO | established |
| Phosphorus | > 4.5 in CKD 3–4 | Phosphate binder consideration | established |
| PTH | > 2x upper normal in CKD 3–4 | Secondary hyperparathyroidism workup | established |
| BP | > 130/80 in CKD + DM | Target BP < 130/80 per KDIGO | established |

### 3.4 Pathophysiology — The Causal Chain

This is the core value of the knowledge graph — modeling how conditions cause other conditions through specific mechanisms.

```
[Type 2 Diabetes]
    │
    ├──DRIVEN_BY──► [Insulin Resistance]
    │                   │
    │                   └──CAUSES──► [Hyperglycemia]
    │                                    │
    │                                    ├──CAUSES──► [Hyperfiltration]
    │                                    │               │
    │                                    │               └──DAMAGES──► [Glomerulus]
    │                                    │                                │
    │                                    │                                └──CAUSES──► [Diabetic Nephropathy]
    │                                    │                                                 │
    │                                    │                                                 └──PROGRESSES_TO──► [CKD]
    │                                    │
    │                                    ├──DAMAGES──► [Blood Vessels]     (glycation)
    │                                    │               │
    │                                    │               └──ACCELERATES──► [Hypertension]
    │                                    │
    │                                    └──DAMAGES──► [Peripheral Nerves] (neuropathy)
    │
    └──RISK_FACTOR_FOR──► [Hypertension]
                              │
                              ├──ACCELERATES──► [CKD]     (via increased glomerular pressure)
                              │
                              └──RISK_FACTOR_FOR──► [Heart Failure]
                                                       │
                                                       └──RISK_FACTOR_FOR──► [Cardiorenal Syndrome]

[CKD]
    │
    ├──IMPAIRS──► [EPO Production]
    │                │
    │                └──CAUSES──► [Anemia of CKD]
    │                                │
    │                                └──MANIFESTS_AS──► [Fatigue]
    │                                └──MANIFESTS_AS──► [Pallor]
    │                                └──MANIFESTS_AS──► [Exercise Intolerance]
    │
    ├──IMPAIRS──► [Potassium Excretion]
    │                │
    │                └──CAUSES──► [Hyperkalemia]
    │                                │
    │                                └──RISK_FACTOR_FOR──► [Cardiac Arrhythmia]
    │
    ├──IMPAIRS──► [Phosphorus Excretion]
    │                │
    │                └──CAUSES──► [Hyperphosphatemia]
    │                                │
    │                                └──CAUSES──► [Secondary Hyperparathyroidism]
    │                                                │
    │                                                └──CAUSES──► [Bone Disease (Renal Osteodystrophy)]
    │
    ├──IMPAIRS──► [Acid-Base Regulation]
    │                │
    │                └──CAUSES──► [Metabolic Acidosis]
    │
    ├──MANIFESTS_AS──► [Edema]           (fluid retention)
    ├──MANIFESTS_AS──► [Nocturia]        (concentrating defect)
    ├──MANIFESTS_AS──► [Fatigue]         (uremia + anemia)
    └──MANIFESTS_AS──► [Pruritus]        (phosphorus + uremia)
```

**Confidence tiers for key causal chains:**

| Relationship | Confidence | Evidence |
|-------------|-----------|---------|
| Diabetes → Hyperfiltration → Glomerular Damage → CKD | established | DCCT, UKPDS, ADVANCE trials |
| Hypertension → Accelerated CKD | established | SPRINT, AASK trials |
| CKD → Reduced EPO → Anemia | established | Textbook pathophysiology |
| CKD → Impaired K+ Excretion → Hyperkalemia | established | Textbook pathophysiology |
| CKD → Hyperphosphatemia → Secondary Hyperparathyroidism | established | KDIGO guidelines |
| HRV decline → Precedes CKD progression | exploratory | Small cohort studies (Brotman 2010, Chandra 2012) |
| Gut microbiome dysbiosis → Accelerates CKD | exploratory | Emerging research (Vaziri 2013, Ramezani 2016) |
| Sleep apnea → Risk factor for CKD | inferred | Observational studies (Ahmed 2011, Molnar 2016) |

### 3.5 Medication Knowledge

| Medication | Class | Treats | Protects | Lowers | Raises | Contraindicated When |
|-----------|-------|--------|----------|--------|--------|---------------------|
| **Metformin** | Biguanide | T2DM | — | Blood Glucose, HbA1c | — | eGFR < 30 (discontinue), eGFR < 45 (dose-reduce) |
| **Lisinopril** | ACE Inhibitor | Hypertension | Kidney (reduces proteinuria) | Blood Pressure, UACR | Potassium, Creatinine (transient) | Bilateral renal artery stenosis, Potassium > 5.5, Pregnancy |
| **Losartan** | ARB | Hypertension | Kidney (reduces proteinuria) | Blood Pressure, UACR | Potassium | Same as ACE inhibitors (alternative if ACE intolerant) |
| **Amlodipine** | Calcium Channel Blocker | Hypertension | — | Blood Pressure | — | Severe aortic stenosis |
| **Empagliflozin** | SGLT2 Inhibitor | T2DM | Kidney, Heart | Blood Glucose, HbA1c, BP | — | eGFR < 20 (for glucose-lowering; renal/cardiac benefit continues) |
| **Insulin Glargine** | Long-acting Insulin | T2DM | — | Blood Glucose, HbA1c | — | Requires dose reduction in CKD (reduced clearance) |
| **Darbepoetin alfa** | ESA (EPO analog) | Anemia of CKD | — | — | Hemoglobin | Target Hgb 10–11.5, avoid > 13 (stroke risk) |
| **Sevelamer** | Phosphate Binder | Hyperphosphatemia (CKD) | — | Phosphorus | — | Bowel obstruction |
| **Sodium Bicarbonate** | Alkalinizing Agent | Metabolic Acidosis (CKD) | Kidney (slows progression) | — | — | Severe edema, uncontrolled HTN |
| **Furosemide** | Loop Diuretic | Edema (CKD) | — | Potassium, Fluid volume | — | Severe hypovolemia |

**Critical drug interactions in this triad:**

| Drug A | Drug B | Interaction | Risk | Confidence |
|--------|--------|------------|------|-----------|
| ACE Inhibitor | Potassium-Sparing Diuretic | Both raise potassium | Hyperkalemia → arrhythmia | established |
| ACE Inhibitor | NSAIDs | NSAIDs reduce ACE inhibitor efficacy + worsen kidney function | Acute kidney injury | established |
| Metformin | Iodinated Contrast | Contrast can cause AKI → metformin accumulation → lactic acidosis | Lactic acidosis | established |
| SGLT2 Inhibitor | Loop Diuretic | Both cause volume depletion | Hypotension, dehydration | established |
| ACE Inhibitor | ARB | Dual RAAS blockade | Hyperkalemia, AKI (no benefit, ONTARGET trial) | established |
| Insulin | Beta Blocker | Beta blockers mask hypoglycemia symptoms | Unrecognized hypoglycemia | established |

### 3.6 Guideline-Backed Recommendations

These are the "if-then" rules that agents apply when reasoning against personal data.

| If (personal data) | Then (knowledge graph reasoning) | Source | Confidence |
|----|----|----|----|
| eGFR declining > 5 mL/min/year | Flag rapid progression — consider nephrology referral | KDIGO 2024 CKD | established |
| eGFR < 60 + Diabetes | Screen UACR every 12 months | ADA Standards of Care 2024 | established |
| UACR > 30 + Diabetes | Start ACE inhibitor or ARB regardless of BP | KDIGO 2024, ADA 2024 | established |
| On Metformin + eGFR crosses below 45 | Flag: dose reduction needed (max 1000mg/day) | FDA label, KDIGO | established |
| On Metformin + eGFR crosses below 30 | Flag: discontinue metformin | FDA label, KDIGO | established |
| On ACE inhibitor + Potassium > 5.5 | Flag: review medication, check for dietary potassium | KDIGO 2024 | established |
| Hemoglobin < 10 + CKD Stage 3+ | Evaluate for anemia of CKD — iron studies, reticulocyte count | KDIGO Anemia 2012 | established |
| HbA1c > 9.0% | Flag: poor glycemic control — discuss intensification with provider | ADA 2024 | established |
| BP > 130/80 + CKD + Diabetes | Flag: target BP < 130/80 per current guidelines | KDIGO 2021 BP | established |
| eGFR < 60 + Diabetes + not on SGLT2i | Consider: SGLT2 inhibitor has proven renal and cardiac benefit (EMPA-REG, CREDENCE, DAPA-CKD trials) | KDIGO 2024 | established |
| Phosphorus > 4.5 + CKD Stage 3–5 | Flag: consider phosphate binder, dietary counseling | KDIGO CKD-MBD 2017 | established |
| PTH > 2x upper normal + CKD 3–4 | Flag: secondary hyperparathyroidism — vitamin D assessment | KDIGO CKD-MBD 2017 | established |

---

## 4. Example — Agent Reasoning Across the Graph

### Scenario: A person uploads new lab results

**Personal data (stored in `/people/igor/labs/2026-03-20-cmp.json`):**
```json
{
  "date": "2026-03-20",
  "panel": "Comprehensive Metabolic Panel + CBC",
  "results": {
    "creatinine": 1.8,
    "eGFR": 42,
    "BUN": 28,
    "potassium": 5.2,
    "hemoglobin": 10.8,
    "HbA1c": 7.8,
    "phosphorus": 4.8,
    "fasting_glucose": 168
  }
}
```

**Previous labs (from personal data store):**
```json
// /people/igor/labs/2025-09-15-cmp.json
{ "eGFR": 52, "creatinine": 1.5, "potassium": 4.8, "hemoglobin": 12.1, "HbA1c": 7.2 }

// /people/igor/labs/2025-03-10-cmp.json
{ "eGFR": 58, "creatinine": 1.4, "potassium": 4.5, "hemoglobin": 13.0, "HbA1c": 6.9 }
```

**Current medications (from `/people/igor/medications/current.json`):**
```json
[
  { "name": "Metformin", "dose": "1000mg", "frequency": "twice daily" },
  { "name": "Lisinopril", "dose": "20mg", "frequency": "once daily" },
  { "name": "Amlodipine", "dose": "5mg", "frequency": "once daily" }
]
```

### Agent Reasoning — What the knowledge graph enables

**Step 1: Trend detection (personal data)**
```
Agent loads lab history → computes trends:
  eGFR: 58 → 52 → 42 over 12 months = declining ~16 points/year
  Hemoglobin: 13.0 → 12.1 → 10.8 = declining
  Potassium: 4.5 → 4.8 → 5.2 = rising
  HbA1c: 6.9 → 7.2 → 7.8 = worsening
  Phosphorus: (no prior) → 4.8 = elevated
```

**Step 2: Knowledge graph traversal — cross-system reasoning**

The agent traverses the knowledge graph and finds these connections:

```
Finding 1: CKD Progression
  eGFR 42 → STAGES → CKD Stage 3b (knowledge graph)
  eGFR declining 16 pts/year → exceeds 5 pts/year threshold → rapid progression (guideline)
  QUESTION TO PREPARE: "My eGFR has dropped from 58 to 42 in one year.
    Should I see a nephrologist? What might be causing this rapid decline?"

Finding 2: Metformin Safety
  On Metformin + eGFR 42 → crosses below 45 threshold (knowledge graph)
  Metformin DOSE_ADJUST_WHEN eGFR < 45 → max 1000mg/day (FDA label)
  Currently taking 1000mg twice daily = 2000mg/day → EXCEEDS safe dose
  QUESTION TO PREPARE: "My eGFR is now 42 and I'm on Metformin 2000mg/day.
    Should my dose be reduced? What are the alternatives?"

Finding 3: Emerging Anemia
  Hemoglobin 10.8 and declining → approaching 10.0 threshold (knowledge graph)
  CKD IMPAIRS EPO Production → CAUSES Anemia of CKD (knowledge graph)
  Anemia MANIFESTS_AS Fatigue (knowledge graph)
  QUESTION TO PREPARE: "My hemoglobin has dropped from 13 to 10.8 over a year.
    Is this related to my kidney function? Should I have iron levels checked?"

Finding 4: Potassium Risk
  Potassium 5.2 + rising trend → approaching 5.5 threshold (knowledge graph)
  Lisinopril RAISES Potassium (knowledge graph)
  CKD IMPAIRS Potassium Excretion (knowledge graph)
  DUAL RISK: ACE inhibitor + declining kidney function = compounding potassium elevation
  QUESTION TO PREPARE: "My potassium is 5.2 and I'm on Lisinopril.
    Is this a concern given my kidney function? Should we adjust?"

Finding 5: Worsening Diabetes Control
  HbA1c 7.8% → suboptimal control (knowledge graph)
  If Metformin must be reduced (Finding 2), glucose control may worsen further
  eGFR < 60 + Diabetes + not on SGLT2i → consider SGLT2 inhibitor (KDIGO guideline)
  Empagliflozin TREATS T2DM + PROTECTS Kidney + PROTECTS Heart (knowledge graph)
  QUESTION TO PREPARE: "My HbA1c is rising and my Metformin may need to be reduced.
    Would an SGLT2 inhibitor like Empagliflozin be appropriate? I understand it
    may also help protect my kidneys."

Finding 6: Mineral Bone Disease
  Phosphorus 4.8 + CKD Stage 3b → exceeds 4.5 threshold (knowledge graph)
  CKD IMPAIRS Phosphorus Excretion → CAUSES Hyperphosphatemia (knowledge graph)
  Hyperphosphatemia CAUSES Secondary Hyperparathyroidism (knowledge graph)
  QUESTION TO PREPARE: "My phosphorus is slightly elevated at 4.8. Should I have
    my PTH and vitamin D levels checked? Do I need dietary changes?"
```

**Step 3: Agent generates a preparation report**

```
══════════════════════════════════════════════════════════
 CONSILIUM — Preparation for Nephrology/Endocrinology Visit
 Based on: Lab results 2026-03-20 + historical trend
══════════════════════════════════════════════════════════

 ⚠ URGENT: Metformin dose may need adjustment
   Your eGFR is 42 (CKD Stage 3b). Current guidelines recommend
   reducing Metformin to max 1000mg/day when eGFR drops below 45.
   You are currently taking 2000mg/day.
   → Ask your doctor about dose adjustment

 ⚠ RAPID KIDNEY DECLINE: eGFR dropped 16 points in 12 months
   Your kidney function is declining faster than the 5 points/year
   threshold that typically triggers a nephrology referral.
   → 12 months ago: eGFR 58 (Stage 3a)
   → 6 months ago: eGFR 52 (Stage 3a)
   → Now: eGFR 42 (Stage 3b)
   → Ask: What is causing this rapid decline? Do I need a referral?

 ⚡ POTASSIUM TRENDING UP: 5.2 mEq/L (approaching 5.5 concern level)
   Two factors are raising your potassium: declining kidney function
   AND Lisinopril (which is known to raise potassium).
   → Ask: Should we monitor more frequently or adjust Lisinopril?

 📋 HEMOGLOBIN DECLINING: 10.8 g/dL (was 13.0 a year ago)
   Kidneys produce erythropoietin (EPO), which stimulates red blood
   cell production. As kidney function declines, EPO production drops,
   leading to anemia. This may explain fatigue.
   → Ask: Should I have iron studies and reticulocyte count done?

 📋 DIABETES CONTROL WORSENING: HbA1c 7.8% (was 6.9% a year ago)
   If Metformin is reduced, control may slip further. An SGLT2
   inhibitor (e.g., Empagliflozin) treats diabetes AND has proven
   kidney and heart protection in clinical trials.
   → Ask: Would an SGLT2 inhibitor be right for me?

 📋 PHOSPHORUS SLIGHTLY ELEVATED: 4.8 mg/dL (normal < 4.5)
   Kidneys normally excrete phosphorus. In CKD, phosphorus builds up
   and can trigger secondary hyperparathyroidism and bone disease.
   → Ask: Should we check PTH and vitamin D levels?

══════════════════════════════════════════════════════════
 This is not medical advice. These are questions to discuss
 with your healthcare provider based on your lab trends and
 established clinical guidelines.
══════════════════════════════════════════════════════════
```

---

## 5. How the Mesh Adds Value

When the local agent generates findings like the above, some questions may benefit from mesh consultation — perspectives from nodes with richer knowledge graphs (especially nodes run by domain experts).

**Example mesh query (anonymized):**
```
Query: "In a case with eGFR declining 16 pts/year, CKD 3b, T2DM with HbA1c 7.8%,
        on Metformin 2000mg + Lisinopril + Amlodipine — what additional factors
        should be investigated for rapid eGFR decline?"

Node A (run by a nephrologist — rich renal knowledge graph):
  "Consider renal artery stenosis workup (renal duplex ultrasound).
   Rapid decline in a diabetic patient on ACE inhibitor can indicate
   bilateral renal artery stenosis — the ACE inhibitor unmasks it.
   Also consider checking urine eosinophils for interstitial nephritis."

Node B (run by an endocrinologist — rich diabetes knowledge graph):
  "HbA1c rise from 6.9 to 7.8 with stable Metformin dose may indicate
   progressive beta-cell failure. Consider C-peptide level to assess
   residual insulin production. If low, may need earlier insulin initiation
   rather than SGLT2i alone."

Node C (general user — standard knowledge graph):
  "Knowledge graph confirms KDIGO recommendation for SGLT2 inhibitor
   in CKD + T2DM. Three major trials support this: EMPA-REG OUTCOME,
   CREDENCE, DAPA-CKD."
```

**The difference:** A standard knowledge graph catches the obvious (metformin adjustment, SGLT2i consideration). An expert's node catches the non-obvious (renal artery stenosis, beta-cell failure) — patterns that come from years of clinical experience crystallized in their knowledge graph via research papers and professional observations.

---

## 6. Cypher Queries — How Agents Actually Traverse the Graph

These are the Neo4j Cypher queries agents would execute against the knowledge graph.

### 6.1 Find all conditions a biomarker indicates

```cypher
MATCH (b:Biomarker {name: "Creatinine"})-[:INDICATES]->(c:Condition)
RETURN c.name, c.icd10
```

### 6.2 Find the full causal chain from a condition

```cypher
MATCH path = (c:Condition {name: "Type 2 Diabetes"})-[:CAUSES|RISK_FACTOR_FOR|ACCELERATES*1..4]->(downstream)
RETURN [n IN nodes(path) | n.name] AS chain,
       [r IN relationships(path) | type(r)] AS relationships
```

### 6.3 Find medication contraindications given a biomarker value

```cypher
// Agent passes in the person's current eGFR from personal data
WITH 42 AS current_eGFR
MATCH (m:Medication)-[:CONTRAINDICATED_WHEN]->(t:BiomarkerThreshold)
WHERE t.biomarker = "eGFR" AND current_eGFR < t.value AND t.operator = "<"
RETURN m.name, t.value, t.clinical_meaning
```

### 6.4 Find drug interactions for current medications

```cypher
// Agent passes in list of current medication names from personal data
WITH ["Metformin", "Lisinopril", "Amlodipine"] AS current_meds
MATCH (a:Medication)-[i:INTERACTS_WITH]->(b:Medication)
WHERE a.name IN current_meds AND b.name IN current_meds
RETURN a.name, b.name, i.risk, i.confidence
```

### 6.5 Cross-system reasoning — why might this person be fatigued?

```cypher
// Find all paths from current conditions to the symptom "Fatigue"
MATCH (c:Condition)-[:CAUSES|MANIFESTS_AS|IMPAIRS*1..3]->(s:Symptom {name: "Fatigue"})
RETURN c.name AS source_condition,
       [n IN nodes(shortestPath((c)-[:CAUSES|MANIFESTS_AS|IMPAIRS*]->(s))) | n.name] AS pathway
```

### 6.6 Find what an SGLT2 inhibitor would do across systems

```cypher
MATCH (m:Medication {name: "Empagliflozin"})-[r]->(target)
WHERE type(r) IN ["TREATS", "PROTECTS", "LOWERS"]
RETURN type(r) AS effect, target.name AS target, r.confidence
```

### 6.7 Trace evidence for a relationship back to source papers

```cypher
MATCH (c1:Condition {name: "CKD"})-[r:CAUSES]->(c2:Condition {name: "Anemia of CKD"})
MATCH (r)-[:EVIDENCED_BY]->(p:ResearchPaper)
RETURN p.title, p.journal, p.year, p.doi, p.citation_count
ORDER BY p.citation_count DESC
```

---

## 7. Graph Statistics — What This Knowledge Graph Contains

For the CKD + Diabetes + Hypertension triad alone:

| Category | Count | Examples |
|----------|-------|---------|
| Organs | 8 | Kidney, Heart, Pancreas, Liver, Adrenal, Blood Vessels, Nerves, Bone |
| Systems | 5 | Renal, Cardiovascular, Endocrine, Hematologic, Musculoskeletal |
| Conditions | 15+ | CKD (5 stages), T2DM, Hypertension, Diabetic Nephropathy, Anemia of CKD, Hyperkalemia, Hyperphosphatemia, Secondary Hyperparathyroidism, Metabolic Acidosis, Neuropathy |
| Biomarkers | 14 | Creatinine, eGFR, BUN, Potassium, Hemoglobin, HbA1c, Glucose, BP, UACR, EPO, Phosphorus, PTH, HRV, RHR |
| Thresholds | 25+ | CKD staging, albuminuria categories, diabetes control, hypertension stages, action thresholds |
| Medications | 10 | Metformin, Lisinopril, Losartan, Amlodipine, Empagliflozin, Insulin Glargine, Darbepoetin, Sevelamer, Sodium Bicarb, Furosemide |
| Drug Interactions | 6 | ACE+K-sparing, ACE+NSAID, Metformin+Contrast, SGLT2i+Diuretic, ACE+ARB, Insulin+Beta-blocker |
| Causal Chains | 20+ | Diabetes→Hyperfiltration→CKD, CKD→Low EPO→Anemia→Fatigue, CKD→Hyperkalemia→Arrhythmia |
| Guidelines | 5+ | KDIGO 2024, ADA 2024, AHA/ACC 2017, KDIGO Anemia 2012, KDIGO CKD-MBD 2017 |

**Estimated graph size for full wellness domain:** This triad represents one clinical scenario. The full wellness knowledge graph (all common conditions, medications, biomarkers) would be 10–50x larger. Importing from Hetionet (~47K nodes, ~2.25M edges) + Uberon (~75K nodes) provides the foundational scale.

---

## 8. What This Validates

This exercise confirms several key architectural assumptions:

1. **Knowledge vs. personal data separation works.** The entire knowledge graph above contains zero personal data. It's textbook medicine. Personal data (lab values, medication lists) lives in per-person folders and is loaded at query time. The separation is clean and natural.

2. **Cross-system reasoning is the killer feature.** No single specialist would catch all six findings in the example above. A nephrologist focuses on CKD. An endocrinologist focuses on diabetes. The knowledge graph connects them — Metformin dose + potassium + anemia + phosphorus — across systems simultaneously.

3. **Thresholds make the graph actionable.** Without thresholds, the graph is just a map. With thresholds (eGFR < 45 = dose-adjust metformin), agents can generate specific, actionable questions for professionals.

4. **Research papers enrich the graph naturally.** Every paper a doctor reads adds relationships and evidence. A nephrologist's knowledge graph has deeper renal pathophysiology. An endocrinologist's has richer diabetes mechanisms. These differences make the mesh valuable — different nodes have different depths.

5. **The mesh adds non-obvious value.** A standard knowledge graph catches textbook findings. An expert's enriched graph catches renal artery stenosis and beta-cell failure — patterns that come from clinical experience, not just guidelines.

6. **Cypher queries are straightforward.** The graph schema maps naturally to the kinds of questions agents need to answer. No complex joins, no data transformation — just graph traversal.

---

## 9. Next Steps

1. **Load this into Neo4j.** Convert the relationships above into Cypher CREATE statements and test actual queries.
2. **Import Hetionet subset.** Start with the renal + cardiovascular + endocrine subgraph and map to this schema.
3. **Build a research paper extractor.** Given a PubMed paper, can an LLM extract entities and relationships that map to this schema?
4. **Test agent reasoning.** Given the personal data scenario above, can an LLM + these Cypher queries produce the preparation report?
5. **Expand to a second domain (finance).** Validate that the same architecture works for a completely different knowledge domain.
