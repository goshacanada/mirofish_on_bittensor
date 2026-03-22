# HealthMesh — PR/FAQ

---

## Press Release

### HealthMesh Launches the First Doctor-Validated, Decentralized Personal Health AI

*A permanent AI medical council on every person's device — powered by Bittensor, validated by real physicians*

**San Francisco, CA** — HealthMesh today announced the launch of its Bittensor subnet for decentralized personal health intelligence. HealthMesh gives every person a permanent team of AI medical specialists running on their own computer, backed by a knowledge graph of their complete health history. When the AI faces a complex case, it consults a global mesh of thousands of other instances — and real, credentialed physicians validate the quality of every AI opinion.

Unlike existing health AI tools (ChatGPT, Apple Health Intelligence, telehealth chatbots), HealthMesh keeps all patient data on the patient's own device. No health record, lab result, or wearable reading ever leaves the patient's machine. Only anonymized clinical questions travel the network; only opinions come back.

"Today's health AI is stateless — it forgets you after every conversation," said [Founder]. "Your real doctor remembers your history, sees your trends, and coordinates with specialists. HealthMesh gives everyone that experience, permanently, with AI specialists that never forget and a global network of peers to consult."

**How it works:**

1. **Your health graph**: Lab results, wearable data, medications, conditions, and doctor notes are stored in a local graph database (Neo4j) on your device. The system comes pre-loaded with a knowledge graph of human physiology — organs, systems, biomarkers, and their relationships — so your AI specialists understand *why* your creatinine matters for your kidneys.

2. **Your consilium**: A permanent team of AI specialist agents (cardiology, nephrology, endocrinology, and others) monitors your health graph continuously. When new data arrives — a lab panel, a week of Apple Watch readings, a doctor's note — the relevant specialists review it, consult each other, and write a plain-language summary with actionable items for your next doctor visit.

3. **The mesh**: When your local consilium faces a case beyond its confidence, it sends an anonymized query to the HealthMesh network — thousands of other instances running for other people. Each responding node draws on its own patient's longitudinal case history to offer an opinion. Your consilium aggregates these mesh opinions alongside its own analysis. No data is shared — only reasoning.

4. **Doctor validation**: Every AI opinion produced by the network is subject to review by credentialed physicians. HealthMesh runs as a Bittensor subnet where AI agent nodes (miners) earn TAO tokens proportional to the quality of their clinical opinions, as judged by board-certified doctors. Doctors review anonymized cases through a simple web portal, earning competitive per-case compensation without needing any cryptocurrency knowledge. Validators — the infrastructure operators connecting doctors to the network — compete to recruit the best physician reviewers, creating a self-improving quality loop.

**HealthMesh is available for early access starting [date]. Patients can run the local consilium for free. Mesh consultations and doctor-validated opinions require a Pro subscription ($29/month).**

For more information, visit [healthmesh.ai].

---

## Frequently Asked Questions

### Customer FAQ

**Q: Is HealthMesh a replacement for my doctor?**

A: No. HealthMesh is a health data organization and monitoring tool that helps you prepare for doctor visits. It tracks your health data over time, identifies trends, and highlights items worth discussing with your physician. It does not diagnose, treat, or prescribe. All agent outputs are labeled as informational, and the system actively directs you to consult your own healthcare provider for medical decisions.

**Q: Where is my health data stored?**

A: On your device only. HealthMesh runs a local graph database (Neo4j) on your computer. Your lab results, conditions, medications, wearable readings, and doctor notes never leave your machine. There is no cloud account, no server upload, and no way for HealthMesh or anyone else to access your data.

**Q: What happens when HealthMesh queries the mesh network? Is my data sent to strangers?**

A: When your consilium consults the mesh, it sends an anonymized clinical question — never your identity, demographics, or raw data. For example: "Patient with Type 2 Diabetes and declining eGFR over 20 months — what would a nephrology specialist watch next?" Other nodes respond with clinical opinions drawn from their own patient's experience. Your node never shares data; it only asks questions and receives answers.

**Q: What data sources does HealthMesh support?**

A: At launch: Apple Health (heart rate, ECG, SpO2, activity, sleep), Oura Ring (HRV, sleep, temperature), Continuous Glucose Monitors (via LibreView/NightScout), lab result PDFs (photographed or uploaded — the AI extracts structured data), and manual entry. HL7 FHIR import is supported for patients who can export records from their healthcare provider's patient portal.

**Q: How accurate are the AI specialist opinions?**

A: Every AI opinion produced by the mesh is subject to review by board-certified physicians in the relevant specialty. Doctors rate AI opinions against structured clinical rubrics, and AI agents that consistently produce low-quality opinions are deprioritized by the network. The system also injects known-correct test cases to continuously calibrate quality. However, HealthMesh is not FDA-cleared as a diagnostic tool, and all opinions should be discussed with your physician before acting on them.

**Q: Do I need to be technical to use HealthMesh?**

A: No. On macOS, you download and install a single app — no terminal, no commands, no configuration. You open the app, link your health accounts (Apple Health, Oura, patient portal), upload lab PDFs, and the system does everything else. The dashboard shows your consilium opinions in plain language.

In the future, we are developing the HealthMesh Home Unit — a dedicated appliance ($399–499) that you plug into power and Wi-Fi. It runs 24/7 in your home, monitoring your health continuously, with everything pre-configured. You access it from any browser on your home network.

**Q: How do I add my health data?**

A: Three ways, all simple:

1. **Link an account**: Connect Apple Health, Oura Ring, or your hospital patient portal (Epic MyChart, Cerner). Data syncs automatically.
2. **Upload a document**: Drag-and-drop a lab PDF or take a photo of a lab printout with your phone. The AI extracts the data and asks you to confirm: "We found: Creatinine 1.8, eGFR 52 — correct?"
3. **Download a summary**: When preparing for a doctor visit, tap "Prepare for visit" to generate a 1-page PDF with your trends, flagged items, and suggested questions.

**Q: How much does it cost?**

A: The local consilium (your health graph + AI specialist team, no mesh) is free and open-source. The Pro tier ($29/month) adds mesh consultations, doctor-validated opinions, and priority case routing. The HealthMesh Home Unit ($399–499, available later) includes the hardware appliance with 1 year of Pro subscription.

**Q: What if I stop using HealthMesh? Can I export my data?**

A: Yes. Your health graph is a standard Neo4j database on your device. You can export it as JSON, CSV, or FHIR-format data at any time. You own your data unconditionally.

---

### Stakeholder FAQ

**Q: How does HealthMesh make money?**

A: Three revenue streams:

1. **Patient subscriptions** ($29/month Pro tier) for mesh consultations and doctor-validated opinions. Target: 10,000 subscribers within 18 months of mainnet launch = $3.5M ARR.
2. **Enterprise API** for hospitals, insurers, and pharmaceutical companies seeking anonymized, aggregated population health intelligence (federated analytics — no raw data leaves patient devices). Pricing: $5K–50K/month depending on query volume and scope.
3. **TAO emissions** from the Bittensor subnet (18% of subnet emissions to the subnet owner). At current emission rates and TAO prices, this provides an additional revenue stream that funds protocol development.

**Q: What is the competitive landscape?**

A: No direct competitor combines all four of: (1) local-first data storage, (2) persistent multi-specialist AI, (3) decentralized mesh consultation, and (4) credentialed physician validation. Adjacent competitors include:

- **Consumer health AI** (ChatGPT, Gemini, Apple Health Intelligence): Stateless, no persistent memory, cloud-based, no physician validation.
- **Clinical decision support** (UpToDate, DynaMed, Isabel): Designed for physicians, not patients. No persistent patient context. Subscription-based.
- **Telehealth platforms** (Teladoc, Amwell): Human doctors, not AI. Episodic, not continuous. Expensive.
- **Bittensor health subnets** (Safe Scan SN76): Medical imaging classification only. No clinical reasoning, no patient-facing product, no physician validation.

HealthMesh's moat is the network effect: more patient nodes = richer mesh opinions = more doctor engagement = better AI agents = more patient nodes. This flywheel is difficult to replicate because it requires simultaneous supply (doctors, compute) and demand (patients) at scale.

**Q: How do you solve the doctor cold-start problem?**

A: We do not launch without doctors. Pre-launch plan:

1. **CME accreditation**: Partner with an ACCME-accredited institution so that reviewing AI diagnostic opinions counts toward mandatory continuing medical education hours. Doctors currently pay $500–2000/year for CME; HealthMesh pays them instead.
2. **Academic partnerships**: Offer residency programs at 2–3 medical centers the review portal as a teaching tool. Residents review AI opinions as part of their clinical reasoning training.
3. **Competitive compensation**: $10–50 per case review (5–10 minutes each), fully asynchronous, mobile-friendly. Competes favorably with telehealth moonlighting rates ($40–100/hour) on flexibility.
4. **Target**: 50 credentialed doctors across cardiology, nephrology, endocrinology, primary care, and pulmonology before subnet registration. 3-month closed beta with 20 patients + 10 doctors to validate the feedback loop.

**Q: What are the regulatory risks?**

A: The primary risk is FDA classification as a Software as a Medical Device (SaMD). We mitigate this by:

1. **Phase 1–3** (local consilium + mesh): Designed to stay within the Clinical Decision Support (CDS) exemption under the 21st Century Cures Act. Agents present evidence, trends, and citations — not direct treatment recommendations. All outputs labeled as informational.
2. **Phase 4+** (agent recommendations): Before launching features that make explicit clinical recommendations, we will pursue an FDA Pre-Submission meeting to determine the appropriate regulatory pathway (De Novo or 510(k)).
3. **Liability**: No doctor-patient relationship exists between reviewing physicians and patients. Doctors rate AI quality; they do not treat patients. Clear terms of service and liability waivers for patients.

International expansion requires engagement with EU MDR, UK MHRA, and other regulatory bodies on a per-market basis.

**Q: How does the Bittensor integration work, in simple terms?**

A: Bittensor is a decentralized network that pays AI systems for producing useful outputs. In HealthMesh:

- **AI agent nodes** (miners) earn cryptocurrency (TAO) for producing high-quality clinical opinions.
- **Validators** are infrastructure operators who run a doctor review portal. They collect physician ratings on AI opinions, aggregate them into quality scores, and submit those scores to the Bittensor network.
- **Doctors** log into a simple web portal, review anonymized AI opinions against clinical rubrics, and get paid in dollars (via Stripe) or optionally in TAO. They never need to understand cryptocurrency.
- The Bittensor network's consensus mechanism (Yuma Consensus) ensures that validators who submit honest quality scores earn more than those who try to game the system.

The result: AI agents that produce poor clinical opinions earn less and are eventually removed from the network. AI agents that produce physician-validated, high-quality opinions earn more and are prioritized. The network self-improves over time.

**Q: What is the TAO token and why does it matter?**

A: TAO is the native cryptocurrency of the Bittensor network. It serves as the incentive mechanism that aligns all participants: AI agent operators earn TAO for quality, validators earn TAO for honest scoring, and the subnet owner earns TAO for maintaining the protocol. TAO is traded on major exchanges (Coinbase, Kraken) and has real monetary value — this is what makes the incentive loop economically sustainable. Patients do not need to hold or understand TAO; it operates in the background.

**Q: What is the long-term vision?**

A: Every person on earth has a permanent AI medical council that knows their complete health history, monitors them continuously, and draws on the collective experience of millions of other patients' longitudinal cases — all while keeping every person's data completely private on their own device. The network of doctor-validated AI agents becomes the world's largest continuously-improving clinical reasoning system, funded by the value it creates rather than by advertising or data monetization.

**Q: What are the biggest risks to this vision?**

A: In order of severity:

1. **Clinical reasoning quality of local LLMs**: If open-source models running on consumer hardware cannot produce opinions that physicians rate as acceptable, the incentive loop breaks. Mitigation: benchmark rigorously; fall back to cloud-hosted models via privacy-preserving inference (TEE) if local models are insufficient.
2. **Doctor adoption**: If we cannot recruit and retain 50+ credentialed physicians, the validation layer is too thin to be meaningful. Mitigation: CME credits, competitive pay, academic partnerships.
3. **Regulatory action**: If the FDA classifies local-only health monitoring as SaMD before we have a regulatory pathway, early growth is blocked. Mitigation: strict CDS exemption compliance in initial phases; proactive FDA engagement.
4. **Patient adoption**: Phase 1 targets MacBook users via a native app (one-click install, zero terminal interaction). Phase 2 introduces the HealthMesh Home Unit — a plug-and-play appliance ($399–499) for mass market. The user never sees a database or a config file; they link accounts, upload PDFs, and read their consilium dashboard.
5. **Network effects may not materialize**: If mesh opinions don't add meaningful value beyond the local consilium, patients won't pay for Pro and the network collapses to local-only use. Mitigation: validate mesh value in closed beta before investing in network growth.
