# ADR 003: Dependency Locking & Isolation

## Status

Accepted

## Context

Logistics automation and VRP solving scripts require absolute version consistency. To ensure that Arthur's pathfinding logic produces the same results in every simulation run, the environment must be deterministic.

## Decision

We enforce a **"One Env Per Case"** policy with strict locking:

### 1. Isolation

* **Environment Name:** `case04-env` (or `af-env`)
* **NEVER** install packages into the global Python or base Conda environment.

### 2. Dependency Locking

We use `pip-tools` for deterministic builds.

* **Source of Truth:** `requirements.in` (High-level deps, e.g., `requests`, `numpy`, `networkx`).
* **Lockfile:** `requirements.txt` (Generated via `pip-compile`). Contains exact versions + hashes.
* **Workflow:**
    1. Edit `requirements.in`.
    2. In `case04-env` (Python 3.11.15), run
       `python -m piptools compile --allow-unsafe --generate-hashes --reuse-hashes requirements.in`.
       Add `--upgrade` when intentionally refreshing package versions.
       `--allow-unsafe` also locks the installer dependencies required by
       `pip-tools`, allowing hash-checked installation into a clean environment.
    3. Install with `python -m pip install --require-hashes -r requirements.txt`.
    4. Commit both files when the dependency change is ready for publication.

## Consequences

* **Positive:** 100% reproducibility. `pip-audit` works effectively.
* **Negative:** Extra step (`pip-compile`) when adding libraries.
