# ADR 002: QA & Security Protocols

## Status

Accepted

## Context

Case 04 (Air Field) involves complex logistics logic (VRP) that could break the showreel sequence if improperly automated. We must also safeguard any airfield layout data or fleet management credentials.

## Decision

We enforce a strict "Shift Left" strategy using `pre-commit` hooks and automated guardrails:

### 1. Guardrails (Pre-commit)

* **Linting:** `black` (Formatting), `flake8` (Logic), `isort` (Imports).
* **Security:**
  * `bandit`: Scans for common security issues (e.g., hardcoded passwords, unsafe exec).
  * `pip-audit`: Checks `requirements.txt` for known vulnerabilities.
* **Hygiene:** `check-added-large-files` (Max 10MB) to prevent repo bloating.

### 2. Testing

* `pytest` must pass locally before push.
* **Domain Validation:** Tests MUST verify logistics logic (e.g., VRP solver output validation, routing schema checks).
* **Note:** Tests are covered by Linters (Black/Flake8) but excluded from Bandit security scans to avoid false positives related to `assert` usage.

### 3. Secrets Management

* **Isolation**: All sensitive data (fleet API keys, credentials) MUST be stored in a `.env` file.
* **Sanity**: The `.env` file MUST be included in `.gitignore`. Never commit secrets to version control.

## Consequences

* **Positive:** Higher code quality, reduced security risk, blocked accidental large files.
* **Negative:** Initial setup friction; `pip-audit` requires maintenance of `requirements.txt`.
