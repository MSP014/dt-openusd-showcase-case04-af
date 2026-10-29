# Case 04: Air Field — Aviation Fuel Logistics

> [!WARNING]
> **Under Development:** Requirements and asset/pipeline preparation are in progress. The logistics runtime, cuOpt integration and operational validation are planned. Some links and visual artefacts are placeholders.

---

> **Role:** Synthetic-data-driven L2-style Decision-Support Prototype
> **Planned pipeline:** SideFX Houdini → OpenUSD → NVIDIA Omniverse; NVIDIA cuOpt for constrained route planning

---

## 📋 Project Overview

This project is developing a **Synthetic-data-driven L2-style Decision-Support Prototype** for apron refuelling. The planned demonstration combines a cinematic airfield, procedural assets and inspectable logistics decisions: which aircraft to service, which route to take and when to replenish the tanker.

**Current implementation — Under Development:** `src/` contains a placeholder, and `tests/test_dummy.py` is an infrastructure smoke test, not logistics validation. The repository currently provides working requirements, environment/dependency setup and development/reference utilities. It does not yet provide a working routing solver, synthetic-data generator or interactive logistics runtime.

**Key Use Case:**
**Planned:** Two identical KrAZ-221 + TZ-22 refuellers alternate active/standby duty to serve a schedule with **three aircraft on stands, two inbound flights and a possible sixth unscheduled arrival**. The system is intended to plan feasible service sequences and ground routes using departure/service deadlines, arrival readiness, travel distances, service times and three independent refueller stocks: aviation fuel, tractor diesel and petrol for the separate pump engine. Planned HUD overlays will expose assignments, fuel and reasons for replanning.

Two refuellers are required, including for fault tolerance. Normal fuel checks must preserve enough tractor diesel to return to the depot with reserve. If a vehicle nevertheless cannot reach it, the second must transfer enough diesel from its own tractor tank to restore that return while retaining its own return reserve. This recovery capability is planned; its equipment, timing and detailed lifecycle remain under requirements review.

Planned depot operations use two posts: aviation-fuel loading and separate diesel/pump-petrol replenishment at an auxiliary post, with no post-occupancy queues. Required visits and internal movement take time; separate depot parking holds both refuellers. Assigned departure requires sufficient stocks for the job and reserves, rather than full tanks. See the [depot transition requirements](docs/plans/requirements/domain_and_state_machines.md#depot-loading-and-departure).

Automatic dispatch is a required capability of the planned system. The boundaries between automatic operation, operator-confirmed recommendations and manual intervention remain under requirements review; manual approval of every dispatch is not a settled rule. Replanning latency and solution quality still need measurement, and global optimality is not claimed.

**Project Focus:**

- **Planned — Constrained routing:** Evaluate cuOpt for event-driven replanning with resource and service-window constraints.
- **Planned — State visualisation:** Separate aircraft, fuel-request and refueller lifecycles, with consistent fuel accounting and USD presentation.
- **Planned — Decision support and dispatch:** Explain recommendations and support automatic dispatch under a reviewed operator-mode contract.

### Why evaluate cuOpt?

Earliest Deadline First (EDF) is the planned simple baseline. It may already meet departure deadlines in straightforward cases. The additional routing objective is to **reduce total refueller travel and estimated diesel cost while satisfying the same service and resource constraints**. Sorting by deadline alone does not optimise the ground route or replenishment sequence.

The planned comparison uses the same revealed schedule/events, two-refueller fleet and rotation rules, road graph, three-stock fuel/service model and feasibility checks for EDF and cuOpt. It will report deadline performance, served demand, total distance and diesel consumption/cost across both refuellers, pump petrol separately, depot visits and calculation overhead. With at most six flights, GPU necessity and a cuOpt advantage are unproven; equal or worse results must be reported. See the [benchmark requirements](docs/plans/requirements/scenarios_and_validation.md#earliest-deadline-first-comparison).

### Synthetic data and maturity

Synthetic inputs are a deliberate development trade-off. This project has no operational data feed from an airport such as Zvartnots; obtaining and validating such data would require cooperation, access agreements and integration work. Reproducible scenarios allow the resource accounting, routing decisions, state transitions and presentation to be developed and tested without that dependency.

This limits claims about real-world calibration and operational readiness, while preserving the value of those engineering results once demonstrated. **L2-style** describes the intended decision-support capability, not a certified maturity level or a live operational Digital Twin. Forecasting, operator involvement and physical-data connectivity need separate evidence; see the [classification and limitations](docs/knowledge_base/digital_twin_maturity_levels.md).

---

## 🎯 Planned Technical Evidence

*These are intended deliverables, not completed runtime features.*

- **Planned — Logistics evidence:** Compare cuOpt routing with EDF, including deadlines, route distance, tanker diesel and replenishment.
- **Under Development — Houdini → USD pipeline:** Prepare procedural airfield and refueller assets for composition and presentation.
- **Planned — State evidence:** Demonstrate consistent aircraft/request/tanker states and resource changes across Python and USD.

## 👁️ Visual Proof

> *Planned evidence; visual artefacts are not available yet.*

1. **Route Comparison — Planned:** `![VRP Graph](docs/img/vrp_graph_placeholder.gif)`
2. **Refueling FUI:** `![Refueling HUD](docs/img/refueling_hud_placeholder.png)`
3. **Apron Layering:** `![USD Layers](docs/img/usd_composition_placeholder.png)`

## 🏗️ Architecture & Decisions

This project follows a **README-driven structure** to manage the complexity of hybrid Houdini/Omniverse pipelines.

- [**View Architecture Decision Records (ADR)**](docs/adr/) – Design notes on Naming Conventions, Security Guardrails, and VRP Logic integration.
- [**Fuel Logistics Requirements**](docs/plans/requirements/README.md) – Working requirements, document status, recorded decisions and next review steps. The synthetic-data provider, fuel model, cuOpt integration and operator workflow remain proposed; the requirements do not establish an implemented runtime.
- [**Omniverse Reference Clients**](tools/mcp/README.md) – Local Kit, OpenUSD and OmniUI query clients for development and reference work, separate from the logistics runtime.

## 📂 Repository Structure

```text
.
├── assets/
│   ├── _external/   # [DOWNLOADED] Runtime Assets (USD, Textures, HDRI) - Git Ignored
│   │   ├── usd/
│   │   ├── tex/
│   │   └── hdri/
│   └── local/       # Lightweight assets tracked by Git
├── docs/             # Documentation and knowledge base
│   ├── adr/          # Architecture Decision Records
│   ├── knowledge_base/  # Reference documentation (Digital Twin frameworks, VRP theory)
│   └── plans/        # Implementation plans & tech debt
├── src/              # Planned logistics runtime; currently a placeholder
├── tests/            # Infrastructure smoke test; domain validation planned
└── tools/            # Internal pipeline utilities
```

---

## 💾 Project Data / Assets

### 🏭 The "Factory" Narrative
>
> This repository follows a strict **"Source vs. Artifact"** philosophy:
>
> - **Houdini (Fabricator):** The procedural "factory" where assets are generated. Source files (`.hip`) are proprietary and **excluded** from this repository.
> - **USD (Artifact):** The intended output of asset preparation, for composition and presentation in Omniverse.
> - **Synthetic Data — Planned:** Deterministic flight schedules, arrivals, readiness changes and fuel-request events will exercise the logistics model. The generator and runtime resource accounting remain to be implemented.

### 📦 Asset Hydration

To keep this repository lightweight, heavy binary assets (USD Crates, Textures, HDRIs) are stored externally.

- [**Download Asset Pack (One Drive / S3 Link TBD)**](https://example.com/placeholder)

**Hydration Steps:**

1. Download the ZIP archive from the link above.
2. **Extract contents** directly into the `assets/_external/` folder.
    - *Note: This folder already exists (anchored by `.gitkeep`), so you simply unzip into it.*
    - *Result:* Your local path should look like `assets/_external/usd/my_asset.usd`.

## 🛠️ Setup & Installation

1. **Clone:** `git clone https://github.com/MSP014/dt-openusd-showcase-case04-af.git`
2. **Hydration:** (See "Asset Hydration" above) - Extract assets to `assets/_external/`.
3. **Env:** Create the Python 3.11.15 environment and install locked dependencies: `conda env create -f environment.yml`
4. **Activate:** `conda activate case04-env`
5. **Hooks:** `python -m pre_commit install`

## 📜 Development Baseline and Planned Runtime

- **Python**: 3.11.15 (standalone project environment)
- **USD**: 23.11+
- **Standalone OpenUSD bindings**: `usd-core==26.11` (`pxr`, Python 3.11)
- **Houdini FX**: 22.0.466
- **NVIDIA Omniverse / Isaac Sim**: 5.1.0 planned presentation/runtime baseline; logistics integration pending
- **NVIDIA cuOpt**: Planned VRP solver evaluation; interface, integration and benchmark results pending

---

## 📜 Changelog

- **Week of Oct 26, 2026:** Adopted Python 3.11.15 with a reproducible Conda environment, refreshed hash-locked dependencies, enabled standalone OpenUSD inspection and aligned validation tooling.
- **Week of May 25, 2026:** Resolved the dependency-tooling security lock by updating pip-tools and refreshing the pinned dependencies and validation commands.
- **Week of Mar 2, 2026:** Designed and implemented a comprehensive 5-part USD Architecture Knowledge Base tailored for logistics and VRP simulation (`docs/knowledge_base/usd_architecture/`), while standardizing repository folder casing (`snake_case`) and resolving pre-commit hook bugs.
- **Week of Jan 26, 2026:** Automated Jira plan synchronization to generate live `case 04 - implementation.md` Markdown plans, refactored sync scripts for strict linting compliance, and established external storage protocols for heavy assets.
- **Week of Jan 19, 2026:** Bootstrapped the initial Readme-driven public repository with complete Tech Pack structure, strict dependency management (pip-tools), and fully configured pre-commit workflows.
