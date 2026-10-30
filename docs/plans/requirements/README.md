# Case 04 — Fuel Logistics Requirements

**Status:** Working drafts; requirements review in progress

**Last updated:** 31 October 2026 (Asia/Yerevan)

**Source discussions:** 28–31 October 2026

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

**Depot and third-stock clarification:** D-018–D-020 record transitions 5–7:
arrival starts `Loading`; required post visits and internal travel take time;
completion leads either to depot parking and `Idle` or directly to `ToAircraft`.
Each post holds one vehicle. Simultaneous arrivals follow D-018's agreed
first-to-aviation/second-to-auxiliary sequence, with the second using aviation
after the first completes required replenishment and departs or proceeds to
parking. This supersedes the blanket no-post-queues assumption; operation,
movement and any required coordination wait take time. Separate depot parking
holds both refuellers. The auxiliary post has separate diesel and petrol
dispensers. D-021 adds pump petrol as the third independent refueller stock,
alongside aviation fuel and tractor diesel. Every aircraft-service assignment
must pass all three guards; full tanks are not required for a specific feasible
assignment, while the reserve vehicle retains full Standby replenishment.

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
| This README | Navigation, decision provenance and review queue assembled | Keep this table, the checkpoint and decision register aligned after each reviewed change | All topic documents |
| [Architecture](architecture.md) | Ownership principles agreed in discussion; automatic-dispatch capability clarified; event/time and authorisation contracts incomplete | Resolve Q-01/Q-02 and align mode-dependent execution authorisation with Q-13 | Domain entities; operating-mode contract |
| [Domain and state machines](domain_and_state_machines.md) | Eight aircraft states/T1–T10 agreed; nine tanker states retained; interrupted-service cancellation and current-flight check gating recorded | Finish Q-05 request/closure and interruption boundaries, Q-02 ordering, Q-14 catalogue and Q-03/Q-07 detail | Entity-specific states; fuel checks; schedule events and field coverage |
| [Fuel and service model](fuel_and_service_model.md) | Three independent stocks, assignment-review-first shortage policy, depot post order, separate auxiliary dispensers and protected return/assistance recorded; units/rates open | Define pump-petrol profile/reserve and remaining depot service/visit details; quantify diesel return/donation and fuel checks (Q-06, Q-07) | Equipment profiles; domain lifecycle |
| [Planning and operator workflow](planning_and_operator_workflow.md) | D-027 fleet comparison and automatic dispatch retained; D-040 fixes separate delay warning and operator authority over ETD changes | Fix horizon/cost policy (Q-08), remaining modes (Q-13), time measurement and solver interface (Q-09); validate against EDF | Domain states; physical constraints; EDF comparison |
| [USD presentation contract](usd_presentation_contract.md) | One-way projection, D-028 object labels/fuel bars and D-029 issued/in-progress/awaiting fuel statistics recorded; schema v1.0 conflicts unresolved | Define label bindings and panel metric/period semantics alongside projection/migration (Q-10); design UI later | Domain/request fields, accounting, fuel profiles and ownership |
| [Scenarios and validation](scenarios_and_validation.md) | Demonstration, all shortage combinations, exclusive depot posts, waiting thresholds/events, actual-position reassignment, proactive return/relief and assistance cases recorded; EDF benchmark specified; no measured solver advantage | Fix common EDF/cuOpt inputs and depot/waiting/rotation/recovery fixtures and thresholds (Q-12); retain separate diesel and petrol accounting | Fuel profiles; lifecycle; objective and operating modes |
| [Reference profiles](reference_profiles.md) | Source claims retained; separate pump drive corroborated; exact early variant and numerical performance unverified | Verify pump-engine/tank variant and select capacities, consumption, rates and reserves; continue aircraft/destination review (Q-11) | Intended scene configuration and fuel model |

## Resume checkpoint

The decision register now extends through D-040. Recorded depot agreements are
**D-018 / depot arrival and operations**, **D-019 / completion without an assignment**, **D-020 / direct
assigned departure** and **D-021 / three independent refueller fuel stocks**.
Transitions 5–7 are now recorded rather than waiting for first discussion.
**30 October correction:** D-015 / transition 4 covers insufficient aviation
fuel, tractor diesel, pump petrol or a combination. D-027 subsequently amends
the trigger: first review other assignments; return only on the selected
replenishment branch rather than automatically after one failed candidate. Normal checks
preserve the protected diesel return; an unexpected breach invokes D-017.
D-018 / transition 5 now records the simultaneous-arrival sequence and exclusive
post occupancy, replacing the blanket no-post-queues assumption. The auxiliary
post provides diesel and petrol through separate dispensers; D-021's stocks
remain independent. Other decision statuses are preserved. Discuss remaining
open cases individually; a table row for an open transition is not an agreed rule.

**Waiting and schedule-event agreements:** D-022 records actual waiting-location
arrival into `Idle`; D-023 permits direct `Repositioning -> ToAircraft` after all
checks from the current position. D-024 fixes Staging Point dwell at **more than
one hour**, then a **one-hour fuel-check-window look-ahead**, with reassessment
on every arrival/departure schedule addition/change and expected fuel-check
window. If a check creates no request, look ahead again. D-025/D-027 permit
proactive return from waiting or repositioning after assignment review selects
replenishment for known upcoming demand. D-026 requires actual full-Standby dispatch to field
parking for coverage, without waiting for the aircraft window or request. The
replacement must arrive and stop there before eligible aircraft-bound departure.
This coverage trip is an exception to D-023's direct in-motion assignment rule.
Coordinate handover to retain at least one ready refueller physically on the
field; a replacement still outside the field does not satisfy that condition.

**Fleet assignment and cost review (D-027):** A 12-tonne request cannot be served
by a vehicle with 10 tonnes, but a feasible 8-tonne request can keep it working
while the second serves the larger request. Check all three resources, reserves
and timing. Compare complete two-vehicle plans, including subsequent depot legs,
rather than assuming that a smaller fitting job saves fuel. Both vehicles may
remain on the field; retain at least one ready vehicle there. cuOpt is to be
evaluated for joint assignment, service order and replenishment timing to reduce
fleet travel/diesel cost; prove any benefit against EDF using the same rules.

**Visual labels (D-028):** Omniverse labels above each tractor show refueller
name/state and tractor diesel; labels above its tank show aviation fuel and pump
petrol separately. At distant zoom the pair may become one refueller label
without combining the three stocks. Aircraft labels show flight name, departure
date/time, current aircraft state and onboard fuel. Layout, anchors, stock/capacity
bindings, display time zone and grouping thresholds remain Q-10 details.

**Fuel statistics (D-029):** Include a fleet-wide UI panel showing actual fuel
issued over a reporting period, fuel currently in progress and fuel awaiting
issue against existing requests. Define the in-progress quantity basis,
period/units and request-state accounting before implementation. Panel/UI design
is deliberately deferred; the information requirement is recorded now.

**Additional review required:** Q-13 records the unresolved operating modes and
the required automatic-dispatch capability. Resolve its authorisation and
intervention rules before implementing dispatch; the earlier blanket
operator-confirmation wording is reopened. This does not pre-approve any further
tanker transition or choose the final mode set. D-016's duty rotation must follow
the same operating-mode contract.

**Cancellation and started-service agreements (D-033/D-034):** Before service,
explicit cancellation or revision triggers actual-position fleet reassignment,
waiting or selected depot return; a departure-time change alone does not cancel
the request. At aircraft Refueling start, retain the original service conditions
and complete that delivery despite later withdrawal/revision/flight cancellation.
Manual cancellation after start is blocked and late commands rejected. If the
flight is cancelled, the fuelled aircraft stays Parked without an active flight
until the provider assigns it a new flight, using its actual retained fuel.
D-035 detected-fault withdrawal overrides normal service continuation. D-039
adds the explicit cancellation exception after interruption, while Alert and
before replacement arrival. Q-05 still owns request/commitment records and
completion/final-check ordering; Q-02 owns simultaneous-event ordering.

**31 October aircraft review completed in discussion:** D-006 now fixes eight
states and T1–T10, including actual approach/stand arrival, Departing and airborne
zone exit. D-007 fixes immediate checks after late stand arrival and separates
check results from states. A changed/new flight uses its own check window;
finishing an earlier delivery before that window leaves the aircraft Parked,
even when fuel is sufficient. Running service is not interrupted by a window
opening or schedule revision, and its demand must not be duplicated.

**D-039 — interrupted-service cancellation:** If the flight is cancelled in
aircraft Alert before replacement arrival, cancel the old undelivered obligation,
return the aircraft to Parked without an active flight and retain actual fuel.
Release the replacement for existing actual-position reassignment; the failed
tanker's withdrawal remains unchanged. A new flight gets a new check and only
its calculated deficit. Running Refueling still follows D-034. Cancellation
after replacement arrival but before resumed service and replacement without
explicit cancellation while Alert remain open, as does cancellation during
running service followed later by a fault; do not silently extend D-039.

**Ready and departure timing:** D-037 is enforced for the provider and all
operator/event inputs: no cancellation, flight replacement or readiness reversal
after Ready. Continue Ready -> Departing -> Departed on actual movement/exit.
D-040 separates scheduled ETD, prediction and actual departure; an operator must
confirm ETD changes. Keep Schedule preserves an infeasible time and its warning,
not an invented feasible plan. Late readiness must not wait for a past timestamp
or rewrite ETD automatically. Departure Delay Risk is separate from state and
applies in Parked, Refueling and Alert. Automatic tanker dispatch remains required.

**Next session — first step:** Work through one end-to-end numerical scenario
linking the fuel-request lifecycle, aircraft onboard fuel and all three
refueller balances. Trace events, agreed/delivered/outstanding/cancelled
quantities and state changes, including interruption, cancellation and a new
flight, without duplicate transfers or fuel resets. Use explicitly declared
illustrative units, rates and timings; do not treat them as approved equipment
parameters. Use the trace to identify the request/event contracts still needed
before implementation.

**Subsequent requirements review:** Complete request/commitment identity and closure,
the remaining interruption boundaries, exact ETD/actual measurement points and
same-time event ordering. Then resolve Q-14's fault catalogue and numerical fuel,
service, coverage and operator-mode contracts. The aircraft state list and the
existing tanker transitions are not reopened by these remaining details.

**State vocabulary (D-038):** Refueling means aircraft service only; Alert
Refueling means tractor-diesel rescue. Repositioning remains one general
airfield-transit state outside ToDepot/ToAircraft, retaining the agreed Loading
internal-depot phases. Faulty ToDepot vehicles remain unavailable for work.

**Later: Q-14's technical-fault catalogue and recovery detail.**
D-031's Maintenance framework is agreed; D-035 closes the fault-withdrawal rule:
any detected technical fault stops aircraft work immediately, retains partial
delivery/unserved demand and removes the vehicle from assignments. If mobile,
it returns unavailable in ToDepot and enters Maintenance on actual depot
repair-area arrival; an immobile vehicle remains Alert pending separately scoped
recovery. Catalogue selection does not block that mechanism. Select which fault
events the provider may emit, mobility/repair evidence and durations. Pump failure
is still a candidate event; whether urgent takeover may bypass D-026 field parking
remains open. No arbitrary fault catalogue or towing capability is adopted.

**D-017/D-032 assistance behaviour is agreed:** The recipient waits in Alert;
the second finishes its current work if busy, approaches in Repositioning and
both use Alert Refueling for tractor-diesel transfer under D-038. The recipient then returns
to depot; the donor rechecks all three stocks for aircraft work, field waiting
or replenishment. The airfield is traversable; no impassable-road/disaster branch
is required. Numerical equipment/rate/amount and record details remain Q-03/Q-07.

**Donor preparation clarification:** If its post-work diesel cannot cover
approach, donation and both vehicles' protected depot returns, the donor first
uses its remaining diesel for its own feasible depot trip. In Loading it
replenishes tractor diesel **and trailer aviation fuel**, independently checking
pump petrol. It then goes straight Loading to Repositioning towards the recipient
after repeated assistance checks. The recipient remains Alert throughout; no
donor parking/Idle detour or intervening aircraft assignment is required.

Any of the three fuel shortages
first triggers D-027 assignment review. A selected replenishment route sends
the vehicle straight to Fuel Depot, with its protected diesel
return preserved or unexpectedly restored through D-017 assistance before
travel. The Standby can depart for **field parking** before current service ends
and without an open check window/request when needed for coverage replacement.
After parking arrival, aircraft-bound departure requires the check window,
actual request and all feasibility guards. Normal replanning/priority changes
do not interrupt ongoing refuelling; Q-14 separately reviews faults that make
continued service impossible. The depot now replenishes aviation fuel at one post and tractor
diesel/pump petrol through separate dispensers at the other. Apply D-018's
simultaneous-arrival sequence, one vehicle per post and elapsed operation,
movement and necessary waiting time. D-017 requires the second refueller to assist a
tractor that cannot return for lack of diesel; equipment, rates, exact timing
and records remain open, while busy-donor waiting is fixed by D-032. Do not infer
permission for either vehicle to travel without sufficient diesel.
See [the transition table](domain_and_state_machines.md#tanker-transitions) and
[resource questions](fuel_and_service_model.md#open-questions-and-next-steps).

**Depot parking movement (D-030):** A valid eligible assignment after required
replenishment may redirect the vehicle straight from Loading to ToAircraft,
including while driving to depot parking, after all checks from actual position
and stocks. No depot parking arrival, stop or Idle is required. This closes
that Q-04 behavioural case; D-026's separate field-parking requirement remains.

Then parameterise D-031/D-035 using selected events/completion evidence and remaining
exceptions. For recorded transitions 5–7, detail remaining visit/skip rules,
first/second tie-breaking, safe holding/movement geometry, auxiliary diesel/petrol
service sequence, durations, rates and readiness targets without reopening the
agreed simultaneous-arrival order or state boundaries. Then complete request and aircraft
lifecycles and the remaining contracts.

Do not reopen D-022–D-026 waiting thresholds, ordinary direct in-motion assignment
or the replacement's required field-parking arrival. D-027's assignment review
and permission for two concurrent field vehicles supersede unconditional return
and a permanent one-Active/one-depot-Standby restriction.
Detail spatial safety, event ordering, actual fuel-check lead times and the
10-tonne-stock/12-tonne-demand relief fixture. Future demand may trigger
replenishment and replacement dispatch to field parking before an executable
aircraft-service request exists. Specify coverage handover timing, field boundary
and readiness criteria; D-018's both-at-depot procedure cannot constitute covered
operation with this two-vehicle fleet. Coverage-loss handling remains explicit.

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
not ADR numbers. Each has one primary home; other documents link to that definition.

| ID | Recorded status | Decision and primary home |
| --- | --- | --- |
| D-001 | Agreed in discussion | [WorldState owns current state](architecture.md#state-ownership) |
| D-002 | Agreed in discussion; existing-contract conflict open | [One-way USD presentation](usd_presentation_contract.md#ownership-and-direction) |
| D-003 | Direction agreed | [Separate lifecycles](domain_and_state_machines.md#entities-and-lifecycles) |
| D-004 | Agreed in discussion | [Unlimited aviation-fuel supply at depot](fuel_and_service_model.md#depot-and-resource-accounting) |
| D-005 | Numerical separation agreed in direction; units proposed; extended by D-021 | [Separate numerical fuel balances](fuel_and_service_model.md#quantities-and-units) |
| D-006 | Eight aircraft states and T1–T10 agreed on 31 October; request records and boundary cases open | [Aircraft lifecycle and arrival type](domain_and_state_machines.md#aircraft-lifecycle); retain the 3 + 2 + optional 1 schedule |
| D-007 | Check behaviour agreed, including late-arrival and current-flight window guards; numerical lead time and ETD rescheduling open | [Pre-departure fuel check](domain_and_state_machines.md#fuel-check-and-request-lifecycle) |
| D-008 | Direction agreed | [Yerevan, destinations and next flight leg](reference_profiles.md#destinations-and-flight-legs) |
| D-009 | Provisional user proposal | [Scheduled transit fuel stop](scenarios_and_validation.md#scheduled-transit-fuel-stop) |
| D-010 | Basic model agreed; D-032 extends original seven states with Alert | [Tanker states and sequential service](domain_and_state_machines.md#tanker-states) |
| D-011 | Agreed in discussion; mechanism open | [Shared queue and operator override](planning_and_operator_workflow.md#requests-and-serve-next); transition 1 links to this rule |
| D-012 | Agreed in discussion | [Arrival and start of refuelling](domain_and_state_machines.md#tanker-transitions), transition 2 |
| D-013 | Agreed in discussion; spatial details open; dwell policy fixed by D-024 | [Depot, waiting locations and Repositioning](domain_and_state_machines.md#locations-and-repositioning) |
| D-014 | Agreed in discussion; fuel-shortage branch closed by D-015; other lifecycle details open | [Post-service branching](domain_and_state_machines.md#tanker-transitions), transition 3 |
| D-015 | Agreed for all fuel shortages; D-027 requires assignment review before selecting depot return; D-017 implementation open | [Reviewed replenishment branch](domain_and_state_machines.md#transition-4-refueling-to-todepot), transition 4 |
| D-016 | Two refuellers and rotation agreed; D-027 allows both on the field; D-026 coverage/parking-first handover applies | [Two-refueller duty rotation](domain_and_state_machines.md#two-refueller-rotation-d-016--agreed-in-discussion) |
| D-017 | Protected diesel return and assistance required; D-032 state sequence agreed; equipment/rates/records open | [Diesel reserve and tractor-to-tractor assistance](domain_and_state_machines.md#diesel-reserve-and-exceptional-assistance-d-017) |
| D-018 | Agreed in discussion, including simultaneous-arrival order and exclusive post occupancy; numerical rates and other visit-policy details open | [Transition 5: ToDepot to Loading](domain_and_state_machines.md#transition-5-todepot-to-loading); two posts, separate auxiliary dispensers and two-vehicle depot parking |
| D-019 | Agreed; D-030 permits direct aircraft assignment during parking movement | [Transition 6: Loading to Idle](domain_and_state_machines.md#transition-6-loading-to-idle); no assigned request, Idle on parking arrival |
| D-020 | Agreed; D-030 includes movement to depot parking; numerical guards open | [Transition 7: Loading to ToAircraft](domain_and_state_machines.md#transition-7-loading-to-toaircraft); direct departure, partial stocks permitted if feasible |
| D-021 | Three independent refueller stocks and guards agreed; exact equipment/parameters open | [Aviation fuel, tractor diesel and pump petrol](fuel_and_service_model.md#three-refueller-fuel-stocks-d-021) |
| D-022 | Agreed in discussion; spatial details open | [Transition 8: Repositioning to Idle](domain_and_state_machines.md#transition-8-repositioning-to-idle); actual arrival and stop at a waiting location |
| D-023 | Agreed for ordinary repositioning; D-026 replacement must first arrive at field parking; operating-mode contract applies | [Transition 9: Repositioning to ToAircraft](domain_and_state_machines.md#transition-9-repositioning-to-toaircraft); immediate redirection after all checks from actual position |
| D-024 | One-hour dwell/look-ahead and event-driven reassessment agreed; event ordering open | [Transition 10: Idle to Repositioning](domain_and_state_machines.md#transition-10-idle-to-repositioning); Staging Point waiting and Main Parking decision |
| D-025 | Proactive return follows D-027 assignment review; D-026 coverage/parking-first rules and service eligibility retained | [Waiting or repositioning to ToDepot](domain_and_state_machines.md#waiting-or-repositioning-to-todepot); known demand can trigger reviewed replenishment |
| D-026 | At least one ready refueller physically on the field and replacement via parking agreed; timing/boundary/readiness details open | [Field coverage and replacement from depot](domain_and_state_machines.md#field-coverage-and-replacement-from-depot-d-026); transition 13, independent of aircraft-request eligibility |
| D-027 | Assignment review before depot return and both vehicles working on the field agreed; horizon and numerical cost/deadline policy open | [Joint assignment, order and replenishment review](planning_and_operator_workflow.md#assignment-review-and-two-refueller-field-operation-d-027); compare complete fleet plans and validate benefit against EDF |
| D-028 | Tractor/tank/aircraft labels, separate fuel bars and optional distant refueller label grouping agreed; layout and bindings open | [Vehicle and aircraft visual labels](usd_presentation_contract.md#vehicle-and-aircraft-visual-labels-d-028) |
| D-029 | Fuel statistics panel and three indicators agreed; precise metric/period semantics open; UI design deferred | [Fuel statistics panel](usd_presentation_contract.md#fuel-statistics-panel-d-029); issued over period, currently in progress and awaiting issue against existing requests |
| D-030 | Immediate assignment during Loading movement to depot parking agreed; all fuel/assignment guards apply | [Transition 7 clarification](domain_and_state_machines.md#transition-7-loading-to-toaircraft); no parking arrival, stop or Idle required after completed replenishment |
| D-031 | Maintenance framework agreed, clarified by D-035; concrete fault events, repair evidence/durations and spatial detail open | [Maintenance framework and fault catalogue](domain_and_state_machines.md#maintenance-framework-and-fault-catalogue-d-031) |
| D-032 | Agreed assistance sequence; D-038 names the transfer state Alert Refueling; busy-donor waiting, depot preparation and checked departures retained | [Diesel-assistance state sequence and scenario scope](domain_and_state_machines.md#diesel-assistance-state-sequence-and-scenario-scope-d-032) |
| D-033 | Agreed: cancellation/revision before service triggers reviewed reassignment, waiting or replenishment; schedule change alone does not cancel | [Request cancellation and revision](domain_and_state_machines.md#request-cancellation-and-revision-d-033) |
| D-034 | Agreed: running service keeps original conditions; manual interruption blocked; D-035 stops faulty service and D-039 cancels interrupted remainder in its reviewed window | [Started-service commitment and cancelled flights](domain_and_state_machines.md#started-service-commitment-and-cancelled-flights-d-034) |
| D-035 | Agreed: any detected technical fault stops aircraft work; mobile vehicle returns unavailable in ToDepot and enters Maintenance on arrival; immobile recovery separately scoped | [Fault withdrawal and depot return](domain_and_state_machines.md#fault-withdrawal-and-depot-return-d-035); catalogue selection does not reopen the mechanism |
| D-036 | Agreed: tanker fault interrupts aircraft Refueling into Alert; replacement resumes only still-valid outstanding service; D-039 cancellation branch applies | [Aircraft interrupted service](domain_and_state_machines.md#aircraft-interrupted-service-and-committed-readiness-d-036d-037) |
| D-037 | Agreed: current-flight window gates Ready; provider and execution reject reversals; actual departure includes Departing and D-040 late timing | [Committed aircraft readiness](domain_and_state_machines.md#aircraft-interrupted-service-and-committed-readiness-d-036d-037) |
| D-038 | Agreed: Refueling for aircraft; separate Alert Refueling for diesel rescue; single general Repositioning state; faulty ToDepot unavailable | [Tanker state vocabulary](domain_and_state_machines.md#tanker-states) |
| D-039 | Agreed: flight cancellation in Alert before replacement arrival cancels the old undelivered obligation; Parked retains fuel; new flight uses its own check | [Interrupted-service cancellation](domain_and_state_machines.md#interrupted-service-cancellation-d-039) |
| D-040 | Agreed: delay warning in Parked/Refueling/Alert; operator decides ETD changes; schedule, prediction and actual departure remain separate; no past-time deadlock | [Departure delay risk and schedule authority](planning_and_operator_workflow.md#departure-delay-risk-and-schedule-authority-d-040) |

## Open review register

These questions consolidate the original open items and review findings from
29 October. Their inclusion does not decide the answer. Detailed next steps
live in the owning document; this register provides a single continuation index.

| ID | Unresolved question | Owner |
| --- | --- | --- |
| Q-01 | Who applies events and commands to WorldState, and who publishes snapshots? Reconcile SDP publication wording with D-001 | [Architecture](architecture.md#open-questions-and-next-steps) |
| Q-02 | What defines simulation time, event ordering, replay, resets and the relationship to USD time? | [Architecture](architecture.md#open-questions-and-next-steps) |
| Q-03 | Which transfer equipment/setup, rates/amounts, exact event timing and records implement D-017/D-032? States, busy-donor waiting, departures and traversable-airfield scope are agreed | [Domain](domain_and_state_machines.md#open-questions-and-next-steps) |
| Q-04 | What field boundary, readiness/coverage handover and parking selection/safety rules apply? D-022–D-026 close waiting/replacement behaviour; D-030 closes assignment during Loading movement; Q-02 owns simultaneous-event ordering | [Domain](domain_and_state_machines.md#open-questions-and-next-steps) |
| Q-05 | Request/commitment IDs, closure/teardown and atomic updates; cancellation after replacement arrival before resumption; replacement without explicit cancellation in Alert; cancellation during service followed later by a fault; fault after full uplift before release. Eight aircraft states/T1–T10 and D-039 are agreed | [Domain](domain_and_state_machines.md#open-questions-and-next-steps) |
| Q-06 | Which units, calculation, reserves, numerical check lead time and rescheduling after accepted ETD changes are canonical? Immediate checks after late stand arrival are agreed | [Fuel model](fuel_and_service_model.md#open-questions-and-next-steps) |
| Q-07 | What pump-petrol capacity/consumption/reserve, remaining depot visit/skip rules, holding/movement geometry, auxiliary dispensing sequence, rates/quantities and D-017 calculations apply within the agreed simultaneous-arrival order? | [Fuel model](fuel_and_service_model.md#open-questions-and-next-steps) |
| Q-08 | What does “serve next” guarantee? What planning horizon and cost/deadline policy govern D-027 assignment/replenishment comparisons, priorities and queue position? | [Planning](planning_and_operator_workflow.md#open-questions-and-next-steps) |
| Q-09 | Which cuOpt interface/features represent the chosen constraints, and how are infeasible, failed or outdated proposals handled? | [Planning](planning_and_operator_workflow.md#open-questions-and-next-steps) |
| Q-10 | How are D-028 labels and D-029 fuel-statistics metrics/periods bound and projected alongside units/time formatting, and how is schema v1.0 reconciled/versioned? UI design is later work | [USD contract](usd_presentation_contract.md#open-questions-and-next-steps) |
| Q-11 | Which equipment variants, operating profiles, destinations, source editions and asset-swap metadata are verified and selected? | [Reference profiles](reference_profiles.md#open-questions-and-next-steps) |
| Q-12 | Which common EDF/cuOpt fixtures, assignment/horizon policy, concurrent field operation, rotation/recovery cases, complete fleet route/diesel-cost model and deadline/coverage criteria establish benefit on the 3 + 2 + optional 1 schedule? | [Scenarios](scenarios_and_validation.md#open-questions-and-next-steps) |
| Q-13 | Remaining automatic-dispatch, confirmation, override and mode-switching rules; D-040 already requires operator confirmation for ETD changes and D-037 rejects changes after Ready | [Planning](planning_and_operator_workflow.md#operator-acceptance-and-unresolved-result-handling) |
| Q-14 | Which fault events, mobility/repair evidence and durations may the provider model? What immobile recovery and urgent takeover route are in scope? D-031/D-035 already fix withdrawal and mobile return; field-parking bypass remains open | [Maintenance and fault catalogue](domain_and_state_machines.md#q-14-synthetic-fault-catalogue-and-interrupted-service) |

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
