# Fuel Logistics — USD Presentation Contract

**Status:** Working draft; one-way projection agreed in discussion; schema v1.0
compatibility and binding/migration decisions unresolved

**Last updated:** 29 October 2026

**Primary decision:** D-002

**Review question:** Q-10; dependencies Q-01, Q-05, Q-06, Q-08

This draft records intended changes beside the existing
[USD master contract](../../knowledge_base/usd_architecture/00_project_usd_contract.md)
and [telemetry/VRP schema v1.0](../../knowledge_base/usd_architecture/04_telemetry_and_vrp_attributes.md).
It does not update either document or approve a new schema version.

## Ownership and direction

**D-002 — agreed in discussion:** Runtime Python state is projected into USD for
presentation. Calculation logic does not read its authoritative current state
back from USD primvars. Visual components may read primvars for display.

```text
Synthetic input/events -> WorldState -> USD Presentation Adapter
                                      -> USD primvars / scene state
                                      -> Omniverse presentation
```

The projection may include primvars and other appropriate scene attributes.
UI commands are a separate input to the calculation/execution layer, not an
instruction to edit a telemetry primvar and read it back as authoritative state.
The exact command transport remains open in [planning](planning_and_operator_workflow.md#operator-acceptance-and-unresolved-result-handling).

**Existing-contract conflict:** The v1.0 guideline describes dispatch scripts
reading primvars to calculate routes, including that behaviour in its Definition
of Done. This conflicts with D-002's direction. Approval requires a deliberate
contract/migration decision; do not silently maintain two state authorities.

## Schema compatibility

| Existing v1.0 field | Existing meaning | Required review |
| --- | --- | --- |
| `primvars:telemetry:schemaVersion` | String `"1.0"` | Decide versioning and migration for the reviewed projection; no new value selected |
| `primvars:vehicle:id` | Logical identifier; examples include tanker and flight IDs | Define uniqueness, flight versus aircraft identity and mapping to prim paths |
| `primvars:logistics:status` | Shared strings `Idle`, `Transit`, `Refueling`, `Maintenance` | Map or replace with separately defined aircraft/request/tanker representations |
| `primvars:logistics:fuelNeeded` | Aircraft fuel needed for departure; litre-valued example | Clarify total required departure stock versus remaining uplift; do not silently repurpose |
| `primvars:logistics:fuelCurrent` | Current payload for both aircraft and tanker | Distinguish onboard aviation fuel, tanker aviation fuel and tractor diesel |
| `primvars:logistics:priority` | Integer `1` highest to `5` lowest | Define mapping from internal score/policy; retain the existing direction unless explicitly revised |

The source notes recognise that the current coarse fields cannot fully express
all lifecycle/resource distinctions. They do not agree new field names, types,
enum mappings, units or a new schema version. Finalise domain and fuel semantics
before approving the projection.

## Identity, display and authoring responsibilities

Logical entity IDs must be associated with USD prim paths/assets by a binding
layer. Solver inputs must remain independent of mesh names and prim paths.
Define the minimum asset/profile binding metadata and behaviour when an ID or
prim cannot be resolved; the exact binding file/schema is still proposed.

The presentation needs to distinguish current state, a proposed plan and an
accepted plan. Routes, assignment labels, fuel/status displays and summaries
should describe the same corresponding state/plan result. How versions are
identified, stale proposals are labelled and failed projections are handled
remains to be reviewed rather than guessed.

Decide which layer receives runtime opinions, which attributes carry geometry
transforms/route curves versus telemetry, and when updates are authored. A
session-layer versus asset-layer policy, update cadence and reset behaviour are
open implementation contracts. Do not infer renderer/shader availability merely
from the presence of a primvar; validate the intended display path later.

## Preserved scene fundamentals

The current project contract remains unchanged:

- Metres per unit `1.0`, Y-up and `timeCodesPerSecond = 24.0`.
- `/World/Stage/` for static environment; `/World/Factory/` for dynamic objects;
  `/__Class__/` or `/World/Prototypes/` for prototypes.
- Existing `LOD` naming, geometry-purpose and instancing rules continue to apply.

Scene time codes do not settle the simulation clock or replay contract. The
standalone inspection bindings do not establish Kit/Houdini runtime compatibility
or a functioning presentation adapter.

## Open questions and next steps

- **Q-10:** Agree entity-ID/prim bindings and a field-by-field projection table:
  source field, units/type, display meaning, target attribute and update owner.
- Reconcile v1.0 dispatch reads, shared status strings, fuel-field ambiguities
  and internal-score mapping. Choose compatibility/migration/versioning policy
  only after domain and resource semantics are reviewed.
- Define runtime authoring layer, missing-prim handling, update/reset behaviour
  and how proposal/accepted/current views remain consistent.

After agreement, update the existing USD documents and their verification
criteria together. A standalone serialisation check and later Kit/viewport
checks can verify different parts of the adopted contract; neither is claimed
to have validated this proposed runtime workflow.
