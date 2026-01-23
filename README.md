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

This project follows the **Showreel Protocol** to manage the complexity of hybrid pipelines.

- [**View Architecture Decision Records (ADR)**](docs/adr/) – Design notes on Naming Conventions, Security Guardrails, and VRP Logic integration.

## 📂 Repository Structure

```text
.
├── docs/        # ADRs and knowledge base
├── plans/       # Implementation plans & tech debt
├── src/         # Core logic and scripts
├── tests/       # Validation and testing suite
└── tools/       # Internal pipeline utilities
```

---

## 💾 Project Data / Assets

To keep this repository lightweight (GitHub < 1GB), heavy binary assets are stored externally.

- [**Download Heavy Assets (One Drive / S3 Link TBD)**](https://example.com/placeholder)
  - Includes: `*.usd` crates, `*.hip` source files, and high-res textures.

## 🛠️ Setup & Installation

1. **Clone:** `git clone https://github.com/MSP014/dt-omniverse-showreel-case04-af.git`
2. **Env:** Create conda env: `conda create -n case04-env python=3.10`
3. **Deps:** `pip install -r requirements.txt`
4. **Hooks:** `pre-commit install`

---

## 📜 Changelog

- **2026-01-23:** Initial repository bootstrap. Established **Showreel Protocol** (ADRs, Pre-commit, Hybrid Access).
