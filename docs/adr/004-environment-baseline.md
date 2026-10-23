# ADR 004: Environment Baseline

## Status

Accepted

## Context

Python versions affect dependency resolution and compiled wheels. Case 04 needs
an explicit baseline for its standalone project tools and numerical libraries.

## Decision

We anchor all Case 04 environments on a specific Python baseline:

### 1. Python Version

* **Baseline**: **Python 3.11.15**, adopted on 23 October 2026.
* **Environment**: `case04-env`, declared in `environment.yml`.
* **Rationale**: Establish the requested Python 3.11 baseline before substantial
  application code is developed, and resolve dependencies under that interpreter.
* **Boundary**: This decision covers standalone project Python. Houdini and Kit
  own their embedded interpreters and binary compatibility; changing Conda does
  not replace those runtimes or establish cross-runtime ABI compatibility.

### 2. Base Configuration

* **Package Manager**: `conda` (Miniconda/Anaconda) for environment creation.
* **Installer**: The environment's `python -m pip` for locked packages.
* **Reproduction**: `conda env create -f environment.yml` creates the interpreter
  and installs `requirements.txt`.
* **Dependency maintenance**: Follow ADR 003 and regenerate the lock using
  `conda run -n case04-env python -m piptools compile --upgrade --allow-unsafe --generate-hashes --reuse-hashes requirements.in`.
  Install the result using the same interpreter and `--require-hashes`.
* **Migration**: Replace compiled dependencies with wheels for Python 3.11;
  retaining Python 3.10 wheels is not a valid environment migration.

## Consequences

* **Positive**: New environments select the exact project interpreter and locked
  dependencies without relying on the system Python default.
* **Negative**: Interpreter upgrades require a fresh dependency resolution,
  compatible binary wheels and validation. The lock is resolved for this Python
  version and the current Windows platform; other platforms require validation.
