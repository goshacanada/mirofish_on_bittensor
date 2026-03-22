# Consilium — PR/FAQ

---

## Press Release

### Consilium Launches Decentralized Personal Knowledge System — Experts Earn by Sharing Expertise Through Micro-Questions

*A permanent AI advisory council on every person's device — powered by Bittensor, improved by credentialed domain experts*

**San Francisco, CA** — Consilium today announced the launch of its Bittensor subnet for decentralized personal knowledge management. Consilium gives every person a permanent team of AI advisory agents running on their own computer, backed by a knowledge graph that organizes complex information and helps users prepare for informed conversations with real-world professionals.

Consilium is a general-purpose knowledge organization and educational entertainment platform. It supports multiple domains — wellness & body literacy, personal finance, legal literacy, engineering, and more — using the same domain-agnostic architecture. Credentialed domain experts earn money by answering targeted micro-questions that continuously improve the knowledge graphs all agents reason against.

"People face complex decisions every day — about their health, finances, legal situations — and they walk into professional appointments unprepared because the information is scattered and hard to understand," said [Founder]. "Consilium organizes your personal data, helps you see connections, and generates the right questions to ask your doctor, advisor, or attorney. It doesn't replace professionals — it makes every conversation with them more productive."

**How it works:**

1. **You load your data**: Drop in a lab report PDF, connect your Apple Watch, upload a brokerage statement, or photograph a legal document. Consilium's local AI classifier detects what kind of data it is.

2. **Domain plugins install on-demand**: If you load a lab report and don't have a wellness plugin installed, Consilium asks: "This looks like health data. Would you like to install the Wellness & Body Literacy plugin to organize it?" You confirm, and the system pulls the relevant open-source plugin from GitHub — including domain-specific agents (like MDAgents for wellness), a foundational knowledge graph, and data parsers. **If you never load health data, no health code ever runs on your machine.**

3. **Your consilium**: A permanent team of AI advisory agents from your installed plugins monitors your knowledge graph. When new data arrives, the relevant agents explore connections and generate questions you might want to discuss with your real-world professional.

4. **The mesh**: When your local consilium faces a question beyond its confidence, it sends an anonymized query to the Consilium network — thousands of other instances. Each node has different plugins installed depending on what data its owner loaded. Your consilium aggregates mesh perspectives alongside its own analysis. No data is shared — only reasoning.

5. **Expert knowledge marketplace**: The knowledge graphs that plugins provide are continuously improved by credentialed domain experts answering targeted micro-questions on their phone — 10–30 seconds each, paid $0.05–0.50 per answer. An LLM-powered Question Engine identifies gaps in the knowledge graph and generates the most informative questions to fill them.

**Consilium does not provide professional advice of any kind.** It is an educational entertainment and knowledge exploration tool that helps users organize information and prepare for conversations with qualified professionals.

**Consilium is available for early access starting [date]. The local consilium is free and open-source. Mesh consultations and expert-validated knowledge require a Pro subscription ($29/month).**

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

A: No. On macOS, you download and install a single app. You open it, link your data sources (Apple Health, Oura, patient portal), upload documents, and the system does everything else. In the future, the Consilium Home Unit ($399–499) is a dedicated appliance you plug into power and Wi-Fi — access it from any browser.

**Q: How do I add my data?**

A: Three ways: (1) Link an account — Apple Health, Oura Ring, patient portals. Data syncs automatically. (2) Upload a document — drag-and-drop a PDF or take a photo. AI extracts structured data and asks you to confirm. (3) Download insights — tap "Prepare for appointment" to generate a question list for your professional.

**Q: How much does it cost?**

A: The local consilium (your knowledge graph + AI advisory team, no mesh) is free and open-source. The Pro tier ($29/month) adds mesh consultations and expert-validated knowledge. The Consilium Home Unit ($399–499, available later) includes the hardware appliance with 1 year of Pro subscription.

**Q: Can I export my data?**

A: Yes. Your knowledge graph is a standard database on your device. Export as JSON, CSV, or domain-specific formats (FHIR for health data) at any time. You own your data unconditionally.

---

### Expert FAQ

**Q: How do I earn money on Consilium?**

A: You answer targeted micro-questions in your domain of expertise — on your phone, during downtime. Each question takes 10–30 seconds: multiple choice, yes/no, threshold sliders, or short rankings. Pay is $0.05–0.50 per answer depending on question type. You earn more as your reputation score increases.

**Q: What kind of questions will I see?**

A: General domain knowledge questions — never case-specific or tied to an individual user. Examples for a physician: "At what eGFR level would nephrology referral typically be considered? [slider]" or "Rank these lab tests by diagnostic yield for anemia workup." You are contributing to a knowledge graph, like writing a textbook entry — not providing patient care.

**Q: Do I need crypto knowledge?**

A: No. You sign up on a web portal, verify your credentials, and answer questions. Payment is in dollars via Stripe. You never need a wallet, TAO, or any cryptocurrency knowledge.

**Q: What about liability?**

A: You answer general domain knowledge questions. You never see user data, never review specific cases, and never provide professional services. No professional-client relationship is created. This is legally comparable to writing exam questions, contributing to a medical textbook, or answering questions on a professional Q&A site.

**Q: How is my answer quality measured?**

A: Agreement with other credentialed experts on the same question (50%), accuracy on known-correct test questions injected blind (30%), and downstream improvement to the knowledge graph (20%). High-reputation experts earn higher pay rates and access to more complex question types.

---

### Stakeholder FAQ

**Q: How does Consilium make money?**

A: Three revenue streams:

1. **User subscriptions** ($29/month Pro tier) for mesh consultations and expert-validated knowledge. Target: 10,000 subscribers within 18 months = $3.5M ARR.
2. **Enterprise API** for organizations seeking anonymized, aggregated knowledge intelligence via federated analytics — no raw data leaves user devices. Pricing: $5K–50K/month.
3. **TAO emissions** from the Bittensor subnet (18% of subnet emissions to the subnet owner).

Future: Consilium Home Unit appliance sales ($399–499, includes 1 year Pro subscription).

**Q: What is the competitive landscape?**

A: No direct competitor combines: (1) local-first data storage, (2) persistent multi-agent AI, (3) decentralized mesh consultation, and (4) expert-validated knowledge graphs. Adjacent products are all narrower: ChatGPT (stateless, cloud, no experts), WebMD (static, no personalization), TurboTax (single domain, no AI agents), Fitbit (data display only).

Consilium's moat is the network effect: more user nodes = richer mesh perspectives = more expert engagement = better knowledge graphs = more users.

**Q: How do you solve the expert cold-start problem?**

A: The micro-question model dramatically lowers the bar:

1. **CME credits** (wellness domain): Partner with an ACCME accreditor. Answering clinical knowledge questions counts toward continuing education. Doctors currently pay for CME — Consilium pays them instead.
2. **Near-zero friction**: 10-second answers on a phone. No case review, no context, no scheduling.
3. **Pay-per-answer**: $0.05–0.50. Not life-changing, but zero-effort passive income.
4. **Academic partnerships**: Residency programs use the question portal as a clinical reasoning training tool.
5. **Target**: 50 credentialed experts across 3 domains before subnet launch. 3-month closed beta.

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

**Q: How does the Bittensor integration work?**

A: Bittensor is a decentralized network that pays AI systems for producing useful outputs. In Consilium:

- **AI agent nodes** (miners) earn TAO for producing high-quality knowledge perspectives.
- **Validators** run infrastructure: a Question Engine that identifies knowledge gaps, a question portal for experts, and automated scoring of agent outputs against the expert-validated knowledge graph.
- **Experts** answer micro-questions on their phone and get paid in dollars via Stripe. Zero crypto knowledge needed.
- Yuma Consensus ensures honest scoring across validators.

The result: the knowledge graphs that all agents reason against get continuously refined by real domain experts, creating a self-improving system funded by the value it creates.

**Q: What are the biggest risks?**

A: In order of severity:

1. **Knowledge graph quality**: If open-source models on consumer hardware can't reason well against the knowledge graph, expert micro-questions can't fix a fundamentally weak AI. Mitigation: benchmark rigorously; fall back to cloud models via privacy-preserving inference if needed.
2. **Expert adoption**: 50 experts across 3 domains before launch. Micro-questions are lower friction than case review, but still need initial momentum. CME credits are the strongest lever.
3. **Regulatory reclassification**: Low probability given the general-purpose framing, but if a regulator targets Consilium specifically, it could slow growth. Mitigation: proactive regulatory engagement, healthcare attorney review ($10–25K).
4. **User adoption**: Running a local database on a laptop isn't mass-market yet. The Consilium Home Unit appliance is the path to mainstream — but requires hardware design and manufacturing.
5. **Network effects may not materialize**: If mesh perspectives don't add meaningful value beyond local consilium, users won't pay for Pro. Validate in closed beta before scaling.
