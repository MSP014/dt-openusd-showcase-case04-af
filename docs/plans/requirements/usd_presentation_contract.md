# Fuel Logistics — USD Presentation Contract

**Status:** Working draft; one-way projection, vehicle/aircraft labels and fuel
statistics panel agreed in discussion; schema v1.0 compatibility/bindings unresolved

**Last updated:** 31 October 2026

**Primary decisions:** D-002, D-028, D-029

**Review question:** Q-10; dependencies Q-01, Q-05, Q-06, Q-08, Q-13

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
| `primvars:logistics:fuelCurrent` | Current payload for both aircraft and tanker | Distinguish aircraft aviation fuel from the refueller's aviation fuel, tractor diesel and pump petrol; one shared value cannot represent all balances |
| `primvars:logistics:priority` | Integer `1` highest to `5` lowest | Define mapping from internal score/policy; retain the existing direction unless explicitly revised |

The source notes recognise that the current coarse fields cannot fully express
all lifecycle/resource distinctions. They do not agree new field names, types,
enum mappings, units or a new schema version. Finalise domain and fuel semantics
before approving the projection.

D-021 requires the separate pump-petrol quantity to remain visible and traceable
alongside the other stocks. `Loading` now covers timed depot operations and
internal movement under D-018–D-020; it does not mean the vehicle is instantly
full or stationary. Decide which current post/operation/location data should be
shown without treating those details as new tanker enum states. No new primvar
name or schema version is approved by these requirements updates.

D-022–D-025 add waiting-arrival, direct reassignment and proactive depot-return
transitions within the tanker state model. Display routes from actual
positions and distinguish anticipated demand/relief planning from assigned
service. Waiting reassessment must not display an unaccepted plan as executed.
Under D-026 distinguish the replacement travelling from depot to field parking,
its actual parking arrival, and subsequent aircraft dispatch. A replacement
dispatch does not itself establish physical field coverage. Decide how coverage
and handover evidence are projected without creating a second state authority.
Under D-027 both refuellers may appear working on the field. Do not infer that
one must be at the depot from a role label. Distinguish a rejected candidate
assignment, its reviewed feasible alternative and an actual selected depot
return; fuel infeasibility for one request is not itself a return command.
Whether dwell time, next check window and the reason for leaving a waiting
location are exposed requires a projection decision; no new primvar is implied.

## Identity, display and authoring responsibilities

Logical entity IDs must be associated with USD prim paths/assets by a binding
layer. Solver inputs must remain independent of mesh names and prim paths.
Define the minimum asset/profile binding metadata and behaviour when an ID or
prim cannot be resolved; the exact binding file/schema is still proposed.

The presentation needs to distinguish current state, a proposed plan and an
accepted plan. Acceptance may follow the automatic execution policy or an
operator decision, depending on the operating-mode contract still open under
Q-13; do not infer manual confirmation of every dispatch. Routes, assignment
labels, fuel/status displays and summaries should describe the same corresponding
state/plan result. How versions are
identified, stale proposals are labelled and failed projections are handled
remains to be reviewed rather than guessed.

Decide which layer receives runtime opinions, which attributes carry geometry
transforms/route curves versus telemetry, and when updates are authored. A
session-layer versus asset-layer policy, update cadence and reset behaviour are
open implementation contracts. Do not infer renderer/shader availability merely
from the presence of a primvar; validate the intended display path later.

D-032 adds Alert (working display **Alert!**); D-038 reserves Refueling for
aircraft and uses separate **Alert Refueling** for tractor-diesel transfer.
Display the affected refueller, reason and assistance progress/role consistently;
do not show an Alert recipient as available or a diesel donation as aircraft
aviation-fuel delivery. Donor and recipient Alert Refueling states refer to one
assistance operation. Select projection fields and alert acknowledgement/clearing
under Q-10; no USD schema version or concrete attribute names are approved here.

## Vehicle and aircraft visual labels (D-028)

D-031/D-035 Maintenance/fault handling requires the presentation to retain
the failed vehicle's actual location and technical unavailability, rather than
showing an implicit completed depot return. A detected technical fault stops
service under D-035; display partial delivery and remaining work consistently with the
reviewed request/aircraft lifecycle and D-029 totals; no new fault label, state
enum, primvar or repair animation is approved here.

**D-028 — agreed in discussion:** Omniverse must display compact, object-anchored
labels/icons above each refueller tractor, its semi-trailer tank and each
aircraft. They follow their corresponding objects as these move. These are
planned visual requirements, not implemented widgets or approved USD field names.

| Anchor | Required information |
| --- | --- |
| Above each TZ-22 tractor | Refueller name/identifier, current refueller state and a small progress bar showing diesel remaining in the tractor tank |
| Above its semi-trailer tank | Clearly associated refueller identity and two distinct small progress bars: aviation fuel in the tank and petrol for the separate pump engine |
| Above each aircraft | Flight name/identifier, departure date and time, current aircraft state and a small progress bar showing onboard aviation fuel |

Tractor and semi-trailer labels belong to the same logical refueller. Keep them
associated even when their anchors move independently; do not invent a separate
semi-trailer lifecycle. The aircraft label uses its aircraft state rather than
the fuel-request state. Departure date/time reflects the current scheduled
departure (including accepted schedule changes); choose an explicit display
time zone/format under Q-02/Q-10 rather than showing an ambiguous clock value.

Each fuel bar represents the current stock relative to the corresponding tank's
adopted capacity. Distinguish **Tractor Diesel**, **Aviation Fuel** and **Pump
Petrol** by readable labels; they are separate D-021 stocks. Aircraft onboard
fuel is a fourth balance. Bar fill represents tank level, not pumping progress,
requested uplift or proof that a service/departure is feasible. Units/capacities
follow Q-06; missing stock/capacity remains visibly unknown, not an invented
empty/full bar.

### Zoom-dependent refueller label grouping

When zoomed too far out for the separate tractor and tank labels to remain
readable, they **may collapse into one combined refueller label**. Preserve the
refueller name/current state and distinguishable indications of all three fuel
stocks; never sum diesel, aviation fuel and petrol into a single fuel level.
When zooming back in, restore the separate labels. Grouping is a presentation
change only: it does not merge logical entities, stocks or state data.

The two refuellers must remain individually identifiable, including when both
are on the field under D-027. Aircraft retain their own labels; this permission
does not combine aircraft and refuellers or the fleet into one label.
Exact label layout, visual styling, anchor offsets, zoom/readability thresholds
and overlap/occlusion behaviour remain Q-10 presentation details. Object
bindings, fuel bars, status and schedule values must use the same authoritative
current state and the existing projection direction.

## Fuel statistics panel (D-029)

**D-029 — agreed in discussion:** The digital-twin interface must include a
fuel-statistics panel with three separately identified quantities:

| Indicator | Required meaning |
| --- | --- |
| Issued during the reporting period | Aviation fuel actually transferred to aircraft within the specified period, rather than planned delivery or a depot reload |
| Currently in progress | Fuel quantity associated with aircraft-refuelling work currently being carried out; the precise quantity basis remains to be agreed |
| Awaiting issue against existing requests | Outstanding aviation-fuel demand from existing requests awaiting service; future flights without a request do not themselves add issued/pending fuel |

These are fleet-wide figures across both refuellers, including concurrent field
operation. Keep aircraft fuel delivery separate from tractor diesel and pump
petrol consumption. Data comes from authoritative fuel accounting and request
state through the presentation contract, not an independent UI ledger.

**Metric definitions to finish before implementation (Q-02/Q-05/Q-06/Q-10):**
Define the reporting-period boundaries/time zone and displayed units; decide
whether the in-progress figure means full assigned quantity or remaining
undelivered quantity for active work. Map active versus awaiting work to the
reviewed request lifecycle, including partial delivery, completion, cancellation
and reassignment. Prevent duplicate counting of a request across vehicles or
active/awaiting groups; specify treatment of already delivered portions before
combining or comparing quantities. Current workload figures are snapshots;
the issued figure covers a time interval and must be labelled accordingly.

Panel layout, styling and the wider UI design will be developed **later**.
This agreement records the information requirement without selecting a widget
layout, adding controls, choosing USD field names or implementing a dashboard.

## Cancellation and committed-service presentation

D-036 aircraft Alert indicates abnormal service interruption while retaining
actual fuel and remaining need; show resumption only on actual replacement
service start. It does not indicate an aircraft mechanical fault. D-037 Ready
is committed departure preparation in this demonstration: no cancellation or
readiness reversal is generated after it. Changed/new-flight labels and fuel
checks before Ready use the current flight's open check window and retained fuel;
completion of an older delivery cannot grant early Ready. Execution also rejects
operator commands that would revoke Ready, cancel or replace that flight.
D-038's Refueling
and Alert Refueling must be visually distinguishable; layout/schema decisions
remain later work. Show the eight D-006 aircraft states, including Departing
for actual movement/take-off and Departed only after airborne zone exit.

D-033/D-034 distinguish cancellation before service from a withdrawn/cancelled
flight with a still-committed delivery. While aircraft Refueling is running, disable
manual request cancellation and explain that service will finish under its
original conditions; a late execution command must also be rejected. Reflect
request revisions/cancellation and the current executable route consistently.
Under D-039, flight cancellation in Alert before replacement arrival instead
shows Parked with no active flight, retains onboard fuel and actual issued
history, and removes the old undelivered remainder from pending work. Do not
show that remainder as completed or leave a cancelled replacement route current.
The failed refueller remains unavailable under its existing lifecycle.

For a flight cancelled during service, show the ongoing original delivery and
actual stock changes, then the fuelled Parked aircraft with no active departure.
Do not show it departing or display the cancelled flight's old time as a current
scheduled departure. A new flight is associated with the same physical aircraft
and its retained onboard fuel. D-029 issued totals retain all actual delivery;
before-service cancellation removes pending demand, while an active committed
delivery remains in current work until completed or D-035 technical withdrawal.
Detailed metrics still follow D-029's reviewed quantity definitions. UI layout
and concrete fields remain Q-10; this adds no implementation or schema version.

## Departure warning and time presentation (D-040)

Show Departure Delay Risk separately from aircraft state in Parked, Refueling
and Alert. A tanker failure must not clear an active flight's delay warning.
Explain the limiting service/replacement time and distinguish the agreed ETD,
predicted departure, and actual stand-departure/take-off times. A prediction or
an unaccepted proposed time must not replace the scheduled label.

Expose Keep Schedule, Accept Proposed Time and Set Time Manually according to
the [operator contract](planning_and_operator_workflow.md#departure-delay-risk-and-schedule-authority-d-040).
Keeping an infeasible schedule retains the warning; accepting or entering a
time requires operator confirmation and current-state validation. After Ready,
flight-changing controls are unavailable and equivalent execution commands are
rejected. A late-ready aircraft continues actual departure without backdating
movement or silently changing ETD. These are information/behaviour requirements,
not an approved widget layout or new USD field schema.

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
  Include D-028's tractor/tank/aircraft label anchors, name/state/date-time
  fields, independent stock/capacity bars, combined distant refueller labels,
  display time zone/format and readable layout/zoom/occlusion rules.
  Complete D-029's issued/in-progress/awaiting-request metric definitions,
  reporting period and accounting/request bindings; retain later UI design.
- Reconcile v1.0 dispatch reads, shared status strings, fuel-field ambiguities
  and internal-score mapping. Choose compatibility/migration/versioning policy
  only after domain and resource semantics are reviewed.
- Define runtime authoring layer, missing-prim handling, update/reset behaviour
  and how proposal/accepted/current views remain consistent.

After agreement, update the existing USD documents and their verification
criteria together. A standalone serialisation check and later Kit/viewport
checks can verify different parts of the adopted contract; neither is claimed
to have validated this proposed runtime workflow.
