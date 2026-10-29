# Fuel Logistics — Scenarios and Validation

**Status:** Working draft; 3 + 2 + optional 1 schedule and two-refueller rotation
recorded; exceptional diesel assistance required; EDF benchmark specified;
fixtures, cost model and numerical thresholds open

**Last updated:** 29 October 2026

**Primary decisions:** D-009, D-016, D-017; D-006 schedule clarified on 29 October 2026;
resource branch D-015

**Review question:** Q-12; dependencies Q-02, Q-05–Q-09, Q-11, Q-13

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
other is fully replenished on standby at the depot. Apply the agreed
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
reserve, stand readiness and service time windows for both.

The minimum visible proof should let a recruiter follow a change in schedule
and its effect on service order and tanker route. The technical evidence should
let an engineer reproduce the input, inspect constraints/resource accounting and
compare deadline performance with route/diesel cost. A favourable objective
alone does not prove global optimality or a benefit over EDF.

## Schedule changes and optional sixth flight

ETA/ETD may change over simulation time. A parked aircraft can have no demand;
an inbound flight can be in the planning horizon without appearing on the apron.
Historical meshes do not determine operational fuel profiles.

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
- Include arrival readiness, aviation-fuel capacity, diesel reserve, loading
  time and committed actions in both paths. Do not weaken EDF's resource checks
  merely to create a solver advantage.
- Run the same fixed event timeline, including the two inbound flights, optional
  F06 and selected ETA/ETD changes. Separate algorithmic replanning behaviour
  from human approval delay by using equivalent mode rules.
- Compare served/unserved demand, completion time and missed deadlines/lateness
  alongside route distance, travel time, depot visits and estimated diesel use.
  Account for both refuellers, including the relieved vehicle's depot return and
  the standby vehicle's departure; report per-vehicle values and fleet totals.
  Report monetary fuel cost only with declared price and consumption assumptions.
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
  request; actual refuelling and a sufficient final check precede `Ready`.
- For a short transit, do not mechanically wait for `ETD - lead_time` if that
  time already passed before arrival. The final check-trigger rule is open under
  [D-007](domain_and_state_machines.md#fuel-check-and-request-lifecycle).
- Compete in the same queue based on deadlines and consequences, not an
  automatic priority increase from the Transit label.

## Illustrative replenishment cases

The original proposal uses a candidate **22,000 L** tanker capacity, subject to
exact-variant verification in [reference profiles](reference_profiles.md#equipment-evidence-register).
The figures below are synthetic input examples, not verified requirements of
specific aircraft or complete route-feasibility proofs.

| Case | Example deliveries | Aviation-fuel arithmetic and intended behaviour |
| --- | --- | --- |
| A | 6,000 L then 8,000 L from a full tanker | Both fit in one load; 8,000 L remain; no immediate capacity-driven depot return |
| B | 13,000 L then 11,000 L | Combined 24,000 L exceeds one load; replenishment is needed between those full deliveries |
| C | 9,000 L then 10,000 L, followed by a new 7,000 L request | First two leave 3,000 L; replenish before the next full delivery |

Each case still needs actual paths, diesel feasibility, loading/service times,
stand readiness and time-window checks. Partial depletion alone does not force
a depot return. Each initial job fitting one full load is a scope proposal;
larger individual demands need a separate agreed policy.

Assess cases B/C against the agreed D-015 aviation-fuel shortage branch and
D-016 rotation; their arithmetic does not settle the remaining diesel-only,
combined-shortage transitions or the detailed D-017 recovery contract. Depot
diesel replenishment is now agreed conceptually under D-016, while its rates,
quantities and timing remain open.
See [resource accounting](fuel_and_service_model.md#depot-and-resource-accounting).

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
- **Assistance unavailable or infeasible:** Keep the unmet recovery need visible;
  do not dispatch an under-fuelled donor or invent an unagreed alternative.
  Detailed failure handling, scheduling alongside aircraft service and the
  recovery lifecycle remain open under Q-03, Q-05, Q-07 and Q-13.

Exact event inputs, equipment, transfer rates and expected lifecycle steps need
agreement before these cases become executable fixtures. Record normal-operation
prevention separately from exceptional recovery; a successful rescue does not
excuse a route that violated the normal diesel-return guard.

## Validation requirements from the source proposal

| Area | Intended evidence | Still to define |
| --- | --- | --- |
| Repeatability | Same scenario ID, seed, configuration and accepted-action sequence reproduce events, demands and expected behaviour | Event/time contract, replay format and solver determinism limits |
| Conservation | Consistent aircraft/tanker aviation-fuel transfers; explicit depot reloads; separate diesel accounting | Units, density, tolerances, rates and operating-mode assumptions |
| State integrity | No negative/over-capacity stock, service before readiness or after departure, or teleportation on replanning | Complete lifecycle/transition guards and failure outcomes |
| Replanning trace | Original plan, trigger, revised priorities, proposal, feasibility, active mode and automatic/operator authorisation | Result IDs/revisions, mode rules and stale-result handling |
| Measurable comparison | EDF versus cuOpt: coverage/lateness, distance/travel time, estimated diesel/cost, depot visits, delivered aviation fuel and calculation overhead | Common fixtures, EDF rules, fuel-cost assumptions, objective weights and numerical thresholds |
| Unknown inputs | Missing destination, operating profile, reserve policy or readiness stays visible as unknown | Diagnostic representation and operator feedback |

Do not display a proposal as executed before the applicable automatic/operator
authorisation and actual execution. Do not fill an operational
data gap from a historical model name. Keep planned, accepted and executed
outcomes distinguishable in the replay and presentation.

## Open questions and next steps

- **Q-12:** Freeze the 3 + 2 + optional 1 demonstration fixture with two
  refuellers, D-016 rotation cases, EDF deadline/tie-break/depot rules, common
  fleet cost model, objective and metrics. Choose pass/fail thresholds without
  promising an arbitrary percentage improvement or a cuOpt win.
- Define D-017 prevention, assistance and donor-unavailable fixtures as its
  lifecycle, transfer equipment and fuel assumptions are agreed.
- For every selected scenario, specify initial state, profile IDs, timed events,
  operator actions, expected transitions, resource accounting and diagnostics.
- Finalise timed arrivals for F04/F05, optional F06 and the transit scenario as
  their lifecycle and fuel assumptions are agreed. Use reduced fixtures only for
  isolated checks, keeping the demonstration schedule intact.
- **Q-13 dependency:** Define the execution mode and operator-intervention rules
  so dispatch behaviour and approval delays are comparable between planners.
- Review missing-data and infeasible-demand cases, conservation, current-service
  preservation and proposal rejection/outdated results alongside success cases.
- Verify behaviour and presentation separately; later tests or viewport checks
  should record evidence against approved requirements, not against these drafts
  as if their unresolved choices were settled.
