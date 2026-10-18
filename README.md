# ReverseOrbit

**AI-Powered Malware Reverse Engineering with Threat Intelligence**
*Turning reverse-engineering evidence into actionable, source-backed threat intelligence.*

> Submission for **AZINHACK 2026** by team **Bug Slayers**.

| | |
|---|---|
| **Team** | Bug Slayers |
| **Members** | Ashish Singh, Akshita Jha, Atul Kumar |
| **Institute** | St. Andrews Institute of Technology and Management |
| **License** | Apache-2.0 |

---

## Table of Contents

1. [Problem Statement](#1-problem-statement)
2. [Proposed Solution](#2-proposed-solution)
3. [Target Users and Use Cases](#3-target-users-and-use-cases)
4. [Role of Machine Learning / AI](#4-role-of-machine-learning--ai)
5. [Proposed Technical Approach](#5-proposed-technical-approach)
6. [Proposed TinyFish Integration](#6-proposed-tinyfish-integration)
7. [Implementation Flow](#7-implementation-flow)
8. [Expected Impact and Validation](#8-expected-impact-and-validation)
9. [Feasibility](#9-feasibility)
10. [Future Scope](#10-future-scope)
11. [Current Implementation Status](#11-current-implementation-status)
12. [Setup Instructions](#12-setup-instructions)
13. [Implementation Details](#13-implementation-details)
14. [Safety, References and License](#14-safety-references-and-license)

---

## 1. Problem Statement

> *"Reverse engineering with accurate data on upcoming future malwares."*

Reverse-engineering evidence is valuable, but hard to connect to the wider
threat landscape:

- **Technical evidence is hard to interpret in isolation.** A function, string
  or import means little without context.
- **Intelligence is fragmented** across advisories, research papers and vendor
  reports.
- **Manual verification is slow**, and new malware may evade signature-based
  detection.
- **Threat data changes constantly**, so yesterday's notes go stale.

**Core question:** how do we connect binary evidence with source-backed
intelligence *without overstating detection*?

## 2. Proposed Solution

ReverseOrbit unifies **analysis, enrichment, correlation and explanation** in
one traceable workflow, linking binary evidence to verified external
intelligence.

| Step | Stage | What happens |
|------|-------|--------------|
| 01 | **Analyze** | Ghidra and radare2 evidence, inside authorized sandboxes |
| 02 | **Enrich** | Public threat telemetry via proposed TinyFish automation |
| 03 | **Correlate** | Source URLs, timestamps, confidence scores and graph links |
| 04 | **Explain** | Evidence-grounded LLM summaries, validated by an analyst |

Every action is recorded as a step in a **hash-chained, tamper-evident
investigation graph**, so every conclusion can be audited and replayed. This
recording core is the part already implemented in this repository
(see [Current Implementation Status](#11-current-implementation-status)).

## 3. Target Users and Use Cases

**Five audiences:** reverse engineers, SOC analysts, incident-response (IR)
teams, academic labs, and AI/ML researchers who need verified analysis data.

| Use case | Description |
|----------|-------------|
| **01. New security advisory** | Track public zero-day alerts and vendor blogs; deliver source-cited records |
| **02. Suspicious binary triage** | Decompile unknown samples, gather related telemetry, produce review-ready dossiers |
| **03. Advanced threat research** | Investigate novel TTPs; map to MITRE ATT&CK only when justified; build traceable datasets |

## 4. Role of Machine Learning / AI

AI **assists the analyst; it does not replace evidence.** A bounded, explainable
AI layer turns verified technical and web evidence into review-ready
intelligence.

1. **Evidence extraction.** Parse analyst-approved Ghidra/radare2 output,
   strings, imports, behaviour notes and retrieved public advisories.
2. **Semantic correlation.** Use embeddings / similarity search to suggest
   related reports and techniques, while retaining exact source URLs and
   timestamps.
3. **Grounded explanation.** An LLM drafts concise summaries and explains why
   evidence may be related, quoting or linking supporting sources.
4. **Human validation.** Analysts confirm or reject suggested links.
   Unsupported claims are labelled *unknown*, never presented as detections.

**MVP boundary:** AI supports triage and research. It does not claim to predict
future malware, or prove attribution, without validated evidence.

**Already implemented:** an AI agent (Anthropic Claude by default; OpenAI,
Gemini, DeepSeek, Mistral, local Ollama / llama.cpp and others) that reverses a
binary through the same recorded tools a human uses. Every AI call carries a
required reason (`why`) and lands on the graph as a hash-chained step. Tool
access is controlled by profiles (Investigate, Read-only, Ask) and per-tool
permissions (allow / confirm / deny).

## 5. Proposed Technical Approach

A layered architecture keeps analysis, intelligence and AI separable.

| Layer | Responsibility |
|-------|----------------|
| **Interface** | Proposed React dashboard for samples, findings and reports |
| **Backend** | Python orchestration, FastAPI if suitable |
| **Reverse engineering** | Ghidra, radare2, isolated services |
| **Web intelligence** | Proposed TinyFish integration with source URLs and timestamps |
| **Correlation and AI** | Graph links, evidence-grounded explanations, MITRE ATT&CK when supported |
| **Storage** | PostgreSQL if selected; portable JSON |
| **Security boundary** | Suspicious samples only in properly isolated, authorized environments, never on an analyst's ordinary workstation |

```
 Authorized sample (isolated lab)
        |
        v
 Ghidra / radare2  ->  evidence (functions, strings, imports, xrefs)
        |
        v                         TinyFish (web intelligence)
 Investigation recorder  <------  search / fetch / research / monitor
 (hash-chained graph)                    |
        |                                v
        +------------>  Verify and correlate (URLs + timestamps)
                                         |
                                         v
                        Grounded LLM summary (citations + limits)
                                         |
                                         v
                       Analyst review (approve / reject)  ->  Final dossier
```

## 6. Proposed TinyFish Integration

TinyFish is the proposed **web-intelligence layer**, grounded in its official
documentation: the **Agent, Research, Search and Fetch** surfaces.

| Capability | Purpose |
|------------|---------|
| **Web signal monitoring** | Track changes on relevant public advisories and pages |
| **Research and intelligence** | Use the documented Agent, Research, Search or Fetch surfaces |
| **Verification and enrichment** | Keep source URLs and timestamps; cross-check originals |
| **Portal automation** | Automate repetitive collection on permitted portals |

**Flow:** source change -> TinyFish retrieval -> source and timestamp capture ->
verification -> correlation -> analyst review.

**Integration plan:**

- Confirm the official docs first; use documented endpoints only.
- Keep API credentials server-side.
- Handle retries, rate limits and cost.
- Validate retrieved content before it is used by the LLM.
- Record each web result as a hash-chained step with URL, retrieval time and
  excerpt, so evidence behind a verdict is reproducible.

Proposed ops (added once to the op table, which generates the console command,
agent tool schema and MCP tool): `web search`, `web fetch`, `intel <hash|domain|ip>`,
`watch <query>`, `research <question>`. Only indicators (hashes, domains, API
names) ever leave the machine, never the sample's bytes.

## 7. Implementation Flow

From binary evidence to a source-backed dossier. The pipeline separates
deterministic analysis, external retrieval, AI assistance and analyst approval.

| # | Stage | Output |
|---|-------|--------|
| 01 | Authorized sample | Isolated lab |
| 02 | Ghidra / radare2 | Extract evidence |
| 03 | TinyFish retrieval | Public sources |
| 04 | Verify and correlate | URLs + timestamps |
| 05 | Grounded AI summary | Citations + limits |
| 06 | Analyst review | Approve / reject |
| 07 | Final dossier | Traceable findings |

- **Decision gate A, source verification:** reject inaccessible or untrusted
  sources; preserve original URL, retrieval time and the relevant excerpt.
- **Decision gate B, analyst approval:** flag conflicting evidence and
  uncertainty; no automated attribution or final detection verdict.
- **Guardrails across the pipeline:** authorized samples only, server-side API
  credentials, source attribution, confidence labels, analyst sign-off.

## 8. Expected Impact and Validation

Success means faster research with stronger provenance, **not** unsupported
claims of perfect detection.

| Area | How it is measured |
|------|--------------------|
| **Research efficiency** | Time from sample intake to a review-ready dossier vs a manual baseline |
| **Evidence coverage** | % of generated claims linked to source URLs, timestamps or extracted technical evidence |
| **Correlation quality** | Analysts label suggested report/sample links; measure precision and false-positive rate on a held-out test set |
| **Trust and safety** | Unsupported statements, analyst override rate, reproducibility, and whether every conclusion is reviewable |

**Success metrics:** attribution (% of claims tied to source URLs), accuracy
(analyst-verified binary correlation rate), efficiency (binary-to-dossier
turnaround), unit cost (average API and compute spend per report).

**Wider impact:** verifiable evidence instead of editable notes; replayable
investigations for teaching; and verified, rationale-rich data for training
reverse-engineering models.

## 9. Feasibility

A practical MVP can be built in **three phases**. Reuse established tools
first; keep uncertain integrations modular until official capabilities are
verified.

**Roadmap**

- **Phase 1:** ingestion UI and Ghidra / radare2 integration.
- **Phase 2:** documented TinyFish pipeline and timestamping.
- **Phase 3:** graph correlation, LLM grounding and benchmarks.

**Feasibility dimensions**

- **Technical:** Ghidra and radare2 provide established analysis workflows;
  Python can orchestrate parsing, enrichment and report generation.
- **Integration:** TinyFish is a proposed layer; only endpoints and workflows
  confirmed in official documentation will be implemented.
- **Operational:** start with public advisories and benign / authorized test
  samples; run suspicious binaries only in isolated, approved environments.
- **Cost and delivery:** begin with a small dataset, caching, rate limits and a
  human-reviewed report; measure latency and cost before scaling.

**Risk controls**

| Risk | Control |
|------|---------|
| Unsupported APIs | Documentation-first design |
| Sample safety | Isolate execution inside containers |
| Hallucinations | Require analyst sign-off on claims |
| Cost and latency | Redis caching and rate limits |

**Key dependencies:** access to documented TinyFish APIs, reproducible sample
labelling, analyst review time, and secure sandbox infrastructure.

## 10. Future Scope

- Broader source coverage for threat intelligence.
- Analyst feedback loops to improve correlation.
- MITRE ATT&CK mapping when justified by evidence.
- Evaluation on more diverse authorized datasets.
- Collaborative investigations: share and merge signed investigation graphs.
- Signed chain anchoring for stronger tamper-evidence.
- Fine-tuning open reverse-engineering models on the exported SFT / DPO data.
- Dynamic-analysis steps in a sandbox, recorded in the same graph.
- Classroom mode: publish an investigation for students to replay and branch.

## 11. Current Implementation Status

This repository contains the working **recording and analysis core** that
ReverseOrbit builds on. Planned items are clearly marked.

| Component | Status |
|-----------|--------|
| Binary engine (ELF / PE / Mach-O; functions, disassembly, CFG, strings, imports, xrefs, hex) | Implemented |
| Optional pseudo-C decompiler (rz-ghidra via rizin) | Implemented (optional) |
| Hash-chained investigation recorder, verify badge, replay, flight log | Implemented |
| Investigation graph (typed edges, key paths, annotations) | Implemented |
| Cross-binary function recall (relocation-independent fingerprints) | Implemented |
| AI agent with multiple providers, profiles, permissions, MCP server | Implemented |
| Dataset export (trajectories, SFT, DPO) and Python chain verifier | Implemented |
| Desktop app (Tauri 2 + React) | Implemented |
| Full Ghidra / radare2 integration | Planned (Phase 1) |
| TinyFish web-intelligence pipeline | Proposed (Phase 2) |
| Graph correlation, embeddings, grounded dossier, benchmarks | Planned (Phase 3) |
| React dashboard, FastAPI backend, PostgreSQL / Redis | Proposed |

## 12. Setup Instructions

**Prerequisites:** Rust >= 1.77, Node >= 20 and the
[Tauri system packages](https://v2.tauri.app/start/prerequisites/). On
Ubuntu/Debian:

```bash
sudo apt install libdbus-1-dev pkg-config libwebkit2gtk-4.1-dev build-essential \
  curl wget file libxdo-dev libssl-dev libayatana-appindicator3-dev librsvg2-dev libgtk-3-dev
```

**Clone and run the desktop app:**

```bash
git clone https://github.com/atulhacks/crosure.git
cd crosure/tauri
npm install
npm run tauri dev
```

Optional shortcuts with [`just`](https://github.com/casey/just):

```bash
just dev        # desktop app
just preview    # same UI in a browser (dev bridge on :1421 + Vite on :1420)
just test       # cargo test --workspace + vitest
just lint       # clippy -D warnings + eslint
```

**Try it:** open the bundled benign crackme
`crates/crosure-engine/tests/fixtures/crackme-x64` (also `-stripped` and `.exe`).

**AI agent (optional):**

```bash
export ANTHROPIC_API_KEY=sk-ant-...        # or save the key in the app
cargo run -p crosure-agent --bin crosure-reverse -- ./sample "Find how the input is validated"
```

No API key? Run the offline demo: `CROSURE_AGENT_DEMO=1` (app) or `--demo` (CLI).

**TinyFish (proposed):** set `TINYFISH_API_KEY` server-side once the
integration lands. Never commit keys.

`CROSURE_HOME` overrides the data directory (default `~/.crosure`).

Prebuilt installers for Windows, macOS and Linux are on the
[Releases](https://github.com/atulhacks/crosure/releases) page.

## 13. Implementation Details

### Repository layout

```
crates/
  crosure-recorder/   step schema, hash chain, SQLite + blob store, verify
  crosure-engine/     Engine trait + native backend (ELF/PE/Mach-O, x86/x64/ARM/AArch64)
  crosure-graph/      investigation graph: typed edges, key paths, replay, stats
  crosure-session/    runs ops on the engine and records each one; console parser
  crosure-agent/      AI agent: providers, tools, profiles, permissions; crosure-reverse CLI
  crosure-mcp/        MCP server exposing the recorded tools to other agents
  crosure-dataset/    verified sessions -> trajectories / SFT / DPO JSONL; crosure-export CLI
python/               stdlib-only chain verifier and dataset loader (TRL / Hugging Face)
tauri/
  src/                React 19 + TypeScript frontend (Tailwind v4, React Flow, Zustand)
  src-tauri/          Tauri 2 app: thin commands over crosure-session
docs/                 overview, plan, AI agent, datasets, decompiler
```

### How it works

- **Every action is a recorded step.** Clicks, console commands, renames,
  hypotheses and findings each become a node with the exact command, target, a
  summary and the full result stored by content hash.
- **Hash chain.** Each step stores `sha256(prev_hash + canonical step)`. The
  verify badge re-derives the whole chain; editing any stored step turns it red.
- **Investigation graph.** Typed edges: `next` (time order), `derived_from`
  (clicked from a previous result), `branch` (jumped back and tried something
  else). The path to each finding is highlighted; dead ends fade.
- **Intent chips and tags** record *why* a step was taken and how it turned out.
  Annotations are new steps and never edit old ones.
- **Native control-flow graph:** functions split into basic blocks with taken /
  not-taken / jump edges (Linear or Graph view).
- **Recall across investigations:** function fingerprints show what earlier
  sessions on other binaries found about the same code.
- **Sessions persist** in SQLite (`~/.crosure/crosure.db`) and can be resumed.
- **Single source of truth:** each op is declared once in
  `crates/crosure-session/src/table.rs`; the console grammar, agent and MCP tool
  schemas and the write-tool list are generated from it, so they cannot drift.
- **Datasets:** *Export -> Training dataset* or `crosure-export` writes verified
  trajectories, SFT examples and DPO preference pairs
  ([docs/datasets.md](docs/datasets.md)).

### Console

```
info                         binary info
fns [--from line] [filter]   list functions
dis [--from line] <fn|addr>  disassemble function
dec [--from line] <fn|addr>  decompile function (rizin + rz-ghidra)
xt  [--from line] <fn|addr>  xrefs to
xf  [--from line] <fn|addr>  xrefs from function
str [--min n] [--from line] [filter]  strings
imp                          imports
hex <addr> [len]             hex dump (default 256 bytes, max 4096)
ren <fn|addr> <name>         rename function
cmt <addr> <text>            comment
hyp <text>                   pin a hypothesis
find <text>                  record a finding
verdict <verdict> [family|-] <text>  malicious, suspicious, benign or unknown
```

### Tech stack

Rust (`object`, Capstone), SQLite, Tauri 2, React 19, TypeScript, Tailwind v4,
React Flow, Zustand. Proposed: Python, FastAPI, PostgreSQL, Redis, Ghidra,
radare2, TinyFish, MITRE ATT&CK.

## 14. Safety, References and License

**Safety.** Analysis is static by default and nothing is executed. Use
authorized samples only, inside isolated environments. Never commit live
malware (see [samples/README.md](samples/README.md)). Unsupported claims are
labelled *unknown*, and no automated attribution or final detection verdict is
made without analyst approval.

**References**

- [Ghidra repository](https://github.com/NationalSecurityAgency/ghidra) and [Ghidra official site](https://ghidra-sre.org/)
- [radare2](https://www.radare.org/)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [arXiv](https://arxiv.org/)
- [TinyFish developer documentation](https://docs.tinyfish.ai/)

**License.** Apache-2.0
