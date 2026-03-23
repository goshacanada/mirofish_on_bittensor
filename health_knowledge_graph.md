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
| `Condition` | name, icd10, snomed_ct, omop_concept_id, chronic (bool), stage_system | A disease or clinical state |
| `Symptom` | name, snomed_ct, specificity (high/medium/low) | An observable symptom |
| `Mechanism` | name, description | A pathophysiological process |

**Standard terminology mappings (required for all clinical nodes):**

| Terminology | Applies to | Purpose | Example |
|-------------|-----------|---------|---------|
| **ICD-10** | Condition | Billing/classification code | E11.65 (T2DM with hyperglycemia) |
| **SNOMED CT** | Condition, Symptom | Unambiguous clinical concept ID | 73211009 (Diabetes mellitus) |
| **OMOP Concept ID** | All clinical nodes | Cross-database interoperability | 201826 (Type 2 diabetes mellitus) |

Without standard terminologies, "Type 2 DM", "T2DM", "Non-insulin dependent diabetes mellitus", and "NIDDM" are four different strings that mean the same thing. SNOMED CT makes them unambiguous. This is essential for entity matching across papers, imported knowledge bases (Hetionet, SPOKE), and external databases.

### 1.3 Measurement Nodes

| Node Label | Properties | Description |
|-----------|-----------|-------------|
| `Biomarker` | name, loinc_code, unit, reference_ranges (list), source | A measurable physiological value |
| `BiomarkerThreshold` | biomarker, operator, value, clinical_meaning | A clinically meaningful cutoff |

**Stratified reference ranges:** Normal ranges are NOT single values — they vary by sex, age, and sometimes ethnicity. The `reference_ranges` property is a list:

```json
// Biomarker: Hemoglobin (LOINC: 718-7)
{
  "name": "Hemoglobin",
  "loinc_code": "718-7",
  "unit": "g/dL",
  "reference_ranges": [
    { "population": "adult male", "low": 13.5, "high": 17.5, "source": "WHO" },
    { "population": "adult female", "low": 12.0, "high": 16.0, "source": "WHO" },
    { "population": "adult female (pregnant)", "low": 11.0, "high": 16.0, "source": "WHO" },
    { "population": "child 6-12 years", "low": 11.5, "high": 15.5, "source": "WHO" },
    { "population": "elderly > 75", "low": 11.0, "high": 17.0, "source": "clinical consensus" }
  ]
}

// Biomarker: Creatinine (LOINC: 2160-0)
{
  "name": "Creatinine",
  "loinc_code": "2160-0",
  "unit": "mg/dL",
  "reference_ranges": [
    { "population": "adult male", "low": 0.7, "high": 1.3, "source": "standard lab" },
    { "population": "adult female", "low": 0.6, "high": 1.1, "source": "standard lab" },
    { "population": "elderly > 70", "low": 0.7, "high": 1.5, "source": "clinical consensus, reduced muscle mass" },
    { "population": "child 6-12 years", "low": 0.3, "high": 0.7, "source": "pediatric reference" }
  ]
}
```

**LOINC codes** disambiguate biomarker identity. "Creatinine" could mean serum creatinine (LOINC 2160-0), urine creatinine (LOINC 2161-8), or creatinine clearance (LOINC 2164-2) — very different clinical measurements. Without LOINC, the system may confuse them. Every Biomarker node requires a LOINC code.

### 1.4 Treatment Nodes

| Node Label | Properties | Description |
|-----------|-----------|-------------|
| `Medication` | name, rxnorm_cui, atc_code, class, mechanism_of_action | A drug |
| `MedicationClass` | name, atc_code, mechanism | A drug class (ACE inhibitors, etc.) |
| `Procedure` | name, cpt_code, type (diagnostic/therapeutic) | A medical procedure |

**Standard terminology mappings (required for all treatment nodes):**

| Terminology | Applies to | Purpose | Example |
|-------------|-----------|---------|---------|
| **RxNorm CUI** | Medication | Normalizes across brand/generic/formulation | 6809 (Metformin) — covers Glucophage, Metformin HCl, etc. |
| **ATC Code** | Medication, MedicationClass | WHO drug classification hierarchy | A10BA02 (Metformin) |
| **CPT/HCPCS** | Procedure | Procedure identification | 80053 (Comprehensive metabolic panel) |

RxNorm is essential for medication matching. "Glucophage", "Metformin", "Metformin HCl 500mg", and "Metformin ER 1000mg" are different strings. RxNorm CUI 6809 unambiguously identifies the active ingredient. The `rxnorm_cui` on Medication nodes enables: (1) matching user-reported medications to graph entities regardless of how they type the name, (2) importing from DrugBank, OpenFDA, and interaction databases that use RxNorm, and (3) cross-referencing with FHIR medication resources.

### 1.5 Knowledge Source Nodes

| Node Label | Properties | Description |
|-----------|-----------|-------------|
| `Guideline` | name, organization, year, url | A clinical practice guideline |
| `ResearchPaper` | doi, title, journal, year, impact_factor, citation_count, peer_reviewed, retracted, replication_count | A source paper |

---

## 2. Schema — Edge Types

Every edge carries **base properties** plus optional **qualifiers** and **context**:

### Base Properties (required on every edge)
- `confidence`: established / inferred / exploratory / unknown
- `evidence_source`: guideline name, paper DOI, or "textbook"
- `evidence_count`: number of supporting sources
- `last_reviewed`: date
- `created_date`: when this edge was first added
- `superseded_by`: DOI or edge ID if newer evidence replaces this (see Knowledge Decay)

### Qualified Claims (optional — critical for medical accuracy)

Medical relationships are rarely universal. Edges carry qualifiers that describe **who** the relationship applies to, **how strong** the effect is, and **what limits** the evidence:

| Qualifier | Type | Example |
|-----------|------|---------|
| `population` | string | "adults with T2DM and eGFR > 20", "post-menopausal women" |
| `effect_size` | string | "HR 0.72 (95% CI 0.64–0.82)", "NNT 22", "OR 2.3" |
| `adjusted_for` | list | ["age", "sex", "baseline eGFR", "smoking status"] |
| `subgroups` | list | Subgroups where the effect differs — e.g., ["no benefit in eGFR < 20", "stronger effect in UACR > 300"] |
| `confounders` | list | Known uncontrolled confounders — e.g., ["concurrent statin use not controlled"] |
| `limitations` | string | "Single-center study", "Post-hoc analysis", "Industry-funded" |
| `number_needed` | string | "NNT 22 to prevent one kidney failure event over 2 years" |

**Example — qualified edge:**
```
[Empagliflozin] ──REDUCES_RISK_OF──► [Kidney Failure]
  confidence: established
  effect_size: "HR 0.72 (95% CI 0.64–0.82, p<0.001)"
  population: "adults with CKD, eGFR 20–45 or eGFR 45–90 with UACR ≥ 200"
  adjusted_for: ["age", "sex", "baseline eGFR", "UACR", "diabetes status"]
  subgroups: ["benefit regardless of diabetes status (p-interaction 0.30)",
              "no significant benefit in eGFR < 20"]
  limitations: "Industry-funded (Boehringer Ingelheim/Lilly)"
  number_needed: "NNT 22 over 2 years"
  evidence_source: "doi:10.1056/NEJMoa2204233 (EMPA-KIDNEY)"
```

### Context Properties (optional — describes WHEN and HOW the relationship applies)

| Context | Type | Example |
|---------|------|---------|
| `severity` | string | "mild", "moderate", "severe", "life-threatening" |
| `temporal` | string | "acute" / "chronic" / "transient" / "progressive" |
| `phenotype` | string | "diabetic nephropathy", "IgA nephropathy", "FSGS" |
| `onset` | string | "within 1–2 weeks", "months to years", "immediate" |
| `reversibility` | string | "reversible", "partially reversible", "irreversible" |
| `dose_dependent` | boolean | true / false |
| `age_dependent` | boolean | true / false |

**Example — contextualized edge:**
```
[ACE Inhibitor] ──RAISES──► [Creatinine]
  confidence: established
  severity: "mild"
  temporal: "transient"
  onset: "within 1–2 weeks of initiation"
  reversibility: "reversible (returns to baseline if stopped)"
  dose_dependent: true
  action_if_rise_under_30pct: "continue — expected, nephroprotective"
  action_if_rise_over_30pct: "investigate renal artery stenosis"
```

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
| `CONFLICTS_WITH` | Edge → Edge | Two edges that present contradictory evidence | See section 2.6 |
| `SUPERSEDED_BY` | Edge → Edge | Newer evidence replaces older | Old threshold edge SUPERSEDED_BY updated guideline edge |

### 2.6 Conflicting Evidence

Medical knowledge often has legitimate disagreements — different trials, different populations, different conclusions. The graph must model these explicitly rather than picking a winner.

**The `CONFLICTS_WITH` edge connects two edges that present contradictory evidence:**

```
Edge A: [Intensive BP Control] ──REDUCES_RISK_OF──► [Cardiovascular Events]
  evidence_source: "SPRINT trial (2015)"
  population: "non-diabetic adults, SBP target < 120"
  effect_size: "HR 0.75 (0.64–0.89)"

Edge B: [Intensive BP Control] ──NO_BENEFIT_FOR──► [Cardiovascular Events]
  evidence_source: "ACCORD-BP trial (2010)"
  population: "adults with Type 2 Diabetes, SBP target < 120"
  effect_size: "HR 0.88 (0.73–1.06, p=0.20, not significant)"

[Edge A] ──CONFLICTS_WITH──► [Edge B]
  resolution: "population-dependent — benefit in non-diabetics (SPRINT) but not demonstrated in diabetics (ACCORD)"
  current_practice: "Target < 130/80 for diabetics (KDIGO), < 120 for high-risk non-diabetics (SPRINT)"
  resolution_status: "resolved_by_population"  // or: "unresolved", "superseded", "ongoing_debate"
```

**Why this matters:** Without `CONFLICTS_WITH`, the graph would either pick one trial's conclusion (wrong) or have two contradictory edges with no connection between them (confusing). The conflict edge lets agents present both sides: *"SPRINT showed benefit for intensive BP control, but ACCORD did not find the same benefit in diabetics — your doctor may consider your diabetes status when setting your BP target."*

**Resolution statuses:**
- `resolved_by_population` — different populations explain the difference
- `resolved_by_methodology` — one study had methodological issues
- `superseded` — newer/larger study replaces older
- `unresolved` — genuine ongoing debate
- `ongoing_debate` — active research area, no consensus

### 2.7 Patient Factor Modifiers (MODIFIED_BY)

Some relationships change based on patient-specific factors — race, sex, pregnancy, genetic variants, prior adverse reactions. These are modeled as `MODIFIED_BY` edges that modify an existing relationship.

| Modifier | What it modifies | How | Example |
|----------|-----------------|-----|---------|
| `Pregnancy` | ACE Inhibitor TREATS Hypertension | Contraindicated | ACE inhibitors are teratogenic — absolute contraindication in pregnancy |
| `History of Angioedema` | ACE Inhibitor TREATS Hypertension | Contraindicated | Prior ACE inhibitor angioedema → switch to ARB |
| `African Ancestry` | ACE Inhibitor TREATS Hypertension | Reduced efficacy as monotherapy | ACE inhibitors less effective as monotherapy in Black patients (ALLHAT) — combine with CCB or thiazide |
| `CYP2D6 Poor Metabolizer` | Metoprolol TREATS Hypertension | Dose adjustment | Reduced metabolism → lower dose needed |
| `Baseline eGFR < 30` | Metformin TREATS T2DM | Contraindicated | Lactic acidosis risk at low eGFR |
| `Baseline Potassium > 5.0` | ACE Inhibitor TREATS Hypertension | Caution | Already elevated K+ → ACE may push above 5.5 |
| `Elderly (> 75)` | Intensive BP Control | Modified target | Frailty considerations — less aggressive targets may be appropriate |

**Schema:**
```
[MODIFIED_BY edge]
  modifier_type: "contraindication" / "dose_adjustment" / "reduced_efficacy" / "enhanced_risk" / "altered_target"
  modifier_factor: "pregnancy" / "genetic_variant" / "age" / "comorbidity" / "prior_adverse_reaction"
  modifier_value: specific value (e.g., "CYP2D6 poor metabolizer", "age > 75")
  action: what to do differently
  evidence_source: guideline or trial
  confidence: established / inferred / exploratory
```

**Example — full modifier chain:**
```
[ACE Inhibitor] ──TREATS──► [Hypertension]
    │
    ├── MODIFIED_BY [Pregnancy]
    │     modifier_type: "contraindication"
    │     action: "Switch to labetalol or nifedipine"
    │     confidence: established
    │     evidence_source: "FDA Black Box Warning, ACOG 2019"
    │
    ├── MODIFIED_BY [African Ancestry]
    │     modifier_type: "reduced_efficacy"
    │     action: "Combine with CCB or thiazide as first-line"
    │     confidence: established
    │     evidence_source: "ALLHAT trial, JNC 8"
    │
    └── MODIFIED_BY [History of Angioedema]
          modifier_type: "contraindication"
          action: "Use ARB instead (lower angioedema risk)"
          confidence: established
          evidence_source: "ACC/AHA 2017 Hypertension Guideline"
```

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

| Biomarker | LOINC | Unit | Normal Range (stratified) | What It Measures | Source |
|-----------|-------|------|--------------------------|-----------------|--------|
| **Creatinine** | 2160-0 | mg/dL | 0.7–1.3 (male), 0.6–1.1 (female), 0.7–1.5 (elderly >70) | Muscle metabolism waste product filtered by kidneys | Blood test |
| **eGFR** | 48642-3 | mL/min/1.73m² | > 90 | Estimated kidney filtration rate (2021 CKD-EPI, race coefficient removed) | Calculated |
| **BUN** | 3094-0 | mg/dL | 7–20 | Protein metabolism waste filtered by kidneys | Blood test |
| **Potassium** | 2823-3 | mEq/L | 3.5–5.0 | Electrolyte regulated by kidneys | Blood test |
| **Hemoglobin** | 718-7 | g/dL | 13.5–17.5 (male), 12.0–16.0 (female), 11.0–16.0 (pregnant), 11.0–17.0 (elderly >75) | Oxygen-carrying protein in red blood cells | CBC |
| **HbA1c** | 4548-4 | % | < 5.7 (normal), 5.7–6.4 (prediabetes), ≥ 6.5 (diabetes) | 3-month average blood glucose | Blood test |
| **Fasting Glucose** | 1558-6 | mg/dL | 70–100 | Current blood sugar level | Blood test |
| **Blood Pressure** | 85354-9 | mmHg | < 120/80 | Force of blood against artery walls | Sphygmomanometer |
| **UACR** | 9318-7 | mg/g | < 30 | Protein in urine — early kidney damage marker | Urine test |
| **Erythropoietin (EPO)** | 15064-8 | mIU/mL | 4–24 | Hormone stimulating red blood cell production | Blood test |
| **Phosphorus** | 2777-1 | mg/dL | 2.5–4.5 | Mineral regulated by kidneys | Blood test |
| **PTH** | 2731-8 | pg/mL | 15–65 | Calcium/phosphorus regulation hormone | Blood test |
| **HRV** | 80404-7 | ms | 20–70+ (age-dependent) | Autonomic nervous system function | Wearable |
| **Resting Heart Rate** | 40443-4 | bpm | 60–100 | Cardiac function baseline | Wearable |

**LOINC codes** are essential for unambiguous biomarker identification. "Creatinine" alone is ambiguous — serum creatinine (2160-0), urine creatinine (2161-8), and creatinine clearance (2164-2) are different measurements with different clinical meanings. Every Biomarker node in the graph carries a LOINC code, and all ingested data is mapped to LOINC at parse time.

**Stratified reference ranges:** Normal ranges vary by sex, age, and pregnancy status. The schema stores multiple reference ranges per biomarker (see Node Types section 1.3). Threshold checking uses the appropriate range for the person's demographics — a hemoglobin of 12.5 g/dL is normal for a female but below range for a male.

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
| Hemoglobin | Below normal range in CKD (< 13.5 male, < 12.0 female) | Check iron studies (ferritin, TIBC, iron saturation) — iron deficiency is most common treatable cause | established |
| Hemoglobin | < 10.0 g/dL in CKD (after iron workup) | Evaluate for EPO therapy; target 10–11.5, do not intentionally exceed 11.5 (KDIGO 2012) | established |
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
    │                                    ├──DAMAGES──► [Blood Vessels]     (AGE glycation, endothelial dysfunction)
    │                                    │
    │                                    └──DAMAGES──► [Peripheral Nerves] (neuropathy)
    │
    ├──DRIVEN_BY──► [Insulin Resistance]
    │                   ├──CAUSES──► [Sodium Retention]     ──RISK_FACTOR_FOR──► [Hypertension]
    │                   ├──CAUSES──► [Sympathetic Activation] ──RISK_FACTOR_FOR──► [Hypertension]
    │                   └──CAUSES──► [RAAS Activation]       ──RISK_FACTOR_FOR──► [Hypertension]
    │
    ├──DAMAGES──► [Blood Vessels] (vascular stiffness)      ──RISK_FACTOR_FOR──► [Hypertension]
    │
    └──RISK_FACTOR_FOR──► [Hypertension]   (multiple mechanisms above)
                              │
                              ├──ACCELERATES──► [CKD]     (via increased glomerular pressure)
                              │
                              └──RISK_FACTOR_FOR──► [Heart Failure]
                                                       │
                                                       └──RISK_FACTOR_FOR──► [Cardiorenal Syndrome]

[CKD] ──ACTIVATES──► [RAAS]
                        │
                        └──CAUSES──► [Hypertension]
                                        │
                                        └──ACCELERATES──► [CKD]
                                        (VICIOUS CYCLE: CKD → RAAS activation → HTN → more CKD)
                                        (This is WHY ACE inhibitors protect kidneys — they break the cycle)

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

HOW KEY MEDICATIONS INTERVENE IN THESE CHAINS:

[ACE Inhibitor (Lisinopril)]
    ├──BLOCKS──► [RAAS]                  → breaks the CKD→RAAS→HTN→CKD vicious cycle
    ├──LOWERS──► [Intraglomerular Pressure] → reduces hyperfiltration injury
    ├──LOWERS──► [UACR]                  → reduces proteinuria (marker of glomerular damage)
    └──RAISES──► [Creatinine] (transient, expected — see drug interactions section)

[SGLT2 Inhibitor (Empagliflozin)]
    ├──BLOCKS──► [Proximal Tubule Glucose Reabsorption]
    │               │
    │               ├──LOWERS──► [Blood Glucose]    (direct glucose-lowering)
    │               │
    │               └──ACTIVATES──► [Tubuloglomerular Feedback]
    │                                   │
    │                                   └──CONSTRICTS──► [Afferent Arteriole]
    │                                                       │
    │                                                       └──LOWERS──► [Intraglomerular Pressure]
    │                                                                       │
    │                                                                       └──PROTECTS──► [Glomerulus]
    │                   (This is WHY SGLT2i protect kidneys — they reverse
    │                    the hyperfiltration that drives diabetic nephropathy)
    │
    ├──LOWERS──► [Blood Pressure]        (natriuresis + mild diuresis)
    └──PROTECTS──► [Heart]               (multiple mechanisms — EMPA-REG, DAPA-HF)
```

**Confidence tiers for key causal chains:**

| Relationship | Confidence | Evidence |
|-------------|-----------|---------|
| Diabetes → Hyperfiltration → Glomerular Damage → CKD | established | UKPDS, ADVANCE, ACCORD trials (Type 2 DM); DCCT (Type 1 DM — same mechanism) |
| Hypertension → Accelerated CKD | established | SPRINT, AASK trials |
| CKD → Reduced EPO → Anemia | established | Textbook pathophysiology |
| CKD → Impaired K+ Excretion → Hyperkalemia | established | Textbook pathophysiology |
| CKD → Hyperphosphatemia → Secondary Hyperparathyroidism | established | KDIGO guidelines |
| HRV decline → Precedes CKD progression | exploratory | Small cohort studies (Brotman 2010, Chandra 2012) |
| Gut microbiome dysbiosis → Accelerates CKD | exploratory | Emerging research (Vaziri 2013, Ramezani 2016) |
| Sleep apnea → Risk factor for CKD | inferred | Observational studies (Ahmed 2011, Molnar 2016) |

### 3.5 Medication Knowledge

| Medication | RxNorm CUI | Class | Treats | Protects | Lowers | Raises | Contraindicated When |
|-----------|-----------|-------|--------|----------|--------|--------|---------------------|
| **Metformin** | 6809 | Biguanide | T2DM | — | Blood Glucose, HbA1c | — | eGFR < 30 (discontinue), eGFR < 45 (dose-reduce) |
| **Lisinopril** | 29046 | ACE Inhibitor | Hypertension | Kidney (reduces proteinuria) | Blood Pressure, UACR | Potassium, Creatinine (transient) | Bilateral renal artery stenosis, Potassium > 5.5, Pregnancy |
| **Losartan** | 52175 | ARB | Hypertension | Kidney (reduces proteinuria) | Blood Pressure, UACR | Potassium | Same as ACE inhibitors (alternative if ACE intolerant) |
| **Amlodipine** | 17767 | Calcium Channel Blocker | Hypertension | — | Blood Pressure | — | Severe aortic stenosis |
| **Empagliflozin** | 1545653 | SGLT2 Inhibitor | T2DM | Kidney (reduces glomerular hyperfiltration by blocking proximal tubule glucose reabsorption → lowers intraglomerular pressure), Heart | Blood Glucose, HbA1c, BP | — | eGFR < 45 (glucose-lowering effect diminishes); kidney/cardiac protective benefit continues to eGFR ~20 (EMPA-KIDNEY, DAPA-CKD) |
| **Insulin Glargine** | 274783 | Long-acting Insulin | T2DM | — | Blood Glucose, HbA1c | — | Requires dose reduction in CKD (reduced clearance) |
| **Darbepoetin alfa** | 237057 | ESA (EPO analog) | Anemia of CKD | — | — | Hemoglobin | Initiate when Hgb < 10; target Hgb 10–11.5; do NOT intentionally target > 11.5 (KDIGO 2012); Hgb > 13 associated with stroke risk (TREAT trial, CREATE trial) |
| **Sevelamer** | 273237 | Phosphate Binder | Hyperphosphatemia (CKD) | — | Phosphorus | — | Bowel obstruction |
| **Sodium Bicarbonate** | 8919 | Alkalinizing Agent | Metabolic Acidosis (CKD) | Kidney (may slow progression — **confidence: inferred**, small RCTs suggest benefit but evidence is debated; larger trials ongoing) | — | — | Severe edema, uncontrolled HTN |
| **Furosemide** | 4603 | Loop Diuretic | Edema (CKD) | — | Potassium, Fluid volume | — | Severe hypovolemia |

**RxNorm CUIs** enable unambiguous medication identification. "Glucophage", "Metformin", "Metformin HCl", and "Metformin ER" are all RxNorm CUI 6809 (the active ingredient). This is essential for: (1) matching user-reported medication names to graph entities, (2) importing from DrugBank, OpenFDA, and interaction databases, and (3) cross-referencing with FHIR medication resources.

**Critical drug interactions in this triad:**

| Drug A | Drug B | Interaction | Risk | Confidence |
|--------|--------|------------|------|-----------|
| ACE Inhibitor | Potassium-Sparing Diuretic | Both raise potassium | Hyperkalemia → arrhythmia | established |
| ACE Inhibitor | NSAIDs | NSAIDs reduce ACE inhibitor efficacy + worsen kidney function | Acute kidney injury | established |
| Metformin | Iodinated Contrast | Contrast can cause AKI → metformin accumulation → lactic acidosis. Per ACR 2023: hold metformin 48h only if eGFR < 30, or eGFR 30–44 with intra-arterial contrast. No hold needed if eGFR ≥ 45. | Lactic acidosis (risk is dose- and renal-function-dependent) | established |
| SGLT2 Inhibitor | Loop Diuretic | Both cause volume depletion | Hypotension, dehydration | established |
| ACE Inhibitor | ARB | Dual RAAS blockade | Hyperkalemia, AKI (no benefit, ONTARGET trial) | established |
| Insulin | Beta Blocker | Beta blockers mask hypoglycemia symptoms | Unrecognized hypoglycemia | established |

**Conditional interactions (drug-drug-disease):**

Simple drug-drug pairs are insufficient. Many interactions only matter in specific disease contexts:

| Drug A | Drug B | Condition | Why it matters | Risk level |
|--------|--------|-----------|---------------|------------|
| ACE Inhibitor | Potassium-Sparing Diuretic | CKD Stage 3+ | Impaired K+ excretion makes hyperkalemia much more likely | Critical (vs. moderate without CKD) |
| NSAIDs | ACE Inhibitor | Heart Failure | "Triple whammy" with diuretic → AKI risk 30x higher | Critical |
| Metformin | — | CKD + acute illness | Sick day rules: hold metformin during dehydration, infection, or surgery to prevent lactic acidosis | Context-dependent |
| SGLT2i | — | Diabetic ketoacidosis risk | Euglycemic DKA — normal glucose but ketoacidotic. Risk increases with surgery, fasting, low-carb diet | Context-dependent |

**Drug-food and drug-supplement interactions:**

| Drug | Food/Supplement | Interaction | Confidence |
|------|----------------|-------------|-----------|
| ACE Inhibitor | Potassium-rich foods (bananas, oranges, potatoes) | Additive potassium elevation | established |
| Metformin | Alcohol | Increased lactic acidosis risk | established |
| Warfarin | Vitamin K foods (leafy greens) | Reduced anticoagulant effect | established |
| Levothyroxine | Calcium, iron supplements | Reduced absorption — take 4h apart | established |
| Statins | Grapefruit | CYP3A4 inhibition → increased statin levels → myopathy risk | established |

**Pharmacogenomic interactions:**

Some drug responses depend on genetic variants. The graph models these as MODIFIED_BY edges (see section 2.7) with pharmacogenomic modifiers:

| Gene | Variant | Drug Affected | Effect | Confidence |
|------|---------|--------------|--------|-----------|
| CYP2D6 | Poor metabolizer | Metoprolol, Codeine | Altered metabolism — dose adjustment needed | established |
| CYP2C19 | Poor metabolizer | Clopidogrel | Reduced activation → inadequate antiplatelet effect | established |
| SLCO1B1 | rs4149056 (C allele) | Simvastatin | Increased myopathy risk — use lower dose or alternative statin | established |
| HLA-B*5801 | Positive | Allopurinol | Severe hypersensitivity (SJS/TEN) — test before prescribing | established |

**Interaction schema — full model:**
```
[INTERACTS_WITH edge]
  interaction_type: "drug-drug" / "drug-drug-disease" / "drug-food" / "drug-supplement" / "pharmacogenomic"
  severity: "minor" / "moderate" / "major" / "contraindicated"
  mechanism: description of pharmacological mechanism
  disease_context: condition that modifies the interaction risk (for drug-drug-disease)
  clinical_action: what to do about it
  monitoring: what to monitor if combination is used
  evidence_source: guideline or trial
  confidence: established / inferred / exploratory
```

**Critical clinical nuance — ACE inhibitor and creatinine:**

ACE inhibitors (Lisinopril) cause a **transient rise in creatinine** (up to 10–30%) when initiated or uptitrated. This is expected, hemodynamically mediated (reduced efferent arteriolar pressure), and **NOT a reason to stop the drug**. A rise > 30% or that continues to worsen suggests renal artery stenosis and warrants investigation.

This is one of the most common clinical errors: physicians see creatinine rise after starting an ACE inhibitor and stop it, depriving the patient of nephroprotective benefit. The knowledge graph must model this explicitly:

```
[ACE Inhibitor] ──RAISES──► [Creatinine]
    properties:
      magnitude: "up to 10–30%"
      temporal: "within 1–2 weeks of initiation"
      expected: true
      action_if_rise_under_30pct: "continue — this is expected and nephroprotective"
      action_if_rise_over_30pct: "investigate renal artery stenosis"
      confidence: established
      common_error: "inappropriately stopping ACE inhibitor for expected creatinine rise"
```

### 3.6 Acute vs. Chronic Differentiation

**A creatinine of 1.8 means completely different things depending on context.** If the patient's baseline creatinine is 1.7 (stable CKD), it's a routine finding. If the baseline is 0.9 (acute kidney injury), it's an emergency. The knowledge graph must model this, and agents must distinguish between the two.

**Acute-on-chronic detection rules:**

| Biomarker | Acute change definition | Chronic change definition | Clinical difference |
|-----------|----------------------|--------------------------|-------------------|
| Creatinine | Rise ≥ 0.3 mg/dL within 48h, or ≥ 1.5x baseline within 7 days | Gradual rise over months | Acute = AKI (KDIGO AKI criteria), may be reversible. Chronic = CKD progression. |
| eGFR | Drop ≥ 25% within days | Drop ≥ 5 pts/year over months | Acute drop with recent ACE inhibitor start → consider renal artery stenosis. Acute drop with IV contrast → contrast nephropathy. |
| Potassium | Rise ≥ 0.5 mEq/L within hours/days | Gradual rise over weeks | Acute rise = cardiac emergency risk. Chronic rise = medication/diet adjustment. |
| Hemoglobin | Drop ≥ 2 g/dL within days | Gradual decline over months | Acute = bleeding. Chronic = CKD anemia or iron deficiency. |

**How agents apply this:** When new labs arrive, the agent compares against the most recent STABLE baseline — not just the previous value:

```
New creatinine: 2.4
Previous creatinine (3 days ago): 1.8
Stable baseline (average of last 3 readings over 6 months): 1.8

Analysis:
  Acute rise: 2.4 - 1.8 = 0.6 mg/dL in 3 days → meets KDIGO AKI Stage 1 criteria
  Chronic baseline: 1.8 (stable CKD Stage 3b)
  Assessment: "ACUTE kidney injury superimposed on chronic kidney disease.
    Creatinine rose 0.6 mg/dL in 3 days (baseline 1.8). This is a NEW change
    requiring urgent evaluation — not a continuation of your CKD trend."
```

**Emergency medication interactions** — interactions that matter in acute/emergency settings:

| Scenario | Interaction | Why it's different from chronic management |
|----------|------------|------------------------------------------|
| About to receive IV contrast for CT scan | Contrast + CKD + Metformin | Hold metformin. Check eGFR before contrast. ACR 2023 guidelines. |
| Acute pain management | NSAIDs + ACE inhibitor + CKD | Triple whammy — AKI risk 30x higher. Use acetaminophen instead. |
| Acute chest pain — need anticoagulation | New anticoagulant + existing medications | Check CYP interactions, renal dosing for DOACs at current eGFR |
| Post-surgical | SGLT2i + surgery/fasting | Hold SGLT2i — risk of euglycemic DKA |
| Acute infection with fever | Metformin + acute illness | Sick day rules — hold metformin during dehydration, infection |

### 3.7 Guideline-Backed Recommendations

These are the "if-then" rules that agents apply when reasoning against personal data.

| If (personal data) | Then (knowledge graph reasoning) | Source | Confidence |
|----|----|----|----|
| eGFR declining > 5 mL/min/year | Flag rapid progression — consider nephrology referral | KDIGO 2024 CKD | established |
| eGFR < 60 + Diabetes | Screen UACR every 12 months | ADA Standards of Care 2024 | established |
| UACR > 30 + Diabetes | Start ACE inhibitor or ARB regardless of BP | KDIGO 2024, ADA 2024 | established |
| On Metformin + eGFR crosses below 45 | Flag: dose reduction needed (max 1000mg/day) | FDA label, KDIGO | established |
| On Metformin + eGFR crosses below 30 | Flag: discontinue metformin | FDA label, KDIGO | established |
| On ACE inhibitor + Potassium > 5.5 | Flag: review medication, check for dietary potassium | KDIGO 2024 | established |
| Hemoglobin below normal range + CKD | Check iron studies (ferritin, TIBC, iron saturation), reticulocyte count — rule out iron deficiency first | KDIGO Anemia 2012 | established |
| Hemoglobin < 10 + CKD Stage 3+ (iron-replete) | Consider ESA therapy (e.g., Darbepoetin); target Hgb 10–11.5, do not exceed 11.5 | KDIGO Anemia 2012 | established |
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

Finding 3: Anemia — Already Present and Worsening
  Hemoglobin 10.8 = below normal range (13.5–17.5 male) → anemia is present NOW
  Hemoglobin trend: 13.0 → 12.1 → 10.8 = significant decline over 12 months
  CKD IMPAIRS EPO Production → CAUSES Anemia of CKD (knowledge graph)
  Anemia MANIFESTS_AS Fatigue (knowledge graph)
  If Hemoglobin drops below 10.0 → KDIGO recommends formal EPO evaluation
  But iron studies (ferritin, TIBC, iron saturation) should be checked NOW —
    iron deficiency is the most common treatable cause and must be ruled out
    before attributing anemia solely to CKD
  QUESTION TO PREPARE: "My hemoglobin has dropped from 13 to 10.8 over a year.
    Can we check iron studies, ferritin, and reticulocyte count? Is this anemia
    of CKD or could there be another cause?"

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

Reports come in two views — **patient view** (educational, with explanations) and **clinician view** (raw findings, evidence sources, 30-second scan). The patient sees the full report below. A clinician tapping "Clinician View" sees the compact version at the bottom.

**Patient View:**
```
══════════════════════════════════════════════════════════
 CONSILIUM — Preparation for Nephrology/Endocrinology Visit
 Based on: Lab results 2026-03-20 + historical trend
 Findings: 2 critical, 2 urgent, 2 monitor
══════════════════════════════════════════════════════════

 🔴 CRITICAL: Metformin dose exceeds safe limit
   Your eGFR is 42 (CKD Stage 3b). Current guidelines recommend
   reducing Metformin to max 1000mg/day when eGFR drops below 45.
   You are currently taking 2000mg/day.
   → Ask your doctor about dose adjustment

 🔴 CRITICAL: Rapid kidney decline — 16 points in 12 months
   Your kidney function is declining faster than the 5 points/year
   threshold that typically triggers a nephrology referral.
   → 12 months ago: eGFR 58 (Stage 3a)
   → 6 months ago: eGFR 52 (Stage 3a)
   → Now: eGFR 42 (Stage 3b)
   → Ask: What is causing this rapid decline? Do I need a referral?

 🟡 URGENT: Potassium trending up — 5.2 mEq/L
   Two factors are raising your potassium: declining kidney function
   AND Lisinopril (which is known to raise potassium).
   → Ask: Should we monitor more frequently or adjust Lisinopril?

 🟡 URGENT: Anemia present — Hemoglobin 10.8 g/dL (was 13.0)
   Your hemoglobin is below normal range (13.5–17.5 for males).
   Kidneys produce erythropoietin (EPO), which stimulates red blood
   cell production. As kidney function declines, EPO production drops.
   However, iron deficiency must be ruled out first — it's the most
   common treatable cause of anemia in CKD.
   → Ask: Can we check iron studies (ferritin, TIBC, iron saturation)
     and reticulocyte count? Is this CKD-related or another cause?

 🔵 MONITOR: Diabetes control worsening — HbA1c 7.8%
   If Metformin is reduced, control may slip further. An SGLT2
   inhibitor (e.g., Empagliflozin) treats diabetes AND has proven
   kidney and heart protection in clinical trials.
   → Ask: Would an SGLT2 inhibitor be right for me?

 🔵 MONITOR: Phosphorus slightly elevated — 4.8 mg/dL
   Kidneys normally excrete phosphorus. In CKD, phosphorus builds up
   and can trigger secondary hyperparathyroidism and bone disease.
   → Ask: Should we check PTH and vitamin D levels?

══════════════════════════════════════════════════════════
 This is not medical advice. These are questions to discuss
 with your healthcare provider based on your lab trends and
 established clinical guidelines.
══════════════════════════════════════════════════════════
```

**Clinician View** (same data, 30-second scan):
```
══════════════════════════════════════════════════════════
 CONSILIUM — Clinician Summary
 Patient data as of: 2026-03-20 | Trend: 12 months
══════════════════════════════════════════════════════════

 CRITICAL
  • Metformin 2000mg/d — eGFR 42 (< 45 threshold). FDA/KDIGO: max 1000mg/d.
  • eGFR 58→52→42 in 12mo (-16/yr). KDIGO: >5/yr = rapid progression → nephrology.

 URGENT
  • K+ 5.2 (4.5→4.8→5.2), on Lisinopril. Approaching 5.5. Dual risk: ACEi + CKD.
  • Hgb 10.8 (13.0→12.1→10.8). Below male range. Iron studies not yet done.

 MONITOR
  • HbA1c 7.8% (6.9→7.2→7.8). Not on SGLT2i. KDIGO 2024: consider if eGFR >20 + DM.
  • Phos 4.8 (>4.5 in CKD 3). PTH not checked.

 MEDS: Metformin 1000mg BID, Lisinopril 20mg QD, Amlodipine 5mg QD
 DX: CKD 3b (E11.65, N18.4), T2DM, HTN
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
| Drug Interactions (drug-drug) | 6 | ACE+K-sparing, ACE+NSAID, Metformin+Contrast, SGLT2i+Diuretic, ACE+ARB, Insulin+Beta-blocker |
| Drug Interactions (drug-drug-disease) | 4 | ACE+K-sparing+CKD, NSAID+ACE+HF, Metformin+CKD+acute illness, SGLT2i+DKA risk |
| Drug-food/supplement interactions | 5 | ACE+potassium foods, Metformin+alcohol, Warfarin+vitamin K, Levothyroxine+calcium, Statins+grapefruit |
| Pharmacogenomic interactions | 4 | CYP2D6+metoprolol, CYP2C19+clopidogrel, SLCO1B1+simvastatin, HLA-B*5801+allopurinol |
| Patient factor modifiers | 7 | Pregnancy, angioedema history, African ancestry, CYP2D6, baseline eGFR, baseline K+, elderly |
| Conflicting evidence pairs | 1+ | SPRINT vs ACCORD (BP targets by diabetes status) |
| Causal Chains | 20+ | Diabetes→Hyperfiltration→CKD, CKD→Low EPO→Anemia→Fatigue, CKD→Hyperkalemia→Arrhythmia |
| Guidelines | 5+ | KDIGO 2024, ADA 2024, AHA/ACC 2017, KDIGO Anemia 2012, KDIGO CKD-MBD 2017 |

**Estimated graph size for full wellness domain:** This triad represents one clinical scenario. The full wellness knowledge graph (all common conditions, medications, biomarkers) would be 10–50x larger. Importing from Hetionet (~47K nodes, ~2.25M edges) + Uberon (~75K nodes) provides the foundational scale.

---

## 8. Knowledge Decay — Keeping the Graph Current

Medical knowledge has a half-life. Guidelines get updated. Trials get superseded. Drugs get recalled. A knowledge graph that only grows but never prunes becomes unreliable over time. Consilium addresses this with a systematic knowledge decay mechanism.

### Staleness Detection

Every edge has a `last_reviewed` date. The system flags stale edges based on domain-specific decay rates:

| Edge type | Review interval | Rationale |
|-----------|----------------|-----------|
| Drug interactions | 12 months | New drugs, new interactions discovered regularly |
| Clinical guidelines | 24 months | Major guidelines update every 2–5 years |
| Pathophysiology | 60 months | Basic mechanisms change slowly |
| Drug thresholds (e.g., eGFR cutoffs) | 12 months | Regulatory updates, new trial data |
| Exploratory/inferred edges | 6 months | Weak evidence needs frequent re-evaluation |
| Epidemiological associations | 36 months | Large cohort studies publish slowly |

**Staleness workflow:**
```
Edge last_reviewed > review_interval
    │
    ├── System searches for newer papers on the same topic (automated)
    │   ├── Newer paper found → extract, validate, update edge or supersede
    │   ├── No newer paper → bump last_reviewed, keep edge as-is
    │   └── Retraction found → flag edge, downgrade confidence, notify user
    │
    └── If edge was originally from a specific guideline:
        └── Check if guideline has a newer version → auto-update if available
```

### Supersession

When newer evidence replaces older evidence, the old edge isn't deleted — it's marked `superseded_by` pointing to the new edge. This preserves the provenance chain.

```
[Old Edge] Metformin CONTRAINDICATED_WHEN eGFR < 30
  evidence_source: "FDA label 2006"
  superseded_by: edge_id_12345
  supersession_note: "2022 ADA Standards now allow cautious use at eGFR 25-30 with monitoring"
  status: "superseded"

[New Edge] Metformin CAUTION_WHEN eGFR 25-30
  evidence_source: "ADA Standards of Care 2022, Inzucchi et al. 2014"
  supersedes: edge_id_old
  confidence: inferred  // not yet established — practice is shifting but FDA label unchanged
```

### Automatic Confidence Downgrade

Edges that fail re-validation get downgraded, not deleted:

| Trigger | Action |
|---------|--------|
| Stale (overdue for review) + no newer evidence found | Mark `stale: true`, keep confidence |
| Contradicted by a single new study | Add CONFLICTS_WITH edge, keep both |
| Contradicted by multiple new studies or a meta-analysis | Downgrade confidence (established → inferred) |
| Source paper retracted | Downgrade to `exploratory`, flag prominently |
| Guideline issuing body publishes update | Supersede with new guideline edge |

### Mesh-Assisted Decay

The mesh helps with staleness detection. When one node discovers a retraction, guideline update, or superseding trial, it broadcasts to the mesh:

```
{
  type: "knowledge_update",
  action: "retraction" / "supersession" / "guideline_update",
  affected_edge: { from: "Metformin", rel: "CONTRAINDICATED_WHEN", to: "eGFR < 30" },
  new_evidence: { doi: "...", summary: "..." },
  broadcast_by: node_id
}
```

All nodes with matching edges can auto-update. This means a single node discovering a retraction propagates the correction across the entire mesh — knowledge hygiene at scale.

---

## 9. What This Validates (and What's Still Missing)

This exercise confirms several key architectural assumptions:

1. **Knowledge vs. personal data separation works.** The entire knowledge graph above contains zero personal data. It's textbook medicine. Personal data (lab values, medication lists) lives in per-person folders and is loaded at query time. The separation is clean and natural.

2. **Cross-system reasoning is the killer feature.** No single specialist would catch all six findings in the example above. A nephrologist focuses on CKD. An endocrinologist focuses on diabetes. The knowledge graph connects them — Metformin dose + potassium + anemia + phosphorus — across systems simultaneously.

3. **Thresholds make the graph actionable.** Without thresholds, the graph is just a map. With thresholds (eGFR < 45 = dose-adjust metformin), agents can generate specific, actionable questions for professionals.

4. **Research papers enrich the graph naturally.** Every paper a doctor reads adds relationships and evidence. A nephrologist's knowledge graph has deeper renal pathophysiology. An endocrinologist's has richer diabetes mechanisms. These differences make the mesh valuable — different nodes have different depths.

5. **The mesh adds non-obvious value.** A standard knowledge graph catches textbook findings. An expert's enriched graph catches renal artery stenosis and beta-cell failure — patterns that come from clinical experience, not just guidelines.

6. **Cypher queries are straightforward.** The graph schema maps naturally to the kinds of questions agents need to answer. No complex joins, no data transformation — just graph traversal.

7. **Qualified claims prevent dangerous oversimplification.** Medical relationships are population-specific, dose-dependent, and full of caveats. The qualified claims schema (effect size, population, confounders, limitations) ensures agents present nuanced information rather than false certainties.

8. **Conflicting evidence is a feature, not a bug.** SPRINT vs. ACCORD shows that two well-designed trials can reach different conclusions for different populations. The CONFLICTS_WITH edge lets agents explain *why* the evidence disagrees rather than silently picking a winner.

9. **Patient modifiers catch dangerous prescribing errors.** MODIFIED_BY edges for pregnancy, genetic variants, and prior adverse reactions act as safety rails. An ACE inhibitor is excellent for most hypertensive patients — but dangerous in pregnancy, less effective in some populations, and contraindicated after angioedema.

10. **Knowledge decay prevents silent staleness.** Medical guidelines update regularly. Without systematic review intervals and supersession tracking, an aging graph becomes confidently wrong — the most dangerous failure mode. The decay mechanism ensures the graph is honest about what it doesn't know anymore.

11. **Standard terminologies make the graph interoperable.** SNOMED CT, RxNorm, and LOINC codes on every clinical node enable entity resolution across imported knowledge bases, research papers, and user-uploaded data. Without them, entity matching is unreliable — "T2DM" vs "Type 2 DM" vs "NIDDM" would be three different entities.

12. **Stratified reference ranges prevent demographic false positives.** Single-range biomarker thresholds generate false alerts for women, elderly patients, and pregnant individuals. Population-stratified ranges are essential for threshold checking accuracy.

13. **Data quality determines system trustworthiness.** Plausibility checks, unit normalization, lab source tracking, and extraction confidence scoring are not nice-to-haves — they prevent the "garbage in, garbage out" failure mode that ruins clinical decision support systems.

14. **Acute-on-chronic differentiation is essential for safety.** A creatinine of 2.4 means "routine CKD worsening" if the baseline is 2.2, but "acute kidney injury requiring emergency evaluation" if the baseline is 1.8 and the rise happened in 3 days. Without acute-on-chronic logic, the system misses emergencies.

15. **Alert severity triage prevents alert fatigue.** Every finding must be classified as critical / urgent / monitor / informational. Without triage, a system that generates 15 findings per patient will be ignored — clinicians and patients alike stop reading after the first 3 undifferentiated alerts.

16. **Medication reconciliation at transitions of care is a life-saving feature.** The #1 cause of preventable harm in hospitals is medication errors during transitions. The system's complete medication history makes it uniquely positioned to catch these — but only if reconciliation is actively triggered when medication lists change.

17. **The clinician view is as important as the patient view.** A doctor will never read "Your kidneys are like a filter." A doctor will read "eGFR 58→52→42 in 12mo (-16/yr). Metformin 2000mg — exceeds FDA threshold. KDIGO 2024." Two views of the same data, two audiences, both essential.

**Known gaps (acknowledged, designed but not yet validated):**
- Social determinants of health are modeled with actionable agent behavior (cost-aware medication suggestions, transportation-aware referrals) but need real-world testing
- Outcome tracking is designed but requires deployment to calibrate thresholds
- Cohort query mode for professionals needs privacy-preserving design review
- Emergency Data Card needs validation with actual ER workflows — does the format work in practice?

---

## 10. Next Steps

**Priority order (informed by data scientist + ER physician review — dependencies and patient safety drive sequencing):**

1. **SNOMED/RxNorm/LOINC mapping.** Map all existing Condition, Medication, and Biomarker nodes to standard terminologies. This is a prerequisite for everything else — entity resolution, Hetionet import, FHIR ingestion, and paper extraction all depend on it. Do this first.
2. **Stratified reference ranges.** Convert all single-value Biomarker ranges to population-stratified reference ranges (sex, age, pregnancy). Without this, threshold checking generates false positives for women and elderly patients.
3. **Alert severity triage.** Implement the critical/urgent/monitor/informational classification and build both patient view and clinician view output formats. This is a prerequisite for any user-facing deployment — without it, alert fatigue kills adoption.
4. **Emergency Data Card.** Build the minimal medication/allergy/condition export to phone lock screen, wallet card, and QR code. This is the simplest feature with the highest life-saving potential — no AI, no graph database needed. Ship it early.
5. **Medication reconciliation.** Implement the transition-of-care comparison logic. This saves lives from day one and requires only the medication list and interaction graph — no complex reasoning.
6. **Acute-on-chronic differentiation.** Implement baseline calculation and KDIGO AKI staging. This is critical for safety — without it, the system can't distinguish emergencies from routine findings.
7. **Hetionet schema alignment proof-of-concept.** Map Hetionet's 24 metaedge types to our relationship types for the renal + cardiovascular + endocrine subset (~5% of Hetionet). Validate entity resolution via UMLS crosswalk.
8. **Load this into Neo4j.** Convert the hand-curated relationships above into Cypher CREATE statements with standard terminology codes and test actual queries.
9. **Build extraction validation pipeline.** Given a PubMed paper, test: dual extraction, quantitative cross-check, negation detection, conditional subgroup extraction. Measure accuracy rates.
10. **Build data quality layer.** Implement plausibility checks, unit normalization, lab source tracking, and extraction confidence scoring for lab result ingestion.
11. **Test agent reasoning.** Given the personal data scenario above, can an LLM + these Cypher queries + stratified reference ranges + alert triage + clinician view produce the full output?
12. **Expand to a second domain (finance).** Validate that the same architecture works for a completely different knowledge domain.
