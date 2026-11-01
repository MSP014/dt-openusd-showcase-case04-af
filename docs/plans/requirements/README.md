# Case 04 — Fuel Logistics Requirements

**Status:** Working drafts; requirements review in progress

**Last updated:** 1 November 2026 (Asia/Yerevan)

**Source discussions:** 28 October–1 November 2026

**Implementation status:** This collection describes intended behaviour. It does
not establish a working provider, fuel model, priority engine, cuOpt integration
or Omniverse operator workflow.

## Purpose and scope

Develop a **Synthetic-data-driven L2-style Decision-Support Prototype** for airfield
refuelling. The demonstration should make a schedule change, its consequences
and a feasible proposed service sequence visible to an operator. Houdini prepares
assets and data; Omniverse presents the scene and operational state; cuOpt is the
planned routing solver. Automatic dispatch is a required capability. The
operating-mode contract must distinguish autonomous simulated dispatch,
operator-confirmed planning and intervention; universal manual confirmation is
not an agreed requirement (Q-13).

**1 November runtime direction:** D-046 targets a standalone logistics host
integrating selected Omniverse libraries, complementing Case 03's Kit-based
application evidence. The host owns state, scheduling and lifecycle; `ovstage`
and `ovrtx` are initial scene/rendering candidates. Final stack adoption depends
on a project-asset prototype, platform compatibility and viewport/UI assessment
(Q-15). See the [primary decision](architecture.md#standalone-omniverse-library-direction-d-046).
No library integration or change to the Python baseline is claimed.

**Point 0 — input-source seam (D-047):** The Synthetic Data Provider supplies
the implemented scenario inputs. Reserve a common input contract and
interfaces/placeholders for future real-data adapters; actual airport-system
bridges are outside scope. Aircraft/refueller positioning imitates GPS using
synthetic coordinates tied to the same airfield/infrastructure frame, not
recorded or live GPS data. See the [primary decision](architecture.md#input-sources-and-synthetic-position-telemetry-d-047)
and the [C4 module-map draft](../../architecture/case04_c4_architecture.md).

The scope covers aircraft fuel requests, refueller movements and replenishment
at one aviation-fuel depot. It excludes baggage, catering, passenger handling,
aircraft towing, landing clearances and wider airport operations. A diversion is
an input event, not a claim to manage landing traffic or aviation emergencies.
Synthetic telemetry does not establish a live airport integration.

**29 October clarification:** The demonstration schedule is three aircraft on
stands, two inbound flights and a possible sixth unscheduled arrival, served by
two identical refuellers in active/standby duty rotation (D-016). D-027 permits
both to work on the field concurrently; one-at-depot is a default arrangement,
not a permanent restriction. A one-refueller,
three-aircraft fixture may help isolate a check but does not replace that
demonstration scope. Compare EDF with cuOpt on deadline performance and total
refueller route/diesel cost; an advantage has not yet been demonstrated.

**Required fleet and fault tolerance:** Two refuellers are mandatory for the
demonstration. D-026 requires at least one operationally ready refueller
physically on the airfield during normal covered operation. Sending the active
vehicle to replenish requires dispatching the full depot replacement to field
parking without waiting for an aircraft request/check window; only subsequent
aircraft service waits for eligibility and all guards. Coordinate actual travel
and handover so coverage does not drop to zero. Normal fuel checks must preserve enough tractor diesel for a
protected return to the depot. If a vehicle nevertheless cannot reach it, the
second must supply enough diesel from its own tractor tank to restore that
return, while retaining its own feasible return reserve (D-017). The assistance
mechanism and exception handling still need detailed requirements.

**Depot and third-stock clarification:** D-018–D-021 retain two exclusive posts,
separate diesel/petrol dispensers and the agreed first-to-aviation/second-to-auxiliary
simultaneous-arrival order, followed by the second's aviation visit after the
first completes required replenishment and departs or proceeds to parking.
D-043 supersedes Loading with RefillTank/RefillServiceFuel and internal
Repositioning. No-task reserve parking ends in Standby with technical readiness
and all three stocks at 100%; field waiting ends in Idle. Direct departure for
a feasible assignment may use partial stocks without a Standby stop.

**31 October profile and lifecycle update:** D-041 fixes normal request
completion and retained history; D-042 adopts numerical refueller parameters
with KrAZ-221 visuals and a KrAZ-258B1 operational profile; D-043 records explicit
depot/Standby states; D-044 selects tyre/pump/petrol-exhaustion cases and excludes
service-time diesel exhaustion; D-045 bounds optimisation to one or two known
requests without hiding the full queue or revealing unknown future events.
Internal depot travel takes time but consumes no diesel by deliberate simplification.

Synthetic inputs are a deliberate trade-off while operational airport data is
unavailable. They enable reproducible model/pipeline validation without claiming
real-airport calibration; see the [classification note](../../knowledge_base/digital_twin_maturity_levels.md).

Period visual replicas and contemporary planning concepts are separate from
configurable equipment capabilities. Preserve the cinematic presentation and
asset reuse while making constraints, assumptions and results inspectable.

## Document status and next steps

All documents below remain drafts. Their individual statements retain the
decision statuses recorded in the source notes; restructuring is not approval.

| Document | Current state | Next requirements step | Depends on |
| --- | --- | --- | --- |
| This README | Decisions through D-047 and current continuation recorded | Keep register/status/checkpoint aligned | All topic documents |
| [C4 architecture and module map](../../architecture/case04_c4_architecture.md) | Proposed application boundaries, internal modules and information flow drawn | Review the module map before deriving implementation dependencies/order | D-001/D-046/D-047 and the topic contracts |
| [Persistence and data storage](persistence_and_storage.md) | Core/telemetry/analytics/settings databases proposed; primary settings DB and critical bootstrap copies recorded | Agree Q-16 persistence, recovery, settings validation/activation and admin contracts | Q-01/Q-02/Q-05 state/time/identity, Q-10/Q-11 bindings and Q-13 activation policy |
| [Architecture](architecture.md) | State ownership, D-046 library direction and D-047 source/GPS seam recorded | Resolve Q-01/Q-02/Q-13, specify source/coordinate contracts and evaluate Q-15 candidates | Domain, event/time, spatial bindings and prototype evidence |
| [Domain and state machines](domain_and_state_machines.md) | Eight aircraft and eleven tanker states; normal Completed/history and selected interruption cases recorded | Complete Q-05 identities/release boundaries and full D-043 transition matrix; detail Q-03/Q-04/Q-14 | Resource guards, events, field coverage |
| [Fuel and service model](fuel_and_service_model.md) | Litre-valued refueller profile, pumping/movement model and 12.5% reserves adopted; three independent stocks retained | Aircraft conversion/tolerances, depot/setup rates, pump use at depot and remaining Q-06/Q-07 | Profiles, lifecycle, assistance |
| [Planning and operator workflow](planning_and_operator_workflow.md) | One/two-request horizon; full known queue, reveal boundary and two-vehicle permission retained | Select nearest requests/spillover policy, cost/deadline rules and remaining Q-08/Q-09/Q-13 | State/time and EDF comparison |
| [USD presentation contract](usd_presentation_contract.md) | Revised states/profiles/accounting and D-046 runtime-scene boundary reflected; one-way projection retained | Resolve Q-10 fields, metrics and v1.0 migration with Q-15's library bridge; design UI later | Domain/request/resource contracts and selected presentation runtime |
| [Scenarios and validation](scenarios_and_validation.md) | Adopted-profile checks and D-046 library prototype criteria specified | Freeze Q-12 fixtures; evaluate Q-15's project-asset/lifecycle prototype | Profiles, transitions, EDF policy and platform evidence |
| [Reference profiles](reference_profiles.md) | KrAZ-221 visual / KrAZ-258B1 capability trade-off and numerical inputs adopted with evidence limits | Verify source applicability separately; select aircraft profiles and destinations (Q-11) | Fuel model, asset binding |

## Resume checkpoint

The decision register extends through **D-047**. Requirements remain working
drafts and do not establish a runtime, solver integration or measured benefit.

**D-047 — implementation point 0:** Define the common source-input contract and
future real-data adapter placeholders before the synthetic implementation.
Actual bridges are outside scope. Simulated GPS-like aircraft/refueller
positions must remain consistent with WorldState, infrastructure and the
scene-coordinate mapping. First review the
[C4 module map](../../architecture/case04_c4_architecture.md), then derive an
implementation order. The earlier construction-order proposal is withdrawn;
no package layout or implemented integration is established.

**Storage review — Q-16:** The C4 map now proposes one MySQL server with separate
core, telemetry, analytics and module-settings databases. Define transactional
persistence and
restore of positions, stocks, requests and simulation time before relying on
the archive. Derived reports must not be the only copy of operational facts.

**D-046 — standalone-library direction:** Case 04 should demonstrate an
application-owned logistics runtime integrating selected Omniverse libraries,
complementing Case 03's Kit application/extension path. Evaluate `ovstage` and
`ovrtx` with a real project USD asset, application-driven movement and repeatable
initialisation/reset/shutdown. Final SDK versions, platform compatibility,
viewport/UI/HUD and deployment remain Q-15. The direction is agreed; the
integration and final stack are not established.

**D-041 — normal request completion/history:** For an unchanged eligible flight,
full agreed uplift closes the original request as Completed and makes the
aircraft Ready without recalculating the same demand. Verify actual delivery
and balanced transfers. Equipment release is separate and still blocks movement
until safe. Partial delivery/executor changes continue the original request;
archive all requests and actual history. Cancelled outstanding quantities are
not delivered fuel. D-034/D-037/D-039 changed/cancelled-flight rules remain intact.

**D-042 — selected simulation profile:** Visual KrAZ-221 + TZ-22; logical and
documented operational profile KrAZ-258B1 + TZ-22. Capacities: 22,000 L aviation
fuel, 330 L diesel (2 x 165 L), conditional 90 L pump petrol. Counted movement
uses 40.1 L/100 km; pumping uses 15 L/h petrol at 1,000 L/min through one hose.
Diesel/petrol reserves are 12.5%: 41.25 L and 11.25 L. Tractor diesel is zero
during aircraft Refueling and internal depot moves. Internal moves still take
time. Values are adopted simulation inputs with separate historical evidence
limits; the 90 L tank is explicitly conditional.

**D-043 — depot/Standby vocabulary:** RefillTank and RefillServiceFuel replace
Loading; Repositioning includes internal depot travel. Standby requires actual
arrival/stopping at depot reserve parking, technical readiness, completed
operations and all three stocks at 100%. Idle is available field waiting;
descriptive tanker Parked maps to Idle. No separate Active/Standby role variables.
Normal operation is one field vehicle and one Standby, with temporary concurrent
field work permitted when needed (D-027). D-026 still dispatches replacement to
field parking without an aircraft request/window and requires actual parking
arrival before service. Preserve physical field coverage and D-018's exclusive
posts/simultaneous-arrival order. D-020/D-030 retain partial-stock direct assignment
after required operations, including during parking-bound Repositioning.

**D-044 — selected exception catalogue:** Tyre puncture during Repositioning
leaves the modelled vehicle mobile but unavailable for service; pump failure and
pump-petrol exhaustion interrupt Refueling and preserve original demand/actual
fuel. Technical repair enters Maintenance on depot repair-area arrival; ordinary
petrol replenishment does not. Service-time diesel exhaustion and its earlier
continue-service donor branches are excluded. Existing D-017/D-032 exceptional
protected-return assistance is retained; further movement-exhaustion scope is
separate. Urgent replacement's field-parking exception is not approved.

**D-045 — limited optimisation:** Solve over one or two nearest known requests,
retaining the full known queue and its deadlines. Hidden scenario events stay
unavailable to the planner; already revealed schedules still support D-024's
one-hour look-ahead and proactive coverage. Which requests count as nearest,
one/two selection and queue spillover handling remain Q-08/Q-12. Use identical
information/horizon rules for EDF and cuOpt; equal or worse cuOpt results are valid.

**Domain continuation:** Trace one numerical end-to-end request using the adopted refueller
profile and explicitly illustrative aircraft/route timings. Show agreement,
actual delivery, interruption, cancellation and a new flight without duplicate
transfers or fuel resets. Do not invent Q-05 policies to complete the trace.

**Platform continuation:** Specify and then evaluate the D-046 standalone-library
prototype against its [acceptance criteria](scenarios_and_validation.md#library-integration-proof-d-046).
Keep this evaluation separate from the unfinished domain trace and EDF/cuOpt
benchmark; a renderer prototype cannot settle their open policies or results.

**Remaining review:** Request/commitment IDs and atomic updates; exact release
and fault-after-full-delivery boundaries; cancellation after replacement arrival
before service, flight replacement without cancellation in Alert, and a running
service cancellation followed by a fault. Complete the new depot transition
matrix, Q-02 ordering, Q-03 assistance equipment/rates, Q-04 spatial coverage,
Q-06 aircraft units/check timing, Q-07 depot/setup/pump-use details, Q-08 selection
policy, Q-09 solver interface, Q-10 projection/metrics and Q-13 operating modes.
Q-14 still owns selected-event timing, repair evidence/durations and any further
recovery scope. Ready remains irreversible and D-040 keeps scheduled ETD,
prediction and actual departure separate with operator authority over ETD changes.
Q-15 owns candidate-library viability and final platform selection, with Q-10
retaining scene/telemetry mapping and Q-13 retaining operator-command semantics.

## Decision status and provenance

- **Agreed in discussion:** Recorded agreement, retained for review; not proof
  of an approved schema, complete behaviour or implementation.
- **Direction agreed:** The principle is recorded; details remain undecided.
- **Provisional:** A candidate model or scenario needs further agreement.
- **Proposed:** An option or working formulation has not been agreed.
- **Open:** No decision has been made. Review findings and numerical examples
  must not silently become requirements.

An eventual **Approved** status requires an explicit requirements review and
resolution of relevant conflicts. Implementation and validation evidence should
be recorded separately from requirements maturity.

D-001–D-014 originate in the 28 October notes; D-015–D-021 record the later
29 October discussions: the two-vehicle fleet, diesel assistance, depot
transitions 5–7 and separate pump-petrol accounting. The 30 October correction
closes transition 4's behaviour for all fuel shortages and clarifies D-018's
simultaneous-arrival sequence. D-022–D-025 record the subsequent 30 October
waiting, reassignment and event-driven relief agreements. D-026 subsequently
clarifies mandatory physical field coverage and the replacement's parking-first
route, superseding direct-depot-to-aircraft relief in that handover. D-027 records
the later assignment-review-first rule and concurrent two-vehicle field work,
amending D-015/D-016/D-025. D-028 records the subsequent vehicle/aircraft labels,
fuel bars and optional distant refueller label grouping. D-029 adds the fuel
statistics information requirement, with UI design deferred. D-030 closes
assignment during movement to depot parking as a clarification of D-020's
existing Loading-to-ToAircraft transition. D-031 records the preliminarily
accepted Maintenance framework and the open synthetic-fault catalogue. D-032
records Alert, assistance travel/transfer/departure states, busy-donor waiting
and the reachable-airfield scenario assumption. D-033/D-034 record pre-service
cancellation/revision and the original-condition commitment after service start,
including a cancelled flight's fuelled aircraft awaiting a new flight. D-035
confirms D-031's framework and immediate technical withdrawal with mobile
ToDepot-to-Maintenance return, separating mechanics from the fault catalogue.
D-036/D-037 record interrupted-aircraft Alert and committed Ready; D-038 splits
Alert Refueling from aircraft Refueling and keeps one Repositioning state.
The 31 October review agrees D-006/D-007 aircraft behaviour and T1–T10, extends
D-037 enforcement to operator commands, adds D-039 interrupted-service
cancellation and D-040 schedule authority/delay warnings. These are provenance IDs,
not ADR numbers. The subsequent 31 October integration adds D-041 normal
completion/history, D-042 adopted visual/capability profile, D-043 explicit depot
and Standby states, D-044 selected exceptions and D-045 bounded planning. These
amend prior state/profile wording explicitly rather than rewriting its provenance.
The 1 November discussion adds D-046's standalone-library direction, with
`ovstage`/`ovrtx` candidates and a prototype gate before final platform adoption.
It also adds D-047's common input-source seam, out-of-scope real bridges and
synthetic GPS-like position telemetry as implementation point 0.
Each has one primary home; other documents link to that definition.

| ID | Recorded status | Decision and primary home |
| --- | --- | --- |
| D-001 | Agreed in discussion | [WorldState owns current state](architecture.md#state-ownership) |
| D-002 | Agreed in discussion; existing-contract conflict open | [One-way USD presentation](usd_presentation_contract.md#ownership-and-direction) |
| D-003 | Direction agreed | [Separate lifecycles](domain_and_state_machines.md#entities-and-lifecycles) |
| D-004 | Agreed in discussion | [Unlimited aviation-fuel supply at depot](fuel_and_service_model.md#depot-and-resource-accounting) |
| D-005 | Numerical separation agreed; D-042 adopts refueller litres/capacities; aircraft conversions remain open | [Separate numerical fuel balances](fuel_and_service_model.md#quantities-and-units) |
| D-006 | Eight aircraft states and T1–T10 agreed on 31 October; request records and boundary cases open | [Aircraft lifecycle and arrival type](domain_and_state_machines.md#aircraft-lifecycle); retain the 3 + 2 + optional 1 schedule |
| D-007 | Initial/late/current-flight checks agreed; D-041 removes repeated unchanged-flight demand calculation after complete delivery; check timing open | [Pre-departure fuel check](domain_and_state_machines.md#fuel-check-and-request-lifecycle) |
| D-008 | Direction agreed | [Yerevan, destinations and next flight leg](reference_profiles.md#destinations-and-flight-legs) |
| D-009 | Provisional user proposal | [Scheduled transit fuel stop](scenarios_and_validation.md#scheduled-transit-fuel-stop) |
| D-010 | Basic model retained; D-043 supersedes Loading with two refill states and explicit Standby, giving eleven recorded states | [Tanker states and sequential service](domain_and_state_machines.md#tanker-states) |
| D-011 | Agreed in discussion; mechanism open | [Shared queue and operator override](planning_and_operator_workflow.md#requests-and-serve-next); transition 1 links to this rule |
| D-012 | Agreed in discussion | [Arrival and start of refuelling](domain_and_state_machines.md#tanker-transitions), transition 2 |
| D-013 | Agreed in discussion; spatial details open; dwell policy fixed by D-024 | [Depot, waiting locations and Repositioning](domain_and_state_machines.md#locations-and-repositioning) |
| D-014 | Agreed in discussion; fuel-shortage branch closed by D-015; other lifecycle details open | [Post-service branching](domain_and_state_machines.md#tanker-transitions), transition 3 |
| D-015 | Agreed for all fuel shortages; D-027 requires assignment review before selecting depot return; D-017 implementation open | [Reviewed replenishment branch](domain_and_state_machines.md#transition-4-refueling-to-todepot), transition 4 |
| D-016 | Two refuellers and rotation agreed; D-027 allows both on the field; D-026 coverage/parking-first handover applies | [Two-refueller duty rotation](domain_and_state_machines.md#two-refueller-rotation-d-016--agreed-in-discussion) |
| D-017 | Protected diesel return and assistance required; D-032 state sequence agreed; equipment/rates/records open | [Diesel reserve and tractor-to-tractor assistance](domain_and_state_machines.md#diesel-reserve-and-exceptional-assistance-d-017) |
| D-018 | Exclusive posts/simultaneous-arrival sequence retained; D-043 changes state boundaries; remaining depot rates/visit details open | [Transition 5: Depot arrival and replenishment](domain_and_state_machines.md#transition-5-todepot-to-replenishment) |
| D-019 | D-043 replaces depot Idle with full-ready Standby after parking arrival; D-030 redirection retained | [Transition 6: Replenishment to Standby](domain_and_state_machines.md#transition-6-replenishment-to-standby) |
| D-020 | Partial-stock feasible departure retained; D-043 revises state vocabulary | [Transition 7: Completed replenishment to ToAircraft](domain_and_state_machines.md#transition-7-completed-replenishment-to-toaircraft) |
| D-021 | Three independent stocks/guards agreed; D-042 selects numerical simulation inputs with evidence limits | [Aviation fuel, tractor diesel and pump petrol](fuel_and_service_model.md#three-refueller-fuel-stocks-d-021) |
| D-022 | Agreed in discussion; spatial details open | [Transition 8: Repositioning to Idle](domain_and_state_machines.md#transition-8-repositioning-to-idle); actual arrival and stop at a waiting location |
| D-023 | Agreed for ordinary repositioning; D-026 replacement must first arrive at field parking; operating-mode contract applies | [Transition 9: Repositioning to ToAircraft](domain_and_state_machines.md#transition-9-repositioning-to-toaircraft); immediate redirection after all checks from actual position |
| D-024 | One-hour dwell/look-ahead and event-driven reassessment agreed; event ordering open | [Transition 10: Idle to Repositioning](domain_and_state_machines.md#transition-10-idle-to-repositioning); Staging Point waiting and Main Parking decision |
| D-025 | Proactive return follows D-027 assignment review; D-026 coverage/parking-first rules and service eligibility retained | [Waiting or repositioning to ToDepot](domain_and_state_machines.md#waiting-or-repositioning-to-todepot); known demand can trigger reviewed replenishment |
| D-026 | At least one ready refueller physically on the field and replacement via parking agreed; timing/boundary/readiness details open | [Field coverage and replacement from depot](domain_and_state_machines.md#field-coverage-and-replacement-from-depot-d-026); transition 13, independent of aircraft-request eligibility |
| D-027 | Assignment review and temporary concurrent field work retained; D-045 bounds horizon, selection/cost policy open | [Joint assignment, order and replenishment review](planning_and_operator_workflow.md#assignment-review-and-two-refueller-field-operation-d-027) |
| D-028 | Tractor/tank/aircraft labels, separate fuel bars and optional distant refueller label grouping agreed; layout and bindings open | [Vehicle and aircraft visual labels](usd_presentation_contract.md#vehicle-and-aircraft-visual-labels-d-028) |
| D-029 | Fuel statistics panel and three indicators agreed; precise metric/period semantics open; UI design deferred | [Fuel statistics panel](usd_presentation_contract.md#fuel-statistics-panel-d-029); issued over period, currently in progress and awaiting issue against existing requests |
| D-030 | Direct assignment during parking movement retained; D-043 uses Repositioning after completed depot operations | [Transition 7 clarification](domain_and_state_machines.md#transition-7-completed-replenishment-to-toaircraft); no parking stop/Standby required |
| D-031 | Maintenance framework retained; D-043 revises depot exit states, D-044 selects technical fault cases; timing/evidence open | [Maintenance framework](domain_and_state_machines.md#maintenance-framework-and-fault-catalogue-d-031) |
| D-032 | Agreed assistance sequence; D-038 names the transfer state Alert Refueling; busy-donor waiting, depot preparation and checked departures retained | [Diesel-assistance state sequence and scenario scope](domain_and_state_machines.md#diesel-assistance-state-sequence-and-scenario-scope-d-032) |
| D-033 | Agreed: cancellation/revision before service triggers reviewed reassignment, waiting or replenishment; schedule change alone does not cancel | [Request cancellation and revision](domain_and_state_machines.md#request-cancellation-and-revision-d-033) |
| D-034 | Agreed: running service keeps original conditions; manual interruption blocked; D-035 stops faulty service and D-039 cancels interrupted remainder in its reviewed window | [Started-service commitment and cancelled flights](domain_and_state_machines.md#started-service-commitment-and-cancelled-flights-d-034) |
| D-035 | Agreed: any detected technical fault stops aircraft work; mobile vehicle returns unavailable in ToDepot and enters Maintenance on arrival; immobile recovery separately scoped | [Fault withdrawal and depot return](domain_and_state_machines.md#fault-withdrawal-and-depot-return-d-035); catalogue selection does not reopen the mechanism |
| D-036 | Agreed: tanker fault interrupts aircraft Refueling into Alert; replacement resumes only still-valid outstanding service; D-039 cancellation branch applies | [Aircraft interrupted service](domain_and_state_machines.md#aircraft-interrupted-service-and-committed-readiness-d-036d-037) |
| D-037 | Agreed: current-flight window gates Ready; provider and execution reject reversals; actual departure includes Departing and D-040 late timing | [Committed aircraft readiness](domain_and_state_machines.md#aircraft-interrupted-service-and-committed-readiness-d-036d-037) |
| D-038 | Agreed: Refueling for aircraft; separate Alert Refueling for diesel rescue; single general Repositioning state; faulty ToDepot unavailable | [Tanker state vocabulary](domain_and_state_machines.md#tanker-states) |
| D-039 | Agreed: flight cancellation in Alert before replacement arrival cancels the old undelivered obligation; Parked retains fuel; new flight uses its own check | [Interrupted-service cancellation](domain_and_state_machines.md#interrupted-service-cancellation-d-039) |
| D-040 | Agreed: delay warning in Parked/Refueling/Alert; operator decides ETD changes; schedule, prediction and actual departure remain separate; no past-time deadlock | [Departure delay risk and schedule authority](planning_and_operator_workflow.md#departure-delay-risk-and-schedule-authority-d-040) |

| D-041 | Agreed: normal full delivery closes Completed without repeated demand calculation; all requests/history retained; release and boundary details open | [Normal completion and request history](domain_and_state_machines.md#normal-completion-and-request-history-d-041) |
| D-042 | Adopted simulation profile and 12.5% reserves; zero diesel in Refueling/internal depot moves; equipment evidence separate | [KrAZ-221 visuals / KrAZ-258B1 capabilities](reference_profiles.md#adopted-simulation-profile-d-042); [resource checks](fuel_and_service_model.md#adopted-resource-checks-d-042) |
| D-043 | Agreed: RefillTank, RefillServiceFuel and explicit Standby; Loading superseded, depot moves Repositioning; full matrix open | [Tanker states](domain_and_state_machines.md#tanker-states) and [depot transitions](domain_and_state_machines.md#depot-loading-and-departure) |
| D-044 | Tyre/pump/petrol-exhaustion cases selected; service-time diesel exhaustion excluded; D-017/D-032 retained | [Selected catalogue](domain_and_state_machines.md#q-14-selected-fault-catalogue-and-interrupted-service-d-044) |
| D-045 | Agreed: one/two-known-request horizon, full queue retained and hidden future excluded; nearest/selection/spillover rules open | [Bounded planning](planning_and_operator_workflow.md#bounded-planning-and-information-visibility-d-045) |
| D-046 | Direction agreed on 1 November; candidate libraries and final runtime stack require prototype evidence under Q-15 | [Standalone Omniverse library direction](architecture.md#standalone-omniverse-library-direction-d-046) |
| D-047 | Agreed on 1 November: common source contract, future real-adapter placeholders only, synthetic GPS-like positioning; actual real-system bridges excluded | [Input sources and synthetic position telemetry](architecture.md#input-sources-and-synthetic-position-telemetry-d-047) |

## Open review register

These questions consolidate the original open items and review findings from
29 October. Their inclusion does not decide the answer. Detailed next steps
live in the owning document; this register provides a single continuation index.

| ID | Unresolved question | Owner |
| --- | --- | --- |
| Q-01 | Who applies events and commands to WorldState, and who publishes snapshots? Reconcile SDP publication wording with D-001 | [Architecture](architecture.md#open-questions-and-next-steps) |
| Q-02 | What defines simulation time, event ordering, replay, resets and the relationship to USD time? | [Architecture](architecture.md#open-questions-and-next-steps) |
| Q-03 | Which transfer equipment/setup, rates/amounts, exact event timing and records implement D-017/D-032? States, busy-donor waiting, departures and traversable-airfield scope are agreed | [Domain](domain_and_state_machines.md#open-questions-and-next-steps) |
| Q-04 | Field boundary, handover/readiness and parking/holding geometry; D-043 revises depot movement/Standby and retains D-030 redirection; Q-02 owns event ordering | [Domain](domain_and_state_machines.md#open-questions-and-next-steps) |
| Q-05 | Request/commitment IDs, atomic closure/release; cancellation after replacement arrival before resumption; replacement without cancellation in Alert; cancellation during service then fault; fault after full uplift before release. D-041 fixes normal Completed/history | [Domain](domain_and_state_machines.md#open-questions-and-next-steps) |
| Q-06 | Aircraft calculation units/density/usable stock, tolerances, demand/reserve framework and check/ETD-change timing; D-042 fixes refueller litres, capacities and reserves | [Fuel model](fuel_and_service_model.md#open-questions-and-next-steps) |
| Q-07 | Depot/setup/repair rates and allowances, onboard pump use at depot, visit/skip and auxiliary dispensing order, holding geometry and assistance calculations; D-042 numeric refueller inputs retained | [Fuel model](fuel_and_service_model.md#open-questions-and-next-steps) |
| Q-08 | Serve next guarantee, nearest-request selection within D-045's one/two-request horizon, queue spillover, complete-fleet cost/deadline and priority/queue-position policy | [Planning](planning_and_operator_workflow.md#open-questions-and-next-steps) |
| Q-09 | Which cuOpt interface/features represent the chosen constraints, and how are infeasible, failed or outdated proposals handled? | [Planning](planning_and_operator_workflow.md#open-questions-and-next-steps) |
| Q-10 | How are D-028 labels and D-029 fuel-statistics metrics/periods bound and projected alongside units/time formatting, and how is schema v1.0 reconciled/versioned? UI design is later work | [USD contract](usd_presentation_contract.md#open-questions-and-next-steps) |
| Q-11 | Which equipment variants, operating profiles, destinations, source editions and asset-swap metadata are verified and selected? | [Reference profiles](reference_profiles.md#open-questions-and-next-steps) |
| Q-12 | Which common EDF/cuOpt fixtures, assignment/horizon policy, concurrent field operation, rotation/recovery cases, complete fleet route/diesel-cost model and deadline/coverage criteria establish benefit on the 3 + 2 + optional 1 schedule? | [Scenarios](scenarios_and_validation.md#open-questions-and-next-steps) |
| Q-13 | Remaining automatic-dispatch, confirmation, override and mode-switching rules; D-040 already requires operator confirmation for ETD changes and D-037 rejects changes after Ready | [Planning](planning_and_operator_workflow.md#operator-acceptance-and-unresolved-result-handling) |
| Q-14 | Timing, repair evidence/durations and urgent replacement route for D-044's selected events; further movement-exhaustion/immobile recovery separately scoped; field-parking bypass open | [Selected fault catalogue](domain_and_state_machines.md#q-14-selected-fault-catalogue-and-interrupted-service-d-044) |
| Q-15 | Which library releases, Windows/GPU/driver/Python combination, scene/lifecycle bridge, viewport/UI/HUD and deployment meet D-046's prototype gate before final stack selection? | [Architecture](architecture.md#standalone-omniverse-library-direction-d-046); [proof criteria](scenarios_and_validation.md#library-integration-proof-d-046) |
| Q-16 | Which database split, transaction/recovery and sampling rules preserve state/history, and which settings schemas, bootstrap precedence, revision/admin and activation rules govern module configuration? | [Persistence and storage](persistence_and_storage.md) |

## Source coverage and existing contracts

The original [architecture proposal](../incoming/fuel_logistics_architecture.md)
and [D-001–D-014 notes](../incoming/case04_architecture_decisions_working_notes.md)
remain historical source material. Continue requirements development here.

| Original architecture proposal | Destination |
| --- | --- |
| Section 1: objective, boundaries and separation of visuals/capabilities | This README; architecture; reference profiles |
| Section 2: modules and ownership | Architecture; fuel model; planning |
| Section 3: event loop and committed actions | Architecture; planning |
| Section 4: aircraft fuel demand | Fuel model |
| Section 5: fleet, scheduling and replenishment examples | Scenarios; domain; reference profiles |
| Section 6: repeatability and acceptance | Scenarios |
| Section 7: repository compatibility | USD contract; architecture runtime boundary |
| Section 8: unresolved decisions | Q-01–Q-12 and each document's next steps |
| Section 9: source anchors and limits | Reference profiles |

The existing [USD master contract](../../knowledge_base/usd_architecture/00_project_usd_contract.md)
and [telemetry schema v1.0](../../knowledge_base/usd_architecture/04_telemetry_and_vrp_attributes.md)
remain unchanged. In particular, the new ownership direction conflicts with the
old dispatch-read requirement. Record and review the migration before updating
those documents. This collection is not an implicit replacement for them, the
[project README](../../../README.md) or the [maturity definition](../../knowledge_base/digital_twin_maturity_levels.md).

After each reviewed decision, update its primary definition, this register and
affected document statuses/next steps. Keep unresolved alternatives explicit.
Promote foundational architectural decisions to ADRs and revise existing
contracts only after agreement; do not turn every D-ID into a separate ADR.
