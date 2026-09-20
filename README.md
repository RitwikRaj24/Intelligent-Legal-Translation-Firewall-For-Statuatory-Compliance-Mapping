# ⚖️ LexGraph: Deterministic Legal Knowledge Graph & Translation Firewall for Indian Law

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/Backend-FastAPI-009688.svg)](https://fastapi.tiangolo.com/)
[![React](https://img.shields.io/badge/Frontend-React%2019%20%7C%20Vite%20%7C%20TS-61DAFB.svg)](https://react.dev/)
[![Streamlit](https://img.shields.io/badge/Dashboard-Streamlit-FF4B4B.svg)](https://streamlit.io/)
[![Obsidian](https://img.shields.io/badge/Knowledge%20Base-Obsidian%20Vaults-7C3AED.svg)](https://obsidian.md/)
[![Accuracy](https://img.shields.io/badge/Gold%20Set%20Accuracy-100%25-brightgreen.svg)]()
[![License](https://img.shields.io/badge/License-MIT%20%2F%20Research-purple.svg)]()

> **LexGraph** is an auditable, deterministic legal knowledge graph and translation engine for the historic 2023 transition of Indian criminal and procedural law. It bridges legacy codes (IPC, CrPC, IEA) to modern statutes (BNS, BNSS, BSA) without relying on probabilistic LLM hallucinations.

---

## 📌 Table of Contents

- [The Legal Transition Crisis](#-the-legal-transition-crisis)
  - [The Number Collision Trap](#the-number-collision-trap)
  - [Why Generic LLM RAG Fails](#why-generic-llm-rag-fails)
- [The LexGraph Architecture & Philosophy](#-the-lexgraph-architecture--philosophy)
- [Key Features](#-key-features)
- [Quantitative Benchmark & Evaluation](#-quantitative-benchmark--evaluation)
- [Visual Showcase & Screenshots](#-visual-showcase--screenshots)
- [Repository File Structure](#-repository-file-structure)
- [Quickstart & Installation](#-quickstart--installation)
  - [Prerequisites](#prerequisites)
  - [1. WhatsApp-Style Channel Demo (FastAPI + React)](#1-whatsapp-style-channel-demo-fastapi--react)
  - [2. Streamlit Legal Exploration App](#2-streamlit-legal-exploration-app)
  - [3. Obsidian Knowledge Graph](#3-obsidian-knowledge-graph)
  - [4. Running Smoke Tests & Verification](#4-running-smoke-tests--verification)
- [Statute Vaults & Ground Truth Coverage](#-statute-vaults--ground-truth-coverage)
- [Core Governance & Operating Rules](#-core-governance--operating-rules)
- [Disclaimer & License](#-disclaimer--license)

---

## 🚨 The Legal Transition Crisis

In 2023, the Parliament of India overhauled the country’s foundational colonial-era legal codes, replacing statutes that had governed the criminal justice system for over a century:

| Legal Domain | Colonial / Legacy Code | Modern Replacement Code |
| :--- | :--- | :--- |
| **Substantive Criminal Law** | **Indian Penal Code (IPC), 1860** (511 sections) | **Bharatiya Nyaya Sanhita (BNS), 2023** (358 sections) |
| **Criminal Procedure** | **Code of Criminal Procedure (CrPC), 1973** (484 sections) | **Bharatiya Nagarik Suraksha Sanhita (BNSS), 2023** (531 sections) |
| **Law of Evidence** | **Indian Evidence Act (IEA), 1872** (167 sections) | **Bharatiya Sakshya Adhiniyam (BSA), 2023** (170 sections) |

This transition created massive operational friction across police stations, trial courts, law firms, legal tech platforms, and citizen helpdesks. Millions of existing FIRs, judgments, and legal precedents reference legacy sections that no longer align with current enactments.

### The "Number Collision Trap"

A dangerous assumption is that section numbers correspond or that section numbers shifted linearly. In reality, **identical section numbers now describe completely different offences and legal mechanisms**:

*   **The Murder Trap:** For 160 years, **IPC §302** defined punishment for murder. In the modern code, murder is **BNS §103**. Conversely, **BNS §302** defines **snatching** (an aggravated form of theft). Confusing the two in an FIR or bail application is disastrous.
*   **The Anticipatory Bail Trap:** **CrPC §438** is the landmark provision for anticipatory bail. Under the new procedure code, anticipatory bail is governed by **BNSS §482**. Conversely, **BNSS §438** governs sessions court revisions.
*   **The Secondary & Electronic Evidence Trap:** **IEA §65** governed secondary evidence. Under the modern code, **BSA §65** pertains to proof of handwriting and signatures, while electronic records are governed under **BSA §61 and §63**.

### Why Generic LLM RAG Fails

Standard Retrieval-Augmented Generation (vector embeddings + LLM synthesis) fails in statutory law:
1.  **Hallucination of Statutory Numbers:** LLMs generate plausible-sounding section numbers that do not exist or map to incorrect legal penalties.
2.  **Semantic Similarity Traps:** Vector cosine similarity frequently conflates provisions with similar language (e.g., hurt vs. grievous hurt, or trial procedures vs. revision powers) despite distinct legal definitions and evidentiary thresholds.
3.  **Lack of Auditability:** In judicial proceedings and police administration, citations must be verifiable against official Gazette bitstreams, not probabilistic token generation.

---

## 🏛️ The LexGraph Architecture & Philosophy

LexGraph rejects unconstrained probabilistic generation in favor of **deterministic graph traversal and audited bridges**:

```
+-----------------------------------------------------------------------------+
|                               USER INTERFACES                               |
|  - Channel Demo (WhatsApp-style React 19 + TypeScript + FastAPI)            |
|  - Streamlit Explorer (Sections, Concepts, Compare, Gold Tests)             |
+-----------------------------------------------------------------------------+
                                       │
                                       ▼
+-----------------------------------------------------------------------------+
|                         CONVERSATIONAL LAYER (converse.py)                  |
|  1. Active Scenario Intake Short-Circuit (preserves ongoing slot-filling)    |
|  2. Local Scenario Trigger Matcher (keyword scoring, bypasses LLM)          |
|  3. Casual / Hinglish Query Rewriter (Gemini Flash / Groq LLaMA)            |
|  4. Deterministic Local Phrase Map (fallback when offline / no LLM key)      |
+-----------------------------------------------------------------------------+
                                       │
                                       ▼
+-----------------------------------------------------------------------------+
|                          Q&A REASONING ENGINE (qa.py)                       |
|  - Intent Detection: Equivalent, Collision, Explain, Concept, Bailability   |
|  - Multi-Section Parsing (e.g., "IPC 323 + 506")                            |
|  - Scenario Flow Controller (orient -> slot collection -> resolve)          |
+-----------------------------------------------------------------------------+
                                       │
                     ┌─────────────────┴─────────────────┐
                     ▼                                   ▼
+------------------------------------+  +-------------------------------------+
|        TRANSLATION FIREWALL        |  |            OBSIDIAN VAULTS          |
|  - Curated Bridge Audits (CSVs)    |  |  - Isolated Markdown Vaults         |
|  - Preference: Manual/Curated over |  |    data/wiki/{statute}/             |
|    Auto-matching                   |  |    ├── sections/                    |
|  - Collision Explainer Cards       |  |    ├── concepts/                    |
|  - Bi-directional Link Resolution  |  |    └── entities/                    |
+------------------------------------+  +-------------------------------------+
                     │                                   │
                     └─────────────────┬─────────────────┘
                                       ▼
+-----------------------------------------------------------------------------+
|                          GROUND TRUTH REPOSITORY                            |
|  - Read-Only Sources: data/raw/ (Official Gazette PDFs, India Code CSVs)    |
|  - Automated Build & Verification Tooling (scripts/)                        |
+-----------------------------------------------------------------------------+
```

### Core Tenets:
1.  **Wiki Cites or Silence:** Answers must derive from verifiable Markdown pages within `data/wiki/`. If a statute is not compiled, the system reports an honest gap.
2.  **Deterministic Translation Firewall:** Bi-directional cross-statute bridges are hand-curated and audited in CSV registries. The engine strictly prefers `manual`/`curated` mappings over algorithmic ones.
3.  **Constrained LLM Role:** LLMs (Gemini / Groq) are used exclusively as conversational rewriters for casual/Hinglish user queries. The LLM is **never** permitted to generate, invent, or hallucinate statutory law.

---

## ✨ Key Features

- 🔄 **Deterministic Section Translation:** Instant, two-way cross-referencing between legacy codes (IPC, CrPC, Evidence Act, Companies 1956) and modern enactments (BNS, BNSS, BSA, Companies 2013).
- ⚠️ **Number Collision Explainer Cards:** Automatic detection of trap sections (§302, §438, §65, etc.) with side-by-side comparison cards explaining divergent penalties and statutory scopes.
- 📚 **18 Statute Obsidian Knowledge Vaults:** Over 3,800+ legal sections compiled into standardized Markdown schemas with raw Gazette text, plain-English summaries, classification schedules, and intra-vault wikilinks.
- 👥 **Guided Citizen Scenario Packs:** Multi-step conversational intake flows structuring complex real-world citizen dilemmas into grounded legal roadmaps:
  - `domestic-violence-spousal-hurt`: PWDVA 2005 protection orders, BNS/IPC cruelty, and HMA grounds.
  - `neighbour-land-encroachment`: Criminal trespass/mischief (BNS) combined with civil injunctions under Specific Relief Act 1963.
  - `cheque-bounce-ni-138`: Step-by-step statutory intake for Negotiable Instruments Act §138–§142.
  - `hindu-succession-inheritance`: Coparcenary rights, Class I heirs, and ancestral property under HSA 1956.
  - `special-marriage-civil`: Civil marriage notice, objections, and mutual consent divorce under SMA 1954.
- 📱 **Modern WhatsApp Channel Demo:** A mobile-first, full-stack conversational desk built with React 19, TypeScript, Lucide icons, and FastAPI. Includes "Understood as" intent pills, collapsible verified sources drawers, and side-by-side statute comparators.
- 🧪 **Complete Benchmark Suite:** Built-in quantitative evaluation harness testing deterministic traversal against lexical/TF-IDF similarity on human-curated gold sets.

---

## 📊 Quantitative Benchmark & Evaluation

LexGraph maintains an automated evaluation harness (`eval/`) to benchmark deterministic graph traversal against statistical vector/lexical retrieval (TF-IDF similarity baseline) across foundational code pairs:

| Statute Pair | Gold Test Set Size | LexGraph Accuracy | Hard Probe Pass Rate | TF-IDF Baseline Accuracy |
| :--- | :---: | :---: | :---: | :---: |
| **BNS ↔ IPC** | 442 pairs | **100%** | **9 / 9 (100%)** | 61.0% |
| **BNSS ↔ CrPC** | 206 pairs | **100%** | **11 / 11 (100%)** | 83.5% |
| **BSA ↔ Evidence** | 34 pairs | **100%** | **9 / 9 (100%)** | 76.5% |

*Key Takeaway:* Lexical and vector similarity models fail when sections are split, renumbered, or rephrased with negative qualifiers. LexGraph’s curated audit architecture guarantees 100% precision on gold test sets.

---

## 🖼️ Visual Showcase & Screenshots

### WhatsApp-Style Citizen Legal Desk (`channel-demo/`)
Conversational query rephrasing, collision warning callouts, and collapsible statutory citation drawers:

| Interactive Citizen Chat Desk | Collision Explainer & Citation Drawer |
| :---: | :---: |
| ![WhatsApp Interface 1](Screenshots/Whatsapp_1.png) | ![WhatsApp Interface 2](Screenshots/Whatsapp_2.png) |

### Platform Overview & Comparative Analysis
Side-by-side section comparison, landing presentation, and architectural breakdown:

| Overview & Philosophy | Section Collision Analysis |
| :---: | :---: |
| ![Home Overview](Screenshots/Home_1.png) | ![Why LexGraph](Screenshots/Why_1.png) |

| Feature Deep Dive | Architectural Presentation |
| :---: | :---: |
| ![Home Features](Screenshots/Home_2.png) | ![Why Collision Cards](Screenshots/Why_2.png) |

---

## 📂 Repository File Structure

```
Github Repo Files/
├── README.md                              # Master project documentation
├── .gitignore                             # Git ignore specifications (secrets, node_modules, pycache)
├── .cursorrules                           # Project development instructions & styling
├── .obsidian/                             # Obsidian vault configuration & graph view styling
├── AGENT.md                               # Operational charter & governance rules
├── ARCHITECTURE.md                        # Detailed software architecture specification
├── LEXGRAPH_APPLICATION_WORKINGS.md       # Comprehensive system reference & deep-dive manual
├── LEXGRAPH_COMPLETE_SUMMARY.md           # Project evolutionary log through v5
│
├── Screenshots/                           # Application showcase screenshots
│   ├── Home_1.png ... Home_4.png
│   ├── Whatsapp_1.png, Whatsapp_2.png
│   └── Why_1.png ... Why_3.png
│
├── channel-demo/                          # Full-Stack WhatsApp Channel Demo
│   ├── README.md                          # Channel demo run guide
│   ├── .env.example                       # API key configuration template
│   ├── api/                               # FastAPI backend
│   │   ├── main.py                        # REST API endpoints (/chat, /orient, /focus)
│   │   ├── sessions.py                    # Multi-turn session state management
│   │   └── requirements.txt               # Backend dependencies (fastapi, uvicorn)
│   └── web/                               # React 19 + TypeScript frontend
│       ├── src/                           # UI components, layout shells, compare desk
│       ├── public/                        # Static assets
│       ├── index.html                     # Web entry point
│       ├── package.json                   # Frontend dependencies
│       └── vite.config.ts                 # Vite build configuration
│
├── ui/                                    # Streamlit Legal Exploration App
│   ├── app.py                             # Streamlit entrance script
│   ├── requirements.txt                   # Streamlit dependencies
│   ├── pages/                             # Multi-page views
│   │   ├── 1_Search.py                    # Natural language & statutory search
│   │   ├── 2_Section_View.py              # Section-by-section raw & breakdown reader
│   │   ├── 3_Bridge_Explorer.py           # Cross-statute bridge browser
│   │   ├── 4_Concept_Hub.py               # Legal doctrine explorer
│   │   └── 5_Demo_302.py                  # High-risk collision demonstration
│   └── lib/                               # Core Python reasoning engine
│       ├── qa.py                          # Deterministic intent parser & answer composer
│       ├── converse.py                    # Conversational rewriter & scenario router
│       ├── bridges.py                     # Bridge audit traversal & priority engine
│       ├── vault.py                       # Markdown vault loader & frontmatter parser
│       ├── llm.py                         # Minimal HTTP client for Gemini & Groq
│       ├── scenarios.py                   # Citizen scenario registry & keyword matcher
│       └── intake.py                      # Multi-turn slot-filling intake controller
│
├── data/                                  # Ground Truth & Knowledge Vaults
│   ├── wiki/                              # 18 statute vaults in Obsidian Markdown format
│   │   ├── Index.md                       # Master cross-vault table of contents
│   │   ├── bns/, ipc/                     # Substantive criminal law vaults
│   │   ├── bnss/, crpc/                   # Criminal procedure vaults
│   │   ├── bsa/, evidence/                # Evidence law vaults
│   │   ├── companies_2013/, _1956/        # Corporate law vaults
│   │   ├── wages/, ir/, ss/, osh/         # Four central labour codes
│   │   ├── hma/, sma/, hsa/, pwdva/       # Family, civil & succession acts
│   │   ├── specific_relief/, ni/          # Property injunctions & commercial paper
│   │   └── scenarios/                     # Citizen intake flow definitions (.json)
│   ├── raw/                               # IMMUTABLE official Gazette PDFs & extracted CSVs
│   ├── meta/                              # Statutory chapter maps & schedules
│   ├── bridge_audit.csv                   # BNS ↔ IPC audited bridge registry
│   ├── bnss_crpc_bridge_audit.csv         # BNSS ↔ CrPC audited bridge registry
│   ├── bsa_evidence_bridge_audit.csv      # BSA ↔ Evidence audited bridge registry
│   ├── companies_bridge_audit.csv         # Companies 2013 ↔ 1956 bridge registry
│   ├── offence_classification.csv         # Bailability, cognizability & triable court schedules
│   └── *_curator_notes.md                 # Curation decisions and override rationales
│
├── scripts/                               # Data Pipelines, Compilers & Quality Assurance
│   ├── extract_statute_sections.py        # PDF -> CSV section extractor
│   ├── compile_new_statute_vaults.py      # CSV -> Obsidian Markdown vault compiler
│   ├── build_wiki_index.py                # Generates data/wiki/Index.md
│   ├── translation_firewall*.py           # Cross-statute bridge generators
│   ├── curate_*_bridges.py                # Manual bridge curation scripts
│   ├── post_bridge_lint.py                # Markdown schema and wikilink linter
│   └── smoke_test_stage.py                # End-to-end stage verification test
│
├── eval/                                  # Quantitative Benchmark Suite
│   ├── requirements.txt                   # Evaluation dependencies
│   ├── pair_config.py                     # Evaluator pair definitions
│   ├── eval_utils.py                      # Evaluation helper routines
│   ├── gold/                              # Curated ground-truth test pairs
│   ├── results/                           # Evaluation benchmark results & visual plots
│   └── scripts/                           # Benchmark runner scripts
│
├── Misc/                                  # Pitch decks, slides, and testing documentation
└── Testing_versions/                      # Historical iteration logs (v1 through v5)
```

---

## 🚀 Quickstart & Installation

### Prerequisites

- **Python 3.10+**
- **Node.js 18+** & **npm** (for the React WhatsApp frontend)
- *(Optional)* Free API key from [Google AI Studio](https://aistudio.google.com/apikey) or [Groq Cloud](https://console.groq.com/keys) for colloquial query rephrasing.

---

### 1. WhatsApp-Style Channel Demo (FastAPI + React)

The WhatsApp channel demo provides the primary citizen and practitioner conversational interface.

#### Backend (FastAPI):
```bash
cd channel-demo/api
pip install -r requirements.txt

# Optional: configure API keys for colloquial query rewriting
cp ../.env.example ../.env
# Edit ../.env and insert your GEMINI_API_KEY or GROQ_API_KEY

# Start the API server on port 8000
python main.py
```
*API docs available at: `http://localhost:8000/docs`*

#### Frontend (React + Vite):
```bash
cd channel-demo/web
npm install
npm run dev
```
*Open your browser at: `http://localhost:5173`*

---

### 2. Streamlit Legal Exploration App

The Streamlit app is designed for deep-dive statutory comparison, concept traversal, and test verification.

```bash
# From repository root
pip install -r ui/requirements.txt
streamlit run ui/app.py
```
*Features multi-page views including Section Viewer, Bridge Explorer, Concept Hub, and Collision Demonstrator.*

---

### 3. Obsidian Knowledge Graph

LexGraph's vaults are natively structured for [Obsidian](https://obsidian.md/):

1. Download and open Obsidian.
2. Select **"Open folder as vault"**.
3. Point to the repository root directory (or `data/wiki/`).
4. Open the **Interactive Graph View** (`Ctrl/Cmd + G`) to explore visual clusters of statutory doctrines, intra-act relationships, and long-range translation bridge projections.

---

### 4. Running Smoke Tests & Verification

Verify vault integrity, frontmatter schema compliance, and translation accuracy:

```bash
# Run the structural & wikilink linter
python scripts/post_bridge_lint.py

# Run the end-to-end system smoke test
python scripts/smoke_test_stage.py

# Run translation firewall unit tests
python scripts/test_translation_firewall.py

# Run quantitative benchmark on gold test sets
python eval/scripts/run_pair_eval.py
```

---

## 📖 Statute Vaults & Ground Truth Coverage

LexGraph indexes **18 statutes** comprising **~3,800+ legal sections**:

| Corpus Code | Statute Title | Enactment Year | Section Count | Role |
| :--- | :--- | :---: | :---: | :--- |
| `bns` | Bharatiya Nyaya Sanhita | 2023 | 358 | Modern Substantive Criminal Law |
| `ipc` | Indian Penal Code | 1860 | 568 | Legacy Substantive Criminal Law |
| `bnss` | Bharatiya Nagarik Suraksha Sanhita | 2023 | 531 | Modern Criminal Procedure |
| `crpc` | Code of Criminal Procedure | 1973 | 536 | Legacy Criminal Procedure |
| `bsa` | Bharatiya Sakshya Adhiniyam | 2023 | 170 | Modern Law of Evidence |
| `evidence`| Indian Evidence Act | 1872 | 184 | Legacy Law of Evidence |
| `companies_2013` | Companies Act | 2013 | 504 | Modern Corporate Governance |
| `companies_1956` | Companies Act | 1956 | 288 | Legacy Corporate Law |
| `wages` | Code on Wages | 2019 | 69 | Labour Code (Wages & Bonus) |
| `ir` | Industrial Relations Code | 2020 | 104 | Labour Code (Trade Unions & Disputes) |
| `ss` | Code on Social Security | 2020 | 160 | Labour Code (PF, ESI & Gratuity) |
| `osh` | Occupational Safety, Health & Working Conditions | 2020 | 143 | Labour Code (Workplace Safety) |
| `hma` | Hindu Marriage Act | 1955 | 32 | Family Law (Marriage & Divorce) |
| `sma` | Special Marriage Act | 1954 | 54 | Civil Marriage & Registration |
| `hsa` | Hindu Succession Act | 1956 | 33 | Property Succession & Coparcenary |
| `pwdva` | Protection of Women from Domestic Violence Act | 2005 | 39 | Domestic Protection Orders |
| `specific_relief` | Specific Relief Act | 1963 | 44 | Civil Remedies & Injunctions |
| `ni` | Negotiable Instruments Act (Banking slice) | 1881 | 8 | Cheque Dishonour (§138–§142) |

---

## 🛡️ Core Governance & Operating Rules

LexGraph strictly enforces the core governance rules defined in `AGENT.md`:

1. **Caution and Verifiable Correctness over Speed:** Every statutory assertion must be backed by an audited section page in `data/wiki/`.
2. **`data/raw/` is Immutable:** Official Gazette PDFs and primary India Code bitstreams are read-only.
3. **No Autonomous Legal Creation:** Large language models are restricted to input query rewriting. They are never permitted to generate legal provisions or cite unindexed statutes.
4. **Honest Gap Reporting:** If a query touches an uncompiled statute or unresolved overlap, the system explicitly reports an informational gap rather than guessing.

---

## ⚖️ Disclaimer & License

**Disclaimer:** LexGraph is an academic research platform and legal informatics reference tool. It is **not** a substitute for licensed legal advice, advocate consultation, or official court filings.

This project is licensed under the terms of the MIT License / Research Open Access. See [LICENSE](LICENSE) for details.
