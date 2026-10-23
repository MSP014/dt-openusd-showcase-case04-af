# Case 04: Air Field (Aviation Logistics Digital Twin)

> [!WARNING]
> **Work in Progress:** This project is currently under active development. Some links and assets may be placeholders.

---

> **Role:** L2 Digital Twin (Optimization & Decision Support)
> **Stack:** SideFX Houdini, NVIDIA Omniverse, NVIDIA cuOpt (VRP Solver)

---

## 📋 Project Overview

This repository showcases a prototype **Aviation Logistics Digital Twin (Level L2)** demonstrating algorithmic optimisation and decision support for airfield ground operations. The case study centres on refuelling logistics at an active apron, visualising optimised routing and resource allocation in real-time.

**Key Use Case:**
The digital twin calculates and visualises the **optimal refuelling sequence and tanker routing** based on flight priorities (departure times). Using NVIDIA cuOpt's Vehicle Routing Problem (VRP) solver, the system generates efficient paths that minimise tanker travel time whilst respecting aircraft schedules. HUD overlays display aircraft fuel status, queue positions, and tanker assignments. This exemplifies how L2 Digital Twins move beyond passive monitoring to provide actionable operational intelligence.

**Project Focus:**

- **Optimisation Logic (VRP):** NVIDIA cuOpt integration for real-time route calculation
- **Multi-Agent Visualisation:** Coordinated state management for aircraft (Waiting/Refuelling) and tankers (Routing/Pumping)
- **Decision Support:** Clear visual representation of system recommendations and resource allocation

---

## 🎯 Technical Highlights

*This setup demonstrates an **L2 Digital Twin (Optimization)**, moving beyond visualization to algorithmic decision support.*

- **Logistics Logic (VRP):** Visualizing the "Vehicle Routing Problem" (Traveling Salesman) for airfield tankers using **NVIDIA cuOpt** logic.
- **Houdini -> USD Pipeline:** Procedural generation of apron markings and ground support equipment.
- **Multi-Agent State:** Managing state visualization for Planes (Waiting/Refueling) and Tankers (Routing/Pumping).

## 👁️ Visual Proof

> *Placeholders for future GIFs*

1. **Optimal Route Visualization:** `![VRP Graph](docs/img/vrp_graph_placeholder.gif)`
2. **Refueling FUI:** `![Refueling HUD](docs/img/refueling_hud_placeholder.png)`
3. **Apron Layering:** `![USD Layers](docs/img/usd_composition_placeholder.png)`

## 🏗️ Architecture & Decisions

This project follows a **README-driven structure** to manage the complexity of hybrid Houdini/Omniverse pipelines.

- [**View Architecture Decision Records (ADR)**](docs/adr/) – Design notes on Naming Conventions, Security Guardrails, and VRP Logic integration.

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
├── src/              # Core logic and scripts
├── tests/            # Validation and testing suite
└── tools/            # Internal pipeline utilities
```

---

## 💾 Project Data / Assets

### 🏭 The "Factory" Narrative
>
> This repository follows a strict **"Source vs. Artifact"** philosophy:
>
> - **Houdini (Fabricator):** The procedural "factory" where assets are generated. Source files (`.hip`) are proprietary and **excluded** from this repository.
> - **USD (Artifact):** The "product" of the factory. These are the optimized files needed to run the Digital Twin in Omniverse.
> - **Synthetic Data:** Telemetry streams are emulated via Python generators to simulate robust edge cases (e.g., extreme thermal loads) that are rarely captured in real-world data.

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

## 📜 Technical Stack

- **Python**: 3.11.15 (standalone project environment)
- **USD**: 23.11+
- **Standalone OpenUSD bindings**: `usd-core==26.11` (`pxr`, Python 3.11)
- **Houdini FX**: 22.0.466
- **NVIDIA Omniverse / Isaac Sim**: 5.1.0
- **NVIDIA cuOpt**: VRP Solver (Route optimisation)

---

## 📜 Changelog

- **Week of May 25, 2026:** Resolved the dependency-tooling security lock by updating pip-tools and refreshing the pinned dependencies and validation commands.
- **Week of Mar 2, 2026:** Designed and implemented a comprehensive 5-part USD Architecture Knowledge Base tailored for logistics and VRP simulation (`docs/knowledge_base/usd_architecture/`), while standardizing repository folder casing (`snake_case`) and resolving pre-commit hook bugs.
- **Week of Jan 26, 2026:** Automated Jira plan synchronization to generate live `case 04 - implementation.md` Markdown plans, refactored sync scripts for strict linting compliance, and established external storage protocols for heavy assets.
- **Week of Jan 19, 2026:** Bootstrapped the initial Readme-driven public repository with complete Tech Pack structure, strict dependency management (pip-tools), and fully configured pre-commit workflows.
