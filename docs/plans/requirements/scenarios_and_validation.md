# Fuel Logistics — Scenarios and Validation

**Status:** Working draft; 3 + 2 + optional 1 schedule and two-refueller rotation
recorded; depot transitions, three-stock guards, waiting/event-driven reassessment
and in-motion assignment recorded; exceptional diesel assistance required;
EDF benchmark and D-046 library prototype specified; D-042 profile adopted,
complete timed fixtures and platform validation open

**Last updated:** 1 November 2026

**Primary decisions:** D-009, D-016, D-017, D-018–D-040;
D-006 schedule clarified on 29 October 2026; resource branch D-015

**Review question:** Q-12; dependencies Q-02, Q-05–Q-09, Q-11, Q-13, Q-15

These are proposed demonstration and validation cases, not executed tests or
evidence of an implemented solver/state workflow. Read the [requirements index](README.md)
for the decision statuses and the [domain model](domain_and_state_machines.md)
for lifecycle rules. The demonstration schedule below supersedes the earlier
description of a three-aircraft minimum. Smaller fixtures may isolate an
individual check; they do not replace the demonstration scope.

## Reduced test fixture

A candidate reduced fixture uses one KrAZ-221 + early TZ-22 refueller, three
aircraft requiring fuel and one aviation-fuel depot. Fixed priorities, demands,
paths and service deadlines can isolate routing and resource-accounting checks.
The exact inputs remain to be selected. This fixture does not exercise D-016's
two-refueller rotation or replace the full demonstration.

## Demonstration baseline

Use **two identical KrAZ-221 + early TZ-22 refuellers** and one Fuel Depot with **three
aircraft initially on stands, two inbound scheduled flights and a possible sixth
unscheduled arrival**. Two vehicles are required, including for D-017 fault
tolerance. Normally one refueller is active on the airfield and the
other is fully replenished on standby at the depot. This is the default
arrangement; D-027 also permits both working on the field. Apply the agreed
[D-016 rotation](domain_and_state_machines.md#two-refueller-rotation-d-016--agreed-in-discussion),
including eligible early relief without interrupting current service. Not all
flight records are simultaneously eligible fuel requests. Use reproducible
inputs with contrasting priorities, demands, locations,
arrival/service-readiness times and deadlines.

| Group | Initial condition | Fuel-service eligibility |
| --- | --- | --- |
| F01–F03 | Three aircraft on stands awaiting departure | Only with a valid request and stand/service readiness |
| F04–F05 | Two inbound scheduled flights | Eligible after actual arrival and ground readiness |
| F06 | Possible unscheduled arrival introduced by an event | Only after necessary flight/demand data, arrival and service readiness are established |

Compare EDF with the proposed cuOpt sequence, using the same travel/service,
demand, fuel and readiness model. Validate aviation-fuel capacity, diesel return
reserve, independent pump-petrol sufficiency, stand readiness and service time
windows for both.

The minimum visible proof should let a recruiter follow a change in schedule
and its effect on service order and tanker route. The technical evidence should
let an engineer reproduce the input, inspect constraints/resource accounting and
compare deadline performance with route/diesel cost. A favourable objective
alone does not prove global optimality or a benefit over EDF.

## Schedule changes and optional sixth flight

ETA/ETD may change over simulation time. A parked aircraft can have no demand;
an inbound flight can be in the planning horizon without appearing on the apron.
Historical meshes do not determine operational fuel profiles.
An existing flight's ETD changes only after operator confirmation under D-040;
a predicted or unaccepted proposed time is not an applied schedule event.

**D-006 — schedule clarified; event details provisional:** Introduce F06 through an event such
as `UnscheduledArrival` or `FlightDiversion`, for example an unavailable intended
destination. It is initially a forecast arrival. Actual stand availability and
ground readiness precede service. Its onward departure, fuel demand and service
window may initially be unknown and need supplied/updated information.

The system evaluates resulting service consequences and replans. Automatic
dispatch is required, while the mode-specific approval/intervention rules remain
open under Q-13; the event does not mandate a human confirmation click in all
modes. Diversion urgency alone does not mean urgent refuelling or highest
priority. A sixth flight record does not require a sixth unique model:
compatible USD assets can be reused.

## Earliest Deadline First comparison

**Purpose:** Establish whether cuOpt improves the refueller's route and estimated
diesel cost while maintaining comparable service/deadline performance. EDF may
already deliver the necessary fuel on time. Its successful dispatch outcome
does not by itself establish an efficient ground route.

The planned reference is a simple **non-preemptive EDF** dispatcher: order
eligible requests by their service deadline, use a declared deterministic
tie-break, and preserve current refuelling. Define how the fuel-service deadline
relates to ETD and any service/departure margin; do not assume the same timestamp
without agreement. The reference must use the same physical feasibility checks
and explicit replenishment assumptions as cuOpt. Review its waiting, skip and
depot-choice rules before fixing the benchmark (Q-12).

Fair comparison requirements:

- Use the same scenario/configuration, revealed schedule and events, initial
  state, fleet size, active/standby roles, graph, capabilities, service durations,
  demands and operating mode. Apply the same D-016 rotation rules to both
  planners, and the same D-017 assistance rules when that exception is exercised.
  Neither receives unannounced future events or free resource resets.
- Include arrival readiness, aviation-fuel capacity, diesel reserve, pump-petrol
  stock/consumption/reserve, timed loading/internal depot travel and committed
  actions in both paths. Apply the same exclusive post occupancy, agreed
  simultaneous-arrival sequence, operation/movement/wait durations and selective
  replenishment rules. Do not weaken EDF's checks to create a solver advantage.
- Run the same fixed event timeline, including the two inbound flights, optional
  F06 and selected ETA/ETD changes. Separate algorithmic replanning behaviour
  from human approval delay by using equivalent mode rules.
- Compare served/unserved demand, completion time and missed deadlines/lateness
  alongside route distance, travel time, depot visits and estimated diesel use.
  Account for both refuellers, including the relieved vehicle's depot return and
  the standby vehicle's departure; report per-vehicle values and fleet totals.
  Report monetary fuel cost only with declared price and consumption assumptions.
  Report pump petrol separately from road diesel and delivered aviation fuel;
  any combined monetary objective requires an explicit cost policy.
- Report any coverage/lateness trade-off explicitly; dropping a job or missing
  more deadlines is not an unqualified fuel-saving improvement. Keep aviation
  fuel delivered separate from the tractor diesel being minimised.
- Measure calculation and integration overhead. With at most six flights, do
  not claim GPU necessity, global optimality or guaranteed superiority over a
  simple policy. Record ties, losses and infeasible cases as well as gains.

| Proposed fixture | What the comparison must reveal |
| --- | --- |
| Straightforward schedule with generous margins | EDF may already be sufficient; report ties without manufacturing a benefit |
| Stands at contrasting distances, with several deadline-feasible orders | Check whether route/diesel cost falls without worse service coverage or lateness |
| Capacity-driven replenishment | Account for depot detours, loading and tractor diesel under the same rules |
| Pump-petrol constraint despite sufficient aviation fuel and diesel | Reject an infeasible service assignment; replenish petrol before depot departure under transition 7 |
| Active/standby relief under D-016 | Check request eligibility and resource guards, preserve current service, and account for both vehicles' travel and replenishment |
| Inbound readiness, revised ETA/ETD and possible F06 | Replan from the actual state while preserving current service and respecting known information |
| Insufficient resources or missing demand data | Expose infeasibility/unknowns instead of fabricating a successful comparison |

The fixed inputs, EDF tie-break/depot rules, objective weights and pass/fail
thresholds remain open. Distance is a cost proxy unless the selected fuel model
supports a diesel estimate; no measured saving or benchmark result exists yet.

## Scheduled transit fuel stop

**D-009 — provisional user proposal:** Add a scheduled aircraft that lands at
Yerevan specifically to refuel and then continue its flight.

- Arrival type stays `Scheduled`. Transit/fuel-stop purpose is a separate
  attribute, not a new aircraft status.
- Supply ETA, ETD, next landing airport from the configured destination set,
  arrival fuel and the calculated requirement for that next leg.
- Check fuel after arrival and actual service readiness. If short, create a
  request; full agreed delivery and D-041 completion accounting precede Ready for an
  unchanged flight; changed/new flights retain D-037 guards.
- For a short transit whose check time passed before arrival, check immediately
  after the aircraft reaches its stand and stops (T2), under
  [D-007](domain_and_state_machines.md#fuel-check-and-request-lifecycle).
- Compete in the same queue based on deadlines and consequences, not an
  automatic priority increase from the Transit label.

## Illustrative replenishment cases

D-042 adopts **22,000 L** as the simulation tank capacity; historical
exact-variant verification remains in [reference profiles](reference_profiles.md#equipment-evidence-register).
The figures below are synthetic input examples, not verified requirements of
specific aircraft or complete route-feasibility proofs.

| Case | Example deliveries | Aviation-fuel arithmetic and intended behaviour |
| --- | --- | --- |
| A | 6,000 L then 8,000 L from a full tanker | Both fit in one load; 8,000 L remain; no immediate capacity-driven depot return |
| B | 13,000 L then 11,000 L | Combined 24,000 L exceeds one load; replenishment is needed between those full deliveries |
| C | 9,000 L then 10,000 L, followed by a new 7,000 L request | First two leave 3,000 L; replenish before the next full delivery |

Each case still needs actual paths, diesel and pump-petrol feasibility,
loading/service times, stand readiness and time-window checks. Partial depletion
alone does not force a depot return. Each initial job fitting one full load is a scope proposal;
larger individual demands need a separate agreed policy.

Assess cases B/C against D-015 and D-016 rotation. D-015 now covers aviation-fuel,
tractor-diesel, pump-petrol and mixed shortages; D-027 requires assignment review
before selecting return or alternative work after current service. The arithmetic
of one vehicle's load does not determine both vehicles' assignments or force an
immediate depot trip. These examples do not define numerical
diesel/petrol guards or D-017's detailed recovery contract. Depot replenishment
is agreed conceptually, while its rates, quantities and timing remain open.
See [resource accounting](fuel_and_service_model.md#depot-and-resource-accounting).

## Depot operations and three-stock checks

Use D-042's [adopted profile](reference_profiles.md#adopted-simulation-profile-d-042)
and D-043's [depot transitions](domain_and_state_machines.md#depot-loading-and-departure).
These are required checks for future fixtures, not executed runtime tests.

| Case | Required behaviour |
| --- | --- |
| Depot arrival | Select the required post/repair destination; no instantaneous fill; enter RefillTank/RefillServiceFuel only at the actual available post |
| Multiple required visits | Sequential timed replenishment with Repositioning between posts; preserve unfinished operations and task |
| Assignment needs only aviation fuel | Skip unnecessary auxiliary visit when diesel/petrol already cover work/reserves |
| Completed replenishment and valid assignment | Release equipment and depart directly ToAircraft with all guards; no parking/Standby requirement |
| Assignment during depot parking movement (D-030) | After required operations, redirect Repositioning -> ToAircraft from actual position without parking arrival/stop/Standby |
| Failed guard or unfinished depot operation | Do not dispatch merely because a request appeared or the state is Repositioning |
| No assigned task | Fill all three stocks to 100%, complete readiness, move to reserve parking and enter Standby only on arrival/stop |
| Depot internal movement | Preserve elapsed time/position/distance; debit zero diesel by D-042 simplification |
| Field waiting arrival | Enter Idle, not tanker Parked or depot Standby |
| Simultaneous arrivals | First to aviation, second to auxiliary; one vehicle per post; preserve first completion/departure-or-parking before second aviation visit |
| Unequal post durations | Account for post clearance, holding and movement; no overlapping occupancy or instantaneous swaps |
| Partial-stock assigned departure | Allowed when all guards pass; must not be labelled full-ready Standby |
| Repair completed | No stock reset; replenish as required, or feasible direct assignment; no-task reserve readiness requires all stocks full and parking arrival |

Record aviation fuel, diesel and petrol separately. Pumping consumes petrol,
not diesel; other counted travel uses 0.401 L/km, while internal depot travel
uses zero diesel. Depot loading equipment/petrol use and additional setup/idle
allowances remain open. A fill of one stock never resets the other two.
Preserve separate dispensers and D-018's coordination sequence; exact rates,
auxiliary dispensing order, tie-breaking and holding geometry remain Q-07.

### Adopted-profile arithmetic checks

Check 41.25 L diesel and 11.25 L petrol protected reserves. A pure 22,000 L
transfer takes 22 minutes and 5.5 L petrol, before additional service time or any
separately selected extra engine use. Compare before/after balances; an otherwise
identical road leg adds 0.401 L diesel per km, while internal depot movement
adds elapsed time without changing diesel. Include just-sufficient and below-
reserve assignments using a declared numerical tolerance; guards use exact
quantities rather than rounded analogue indications. No measured equipment
performance is established by these arithmetic checks.

## Transition 4: all fuel-shortage returns

For [D-015](domain_and_state_machines.md#transition-4-refueling-to-todepot),
define a fixture for each non-empty subset of insufficient stocks: aviation
fuel only, tractor diesel only, pump petrol only, each of the three pairs, and
all three. Every case completes current service, disconnects equipment and
reviews other assignments under D-027 instead of taking the infeasible candidate.
For each shortage case test both outcomes: a selected feasible alternative
retains the vehicle on the field, while a selected replenishment branch returns
directly to depot. Preserve all guards; a failed candidate alone must not force
an unconditional return.

For the normal diesel-insufficient-for-assignment fixture, retain enough diesel
for the protected depot return. Separately, inject an unexpected failure of that
return condition and exercise D-017 assistance before travel. The behaviour and D-042 base profile are agreed; transfer equipment/rates
and detailed recovery traces still need the Q-03/Q-07 contract. Do not interpret a diesel shortage for the next job as
permission to drive with insufficient diesel for the depot.

## Waiting, in-motion assignment and schedule events

Validate [D-022–D-025](domain_and_state_machines.md#waiting-reassignment-and-proactive-replenishment)
with a common event timeline and resource checks for EDF and cuOpt:

| Case | Required behaviour |
| --- | --- |
| Waiting-location arrival | Enter Idle only on actual arrival and stop in the assigned permitted area with no service assignment; record actual location and start Staging Point dwell time |
| Assignment during ordinary repositioning to Staging Point or Main Parking | If all guards pass, redirect Repositioning to ToAircraft immediately; the D-026 replacement trip from depot is excluded and must first reach field parking |
| Request received but assignment infeasible | Do not redirect to the aircraft merely because a request appeared; apply replenishment/relief rules for insufficient resources |
| Exactly one hour at Staging Point | The more-than-one-hour dwell condition alone has not yet been met; schedule/demand events still trigger assessment |
| More than one hour, check window opening in the next hour | Remain Idle at Staging Point, awaiting a possible assignment |
| More than one hour, no such window and no assignment | Move Idle to Repositioning towards Main Parking; enter Idle on actual arrival |
| Expected check moved from 40 minutes away to three hours away | Reassess on the schedule change; if no other check opens in the next hour and dwell exceeds one hour, leave for Main Parking |
| Check opens but produces no request | Reassess the following hour rather than waiting indefinitely for a request that was not created |
| New or changed arrival/departure event before one-hour dwell | Reassess immediately; the dwell gate does not delay a feasible assignment or proactive replenishment |
| Waiting vehicle carries 10 tonnes, new diversion/transit uplift is 12 tonnes | Review other work first under D-027; retain it for a selected feasible 8-tonne job while the second handles 12 tonnes, or use the reviewed replenishment branch; apply D-026 field parking and coverage |
| Replacement before the first reaches depot | Replacement movement to field parking does not wait for a window or request; it can overlap the first vehicle's return, subject to uninterrupted physical field coverage |
| Future flight without an eligible request | Send the replacement to field parking to maintain coverage; only its subsequent aircraft-bound departure requires the open check window, actual request and all guards |
| Request becomes eligible during the replacement's depot-to-parking trip | Complete actual field-parking arrival and stop before dispatch to the aircraft; ordinary D-023 redirection must not skip this step |
| Coverage handover | Track actual field boundary and readiness times; at least one ready refueller must be physically present throughout covered operation |
| First vehicle already left, replacement still outside the field | Detect and report a coverage gap; a dispatch command is not proof that the invariant held |
| Both vehicles at depot | D-018's post sequence can be exercised, but this configuration cannot pass normal field-coverage validation with only two refuellers |

Declare mass/volume units and any density conversion for the 10/12-tonne fixture.
The returning vehicle's travel and depot service take time and consume resources;
neither vehicle teleports or gains fuel through reassignment. Fix same-time
schedule/arrival/assignment event ordering under Q-02 before executable traces
are finalised. D-030/D-043 retain assignment during parking-bound Repositioning
after depot operations. D-031/D-035 fix Maintenance/fault withdrawal; D-044 selects
events while Q-14 retains exact timing, repair evidence and recovery detail.
Keep D-026's replacement field-parking
arrival distinct from the depot parking that D-030 allows bypassing.

## Joint assignment and fleet-cost comparison (D-027)

Use the [agreed assessment sequence](planning_and_operator_workflow.md#assignment-review-and-two-refueller-field-operation-d-027)
for the synthetic 12/10/8-tonne case. Establish complete alternative plans for
both vehicles using the same revealed demand and event horizon:

| Fixture | Evidence required |
| --- | --- |
| Nearby feasible 8-tonne request | First retains suitable work, second handles the 12-tonne request; both may operate on the field; compare against replenishing/reassigning |
| Distant 8-tonne request | Include approach and later depot travel; remaining fuel fitting the request must not be reported as proof of savings |
| Smaller request conflicts with urgent service | Reject or expose a timing/coverage trade-off rather than claiming an unqualified diesel saving |
| No selected feasible alternative, or replenishment plan preferred | Use the applicable protected depot-return branch and preserve physical field coverage |
| Both vehicles on field | Permit the plan; do not force either to depot solely to restore one Active and one depot Standby |
| EDF and cuOpt comparison | Apply the same assignment-review options, two-vehicle permission, service/resource guards and field coverage; declare EDF's assignment/tie-break/replenishment policy before measuring |

Report both vehicles' routes and total distance, estimated diesel use/cost,
service deadlines/coverage, depot visits and operation durations, including the
second vehicle's field-parking leg and each later depot return. Keep pump petrol
and delivered aviation fuel separate. The objective is fleet operating-cost
reduction through joint assignment, service order and replenishment decisions;
measure the result against EDF and report ties or regressions. Use D-042's
refueller profile and D-045's one/two-request horizon; aircraft/route fixtures,
request selection, objective policy and pass/fail thresholds still need definition.

## Fault tolerance and exceptional diesel assistance

The full demonstration requires two refuellers, and [D-017](domain_and_state_machines.md#diesel-reserve-and-exceptional-assistance-d-017)
requires the following behaviour to be validated once its detailed contract exists:

- **Normal operation:** Fuel checks reject assignments that would leave a
  tractor unable to reach the depot with reserve. Normal routing and rotation
  must not rely on rescue transfers to complete otherwise infeasible work.
- **Exceptional diesel shortage:** Introduce an explicit event that leaves one
  tractor unable to reach the depot. Send the second to supply enough diesel
  from its own tractor tank for the recipient's return; preserve the donor's
  own approach/return feasibility and account for both vehicles' fuel and time.
- **Transfer accounting:** Verify recipient capacity, donor debit, recipient
  credit, any separately modelled consumption and elapsed transfer duration.
  Neither vehicle is teleported or given an implicit refill.
- **Recipient waiting:** Enter Alert at actual location and exclude it from
  aircraft assignments. If the donor is busy, wait for its current committed
  work to finish without interrupting aircraft service.
- **Assistance approach and transfer:** Use donor Repositioning to reach the
  recipient, then Alert Refueling for both with explicit tractor-diesel operation and
  participant roles. Assistance does not add aviation-fuel delivery or change
  an aircraft's state.
- **After transfer:** Recipient returns directly to depot with restored reserve.
  Recheck all three donor stocks before aircraft service, field waiting or depot
  replenishment. If donor fuel preparation is needed before assistance, the
  recipient continues waiting in Alert until it can approach and donate.
- **Donor depot preparation:** After current work, make approach/donation/both
  protected returns exceed the donor's remaining diesel while its own depot
  trip remains feasible. Expect ToDepot, timed D-043 replenishment of tractor diesel and
  trailer aviation fuel, independent pump-petrol check/top-up, then direct
  Repositioning to the recipient after completed operations and repeated checks.
  The recipient stays Alert; no parking/Standby detour or intervening aircraft job.
- **Scope:** Assistance locations are reachable over the modelled traversable
  airfield network. Do not add blocked-road/destruction scenarios. Concurrent
  technical faults remain Q-14, not an implicit D-017 impossibility branch.

The D-032 state sequence is agreed; exact event inputs, equipment, transfer
rates/amounts and records need definition before executable fixtures. Record normal-operation
prevention separately from exceptional recovery; a successful rescue does not
excuse a route that violated the normal diesel-return guard.

## Vehicle and aircraft label validation (D-028)

Validate the [visual-label requirements](usd_presentation_contract.md#vehicle-and-aircraft-visual-labels-d-028)
in a later Omniverse viewport implementation:

| Case | Required visual evidence |
| --- | --- |
| Near view of a refueller | Tractor label shows its name/current state and diesel bar; tank label separately shows aviation fuel and pump petrol |
| Object movement and two field refuellers | Labels follow the correct tractor/tank/aircraft; identities and fuel stocks do not swap between vehicles |
| Distant refueller label grouping, if used | One combined label retains name/state and three distinguishable stocks; zooming back in restores separate labels |
| Aircraft label | Flight identity, agreed departure date/time, current aircraft state and onboard-fuel bar match current state; only an accepted ETD change replaces the scheduled label, while prediction/actual times stay distinct |
| Independent stock changes | Diesel consumption, aviation-fuel transfer and pump-petrol consumption update only their respective balances; bar fill uses the corresponding tank capacity |
| Missing display data | Unknown stock/capacity/date-time is distinguishable from an empty/full tank or a known departure; date/time zone is explicit |

These are requirements for future visual validation, not evidence that a HUD or
USD projection has been implemented. Finalise layout/zoom thresholds, bindings,
units/capacities and update contracts under Q-10 before fixing visual fixtures.

## Fuel statistics validation (D-029)

After [panel metric definitions](usd_presentation_contract.md#fuel-statistics-panel-d-029)
are agreed, validate issued fuel against actual timed transfers for the chosen
reporting period, and the in-progress/awaiting figures against the same current
request/accounting snapshot. Cover both refuellers working concurrently, partial
delivery, completion, cancellation and reassignment without duplicated requests
or transfers. Verify period boundaries/units and keep tractor diesel/pump petrol
out of aircraft-fuel delivery totals. Missing data must not appear as a known
zero. This defines future data validation; panel/UI design is deferred.

## Maintenance and technical-fault scenarios (D-031/D-035)

D-044's [selected catalogue](domain_and_state_machines.md#q-14-selected-fault-catalogue-and-interrupted-service-d-044)
defines the adopted event types. Timed fixtures, repair evidence and runtime
validation are still pending.

| Check | Required evidence |
| --- | --- |
| Planned withdrawal | Finish current service, coordinate coverage, travel to repair area and enter Maintenance on arrival |
| Tyre puncture in Repositioning | Preserve the selected mobility assumption, expose unavailability, replan actual task and return ToDepot for wheel replacement; do not extend event to other motion states |
| Pump failure during Refueling | Stop transfer immediately; aircraft Alert, original request/remainder retained; mobile vehicle returns unavailable for Maintenance |
| Pump-petrol exhaustion during Refueling | Same interruption/remainder preservation; return for replenishment, not pump repair; normal assignment guards must prevent the shortage |
| Replacement arrival versus service start | Resume aircraft Refueling only after actual arrival plus checks/setup/start; transfer only valid remainder |
| Service-time diesel exhaustion | Not generated: tractor engine off and diesel use zero; obsolete continue-service donor branches excluded |
| Exceptional protected-return diesel deficit | Retain D-017/D-032 assistance separately; do not infer its removal or approve additional movement-exhaustion events |
| Repair without implicit refill | Use actual stocks and D-043 post/assignment/Standby outcomes; repair alone does not fill tanks |
| Urgent takeover | Validate actual location, stocks, commitments and travel; any field-parking bypass remains open |
| Missing replacement/coverage loss | Report unmet service and actual gap; do not invent another ready vehicle |

The original request and every physical transfer remain in history through
interruptions and executor changes. Apply D-039 cancellation before replacement
arrival; stale resumed-service commands cannot revive the cancelled remainder.
Select concrete durations later, with pump repair longer than ordinary petrol
replenishment in the chosen qualitative profile. No probabilities, towing or
additional immobile technical-recovery model are adopted.

## Request cancellation, revision and committed service (D-033/D-034)

Validate [pre-service review](domain_and_state_machines.md#request-cancellation-and-revision-d-033)
and [started-service commitment](domain_and_state_machines.md#started-service-commitment-and-cancelled-flights-d-034)
against the same scenario events for EDF and cuOpt:

| Case | Required behaviour |
| --- | --- |
| Explicit cancellation while ToAircraft | Cancel unstarted demand, preserve actual location/travel/time/fuel, review fleet assignments; redirect to feasible service, waiting or selected replenishment without an automatic depot trip |
| Request quantity/timing revised before start | Recheck all three fuel and route/readiness/time guards; retain the assignment only if feasible and selected |
| Departure time changed alone | Recalculate/replan as needed; do not automatically cancel the request |
| Manual cancellation while aircraft Refueling is running | UI prevents interruption; a late/stale command is rejected by execution; complete the original service conditions |
| External withdrawal or revised quantity during service | Preserve the original started-service commitment and actual fuel accounting; do not release the tanker or change the active delivery target |
| Flight cancelled during refuelling | Finish original delivery; release tanker under normal branching; aircraft remains fuelled and Parked without an active departure |
| Provider assigns a new flight to that aircraft | Retain the physical aircraft and its actual onboard fuel; apply new flight demand/check rules without a fuel reset, duplicate aircraft or repeated original uplift |
| Detected technical fault during committed service | D-035 stops aircraft work despite the original-condition commitment; preserve partial delivery/unserved demand and use the appropriate mobile return or scoped Alert recovery |
| Flight cancelled in Alert before replacement arrival | T10 returns aircraft to Parked; cancel old undelivered commitment, retain actual onboard/issued fuel, release replacement for existing actual-position review |
| Stale replacement-start action after that cancellation | Reject it; do not resume the cancelled request, transfer fuel or restore its pending quantity |
| New flight after cancelled interrupted service | For an 8-tonne operation with 3 delivered and 5 cancelled, retain 3 onboard; a new 6-tonne onboard requirement creates 3 tonnes uplift at its check window, assuming no intervening burn |

Specify original and revised request conditions and actual transfers so the
comparison can prove the started target was retained. Verify D-029 totals:
pre-service cancellation removes pending demand, ongoing committed delivery
remains current work, and actual issued fuel is retained after flight cancellation.
D-039 cancellation after interruption removes only the outstanding obligation
from pending work; it neither reverses issued fuel nor marks the remainder delivered.
Simultaneous start/cancellation ordering, remaining request enums and release/
identity boundaries remain Q-02/Q-05. D-041 fixes normal completion; this section
records future validation, not test results.

## Aircraft Alert, Ready commitment and state vocabulary (D-036–D-038)

The aircraft requirements review was agreed on 31 October. These are future
validation requirements, not executed tests or implemented behaviour:

| Case | Expected behaviour |
| --- | --- |
| Tanker fault interrupts aircraft service | Aircraft Refueling to Alert, retaining actual stock and remaining need; faulty tanker withdraws under D-035 |
| Replacement resumes service | Actual arrival/setup/checks permit aircraft Alert to Refueling; no reset or duplicate delivery |
| Changed/new flight after earlier fuelling | At the current flight's check window use retained stock: sufficient means Ready; otherwise remain Parked with additional demand |
| Flight cancelled while Parked | Remain Parked without an active flight; cancel unstarted demand and await a new flight/check |
| Flight cancelled during running Refueling | Finish the original committed delivery, then Parked; use D-039 only for the separately reviewed interrupted-service case |
| Flight cancelled in Alert before replacement arrival | Alert to Parked; old remainder cancelled and delivered fuel retained |
| Aircraft enters Ready | Provider generates no readiness reversal; execution rejects equivalent operator/event commands; actual movement then zone exit follows Ready -> Departing -> Departed |
| Tractor-diesel assistance | Both participants use Alert Refueling, while aircraft service uses Refueling; Repositioning remains general transit and task roles/destinations remain explicit |
| Faulty tanker returning ToDepot | It remains technically unavailable for assignments/coverage despite being mobile |

### Aircraft transition and timing fixtures

| Case | Required behaviour |
| --- | --- |
| Initial three aircraft on stands | Initialise Parked without fabricated arrival events; retain physical IDs and onboard stock |
| ETA reached without actual approach event | Remain Expected; only actual approach starts Inbound |
| Landed but still taxiing | Remain Inbound until assigned stand arrival and full stop |
| First check finds sufficient fuel | Parked to Ready without a tanker request |
| Late stand arrival after check time | Check immediately after T2; no wait for a future opening |
| Delivery for an earlier flight completes before new flight's window | Parked despite sufficient stock; at the new window check retained fuel and enter Ready only if sufficient |
| Current flight's window opens during running service | Do not interrupt committed delivery or duplicate its demand; evaluate current-flight sufficiency after completion |
| Full agreed operation finishes but current-flight fuel is insufficient | Parked with additional calculated demand under its eligibility rules; retain the earlier actual delivery |
| Refueller arrives but service has not begun | No premature Parked/Alert to Refueling; require actual start and guards |
| Ready but stationary | Remain Ready; do not infer Departing from ETD alone |
| Departing but not airborne/outside zone | Remain Departing; Departed requires both exit conditions |
| Cancellation/replacement command after Ready | Execution rejects it, including a stale command; UI restriction alone is insufficient |
| Delay risk during Alert | Warning remains separate from state and includes replacement feasibility/timing |
| Keep Schedule with no feasible on-time plan | Preserve ETD and infeasibility/warning, not a fabricated feasible result |
| Proposed or manually supplied new ETD | Operator confirms the change; validate against current state and replan without interrupting running service |
| ETD 14:00, actual readiness 14:10 | Preserve scheduled 14:00 unless changed by the operator; continue actual departure without waiting for 14:00, backdating or hiding lateness |

Use identical revealed events, operator actions and time-measurement conventions
for EDF/cuOpt. D-041 fixes normal completion; remaining release/identity boundaries and simultaneous cancellation/resumption
ordering, cancellation after replacement arrival but before service, flight
replacement without explicit cancellation while Alert, cancellation during
running service followed later by a fault, and exact ETD/actual
measurement points remain open. Do not invent their expected outcomes as tests.

## Validation requirements from the source proposal

| Area | Intended evidence | Still to define |
| --- | --- | --- |
| Repeatability | Same scenario ID, seed, configuration and accepted-action sequence reproduce events, demands and expected behaviour | Event/time contract, replay format and solver determinism limits |
| Conservation | Consistent aircraft/tanker aviation-fuel transfers; explicit depot reloads; independent tractor diesel and pump petrol; conserved diesel assistance | Aircraft units/density, tolerances, depot/setup rates and remaining operating-mode assumptions; D-042 inputs adopted |
| State integrity | No negative/over-capacity stock, service before readiness or after departure, or teleportation on replanning | Complete lifecycle/transition guards and failure outcomes |
| Replanning trace | Original plan, trigger, revised priorities, proposal, feasibility, active mode and automatic/operator authorisation | Result IDs/revisions, mode rules and stale-result handling |
| Measurable comparison | EDF versus cuOpt: coverage/lateness, distance/travel time, estimated diesel/cost, depot visits, delivered aviation fuel and calculation overhead | Common fixtures, EDF rules, fuel-cost assumptions, objective weights and numerical thresholds |
| Unknown inputs | Missing destination, operating profile, reserve policy or readiness stays visible as unknown | Diagnostic representation and operator feedback |

Do not display a proposal as executed before the applicable automatic/operator
authorisation and actual execution. Do not fill an operational
data gap from a historical model name. Keep planned, accepted and executed
outcomes distinguishable in the replay and presentation.

## Normal completion, history and bounded planning checks

D-041 requires a normal unchanged-flight trace: initial check, calculated deficit,
assignment, actual balanced transfers, full agreed delivery, Completed and Ready.
No second same-flight demand calculation occurs. Keep equipment release and
aircraft movement guards separate; partial delivery cannot close Completed.
The tanker may directly serve another feasible aircraft, wait via Repositioning
then Idle, or use selected depot replenishment. Archive completed/cancelled
requests and interruptions/executor changes; no automatic duplicate request.

D-045 requires fixtures with one and two selected known requests plus a larger
known queue. Retain excluded-from-solve requests/deadlines, use the same reveal
and selection rules for EDF/cuOpt, and prohibit access to hidden future events.
Known schedule windows still drive D-024 waiting and D-026 request-independent
coverage. Fix the nearest/selection/spillover policy before executing these
fixtures; do not assume a solver benefit at this horizon.

## Library-integration proof (D-046)

The [agreed runtime direction](architecture.md#standalone-omniverse-library-direction-d-046)
requires a small project-specific integration prototype before final stack
adoption. `ovstage` and `ovrtx` are candidates, not installed/validated
dependencies. These criteria specify future evidence, not completed checks.

| Check | Required observable evidence |
| --- | --- |
| Platform bring-up | Record actual SDK/package versions, Windows, Python, GPU and driver, setup steps and compatibility outcome. Run the library path from our host rather than a Kit application. |
| Real project content | Load a real Houdini-exported project USD asset and render it through the candidate library. Prefer a TZ-22 when available; another actual project asset can isolate the first check. Retain asset identity, input and output. |
| Host-owned updates | Move a visible object from application-owned state and time. Pair a state/update trace with rendered output; exercise fuel-state projection as its mapping becomes available without claiming an unfinished gauge/HUD. |
| Repeatable lifecycle | Initialise, step, pause, reset and shut down, then repeat the cycle. Show the expected initial scene/state and absence of stale handlers or updates; record failures and cleanup limits. |
| Presentation scope and cost | Record render/update timing and assess materials, camera/viewport interaction, route display, labels, HUD and operator-command work still needed. Declare what the prototype actually demonstrates. |

Do not require a new UI framework merely to obtain the first rendered frame.
Do assess the eventual operator workflow before adopting the stack. Importing
`usd-core`, calling cuOpt, opening a vendor sample or running Kit headlessly
does not independently satisfy the project-specific library-integration proof.
Additional physics libraries require a justified physical-simulation task.

A renderer prototype does not validate logistics, the full fleet scenario,
EDF/cuOpt benefit or solver determinism. Record its outcome under Q-15, preserve
Q-02/Q-10/Q-13's open contracts, then prepare the platform ADR and align the root
README/dependency baseline before claiming an adopted or working runtime.

## Source boundary and synthetic position checks (D-047)

Check the [input-source seam](architecture.md#input-sources-and-synthetic-position-telemetry-d-047)
when implementing the provider and presentation adapters:

- The synthetic provider conforms to the normalised input contract; consumers
  do not depend on generator-specific packet fields.
- Future real-data adapter interfaces/placeholders are identifiable; real-data
  adapters, airport bridges and live connections are outside implementation scope.
  No real data ingestion or recorded GPS feed is claimed.
- Synthetic GPS-like samples and simulated movement describe the same entity,
  time and location. Applying input does not duplicate travel or fuel changes.
- Scenario events and source-like observations use the common input boundary;
  each position sample identifies its source state revision and measurement
  time. Reprocessing an observation does not move the object again, duplicate a
  transfer or create a second trajectory. Samples derived from simulation are
  not independent evidence that the simulated position is correct.
- Exercise delayed, duplicate and missing observations against the selected
  policy; retain provenance and expose the outcome without silently replacing
  newer state with an older position. The policy remains Q-01/Q-02.
- The declared coordinate mapping locates vehicles, aircraft and infrastructure
  consistently in the ground graph and Y-up/metre scene.
- Verify geographic/local reference point, compass orientation, units, height
  reference and known north/east displacements against independent expected
  coordinates within declared tolerances. Exercise round-trip conversion and
  non-origin reference points, and retain the reference version on historical
  samples. A self-consistent round trip alone does not prove geographic accuracy.
- Handle invalid/missing references and incompatible revisions under the
  selected policy; changes must not silently reinterpret prior observations,
  active positions or paths.

These are future contract checks, not evidence that an adapter or geolocation
implementation already exists. Representation and event application still need
their agreed field/time/spatial contracts.

## Open questions and next steps

- **Q-12:** Freeze the 3 + 2 + optional 1 demonstration fixture with two
  refuellers, D-016 rotation cases, EDF deadline/tie-break/depot rules, common
  fleet cost model, objective and metrics. Choose pass/fail thresholds without
  promising an arbitrary percentage improvement or a cuOpt win.
- Define D-017/D-032 prevention, busy-donor waiting, fuel preparation, typed
  transfer and donor/recipient departure fixtures as equipment/rates/records
  are specified; respect the traversable-airfield scenario scope.
- Parameterise D-044's selected fault/shortage cases, actual partial demand and
  repair-completion evidence; resolve the urgent replacement route before fixing
  its expected trace. Do not reintroduce excluded service-time diesel exhaustion.
- Specify D-022–D-025 waiting/assignment/relief fixtures, including the strict
  one-hour dwell boundary, one-hour look-ahead, schedule change, no-request
  fuel check, actual-position rerouting and known-demand proactive return.
- Specify D-026 replacement-to-parking, window-independent coverage dispatch,
  required parking arrival, physical coverage timing and loss-of-coverage
  fixtures. Keep handover coverage distinct from aircraft-service eligibility.
- Specify D-027 alternative-assignment, concurrent two-vehicle field operation
  and complete-fleet-cost fixtures, with both beneficial and unfavourable smaller
  jobs; fix EDF comparison and D-045 selection rules before measuring savings.
- Fix D-018–D-021 depot/three-stock fixture values and expected traces, including
  independent pump-petrol failure, timed internal movement, partial-stock direct
  departure and the agreed simultaneous-arrival sequence with exclusive post
  occupancy, separate auxiliary dispensers and unequal operation durations.
- For every selected scenario, specify initial state, profile IDs, timed events,
  operator actions, expected transitions, resource accounting and diagnostics.
- Finalise timed arrivals for F04/F05, optional F06 and the transit scenario as
  their lifecycle and fuel assumptions are agreed. Use reduced fixtures only for
  isolated checks, keeping the demonstration schedule intact.
- **Q-13 dependency:** Define the execution mode and operator-intervention rules
  so dispatch behaviour and approval delays are comparable between planners.
- **Q-15 dependency:** Evaluate the D-046 prototype and retain its environment,
  outputs, lifecycle trace and presentation limitations before final stack adoption.
- Review missing-data and infeasible-demand cases, conservation, current-service
  preservation and proposal rejection/outdated results alongside success cases.
- Verify behaviour and presentation separately; later tests or viewport checks
  should record evidence against approved requirements, not against these drafts
  as if their unresolved choices were settled.
