# ADR 001: Naming Convention & Standards

## Status

Accepted

## Context

Case 04 (Air Field) involves large-scale logistics automation. To prevent "where is that file?" fatigue in a hybrid Houdini/Omniverse pipeline, we need a standardised grammar for assets, telemetry, and documentation.

## Decision

We will enforce the following naming and operational rules:

### 1. Repository Naming

Format: `dt-omniverse-showreel-case##-[key]`

* **Example:** `dt-openusd-showcase-case04-af` (this repo)
* **dt**: Digital Twin
* **omniverse**: Platform
* **showreel**: Project type
* **case04**: Sequence ID
* **af**: Project Key (Air Field)

### 2. Folders and File Layers (Snake Case)

* **Directories:** All directories MUST be written in `snake_case` (e.g., `refs/`, `assets/`, `audio_tracks/`).
* `mesh_*` (Geometry, e.g., `mesh_rack_A01`)

* `mesh_*` (Geometry, e.g., `mesh_tanker_truck`)
* `mat_*` (Materials / Shaders)
* `light_*` (Lighting setups)
* `sim_*` (Simulation caches / VRP data)

### 3. USD Suffixes

* `.usda`: ASCII (Human-readable, git-friendly). Use for composition arcs and root layers.
* `.usdc`: Binary (Performance). Use for heavy geometry/caches. **GITIGNORE this extension.**

### 4. Language Standards

* **Documentation**: All technical documentation and comments MUST be in **British English** (en-GB). Use `s` instead of `z` (e.g., *optimise*, *standardise*).
* **Commit Messages**: British English, imperative mood (e.g., "Add feature", not "Added feature").

### 5. Git Workflow

* **Branches**: `feature/description-of-change` or `fix/issue-id`.
* **Tags**: Use semantic versioning for milestones (e.g., `v1.0.0-gold`).

## Consequences

* **Positive:** Predictable file system, clear separation of binary vs text data, and consistent international documentation code.
* **Negative:** Requires discipline to rename existing assets during import.
