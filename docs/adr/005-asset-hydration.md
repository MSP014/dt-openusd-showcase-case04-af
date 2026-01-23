# ADR 005: Asset Life Cycle & Hydration

## Status

Proposed

## Context

The Air Field Logistics project (Case 04) involves VRP data assets and environment scans. These heavy files must be managed outside the main Git repository while remaining instantly accessible for deployment.

## Decision

We will implement a **Hydration Protocol** to reconstruct the operational environment:

1. **External Storage**: All non-textual assets (USD crates, environment data, textures) will be hosted on high-availability external storage.
2. **Directory Mapping**: The external archive structure must mirror the local directory skeleton (`/assets`, `/geo`, `/cache`, `/HIP`).
3. **Relative Path Integrity**: Every USD reference and Python script path MUST use relative paths. Absolute paths (e.g., `C:/Users/...`) are strictly forbidden to ensure machine-agnostic portability.
4. **Automatic Provisioning (Future)**: A `bootstrap.py` utility in `/tools` will be developed to automate directory creation and asset synchronisation.

## Consequences

* **Positive**: Fast repository cloning, professional handling of heavy binary data, and guaranteed portability for the showreel presentation.
* **Negative**: Requires an additional manual or scripted "hydration" step after cloning.
