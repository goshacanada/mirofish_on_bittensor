# Consilium — PR/FAQ

---

## Press Release

### Consilium — Open-Source Personal Knowledge System That Prepares You for Professional Appointments

*Your data, your device — give your doctor the complete picture in the 15 minutes they have*

Consilium is a free, open-source system that helps people advocate for themselves in complex domains outside their expertise. Upload your lab reports, and Consilium organizes your data against a medical knowledge graph, checks for drug interactions, flags values that exceed safety thresholds, and generates a factual summary — the information your doctor needs but doesn't have time to assemble. Your data never leaves your device.

With large patient-to-doctor ratios, a doctor's attention is measured in minutes, not hours. They see today's labs, not the trend over 18 months. They see a medication list but don't have time to cross-check every interaction against every comorbidity across three different specialists. Important information gets missed — not because doctors are careless, but because the system gives them too many patients and too little time. The patient is the only person who has ALL their data across ALL providers over ALL time. Consilium organizes that data so the right information reaches the doctor.

Consilium is a general-purpose knowledge organization and educational entertainment system. The platform is built on a domain-agnostic architecture that will support multiple domains over time — wellness & body literacy first, with personal finance, legal literacy, and others planned. But that future is built one validated step at a time.

"An engineer gets lab results back showing declining kidney function and elevated potassium. Their nephrologist prescribed an ACE inhibitor. Their endocrinologist kept them on metformin. Their primary care doctor doesn't have time to cross-check all of it — they have 15 minutes and 20 patients that day. Nobody is being negligent. There's just too much information scattered across too many providers and too little time. Consilium assembles the complete picture: here's the trend over 18 months, here are the interactions between all your medications, here's the threshold your eGFR crossed last month. Not 'Consilium thinks you should do X' — just the organized facts. The doctor brings the judgment. Consilium brings the information. A 15-minute appointment where the doctor gets the complete picture is worth more than a 30-minute appointment where they're assembling it from scratch."

**How it works today:**

1. **You load your data**: Drop in a lab report PDF or connect your Apple Health data. Consilium's local AI extracts structured values, checks them for plausibility (catches extraction errors and unit confusion), and stores them in per-person folders on your device. You can organize by household member. Your data never leaves your machine.

2. **The knowledge graph connects the dots**: Your data is analyzed against a curated medical knowledge graph — a map of how conditions, medications, biomarkers, and symptoms are connected. Every relationship carries a confidence tier (established, inferred, exploratory) and standard medical codes (SNOMED CT, RxNorm, LOINC) for precision.

3. **Deterministic safety checks**: The system runs rule-based threshold checks — not probabilistic LLM guesses. If your metformin dose exceeds safe limits for your kidney function, it will always catch it. Drug-drug interactions are checked against every combination in your medication list. These are deterministic rules that fire every time, not suggestions an AI might or might not surface.

4. **Your preparation report**: Every finding is classified by severity — 🔴 Critical (immediate danger), 🟡 Urgent (address at next appointment), 🔵 Monitor (worth tracking). The report presents factual information — complete medication list across all prescribers, lab trends over months/years, threshold violations with guideline citations, drug interactions with severity. Not "Consilium thinks you should do X" — just the organized data your doctor needs to develop critical insights. The doctor brings clinical judgment. Consilium brings the complete, organized picture.

**What's coming next** (the system is designed for this from day one):

5. **Multi-agent specialist teams** *(Phase 1)*: Instead of one generalist agent, specialist agents (nephrology, endocrinology, hematology) will form swarms based on your data, debate findings, and produce richer analysis. Longitudinal trend monitoring will flag gradual declines before they become emergencies.

6. **Peer-to-peer mesh** *(Phase 2)*: Your node will be able to query other Consilium instances for perspectives — anonymized, no personal data shared. Expert nodes enriched by years of research papers will provide the most valuable responses.

7. **Expert knowledge network** *(Phase 2)*: Credentialed domain experts will answer targeted micro-questions to improve the shared knowledge graph. Pro subscribers will fund these questions. The system will proactively discover and ingest research papers.

8. **Additional domains** *(Phase 3)*: Personal finance, legal literacy, engineering — the same domain-agnostic architecture, new plugins.

**Consilium does not provide professional advice of any kind.** It is an educational entertainment and knowledge exploration tool that helps users organize information and prepare for conversations with qualified professionals.

**Consilium is available for early access starting [date]. The platform is free and open-source. Upload your lab results, see what your data means, and walk into your next appointment with the right questions. Future phases will add mesh consultation, expert validation, and additional domains — but the foundation is useful from day one.**

---

## Frequently Asked Questions

### User FAQ

**Q: What is Consilium?**

A: Consilium is a patient advocacy tool that runs on your own computer. Your doctor has 15 minutes per patient. They can't cross-check every medication interaction across three specialists or spot a slow trend over 18 months of labs from different providers. You upload your lab reports and medication lists, and Consilium organizes your data against a medical knowledge graph — surfacing the key information your doctor needs: lab trends, threshold violations, drug interactions, the complete medication picture across all prescribers. Not opinions — facts. The doctor brings clinical judgment; Consilium brings the organized information. The platform is designed to support additional domains (finance, legal, engineering) in the future.

**Q: Is Consilium medical/financial/legal advice?**

A: No. Consilium is an educational entertainment and knowledge exploration tool — legally, it is a general-purpose knowledge platform, not a professional service. For experts, it functions as a professional knowledge management system (capturing career knowledge, organizing client data, cross-specialty consultation), but it does not diagnose, treat, prescribe, advise, or recommend. It helps you understand your own data and generates questions you might want to discuss with qualified professionals. Always consult your healthcare provider, financial advisor, or attorney before making any decisions.

**Q: Where is my data stored?**

A: On your device only. Consilium stores two things locally: (1) a **knowledge graph** — domain knowledge extracted from research papers, expert answers, and your observations, and (2) a **personal data store** — your raw records (lab reports, financial statements, etc.) organized in per-person folders. Your personal data never leaves your machine. The knowledge graph captures domain knowledge (like "elevated creatinine correlates with CKD"), not personal information. You can audit, export, and delete any record at any time. You can configure your own backup solution (Google Drive, OneDrive, etc.) — Consilium provides the export mechanism but takes no responsibility for backup security or retention.

**Q: What domains does Consilium support?**

A: At launch, Consilium supports **Wellness & Body Literacy** — understanding your health data and preparing for doctor visits. The platform architecture is domain-agnostic, designed so that additional domains (personal finance, legal literacy, engineering) can be added as plugins without changing the core platform. These will come in future phases as the wellness domain proves the model works.

**Q: Do I need to be technical to use Consilium?**

A: No. On macOS, you download and install a single app. You open it, upload lab report PDFs or import your Apple Health data, and the system does everything else — extraction, knowledge graph analysis, preparation report generation. You never see a terminal, a database, or a config file. In Phase 3, a dedicated hardware appliance (open-source design) will make this accessible on non-Mac devices.

**Q: How do I add my data?**

A: At launch, two ways: (1) Upload a document — drag-and-drop a lab report PDF. AI extracts structured data, checks for plausible values and correct units, and asks you to confirm before storing. (2) Export Apple Health data — the system parses your Apple Health XML export for heart rate, activity, sleep, and other metrics. All extracted values are mapped to standard medical terminologies (LOINC, SNOMED CT, RxNorm) so that data from different sources matches correctly. Additional integrations (Oura Ring, patient portals, CGMs) will come in future phases based on user demand.

**Q: How much does it cost?**

A: The platform is free and open-source. At launch, everything — the local knowledge graph, data ingestion, preparation reports, drug interaction checking — costs nothing. In future phases, Consilium Pro (≤$20/month) will add expert-validated knowledge via the Expert Portal and mesh consultation. That's the same price as ChatGPT Plus — but Consilium runs on your data, remembers everything, and has a knowledge graph with deterministic safety checks. Free users will always benefit from knowledge improvements funded by Pro users.

**Q: Can domain experts use Consilium themselves?**

A: Yes — and they're a key target audience. No one knows everything: a cardiologist needs nephrology insight, a primary care doctor needs help figuring out which specialist to refer a patient to. At launch, experts can organize patient or client data in per-person folders and use the knowledge graph + preparation reports just like any user. In future phases, experts will be able to feed research papers into their knowledge graph, build a career-long personal knowledge base, tap cross-specialty insight through the mesh, and optionally earn income by answering micro-questions through the Expert Portal.

**Q: How does Consilium handle research papers?**

A: At launch, the knowledge graph is pre-curated with established medical knowledge (CKD, diabetes, hypertension, drug interactions). In Phase 1, users will be able to upload research papers — the system will extract knowledge, assess credibility, and add findings to the graph. In Phase 2, the system will proactively discover papers, validate them through mesh consensus across thousands of nodes, and generate targeted micro-questions for experts when it hits genuine uncertainties. Over time, the mesh becomes a self-curating knowledge network that gets better the more it grows.

**Q: Can I organize data for multiple people?**

A: Yes. Consilium supports per-person folders. A family can have separate folders for each household member (dad's health records, mom's health records, child's health records). Each person's data is fully isolated by default — the agent operating on one person's data cannot access another's. The knowledge graph is shared across all folders because it contains domain knowledge, not personal data. In future phases, professionals will be able to use per-client folders with cohort query capabilities.

**Q: Can Consilium use cloud AI for harder questions?**

A: Not at launch — Phase 0 runs entirely on your local hardware via Ollama. In Phase 1, you'll be able to configure an API key for external AI services (OpenAI, Anthropic, Google) to escalate harder reasoning tasks to more powerful models. This will be optional and user-controlled. In Phase 2, the reasoning chain expands to: local LLM → mesh consultation → external AI → Expert Portal.

**Q: How does Consilium keep its knowledge up to date?**

A: At launch, the knowledge graph ships with curated, current medical knowledge and receives updates through the open-source plugin repository — like app updates. In Phase 2, the system will add automated knowledge decay: every edge will have a review interval, the system will automatically search for newer evidence, and mesh-propagated corrections will keep the entire network current. The architecture supports this from day one — every edge already carries `last_reviewed_date` and `superseded_by` properties — but the automated staleness workflow is a Phase 2 feature.

**Q: What if I'm in an emergency and can't access my phone?**

A: In Phase 3, Consilium will generate an **Emergency Data Card** — a minimal summary of your current medications, allergies, active conditions, and most recent critical lab values, exportable to your phone's lock screen medical ID, a printable wallet card, or an NFC bracelet. At launch, you can always export your medication and condition list as a PDF to print or save to your phone.

**Q: What happens when my medications change after a hospital stay?**

A: In Phase 1, Consilium will add **medication reconciliation** — when you update your medication list after an appointment or hospital discharge, the system compares the new list against your previous one and flags: new medications that interact with existing ones, medications that were removed (intentional or oversight?), doses that changed, and medications that should have been adjusted but weren't. At launch (Phase 0), the system always checks your current medication list for drug interactions and threshold violations — so if you update your medications, the next preparation report will catch any new issues.

**Q: Can my doctor see my Consilium data?**

A: Yes — and this is a core use case. You can show the preparation report on your phone or print it as a PDF. The report is factual, not opinioned: complete medication list, lab trends, threshold violations with guideline citations, drug interactions. It gives the doctor the complete picture they don't have time to assemble from your chart — especially information from other providers they may not even know about. In Phase 1, a dedicated **Clinician View** will add standard medical abbreviations, ICD-10 codes, and compact formatting that a doctor can scan in 30 seconds.

**Q: Does Consilium learn from my doctor's feedback?**

A: Not at launch. In Phase 2, you'll be able to record outcomes after appointments — whether your doctor agreed with a flag, dismissed it, or ordered more tests. This will calibrate the system to your specific situation over time. Anonymized outcome signals will be shareable via mesh to help calibrate thresholds across the network. Outcome recording will always be optional.

**Q: How does Consilium handle data from different labs?**

A: The system normalizes units automatically (e.g., creatinine in μmol/L → mg/dL), checks for physiologically impossible values (eGFR of 420 = likely extraction error), and asks you to confirm any extracted value that triggers a safety-critical threshold. In Phase 1, when longitudinal trend analysis is added, the system will also track which lab produced each measurement and warn when a trend line includes values from different labs — because the same blood sample can produce slightly different values at different facilities.

**Q: Can I export my data?**

A: Yes. Your knowledge graph and personal data store are both on your device. Export as JSON, CSV, or domain-specific formats (FHIR for health data) at any time. You can audit every record Consilium holds, delete any record at will (immediately and permanently), and configure your own backup solution (Google Drive, OneDrive, etc.). You own your data unconditionally.

---

### Expert FAQ *(Expert Portal launches in Phase 2)*

**Q: How do I earn money on Consilium?**

A: First and foremost, Consilium is a personal knowledge assistant for your professional work — organize patient or client data in per-person folders, and (in Phase 1) feed research papers into your knowledge graph. Second, when the Expert Portal launches (Phase 2), you can earn income by answering targeted micro-questions in your specialty — on your phone, during downtime. Each question takes 10–30 seconds: multiple choice, yes/no, threshold sliders, or short rankings. Pay is $0.05–0.50 per answer, funded by Pro subscriptions. You earn more as your reputation score increases — like Uber, high-rated experts get more questions and better pay rates. For retired professionals, this is a way to keep your knowledge growing, stay connected to your field, and earn supplemental income — all on your own schedule, with no scheduling, no commute, and no liability.

**Q: What kind of questions will I see?**

A: Targeted micro-questions that help the system curate knowledge — never case-specific or tied to an individual user. Many questions arise when the system encounters uncertainties during automated paper curation. Examples for a physician: "Is there evidence for gut microbiome affecting CKD progression? [Strong / Emerging / No evidence]" or "A recent RCT suggests Metformin may be safe at eGFR 25–30. Does this align with current practice? [Yes, shifting / No, still contraindicated / Depends]" or "At what eGFR level would nephrology referral typically be considered? [slider]". Each answer doesn't just add knowledge — it improves the system's ability to curate future papers in that domain. You are calibrating an automated knowledge system, not reviewing papers or providing patient care.

**Q: How do I sign up?**

A: You sign up on the Consilium Expert Portal, verify your credentials (license number, certification, etc.), and start answering questions. Payment is in dollars via Stripe — weekly payouts. No cryptocurrency, no special software, no scheduling.

**Q: What about liability?**

A: You answer general domain knowledge questions. You never see user data, never review specific cases, and never provide professional services. No professional-client relationship is created. This is legally comparable to writing exam questions, contributing to a medical textbook, or answering questions on a professional Q&A site.

**Q: How is my answer quality measured?**

A: Agreement with other credentialed experts on the same question (50%), accuracy on known-correct test questions injected blind (30%), and downstream improvement to the knowledge graph (20%). High-reputation experts earn higher pay rates and access to more complex question types.

---

### Community & Contributor FAQ

**Q: How does Consilium sustain itself?**

A: At launch, Consilium is free and open-source — sustained by community contributions. Revenue comes later:

1. **Phase 0–1**: The platform is free. No revenue. Sustained by the open-source community. This is the "prove it works" phase.
2. **Phase 2**: The **Expert Portal** launches as a company-operated service funded by Pro subscriptions (≤$20/month). The subscription revenue covers expert payouts + portal operations. This is the sustainable revenue model.

The platform itself will always be free with no paywalls. Pro adds expert-validated knowledge — the same price people already pay for ChatGPT Plus, but running on your own data with a knowledge graph and deterministic safety checks.

**Q: What makes Consilium different from existing tools?**

A: Consilium is the only tool that gives the patient — the one person with ALL their data across ALL providers — the ability to organize it and bring the complete picture to their doctor. No existing tool does this: (1) local-first data storage where your data never leaves your device, (2) a medical knowledge graph with deterministic threshold and drug interaction checking, (3) severity-triaged factual reports that surface the information the doctor needs — not opinions, not "AI thinks you should," but organized facts: trends, interactions, threshold violations with citations. ChatGPT forgets everything between sessions and gives probabilistic opinions. WebMD is static. Fitbit shows data without connecting it. EHR clinical decision support fires so many alerts that doctors override 95% of them — and patients never see any of it.

Consilium puts the patient in a position to advocate for themselves with organized information, not confusion. As the system grows through future phases, it becomes even more powerful — but the foundation is useful from day one.

**Q: How do you solve the expert cold-start problem?**

A: The Expert Portal is Phase 2 — it's not needed for launch. Phase 0 ships with a curated knowledge graph; Phase 1 adds user-driven paper ingestion. By the time the Expert Portal launches, there should be an active user base generating real knowledge gaps that experts can fill. When that time comes, the micro-question model dramatically lowers the bar: 10-second answers on a phone, $0.05–0.50 per answer, CME credit partnerships, and the "experts are users first" lever — experts join because Consilium is the best personal knowledge tool for their own work, and Expert Portal participation is a bonus. Target: 50 credentialed experts before Expert Portal launch.

**Q: Why is Consilium not a regulated product?**

A: Consilium is a general-purpose knowledge organization and educational entertainment platform. It does not diagnose, treat, prescribe, or recommend. It organizes user-provided data, checks it against a knowledge graph, and generates questions for the user to discuss with their professional.

The platform architecture is domain-agnostic — it's a graph database + agent framework. At launch, the wellness knowledge graph is bundled, but the platform itself contains no hardcoded medical logic. In future phases, domain intelligence will move to external open-source community plugins — the same model as VS Code (doesn't regulate extensions), Docker (doesn't regulate containers), and WordPress (doesn't regulate plugins).

Consilium uses dual framing appropriate to each audience: "educational entertainment and knowledge exploration" for regulatory and legal contexts, and "professional knowledge management" for expert recruitment and product documentation. A spreadsheet used by a doctor is both "general-purpose software" (legally) and "a clinical tool" (practically). Same principle.

Key defenses:
1. **No professional advice**: Agents present patterns and generate questions — never recommendations, diagnoses, or prescriptions.
2. **Local data only**: User data never leaves the device. No HIPAA covered entity relationship.
3. **User-initiated everything**: The user drives every action — loading data, generating reports, sharing with their doctor.

**Q: How does the domain plugin system work?**

A: At launch (Phase 0), the wellness plugin is bundled with the application — you install Consilium and the wellness knowledge graph is ready to use. The platform architecture is designed for a future (Phase 3) where plugins are pulled dynamically from GitHub repositories when users load new types of data, enabling community-created domains.

Each plugin will be a self-contained GitHub repo containing: (1) a foundational knowledge graph for the domain, (2) domain-specific AI agents, (3) data parsers for domain document types, and (4) micro-question templates for expert validation. The format will be open and documented. Critically: the platform will never auto-install plugins or pre-load domain capabilities. Every domain activation is user-initiated — creating a clean legal separation between the platform (generic infrastructure) and domain intelligence (user-installed community software).

**Q: How does the mesh work?**

A: The mesh is a Phase 2 feature — not available at launch. When it ships, it will be a BitTorrent-style peer-to-peer network that does two things: (1) **answers questions** — when your local agents face something beyond their confidence, they query other nodes for perspectives, and (2) **shares knowledge** — when any user discovers and verifies a research paper, the extracted knowledge is announced so all interested nodes can benefit. Mesh queries and paper sharing will use a credit-based barter system (earn by serving, spend by asking). No money, no crypto, no central server. The mesh architecture is designed from day one but implemented after the local-only experience proves its value.

**Q: How does the Expert Portal work?**

A: The Expert Portal is a Phase 2 feature. When it launches, it will be a company-operated service connecting Pro subscribers (≤$20/month) with credentialed domain experts. When your local agents identify a knowledge gap, the system will generate a targeted micro-question and route it to matched experts. Experts answer on their phone (10–30 seconds), get paid via Stripe, and the answer improves the shared knowledge graph for everyone. Expert quality is managed through an Uber-style reputation system. The portal is domain-agnostic — it routes by competence tags, not by domain labels.

**Q: How does Consilium handle conflicting medical evidence?**

A: The knowledge graph is designed to model conflicting evidence honestly. Where medical evidence conflicts — different trials reaching different conclusions for different populations — the graph stores both sides with a `CONFLICTS_WITH` edge explaining why. For example, blood pressure targets differ for diabetics vs. non-diabetics. The agent presents both sides and notes current guidelines. As the graph grows through paper ingestion (Phase 1) and expert validation (Phase 2), this becomes increasingly important — the system never silently picks a winner when the evidence genuinely disagrees.

**Q: What are the biggest risks?**

A: In order of severity, organized by phase:

**Phase 0 risks (immediate):**
1. **Data quality from PDF parsing**: Lab report PDFs have wildly inconsistent formats. Unit confusion, misread values, and extraction errors can produce wrong alerts. Mitigation: plausibility checks, unit normalization, extraction confidence scoring, user confirmation for safety-critical thresholds.
2. **Standard terminology mapping**: Without SNOMED CT, RxNorm, and LOINC on all clinical nodes, entity resolution will be unreliable. "Type 2 DM" must equal "T2DM" must equal "NIDDM." This is a prerequisite — built into Phase 0.
3. **User adoption**: Does the core value proposition work? Do people actually upload their lab results and take the preparation report to their doctor? If not, nothing else matters.
4. **Local LLM quality**: Can a quantized 14B model on consumer hardware produce useful preparation reports? If local inference is too weak, the product doesn't work without external AI (Phase 1).
5. **Regulatory reclassification**: Low probability given the general-purpose framing, but if a regulator targets Consilium specifically, it could slow adoption. Mitigation: proactive regulatory engagement, healthcare attorney review, dual framing strategy.

**Phase 1+ risks (future):**
6. **LLM extraction accuracy for papers**: Negation detection fails 5–15% of the time. Conditional/subgroup extraction is worse. Mitigation: dual extraction, quantitative cross-check, dedicated negation pass. Uses frontier external AI, not local LLMs.
7. **Schema alignment with existing KGs**: Importing Hetionet (2.25M edges) and SPOKE (4M+ nodes) is a major data engineering effort. Deferred to Phase 2.
8. **Expert adoption**: Deferred to Phase 2. By then, user base and knowledge gaps will provide natural demand.
9. **Network effects may not materialize**: If mesh perspectives don't add meaningful value, users won't engage. Validate before scaling.
