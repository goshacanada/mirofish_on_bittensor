# Consilium — PR/FAQ

---

## Press Release

### Consilium — Open-Source AI That Discovers What Experts Miss

*A knowledge graph that grows smarter with every user — finding connections no single specialist can see*

A patient with CKD, diabetes, and hypertension sees three specialists. Each manages their piece. Nobody notices that the ACE inhibitor from the nephrologist, the potassium supplement from the endocrinologist, and the declining kidney function together create a life-threatening hyperkalemia risk — because that connection lives at the intersection of three specialties, buried in 18 months of labs from different providers. A note-taking app can't find it. ChatGPT can't find it — it doesn't have your history, and it reasons from flat text, not structured relationships. A knowledge graph with AI agents traversing it finds it in seconds, because the path from Drug A → elevates → Potassium ← impaired clearance ← CKD Stage 3b ← worsened by → Drug B is an explicit, traversable chain in the graph.

**This is what Consilium does.** It runs a medical knowledge graph on your own computer — a structured map of how conditions, medications, biomarkers, symptoms, and mechanisms connect. AI agents traverse this graph against your personal data, following multi-hop relationship chains that cross specialty boundaries. They find the connections that individual specialists miss — not because those doctors aren't skilled, but because no human can hold a 50,000-node knowledge network in their head while seeing 20 patients a day.

**And the knowledge grows.** Every research paper ingested adds edges to the graph. Every expert micro-question calibrates a threshold. Every node on the peer-to-peer mesh that validates a finding increases confidence. A Consilium knowledge graph with 10 years of accumulated research papers and expert validation finds connections that a fresh installation cannot. When millions of users share knowledge through the mesh — each contributing verified edges from papers they've read, patterns their agents have discovered, and validations their experts have confirmed — the collective graph becomes something no institution, no database, and no single AI model can match: a living, distributed, community-curated map of domain expertise that gets smarter every day.

**This is not a record-keeping system.** Notes can't traverse relationships. Spreadsheets can't reason about multi-hop causal chains. ChatGPT can't remember your data across sessions or systematically check every medication against every condition against every other medication. Consilium is a knowledge engine — the graph is the product.

Consilium is free and open-source. Your data never leaves your device. You own your information. You only share domain knowledge — verified facts about how conditions, medications, and biomarkers relate — never personal data. You benefit from the knowledge others contribute, and they benefit from yours. The code is auditable by anyone. The platform is built for the common good.

"No single doctor can know everything. A nephrologist doesn't think about endocrinology. A cardiologist doesn't think about nephrology. But kidney function affects the heart, and heart medications affect the kidneys, and diabetes accelerates both. The connections are in the medical literature — in 50,000 research papers, across 200 journals, spanning 30 specialties. No human can hold all of that. But a knowledge graph can. And when AI agents traverse that graph against your specific data — your medications, your labs, your trends — they find the connections that matter for YOU. Not generic advice. Not 'this is what creatinine means.' The specific 4-hop chain that explains why your fatigue is getting worse and what your doctor needs to know about it."

**How it works:**

1. **You load your data.** Drop in lab report PDFs, connect Apple Health, enter your medications. Local AI extracts structured values, checks for errors, maps everything to standard medical codes (SNOMED CT, RxNorm, LOINC). Your data stays on your device in per-person folders — organize by household member. Nothing leaves your machine.

2. **The knowledge graph reasons about your data.** This is not keyword matching or simple lookups. AI agents traverse a structured graph of medical knowledge — following chains like: your medication → affects → this biomarker → which is filtered by → this organ → which is damaged by → this condition you have → which is worsened by → this other medication you're on. These multi-hop chains cross specialty boundaries. They find the connections that live in the gaps between your nephrologist, your endocrinologist, and your cardiologist.

3. **Deterministic safety checks run alongside.** Drug interactions, threshold violations, and guideline-based rules fire deterministically — not probabilistic LLM guesses. If metformin exceeds safe limits for your eGFR, it catches it every time. These are the guardrails. The graph-based reasoning is the discovery engine.

4. **Your report surfaces what matters.** Findings are classified by severity — 🔴 Critical, 🟡 Urgent, 🔵 Monitor. The report gives your doctor the information they need to develop critical insights: the multi-hop connections, the trends they don't have time to spot, the cross-specialty interactions nobody is tracking. Not "Consilium thinks you should do X" — the graph-derived connections with evidence citations. The doctor brings clinical judgment. The graph brings the connections.

5. **The knowledge grows over time.** Research papers are ingested into the graph — entities extracted, relationships mapped, confidence assigned. Every paper adds edges. Every edge makes future reasoning richer. Your personal knowledge graph gets smarter the more you use it — not because the LLM improves, but because the graph it reasons against becomes deeper and more connected.

6. **The mesh multiplies everything** *(future phase)*. When your node connects to the peer-to-peer mesh, it joins a network where knowledge flows. When a nephrologist in Tokyo verifies a paper about SGLT2 inhibitors, the extracted edges propagate to every CKD-interested node worldwide. When thousands of nodes independently validate the same finding, confidence increases. When one node discovers a retraction, the correction spreads to all. No personal data is shared — only verified domain knowledge. The mesh is how the collective graph grows to millions of edges, curated by thousands of experts and validated across thousands of independent graphs. This is the moat. This is what makes Consilium impossible to replicate with a note-taking app.

**Consilium does not provide professional advice of any kind.** It is an educational entertainment and knowledge exploration tool that surfaces connections in your data for you to discuss with qualified professionals.

**Consilium is free and open-source. Your data is yours. The knowledge is everyone's. The code is auditable. Early access starting [date].**

---

## Why a Knowledge Graph — Not Notes, Not ChatGPT

This section exists because the question "why not just use notes?" deserves a direct answer.

| Capability | Notes/Spreadsheet | ChatGPT | Consilium |
|-----------|-------------------|---------|-----------|
| Store your lab results | ✅ | ❌ (forgets between sessions) | ✅ (persistent, per-person) |
| Explain what a value means | ❌ | ✅ (good for single values) | ✅ |
| Track trends over months/years | Manual | ❌ (no memory) | ✅ (automatic) |
| Check ALL drug interactions against ALL conditions | ❌ | Probabilistic (may miss) | ✅ Deterministic (graph-complete) |
| Find 4-hop cross-specialty connections | ❌ | Unlikely (flat reasoning) | ✅ (graph traversal) |
| Get smarter as papers are published | ❌ | Only at model retrain | ✅ (continuous graph updates) |
| Learn from millions of other users' graphs | ❌ | ❌ | ✅ (mesh) |
| Run locally, data never leaves device | ✅ | ❌ (cloud) | ✅ |
| Auditable open-source code | N/A | ❌ (proprietary) | ✅ |
| Deterministic safety checks | ❌ | ❌ | ✅ |

**The graph is the product.** Without it, Consilium is just another health app. With it, Consilium discovers connections that trained specialists miss — because those connections require traversing relationships that span specialties, time periods, and thousands of research papers. No human can do that. Flat text can't do that. Only a structured knowledge graph with agents traversing it can do that.

**The mesh is the moat.** A single knowledge graph is valuable. A million knowledge graphs sharing verified domain knowledge through a peer-to-peer mesh — each contributing edges from papers they've read, patterns their agents have found, and corrections their experts have validated — is something no company, no institution, and no AI model can replicate. The more people use Consilium, the smarter every node becomes. This is viral by design: you join because it helps you, and your participation makes it better for everyone else.

---

## Building Phases — MVP to Full Vision

**Phase 0 (MVP):** Local knowledge graph + single agent + wellness domain. The graph is small (hand-curated) but demonstrates the core capability: multi-hop reasoning against your data, deterministic safety checks, severity-triaged reports. Users upload labs, the graph finds connections, the report surfaces what matters. This validates whether graph-based discovery actually produces insights people take to their doctors.

**Phase 1:** Multi-agent specialist swarms, research paper ingestion (graph grows from papers), external LLM escalation, trend analysis, medication reconciliation. The knowledge graph starts growing beyond the initial curation.

**Phase 2:** Peer-to-peer mesh (knowledge sharing across nodes), Expert Portal (credentialed experts calibrate the graph), automated paper discovery, knowledge decay management. Network effects begin — the graph grows from collective contribution.

**Phase 3:** Additional domains (finance, legal, engineering), hardware appliance, advanced privacy (TEE, ZK proofs), cohort queries for professionals.

**The MVP is NOT a record-keeping system.** It is a small but real knowledge graph with an agent that traverses it. The graph has hundreds of nodes and thousands of edges representing real medical relationships. The agent follows multi-hop chains. The deterministic rules catch interactions. If the MVP just organized data into a spreadsheet, it would fail — because that's what notes already do. The MVP must demonstrate DISCOVERY: "here is a connection in your data that you didn't know about, that your doctor might not catch, and that a note-taking app could never find."

---

## Frequently Asked Questions

### User FAQ

**Q: What is Consilium?**

A: Consilium is an AI-powered knowledge discovery system that runs on your own computer. It maintains a medical knowledge graph — a structured map of how conditions, medications, biomarkers, and symptoms connect — and uses AI agents to traverse that graph against your personal data. Unlike ChatGPT (which reasons from flat text and forgets between sessions) or note-taking apps (which store data but can't reason about it), Consilium finds multi-hop connections that cross specialty boundaries. It discovers the 4-hop chain between your medication, your kidney function, your other medication, and your potassium level that no single specialist is tracking. The knowledge graph grows over time as papers are ingested and the mesh network shares verified findings across nodes.

**Q: Why can't I just use ChatGPT?**

A: Three reasons. First, ChatGPT has no memory — it can't track your labs over 18 months or know what medications you were on six months ago. Second, ChatGPT reasons from flat text, not structured relationships — it may know individual facts but it can't systematically traverse a 50,000-node knowledge network to find every relevant chain. It's probabilistic; it might mention a drug interaction or it might not. A knowledge graph is deterministic — if the edge exists, the agent finds it. Third, ChatGPT can't learn from new research papers or from the collective knowledge of thousands of other users' graphs. Consilium's knowledge grows; ChatGPT's knowledge is frozen at training time.

**Q: Is Consilium medical/financial/legal advice?**

A: No. Consilium is an educational entertainment and knowledge exploration tool. It surfaces connections in your data — graph-derived relationships with evidence citations — for you to discuss with qualified professionals. It does not diagnose, treat, prescribe, or recommend. The doctor brings clinical judgment; Consilium brings the connections they don't have time to find.

**Q: Where is my data stored?**

A: On your device only. Consilium stores two things locally: (1) a **knowledge graph** — domain knowledge about how conditions, medications, and biomarkers relate, and (2) a **personal data store** — your raw records in per-person folders. Your personal data never leaves your machine. When the mesh is active, only verified domain knowledge (e.g., "Empagliflozin protects kidneys — confirmed by 3 RCTs") is shared — never personal data. The code is open-source and auditable by anyone.

**Q: What makes this different from a note-taking app?**

A: Notes store data. Consilium reasons about it. A note-taking app can record that your creatinine is 1.8 and your potassium is 5.2. It cannot tell you that creatinine 1.8 means eGFR ~42 which means CKD Stage 3b which means your kidneys aren't clearing potassium efficiently which means your potassium of 5.2 is more dangerous than it looks which means the ACE inhibitor you're on (which also raises potassium) creates a triple risk. That chain requires a structured knowledge graph and an agent that traverses it. Notes can't do this. Spreadsheets can't do this. Only a graph can.

**Q: How does the knowledge graph grow?**

A: Multiple sources: (1) **Research papers** — when you or the system ingests a paper, entities and relationships are extracted and added as edges with confidence tiers. (2) **Expert validation** — credentialed experts answer micro-questions that calibrate thresholds and confirm relationships. (3) **Mesh propagation** — when another node discovers and verifies a finding, the edges propagate to all interested nodes. (4) **Plugin updates** — the open-source community curates foundational knowledge. A graph with 1,000 edges finds basic connections. A graph with 500,000 edges — accumulated from years of papers, expert validation, and mesh propagation — finds connections that no textbook contains, because they emerge from the intersection of thousands of individual findings.

**Q: What domains does Consilium support?**

A: At launch, **Wellness & Body Literacy** — the domain with the richest available knowledge graphs and the largest user base. The platform architecture is domain-agnostic: the graph database, agent framework, and mesh protocol work identically across domains. Adding a new domain means creating a plugin with a foundational knowledge graph + domain-specific agents. Finance, legal, and engineering domains are planned for future phases.

**Q: How much does it cost?**

A: Free and open-source. At launch, everything costs nothing — the knowledge graph, agents, data ingestion, reports. In future phases, Consilium Pro (≤$20/month) will add expert-validated knowledge via the Expert Portal and mesh consultation. Free users benefit from knowledge improvements funded by Pro users. The platform will always be free.

**Q: Can domain experts use Consilium?**

A: Experts are the most powerful users. A cardiologist who feeds nephrology papers into their graph gains cross-specialty insight they'd never have otherwise. A primary care doctor with a deep knowledge graph can spot the connection between a patient's symptoms and a rare condition because the graph traversal finds a path they'd never think to look for. Expert nodes produce the most valuable mesh responses. In future phases, experts can earn income through the Expert Portal by answering micro-questions that calibrate the collective knowledge graph.

**Q: How does the mesh work?**

A: The mesh is a peer-to-peer network where nodes share verified domain knowledge — never personal data. When your node ingests a paper and extracts edges, those edges can propagate to every interested node on the mesh. When thousands of nodes independently validate the same finding, confidence increases. When one node discovers a retraction, the correction spreads to all. The mesh uses a BitTorrent-style credit system: you earn credits by serving queries and sharing knowledge, spend them by asking. No money, no crypto, no central server. The more nodes contribute, the richer every node's graph becomes. This is the network effect that makes Consilium impossible to replicate.

**Q: Can my doctor see my Consilium data?**

A: Yes. You show the report on your phone or print it as a PDF. The report presents graph-derived connections with evidence citations — the multi-hop chains, the trend analysis, the interaction flags. It gives the doctor the connections they don't have time to find themselves. In future phases, a Clinician View will present this in compact medical format with ICD-10 codes and guideline references.

**Q: How does Consilium handle conflicting evidence?**

A: The knowledge graph models both sides. Where trials disagree — different conclusions for different populations — the graph stores a `CONFLICTS_WITH` edge explaining why the evidence conflicts. Agents present both sides with context. The system never silently picks a winner. As the graph grows through paper ingestion and expert validation, the nuance of which findings apply to which populations becomes increasingly precise.

---

### Expert FAQ *(Expert Portal launches in Phase 2)*

**Q: How do I earn money on Consilium?**

A: When the Expert Portal launches, you answer targeted micro-questions in your specialty — on your phone, 10–30 seconds each. Pay is $0.05–0.50 per answer, funded by Pro subscriptions. Higher reputation = more questions + better pay. But the primary value of Consilium for experts is the knowledge graph itself: feed research papers over your career, build a personal knowledge base that grows with every paper, and tap cross-specialty insight through the mesh. Your expertise, crystallized in a graph, becomes more valuable over time — both to you and to everyone your node serves on the mesh.

**Q: What kind of questions will I see?**

A: Targeted micro-questions that calibrate the knowledge graph — never case-specific. Examples: "Is there evidence for gut microbiome affecting CKD progression? [Strong / Emerging / No evidence]" or "At what eGFR would you typically refer to nephrology? [slider: 30–60]". Each answer calibrates edges in the graph that thousands of agents will use. You are shaping a collective knowledge network.

**Q: What about liability?**

A: You answer general domain knowledge questions — never case-specific, never see user data, never provide professional services. This is comparable to writing textbook entries or answering exam questions. No professional-client relationship is created.

---

### Community & Contributor FAQ

**Q: What makes Consilium different from existing tools?**

A: The knowledge graph + the mesh. No existing tool combines structured graph-based reasoning (not flat LLM text), persistent longitudinal data (not stateless chat), deterministic safety checks (not probabilistic), and a peer-to-peer mesh where knowledge grows from collective contribution. ChatGPT gives you opinions from frozen training data. WebMD gives you static pages. Consilium gives you graph-traversal-powered discovery against YOUR data, backed by a knowledge network that grows every day.

The key insight: as the mesh grows, every node benefits from every other node's contributions. A paper verified by a nephrologist in one city enriches the knowledge graph of a patient in another city. A pattern discovered across 10,000 nodes becomes a finding that no single doctor, hospital, or research institution has the data to see. This is collective intelligence for domain expertise — open source, decentralized, owned by no one, beneficial to all.

**Q: Why open source?**

A: Trust. When a system makes claims about your medications and health data, you need to know what it's doing. Open source means anyone can audit the code, verify the knowledge graph, and confirm that the system does what it says. No black boxes. No proprietary algorithms making health-related claims you can't inspect. Your data is yours. The knowledge is everyone's. The code is transparent.

**Q: How does Consilium sustain itself?**

A: The platform is free and open-source — sustained by community contributions. Revenue comes from the Expert Portal (Phase 2): Pro subscriptions (≤$20/month) fund expert micro-questions that improve the shared knowledge graph. The platform itself will always be free with no paywalls.

**Q: How do you solve the expert cold-start problem?**

A: The Expert Portal is Phase 2 — not needed for launch. By then, an active user base will generate real knowledge gaps. The micro-question model is zero friction: 10-second answers on a phone, $0.05–0.50 each, CME credit partnerships. But the strongest lever is that experts join because Consilium is the best knowledge tool for their OWN work — the Expert Portal income is a bonus. Target: 50 credentialed experts before Expert Portal launch.

**Q: Why is Consilium not a regulated product?**

A: The platform is a general-purpose knowledge exploration tool. It does not diagnose, treat, prescribe, or recommend. It surfaces graph-derived connections for the user to discuss with their professional. The architecture is domain-agnostic — a graph database + agent framework. Domain knowledge comes from open-source community plugins. Same legal model as VS Code (doesn't regulate extensions) or WordPress (doesn't regulate plugins). The code is open source and auditable.

**Q: What are the biggest risks?**

A: In order of severity:

1. **Knowledge graph quality**: If the graph has wrong edges, agents produce wrong insights. Mitigation: confidence tiers on every edge, standard terminology mapping (SNOMED/RxNorm/LOINC), extraction validation pipeline, mesh consensus, expert calibration.
2. **LLM extraction accuracy**: Extracting structured relationships from papers fails 5–15% of the time on negation alone. Mitigation: dual extraction, quantitative cross-check, dedicated negation pass, extraction confidence scoring.
3. **Data quality from PDF parsing**: Lab report formats vary wildly. Mitigation: plausibility checks, unit normalization, user confirmation for safety-critical values.
4. **User adoption**: Does graph-based discovery produce insights people actually act on? This is the Phase 0 validation question.
5. **Network effects may not materialize**: If mesh knowledge sharing doesn't add value beyond the local graph, the viral growth thesis fails. Validate in Phase 2 beta.
