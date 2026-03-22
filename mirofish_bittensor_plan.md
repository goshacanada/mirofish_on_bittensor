# MiroFish × Bittensor Subnet — Implementation & Deployment Plan

**Tooling decision:** Use **Claude Code** (terminal, `claude` CLI) throughout.
Cowork is for desktop automation (files, apps, forms). Claude Code writes, runs, and iterates on real software in your terminal — the right tool for every step here.

**MacBook prototyping:** Yes, fully supported. All Phase 1–3 work runs locally.
Minimum specs: macOS 13+, 16 GB RAM (32 GB preferred for 500+ agents), Python 3.11–3.12, Node.js 18+, Docker Desktop.

---

## Overview of Phases

| Phase | What | Where |
|---|---|---|
| 0 | Create GitHub repos, fork MiroFish | GitHub + MacBook |
| 1 | Run MiroFish locally, understand internals | MacBook |
| 2 | Build the subnet miner wrapper around MiroFish | MacBook |
| 3 | Run on Bittensor testnet (local chain) | MacBook |
| 4 | Deploy miner to cloud, connect to testnet | VPS / bare metal |
| 5 | Launch on Bittensor mainnet | Cloud + chain |

---

## Phase 0 — GitHub Setup and Repository Structure

**Goal:** Establish your GitHub presence before writing a single line of code. All work flows into your repos from day one, giving you version history, easy cloud deployment via git pull, and a public project page for attracting future miners.

### Why fork instead of clone

You need to **fork** the MiroFish repos to your GitHub account, not just clone them locally. The reasons:

- **Ownership**: A fork lives permanently under your GitHub account (`github.com/YOUR_USERNAME/MiroFish-Offline`). A local clone disappears when you close the terminal.
- **Customization tracking**: You will modify MiroFish internals to work as a subnet miner. Those changes need to be committed somewhere. Without a fork, you have nowhere to push them.
- **Upstream sync**: Forks let you pull future improvements from the original repo (`git fetch upstream`) while keeping your own changes — critical since MiroFish is v0.1 and evolving fast.
- **Deployment**: In Phase 4, your cloud server does `git clone git@github.com:YOUR_USERNAME/mirofish_on_bittensor` — it needs to pull from *your* repo, not the original.
- **Attribution**: Forks maintain the original license and credit the original authors automatically.

### Step 0.1 — Install prerequisites first

```bash
brew install python@3.11 node@18 git gh
brew install --cask docker
```

`gh` is the GitHub CLI — used in the steps below to create repos and fork from the terminal.

### Step 0.2 — Authenticate GitHub CLI

```bash
gh auth login
# Choose: GitHub.com → HTTPS → Login with a web browser
# Follow the prompts — opens browser, authenticates
gh auth status   # verify it worked
```

### Step 0.3 — Fork MiroFish-Offline to your GitHub account ✅ DONE

```bash
gh repo fork nikmcfly/MiroFish-Offline --clone=false
```

This is the single MiroFish fork you'll use — both for learning and as the miner's simulation engine. It uses Neo4j + Ollama, runs entirely locally, and is the most complete and documented variant. `github.com/goshacanada/MiroFish-Offline` is already created.

### Step 0.4 — Create your main subnet project repo ✅ DONE

This is the repo where all original subnet code lives — miner, validator, consumer API, deploy scripts.

**Already done:** `mirofish_on_bittensor` repo created at `github.com/goshacanada/mirofish_on_bittensor` and cloned to `~/GitHub/mirofish_on_bittensor`.

Add a `.gitignore` immediately:

```bash
cat > .gitignore << 'EOF'
# Python
__pycache__/
*.pyc
*.pyo
.venv/
venv/
dist/
build/
*.egg-info/

# Environment — NEVER commit secrets
.env
.env.*
!.env.example
!.env.template

# Bittensor wallets — NEVER commit private keys
~/.bittensor/
wallets/
*.json.bak

# Data / simulation state
data/groups/*.json
data/simulations/
backend/uploads/

# Node
node_modules/
.next/

# IDE
.vscode/
.idea/
*.swp

# OS
.DS_Store
Thumbs.db

# Docker
*.tar
EOF

git add .gitignore
git commit -m "chore: initial .gitignore — protect secrets and wallets"
git push origin main
```

### Step 0.5 — Clone MiroFish-Offline locally and add upstream remote

```bash
git clone git@github.com:goshacanada/MiroFish-Offline.git ~/GitHub/mirofish-offline
cd ~/GitHub/mirofish-offline
git remote add upstream https://github.com/nikmcfly/MiroFish-Offline.git
git remote -v   # should show both origin (yours) and upstream (nikmcfly's)
```

To sync upstream changes later: `git fetch upstream && git merge upstream/main`

### Step 0.6 — Set up your local workspace layout

You should have exactly this folder structure on your Mac before starting Phase 1:

```
~/GitHub/
├── mirofish_on_bittensor/   ← your main subnet project ✅ DONE
└── mirofish-offline/        ← fork of nikmcfly/MiroFish-Offline (learning + miner engine)
```

### Step 0.7 — Protect your secrets from day one

**This is critical.** Bittensor wallet keys and API keys must never be committed to GitHub.

Add a `secrets` entry to your Mac's shell config:

```bash
# Add to ~/.zshrc or ~/.bashrc
export WALLET_NAME="my-mirofish-wallet"
export WALLET_HOTKEY="default"
export LLM_API_KEY=""          # fill in when you have one
export OPENROUTER_API_KEY=""   # fill in when you have one
```

For each project, use `.env` files locally but only commit `.env.example` with placeholder values — never real keys. The `.gitignore` above already protects `.env`, but verify it every time before `git add .`.

### Step 0.8 — Create a project README skeleton

```bash
cd ~/GitHub/mirofish_on_bittensor
cat > README.md << 'EOF'
# MiroFish Subnet — Decentralized Opinion Intelligence on Bittensor

A Bittensor subnet that runs persistent swarm simulations using MiroFish/OASIS
to deliver on-demand and continuous opinion intelligence across topic domains.

## Status: 🚧 In Development (Phase 1)

## Architecture
- **Miners**: CPU-only servers running OASIS agent populations per topic domain
- **Validators**: Score miners on opinion diversity, consistency, and accuracy
- **Consumer API**: REST + WebSocket access to simulation outputs

## Domains
Finance · Geopolitics · Tech / AI · Health · Culture · General

## Quick Start (for miners)
*Coming in Phase 5*

## License
MIT
EOF

git add README.md
git commit -m "docs: initial README skeleton"
git push origin main
```

---

## Phase 1 — Run MiroFish Locally and Understand It

**Goal:** Get MiroFish running on your MacBook, run a real simulation, and understand the codebase well enough to wrap it.

### Step 1.1 — Work from your fork, not the original

All local work starts from your forked repos (cloned in Phase 0). Never work directly in a repo without an upstream remote — you'd lose the ability to track your changes or push to GitHub.

```bash
cd ~/GitHub/mirofish-offline
```

### Step 1.2 — Configure the offline fork

The original MiroFish uses Chinese UI and Zep Cloud. The offline fork replaces these with Neo4j + Ollama running locally. Create your local `.env` from the example — this file stays local and is never committed:

```bash
cp .env.example .env
```

Edit `.env`:
```
LLM_API_KEY=ollama
LLM_BASE_URL=http://localhost:11434/v1
LLM_MODEL_NAME=qwen3:14b           # use 14b on MacBook (24GB RAM), larger on server
NEO4J_URI=bolt://localhost:7687
NEO4J_AUTH=neo4j/mirofish
```

### Step 1.3 — Start all services

```bash
docker compose up -d               # starts Neo4j + Ollama + MiroFish
docker exec mirofish-ollama ollama pull qwen2.5:14b
docker exec mirofish-ollama ollama pull nomic-embed-text
```

Open `http://localhost:3000` — MiroFish UI should appear.

### Step 1.4 — Run your first simulation with Claude Code

```bash
cd ~/GitHub/mirofish-offline
claude
```

**Prompt to give Claude Code:**
> "Help me run a test simulation on MiroFish with a finance topic. Create a seed document about a hypothetical central bank rate decision, run a 20-agent simulation, and show me the output report structure. Then explain the backend code flow from seed ingestion through OASIS simulation to report generation."

**What to learn from this step:**
- How `simulation_manager.py` orchestrates stages
- What a simulation output JSON looks like (this becomes the miner's output)
- How agent personas are structured (this matters for the incentive mechanism)
- What parameters control agent count, rounds, and topic

Commit any notes or small fixes you make during exploration:

```bash
git add -p   # stage only intentional changes
git commit -m "explore: document simulation output structure and flow"
git push
```

---

## Phase 2 — Build the Bittensor Miner Wrapper

**Goal:** Create a new Python service (`mirofish_miner`) that wraps MiroFish's simulation engine and exposes a Bittensor Axon interface. This is the core engineering work.

### Step 2.1 — Set up the project structure

Your `mirofish_on_bittensor` repo was created in Phase 0. Now populate it with code:

```bash
cd ~/GitHub/mirofish_on_bittensor
claude
```

**Claude Code prompt:**
> "In this existing git repo, create a Python project called `mirofish_on_bittensor` with this structure:
> - `miner/` — Bittensor miner node (Axon server)
> - `validator/` — Bittensor validator node (scoring engine)
> - `simulation/` — MiroFish simulation wrapper (reuse MiroFish-Offline internals)
> - `api/` — Consumer REST API (FastAPI)
> - `shared/` — Shared types, schemas, scoring utils
> - `docker/` — Dockerfiles for each component
> - `tests/` — Unit + integration tests
>
> Use Python 3.11, uv for package management, and FastAPI. The repo is already initialized (do not run `git init`). Create a comprehensive `pyproject.toml` with dependencies including: bittensor>=8, fastapi, uvicorn, pydantic, httpx, redis, and camel-ai[oasis]. Also create an `.env.example` file listing all required environment variables with placeholder values but NO real secrets."

After Claude Code generates the scaffold:

```bash
git add .
git commit -m "feat: initial project scaffold — miner, validator, api, simulation"
git push origin main
```

### Step 2.2 — Define the miner protocol (the hardest design decision)

**Claude Code prompt:**
> "Design the core protocol for a MiroFish Bittensor subnet miner. The miner:
>
> 1. Maintains N 'opinion groups' — persistent collections of AI agents, each group specialized for a topic domain (Finance, Geopolitics, Tech, Health, etc.)
> 2. Each group runs agents continuously in the background, updating agent memories with new seed events
> 3. When a validator sends a query (a seed event + topic + question), the miner runs a focused simulation on the relevant group and returns a structured OpinionReport
>
> Create these Pydantic models in `shared/protocol.py`:
> - `TopicDomain` enum (Finance, Geopolitics, Tech, Health, Culture, General)
> - `AgentPersona` (id, name, age, occupation, opinion_bias, influence_level, memory_summary)
> - `SimulationQuery` (query_id, seed_text, topic_domain, question, num_rounds, requested_at)
> - `OpinionReport` (query_id, topic_domain, agent_count, rounds_run, opinion_distribution, key_narratives, consensus_score, dissent_score, generated_at)
> - `MinerCapabilities` (miner_uid, supported_domains, agents_per_domain, avg_latency_ms, uptime_hours)
>
> The `opinion_distribution` should capture what % of agents hold which position after simulation."

```bash
git add shared/
git commit -m "feat: define subnet protocol — SimulationQuery, OpinionReport, MinerCapabilities"
git push
```

### Step 2.3 — Build the simulation wrapper

**Claude Code prompt:**
> "In `simulation/engine.py`, build a `SimulationEngine` class that:
>
> 1. On init, loads or creates persistent agent populations for each `TopicDomain` (stored as JSON in `data/groups/`)
> 2. Has a `run_query(query: SimulationQuery) -> OpinionReport` async method that:
>    - Selects the relevant agent group
>    - Runs an OASIS simulation using the MiroFish-Offline backend's `simulation_runner.py` as a subprocess
>    - Parses the simulation output into an `OpinionReport`
>    - Updates persistent agent memories with what happened
> 3. Has a `background_update(domain: TopicDomain, news_item: str)` method for continuous updates
> 4. Exposes `get_capabilities() -> MinerCapabilities`
>
> Use the LLM endpoint from environment variables (default to Chutes via OpenRouter — `https://openrouter.ai/api/v1` with model `chutes/deepseek-v3`). This keeps the miner CPU-only — no local GPU needed."

```bash
git add simulation/
git commit -m "feat: SimulationEngine — persistent agent groups, async query, background updates"
git push
```

### Step 2.4 — Build the Bittensor Axon interface

**Claude Code prompt:**
> "In `miner/neuron.py`, build a Bittensor miner neuron that:
>
> 1. Registers a hotkey on a subnet (netuid from env var)
> 2. Starts an Axon server that listens for `SimulationQuery` requests from validators
> 3. On receiving a query: passes it to `SimulationEngine.run_query()` and returns the `OpinionReport`
> 4. Handles the standard Bittensor miner lifecycle: blacklisting, priority, and deregistration safety
> 5. Logs performance metrics (latency, agent count, domain coverage) for validator scoring
>
> Use bittensor SDK v8+. Include a `__main__` entry point that reads config from environment variables: `WALLET_NAME`, `WALLET_HOTKEY`, `NETUID`, `SUBTENSOR_NETWORK`, `LLM_API_KEY`, `LLM_BASE_URL`, `LLM_MODEL_NAME`."

```bash
git add miner/
git commit -m "feat: Bittensor Axon miner neuron — registration, query handling, lifecycle"
git push
```

### Step 2.5 — Build the validator scoring engine

**Claude Code prompt:**
> "In `validator/neuron.py`, build a Bittensor validator that:
>
> 1. Periodically (every tempo = ~72 min) queries all registered miners with a `SimulationQuery`
> 2. Scores each miner's `OpinionReport` using `validator/scorer.py` with these criteria:
>    - **Diversity score** (0–1): how spread out are agent opinions? Reward heterogeneity, penalize herd behavior
>    - **Consistency score** (0–1): do repeated queries on the same topic produce stable distributions?
>    - **Calibration score** (0–1, deferred): when ground truth exists (prediction market resolution), how accurate was the consensus?
>    - **Latency score** (0–1): penalize miners slower than 60s for a 100-agent sim
>    - **Coverage score** (0–1): reward miners supporting more topic domains
> 3. Combines scores into a final weight per miner and submits to chain via `set_weights()`
> 4. Caches last 10 queries per miner for consistency scoring
>
> In `validator/scorer.py`, implement each scoring function with clear docstrings. Keep it simple and deterministic — no LLM calls in scoring."

```bash
git add validator/
git commit -m "feat: validator neuron + scoring engine — diversity, consistency, latency, coverage"
git push
```

### Step 2.6 — Build the consumer API

**Claude Code prompt:**
> "In `api/main.py`, build a FastAPI service that:
>
> 1. `POST /query` — accepts `{seed_text, topic_domain, question}`, forwards to best available miner via the validator's miner registry, returns `OpinionReport`
> 2. `GET /groups` — lists available topic domain groups and their agent counts across known miners
> 3. `GET /miners` — returns miner capabilities from the metagraph
> 4. `WebSocket /stream/{domain}` — streams continuous opinion updates for a domain as miners produce them
>
> The API connects to the Bittensor network as a read-only client (no mining, no staking needed for the API layer). Add API key auth via header `X-API-Key`."

```bash
git add api/
git commit -m "feat: consumer API — POST /query, GET /miners, WebSocket /stream"
git push
```

### Step 2.7 — Write tests

**Claude Code prompt:**
> "Write tests in `tests/`:
> 1. `test_protocol.py` — validate all Pydantic models serialize/deserialize correctly
> 2. `test_simulation.py` — mock the OASIS subprocess and test SimulationEngine with 10 agents, 5 rounds
> 3. `test_scorer.py` — unit test each scoring function with known inputs and expected outputs
> 4. `test_miner_integration.py` — spin up a local Bittensor chain using `btcli` in test mode and verify miner registration + query response round-trip
>
> Run all tests and fix any failures."

```bash
git add tests/
git commit -m "test: protocol models, simulation engine, scorer, miner integration"
git push
```

---

## Phase 3 — Run on Local Bittensor Testnet

**Goal:** Prove the full subnet works — miner receives queries, produces reports, validator scores and submits weights — all on your MacBook before touching real TAO.

### Step 3.1 — Set up local Bittensor chain

```bash
cd ~/GitHub/mirofish_on_bittensor
claude
```

**Claude Code prompt:**
> "Set up a local Bittensor subtensor node for development:
> 1. Clone `https://github.com/opentensor/subtensor` and run it in fast-blocks Docker mode
> 2. Create owner, miner, and validator wallets using `btcli`
> 3. Fund all wallets with test TAO
> 4. Create a new subnet (netuid will be assigned)
> 5. Register the miner and validator hotkeys on the subnet
> 6. Verify the metagraph shows both nodes registered
>
> Document all wallet names, hotkeys, and the assigned netuid in a `local_dev.env.example` file (with placeholder values only — real values stay in your local `local_dev.env` which is git-ignored)."

```bash
git add local_dev.env.example
git commit -m "docs: local dev environment template for testnet setup"
git push
```

### Step 3.2 — Run the full stack locally

```bash
# Terminal 1 — local chain
docker run subtensor --dev --fast-blocks

# Terminal 2 — miner
source local_dev.env && python -m miner.neuron

# Terminal 3 — validator
source local_dev.env && python -m validator.neuron

# Terminal 4 — consumer API
uvicorn api.main:app --reload
```

**Claude Code prompt:**
> "Debug the local testnet run. Verify:
> 1. Miner registers and shows as active in metagraph
> 2. Validator queries the miner successfully and gets an OpinionReport back
> 3. Validator submits weights successfully on-chain
> 4. Consumer API returns a valid response to `POST /query`
>
> Fix any issues found. Log the full request/response cycle."

### Step 3.3 — Tune simulation parameters for speed

**Claude Code prompt:**
> "Profile the SimulationEngine with different agent counts on a MacBook (M-series). Find the sweet spots for:
> - 50 agents — target < 15s
> - 100 agents — target < 30s
> - 500 agents — target < 120s
>
> Adjust batch sizes, concurrency limits, and LLM call parameters to hit these targets. Update the miner's `MinerCapabilities` to report realistic latency."

---

## Phase 4 — Cloud Deployment (Testnet)

**Goal:** Move the miner off your MacBook onto a cloud server, connect to Bittensor testnet (not mainnet yet), and test under real network conditions.

### Step 4.1 — Choose and provision a cloud server

Recommended: **Hetzner CX52** (~€35/month) — 8 vCPU, 32 GB RAM, 240 GB SSD. No GPU needed.

Or AWS `c6i.2xlarge` (8 vCPU, 16 GB) if you prefer AWS. 32 GB preferred for 500+ agents.

**Claude Code prompt:**
> "Create deployment scripts for the mirofish_on_bittensor project:
> 1. `deploy/setup_server.sh` — SSH setup script that installs Docker, Python 3.11, clones `git@github.com:YOUR_USERNAME/mirofish_on_bittensor.git`, and runs initial setup
> 2. `deploy/docker-compose.prod.yml` — production compose file with miner + validator + API + Redis services
> 3. `deploy/env.template` — template for production `.env` with all required variables (no real values)
> 4. `deploy/start.sh` — starts all services, verifies health endpoints
> 5. A `Makefile` with targets: `deploy`, `status`, `logs`, `restart`, `pull` (git pull + restart)
>
> The miner should auto-restart on crash and reconnect to the Bittensor network. The `pull` target makes updates as simple as running `make pull` on the server — it pulls the latest code from GitHub and restarts services."

```bash
git add deploy/ Makefile
git commit -m "feat: deployment scripts — server setup, Docker Compose, Makefile"
git push
```

### Step 4.2 — Connect to Bittensor testnet

**Claude Code prompt:**
> "Update the miner and validator configuration to connect to Bittensor testnet (`--subtensor.network test`). Obtain testnet TAO from the Discord faucet (document the Discord link and process in README). Register the miner and validator on testnet subnet (use an existing prediction or forecasting subnet if one is available, or create a new subnet on testnet). Verify registration and first successful weight submission."

### Step 4.3 — Configure Chutes as the LLM backend

**Claude Code prompt:**
> "Update the miner's LLM configuration to use Chutes via OpenRouter in production:
> - `LLM_BASE_URL=https://openrouter.ai/api/v1`
> - `LLM_MODEL_NAME=chutes/deepseek-v3` (or the latest available Chutes model on OpenRouter)
>
> Add cost tracking to the SimulationEngine — log estimated token cost per simulation query. Calculate the break-even: how many validator queries per day does the miner need to cover its LLM API costs at current Chutes pricing (~$0.28/M tokens for DeepSeek-V3)?"

### Step 4.4 — Load testing

**Claude Code prompt:**
> "Write a load test script `tests/load_test.py` that:
> 1. Fires 10 concurrent SimulationQuery requests at the miner
> 2. Measures p50/p95/p99 latency
> 3. Checks that all responses are valid OpinionReports
> 4. Simulates 24 hours of validator queries at 1 query per tempo per miner
>
> Run the load test and identify any bottlenecks. Optimize if p95 > 90s for 100-agent simulations."

---

## Phase 5 — Mainnet Launch

**Goal:** Register the subnet on Bittensor mainnet, attract miners, and open the consumer API to the public.

### Step 5.1 — Acquire TAO for subnet registration

Current registration cost fluctuates dynamically. Watch `taostats.io/subnets` for the current burn cost. Timing matters — register immediately after another subnet registers (cost resets lower).

Options for acquiring TAO:
- Buy on Coinbase, Kraken, or Gate.io
- Apply for Bittensor Subnet Ideathon (reduced/subsidized registration for promising new subnets — apply at `bittensor.com`)

### Step 5.2 — Pre-launch checklist

**Claude Code prompt:**
> "Generate a pre-launch checklist for the MiroFish subnet mainnet registration. Include:
> 1. Security audit of miner code (no exposed wallet keys, rate limiting on Axon)
> 2. Incentive mechanism review — document the exact scoring formula and verify it cannot be gamed by a miner returning static responses
> 3. Miner onboarding docs — README that lets a new miner get running in < 30 min
> 4. Validator docs — how to run a validator, hardware requirements, expected emissions
> 5. Consumer API docs — OpenAPI spec, authentication, rate limits, example curl commands
> 6. Monitoring setup — alerts for miner deregistration, validator weight submission failures
>
> Fix any gaps identified."

### Step 5.3 — Register the subnet on mainnet

```bash
btcli subnet create \
  --wallet.name owner \
  --wallet.hotkey default \
  --subtensor.network finney
```

This burns the registration TAO. The subnet gets assigned a netuid. It is inactive for ~1 week — use this time to:
- Register your own miner and validator
- Publish the GitHub repo and onboarding docs
- Post in Bittensor Discord to attract early miners

### Step 5.4 — Bootstrap the network

**Claude Code prompt:**
> "Create a `bootstrap/` directory with:
> 1. `seed_agents.py` — generates 5 starter opinion groups (Finance 500 agents, Geopolitics 400, Tech 600, Health 300, General 400) with diverse personas using the Chutes API. Each agent needs realistic demographics and opinion distributions.
> 2. `verify_subnet.py` — checks that the subnet is healthy: validator is submitting weights, at least 3 miners are active, and the consumer API returns valid responses.
> 3. `announce_template.md` — Discord announcement template with subnet netuid, how to mine, how to use the consumer API."

### Step 5.5 — Open the consumer API

Deploy the consumer API on a separate server (or same server, different port). Set pricing:
- Free tier: 5 queries/day, Finance and Tech topics only, max 100 agents
- Paid tier: unlimited queries, all 6 domains, up to 1000 agents, WebSocket streaming

**Claude Code prompt:**
> "Add tiered API key management to `api/main.py` using Redis for rate limiting. Implement:
> - Free tier: 5 req/day, domains [Finance, Tech], max 100 agents in query
> - Pro tier ($20/mo): unlimited req, all domains, max 500 agents, WebSocket access
>
> Add Stripe webhook handling for subscription management. Store subscription state in Redis with TTL-based key rotation."

---

## Key Technical Decisions Summary

| Decision | Choice | Reason |
|---|---|---|
| GitHub structure | 2 repos: subnet (yours) + MiroFish-Offline fork | Clean separation, upstream sync, deployment via git |
| MiroFish fork strategy | Fork nikmcfly/MiroFish-Offline, add upstream remote | Most complete and documented variant; Neo4j is fine in Docker Compose |
| MiroFish engine | nikmcfly/MiroFish-Offline (Neo4j + Ollama) | Complete, well-documented, English UI |
| LLM backend | Chutes via OpenRouter | CPU miner stays GPU-free, ~85% cheaper than AWS |
| Local LLM (dev) | Ollama + qwen3:14b | Free, runs on MacBook with 24GB RAM |
| Graph storage | Neo4j (via Docker Compose) | Used by MiroFish-Offline, well-supported |
| Miner hardware | CPU-only, 8 vCPU / 32 GB RAM | Accessible, low barrier for global miners |
| Scoring | Diversity + Consistency + Latency | Objective, hard to game, rewards genuine quality |
| Consumer API | FastAPI + Redis | Async, fast, easy to deploy |
| Deployment | `git pull` + Docker Compose | Single command updates from GitHub to server |

---

## Estimated Timeline

| Phase | Effort | Wall time |
|---|---|---|
| Phase 0 — GitHub setup | 1–2 hours | Day 1 (morning) |
| Phase 1 — Run MiroFish locally | 1–2 days | Day 1–2 |
| Phase 2 — Build miner/validator | 5–7 days | Day 3–10 |
| Phase 3 — Local testnet | 2–3 days | Day 11–13 |
| Phase 4 — Cloud + testnet | 3–5 days | Day 14–18 |
| Phase 5 — Mainnet launch | 1–2 weeks (TAO acquisition + timing) | Day 19–32 |

Total to mainnet: **~4–5 weeks** of focused work with Claude Code driving implementation.

---

## How to Use This Plan With Claude Code

Each step has a "Claude Code prompt" — these are ready to paste directly into `claude` (Claude Code CLI). The workflow for every step is:

1. `cd` into the relevant repo directory (either `mirofish_on_bittensor` or `mirofish-offline`)
2. Run `claude` to start a session
3. Paste the prompt
4. Let Claude Code write the code, run it, and fix errors iteratively
5. Review the output, test manually
6. **Commit and push to GitHub** before moving to the next step

Claude Code will handle: writing all Python files, running `pip install`, executing `btcli` commands, debugging errors, and writing tests. You drive the architecture decisions; Claude Code handles implementation. GitHub holds the permanent record of everything built.

**The commit cadence matters.** Small, frequent commits (`git add -p` to stage only intentional changes) make it easy to roll back if something breaks, and give you a clear history of how the project evolved — valuable if you ever want to write about it or attract contributors.

