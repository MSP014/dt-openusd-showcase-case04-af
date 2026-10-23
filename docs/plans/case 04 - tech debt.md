# Case 04 Technical Debt

## Unresolved Technical Debts

_No open technical debts at the moment._

## Resolved Technical Debts

### [ENVIRONMENT] Install OpenUSD Python Bindings (`pxr`)

- **Status:** Resolved (2026-10-23)
- **Severity:** Medium (Tooling)
- **Reason:** Standalone USD diagnostics require the `pxr` modules in the project
  environment to inspect stages, variants, primvars, bindings and payloads.
- **Resolution:** Added `usd-core` to `requirements.in`, pinned `usd-core==26.11`
  with its Windows/Python 3.11 wheel hash in `requirements.txt`, and installed it
  in `case04-env` under Python 3.11.15.
- **Verification:** Imported `Usd`, `UsdGeom`, `Sdf`, `UsdShade`, `Gf` and `Vt`;
  created a Y-up, metre-scale, 24 fps stage; authored a typed schema and telemetry
  primvar; and verified a USDA serialisation/readback round trip. `pip check`
  reported no broken requirements. `Usd.GetVersion()` returned `(0, 26, 11)`.
- **Smoke Test:** `conda run -n case04-env python -c "from pxr import Usd, UsdGeom, Sdf; print('pxr ok', Usd.GetVersion())"`
- **Boundary:** These bindings serve standalone inspection. Houdini and Kit use
  their own embedded USD builds and binary compatibility requirements.

### [SECURITY] Pip Security Lock & Dependencies Conflict

- **Status:** Resolved (2026-05-21)
- **Severity:** High
- **Description:** Environment previously used `pip 25.3` due to historic compatibility issues between `pip 26.0+` and older `pip-tools`.
- **Resolution:** Upgraded tooling in `case04-env` and validated lock generation workflow.
  - `pip` -> `26.1.1`
  - `pip-tools` -> `7.5.3`
  - Lock refresh command: `conda run -n case04-env pip-compile --upgrade requirements.in`
  - Result: `requirements.txt` pins `pip-tools==7.5.3`.
- **Verification Date:** 2026-05-21
