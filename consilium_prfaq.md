# Consilium — PR/FAQ

---

## Press Release

### Consilium — Open-Source Personal Knowledge System Where Domain Experts Earn by Sharing Expertise

*A permanent AI advisory council on every person's device — a peer-to-peer mesh improved by credentialed domain experts*

Consilium is a new open-source system for decentralized personal knowledge management. It gives every person a permanent team of AI advisory agents running on their own computer, backed by a knowledge graph that organizes complex information and helps users prepare for informed conversations with real-world professionals.

Consilium is a general-purpose knowledge organization and educational entertainment system. The platform is free and open-source. It supports multiple domains — wellness & body literacy, personal finance, legal literacy, engineering, and more — using the same domain-agnostic architecture. Credentialed domain experts, including retirees who want to stay active and share their lifetime of knowledge, earn micro-payments through the Consilium Expert Portal by answering targeted questions that continuously improve the knowledge graphs all agents reason against.

"People face complex decisions every day — about their health, finances, legal situations — and they walk into professional appointments unprepared because the information is scattered and hard to understand. Consilium organizes your personal data, helps you see connections, and generates the right questions to ask your doctor, advisor, or attorney. It doesn't replace professionals — it makes every conversation with them more productive. And for retired doctors, lawyers, and engineers, it's a way to keep contributing their expertise — on their own schedule, from their phone, with real impact."

**How it works:**

1. **You load your data**: Drop in a lab report PDF, connect your Apple Watch, upload a brokerage statement, or photograph a legal document. Consilium's local AI classifier detects what kind of data it is.

2. **Domain plugins install on-demand**: If you load a lab report and don't have a wellness plugin installed, Consilium asks: "This looks like health data. Would you like to install the Wellness & Body Literacy plugin to organize it?" You confirm, and the system pulls the relevant open-source plugin from GitHub — including domain-specific agents (like MDAgents for wellness), a foundational knowledge graph, and data parsers. **If you never load health data, no health code ever runs on your machine.**

3. **Your consilium**: A permanent team of AI advisory agents from your installed plugins monitors your knowledge graph. When new data arrives, the relevant agents explore connections and generate questions you might want to discuss with your real-world professional.

4. **The mesh**: When your local consilium faces a question beyond its confidence, it sends an anonymized query to the Consilium network — thousands of other instances. Each node has different plugins installed depending on what data its owner loaded. Your consilium aggregates mesh perspectives alongside its own analysis. No data is shared — only reasoning.

5. **Expert knowledge network**: The knowledge graphs that plugins provide are continuously improved by credentialed domain experts answering targeted micro-questions on their phone — 10–30 seconds each, earning $0.05–0.50 per answer through the Consilium Expert Portal. Pro subscribers ($20/month or less) fund these questions — the portal handles routing, quality control, and expert payments. Retired professionals find this especially rewarding — it keeps their expertise active and benefits people, with zero scheduling or liability. An LLM-powered Question Engine identifies gaps in the knowledge graph and generates the most informative questions to fill them.

**Consilium does not provide professional advice of any kind.** It is an educational entertainment and knowledge exploration tool that helps users organize information and prepare for conversations with qualified professionals.

**Consilium is available for early access starting [date]. The platform is free and open-source — local consilium and mesh consultations cost nothing. Expert-validated knowledge is available through Consilium Pro at $20/month or less — the same price as ChatGPT Plus, but with your data, persistent memory, a knowledge graph, mesh consultation, and human expert validation included.**

---

## Frequently Asked Questions

### User FAQ

**Q: What is Consilium?**

A: Consilium is a personal knowledge organization and exploration tool. It helps you organize complex data (health readings, financial records, legal documents), see connections and patterns, and prepare informed questions for your real-world professionals — doctors, financial advisors, lawyers. Think of it as a permanent AI study group that helps you do your homework before important appointments.

**Q: Is Consilium medical/financial/legal advice?**

A: No. Consilium is an educational entertainment and knowledge exploration tool. It does not diagnose, treat, prescribe, advise, or recommend. It helps you understand your own data and generates questions you might want to discuss with qualified professionals. Always consult your healthcare provider, financial advisor, or attorney before making any decisions.

**Q: Where is my data stored?**

A: On your device only. Consilium runs a local database on your computer. Your data never leaves your machine. There is no cloud account, no server upload, and no way for Consilium or anyone else to access your data.

**Q: What domains does Consilium support?**

A: Consilium itself is domain-agnostic — it's a blank canvas. Domains are added through **open-source plugins** that install automatically when you load relevant data. If you upload a lab report, it asks if you want the Wellness plugin (which wraps projects like MDAgents and Hetionet from GitHub). If you upload a brokerage statement, it asks about the Finance plugin. If you never load health data, no health code ever touches your machine. Anyone can create a domain plugin — there's a community-maintained registry of available plugins.

**Q: Do I need to be technical to use Consilium?**

A: No. On macOS, you download and install a single app. You open it, link your data sources (Apple Health, Oura, patient portal), upload documents, and the system does everything else. In the future, the Consilium Home Unit is an open-source hardware design — a dedicated appliance you plug into power and Wi-Fi, accessible from any browser. Anyone can build one from the published design, or get a pre-assembled unit from the community.

**Q: How do I add my data?**

A: Three ways: (1) Link an account — Apple Health, Oura Ring, patient portals. Data syncs automatically. (2) Upload a document — drag-and-drop a PDF or take a photo. AI extracts structured data and asks you to confirm. (3) Download insights — tap "Prepare for appointment" to generate a question list for your professional.

**Q: How much does it cost?**

A: The platform is free and open-source — local consilium, mesh consultations, and the base knowledge graphs from domain plugins are all free. Consilium Pro (≤$20/month) adds expert-validated knowledge: your system can route micro-questions to credentialed human experts who improve the knowledge graph. That's the same price as ChatGPT Plus — but Consilium runs on your data, remembers everything, consults a mesh of other instances, and has real human experts validating the knowledge. Free users still benefit from knowledge improvements funded by Pro users. The Consilium Home Unit hardware design is also open-source — you can build one yourself for ~$150–250 in parts.

**Q: Can I export my data?**

A: Yes. Your knowledge graph is a standard database on your device. Export as JSON, CSV, or domain-specific formats (FHIR for health data) at any time. You own your data unconditionally.

---

### Expert FAQ

**Q: How do I earn money on Consilium?**

A: You answer targeted micro-questions in your domain of expertise — on your phone, during downtime. Each question takes 10–30 seconds: multiple choice, yes/no, threshold sliders, or short rankings. Pay is $0.05–0.50 per answer depending on question type, funded by Pro user subscriptions. You earn more as your reputation score increases — like Uber, high-rated experts get more questions and better pay rates. For retired professionals, this is a way to stay active, share your lifetime of expertise, and earn supplemental income — all on your own schedule, with no scheduling, no commute, and no liability.

**Q: What kind of questions will I see?**

A: General domain knowledge questions — never case-specific or tied to an individual user. Examples for a physician: "At what eGFR level would nephrology referral typically be considered? [slider]" or "Rank these lab tests by diagnostic yield for anemia workup." You are contributing to a knowledge graph, like writing a textbook entry — not providing patient care.

**Q: How do I sign up?**

A: You sign up on the Consilium Expert Portal, verify your credentials (license number, certification, etc.), and start answering questions. Payment is in dollars via Stripe — weekly payouts. No cryptocurrency, no special software, no scheduling.

**Q: What about liability?**

A: You answer general domain knowledge questions. You never see user data, never review specific cases, and never provide professional services. No professional-client relationship is created. This is legally comparable to writing exam questions, contributing to a medical textbook, or answering questions on a professional Q&A site.

**Q: How is my answer quality measured?**

A: Agreement with other credentialed experts on the same question (50%), accuracy on known-correct test questions injected blind (30%), and downstream improvement to the knowledge graph (20%). High-reputation experts earn higher pay rates and access to more complex question types.

---

### Community & Contributor FAQ

**Q: How does Consilium sustain itself?**

A: Two layers:

1. **The platform** (mesh software, agent framework, domain plugins) is free and open-source. It sustains itself through community contributions — developers, domain experts, and plugin authors contributing because they believe in the mission.
2. **The Expert Portal** is a company-operated service that routes micro-questions to credentialed experts and handles payments. It is funded by Pro subscriptions (≤$20/month). The subscription revenue covers expert payouts + portal operations.

The platform is free with no paywalls. Pro adds expert-validated knowledge for ≤$20/month — the same price people already pay for ChatGPT Plus, but with persistent agents on your own data, a knowledge graph, mesh consultation, and real human experts.

**Q: What makes Consilium different from existing tools?**

A: No existing tool combines: (1) local-first data storage, (2) persistent multi-agent AI, (3) decentralized mesh consultation, and (4) expert-validated knowledge graphs — all free and open-source. Adjacent tools are all narrower: ChatGPT (stateless, cloud, no experts), WebMD (static, no personalization), TurboTax (single domain, no AI agents), Fitbit (data display only).

Consilium's strength is the network effect: more user nodes = richer mesh perspectives = more expert engagement = better knowledge graphs = more users.

**Q: How do you solve the expert cold-start problem?**

A: The micro-question model dramatically lowers the bar:

1. **CME credits** (wellness domain): Partner with an ACCME accreditor. Answering clinical knowledge questions counts toward continuing education. Doctors currently pay for CME — Consilium provides it as a benefit.
2. **Near-zero friction**: 10-second answers on a phone. No case review, no context, no scheduling.
3. **Retired professionals**: A retired physician, attorney, or engineer can contribute their lifetime of expertise in 10-second increments. The income is supplemental; the sense of purpose and continued relevance is the real draw. Self-worth matters, especially after leaving full-time work.
4. **Pay-per-answer**: $0.05–0.50, funded by Pro subscriptions. Zero-effort income for active and retired professionals alike.
5. **Academic partnerships**: Residency programs use the question portal as a clinical reasoning training tool.
6. **Target**: 50 credentialed experts across 3 domains before launch. 3-month closed beta.

**Q: Why is Consilium not a regulated product?**

A: The platform ships with **zero domain-specific code**. No health logic, no financial logic, no legal logic. It is a graph database + agent framework + mesh protocol — pure infrastructure. All domain intelligence is delivered through open-source community plugins that install only when the user loads relevant data and explicitly confirms.

This is the same model as VS Code (doesn't regulate extensions), Docker (doesn't regulate containers), WordPress (doesn't regulate plugins), and npm (doesn't regulate packages). The FDA cannot classify a generic platform as a Software as a Medical Device when the platform contains no medical software. The health-specific code (MDAgents, Hetionet, etc.) is open-source community software that the user independently chooses to install.

Additional defenses, each independently sufficient:

1. **No professional advice**: Agents present patterns and generate questions — never recommendations, diagnoses, or prescriptions.
2. **No professional-client relationship**: Experts answer general knowledge questions, never see user data.
3. **Local data only**: User data never leaves the device. No HIPAA covered entity relationship.
4. **User-initiated everything**: The platform never suggests loading health data or installing health plugins. The user drives every domain-specific action.

Precedent: VS Code has medical imaging extensions. Docker Hub hosts clinical containers. npm hosts healthcare packages. None of these platforms are FDA-regulated because they are generic infrastructure, not medical products.

**Q: How does the domain plugin system work?**

A: When a user loads data (drops a PDF, connects a data source), the platform's local LLM classifier detects the domain (wellness, finance, legal, etc.). If no plugin for that domain is installed, the platform asks: "This looks like [domain] data. Would you like to install the [domain] plugin?" The user confirms, and the platform pulls the open-source plugin repository from GitHub.

Each plugin is a self-contained GitHub repo containing: (1) a foundational knowledge graph for the domain, (2) domain-specific AI agents, (3) data parsers for domain document types, and (4) micro-question templates for expert validation. Anyone can create a plugin — the format is open and documented.

Critically: the platform never auto-installs plugins, never pre-loads domain capabilities, and never suggests what data the user should load. Every domain activation is user-initiated. This creates a clean legal separation between the platform (generic infrastructure) and domain intelligence (user-installed community software).

Existing open-source projects serve as plugin foundations: MDAgents and Hetionet for wellness, FinGPT for finance, LegalBench for legal reasoning.

**Q: How does the mesh work?**

A: Consilium uses a BitTorrent-style peer-to-peer mesh. When your local agents face a question beyond their confidence, they query other nodes on the network. Mesh queries are free — they use a credit-based barter system: you earn credits by serving queries to others, and spend credits by querying others. No money, no crypto, no central server. You configure how many queries your node serves per day. The more you contribute, the more you can ask.

**Q: How does the Expert Portal work?**

A: The Expert Portal is a company-operated service that connects Pro subscribers with credentialed domain experts. When your local agents identify a knowledge gap, the system generates a targeted micro-question and sends it to matched experts via the portal. Experts answer on their phone (10–30 seconds), get paid via Stripe, and the answer improves the shared knowledge graph for everyone — free and Pro users alike. The portal is domain-agnostic — it routes by competence tags, not by domain labels. Expert quality is managed through an Uber-style reputation system: high-rated experts get more questions and better pay; low-rated experts naturally receive fewer questions.

**Q: What are the biggest risks?**

A: In order of severity:

1. **Knowledge graph quality**: If open-source models on consumer hardware can't reason well against the knowledge graph, expert micro-questions can't fix a fundamentally weak AI. Mitigation: benchmark rigorously; fall back to cloud models via privacy-preserving inference if needed.
2. **Expert adoption**: 50 experts across 3 domains before launch. Micro-questions are lower friction than case review, but still need initial momentum. CME credits and the retired-expert angle are the strongest levers.
3. **Regulatory reclassification**: Low probability given the general-purpose framing, but if a regulator targets Consilium specifically, it could slow adoption. Mitigation: proactive regulatory engagement, healthcare attorney review.
4. **User adoption**: Running a local database on a laptop isn't mass-market yet. The Consilium Home Unit appliance (open-source hardware design) is the path to mainstream — but requires community hardware effort.
5. **Network effects may not materialize**: If mesh perspectives don't add meaningful value beyond local consilium, users won't engage with the mesh. Validate in closed beta before scaling.
