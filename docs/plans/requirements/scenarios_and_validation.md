# Fuel Logistics — Scenarios and Validation

**Status:** Working draft; scenarios and validation goals collected; fixed
fixtures, comparison routes and numerical acceptance thresholds open

**Last updated:** 29 October 2026

**Primary decision:** D-009; related provisional scenario D-006

**Review question:** Q-12; dependencies Q-02, Q-05–Q-09, Q-11

These are proposed demonstration and validation cases, not executed tests or
evidence of an implemented solver/state workflow. Read the [requirements index](README.md)
for the decision statuses and the [domain model](domain_and_state_machines.md)
for lifecycle rules. The three-aircraft baseline and five-plus-one scenario are
successive scopes, not competing fleet requirements.

## Minimum reproducible baseline

Start with one KrAZ-221 + early TZ-22 refueller, three aircraft requiring fuel
and one aviation-fuel depot as a **minimal test fixture**, not the final
fleet size. The agreed extended scenario uses two identical refuellers with
alternating active/standby duty (D-016). Use fixed inputs with contrasting priorities,
demands and service deadlines. The exact fixture remains to be selected.

Compare a fixed reference service sequence with a proposed sequence. Record
travel/service time, demand coverage and any depot visit; validate aviation-fuel
capacity, diesel return reserve, stand readiness and service time windows.

The minimum visible proof should let a recruiter follow a change in schedule
and its effect on service order. The technical evidence should let an engineer
reproduce the input, inspect constraints/resource accounting and assess the
result against the reference. A favourable objective alone does not prove
global optimality or useful decision support.

## Expanded schedule and disruption

The proposed expanded scenario has **five scheduled flight records**, not five
simultaneously ready fuel requests. Initial-condition examples:

| Group | Initial condition | Fuel-service eligibility |
| --- | --- | --- |
| F01–F03 | Three aircraft on stands awaiting departure | Only with a valid request and stand/service readiness |
| F04 | Expected scheduled arrival | Eligible after actual arrival and ground readiness |
| F05 | Preparing for near-term departure | Depends on readiness and whether fuel uplift is still required |

ETA/ETD may change over simulation time. A parked aircraft can have no demand;
an inbound flight can be in the planning horizon without appearing on the apron.
Historical meshes do not determine operational fuel profiles.

**D-006 — provisional disruption scenario:** Introduce F06 through an event such
as `UnscheduledArrival` or `FlightDiversion`, for example an unavailable intended
destination. It is initially a forecast arrival. Actual stand availability and
ground readiness precede service. Its onward departure, fuel demand and service
window may initially be unknown and need supplied/updated information.

The system evaluates resulting service consequences and proposes replanning;
the operator confirms execution. Diversion urgency alone does not mean urgent
refuelling or highest priority. A sixth flight record does not require a sixth
unique model: compatible USD assets can be reused.

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

Cases B/C do not approve tanker transition 4 or assume the base replenishes
diesel. See [resource accounting](fuel_and_service_model.md#depot-and-resource-accounting).

## Validation requirements from the source proposal

| Area | Intended evidence | Still to define |
| --- | --- | --- |
| Repeatability | Same scenario ID, seed, configuration and accepted-action sequence reproduce events, demands and expected behaviour | Event/time contract, replay format and solver determinism limits |
| Conservation | Consistent aircraft/tanker aviation-fuel transfers; explicit depot reloads; separate diesel accounting | Units, density, tolerances, rates and operating-mode assumptions |
| State integrity | No negative/over-capacity stock, service before readiness or after departure, or teleportation on replanning | Complete lifecycle/transition guards and failure outcomes |
| Replanning trace | Original plan, trigger, revised priorities, proposal, feasibility and operator decision | Result IDs/revisions, acceptance scope and stale-result handling |
| Measurable comparison | Distance/travel time, service completion/lateness, depot visits, delivered volumes, missed deadlines and unserved/infeasible jobs | Reference sequence, objective weights, numerical thresholds and chosen reporting units |
| Unknown inputs | Missing destination, operating profile, reserve policy or readiness stays visible as unknown | Diagnostic representation and operator feedback |

Do not display an unapproved proposal as executed. Do not fill an operational
data gap from a historical model name. Keep planned, accepted and executed
outcomes distinguishable in the replay and presentation.

## Open questions and next steps

- **Q-12:** Freeze the minimum fixture, reference sequence, objective and metrics;
  choose explicit pass/fail thresholds without promising an arbitrary percentage
  improvement. Define comparable solver inputs and physical checks.
- For every selected scenario, specify initial state, profile IDs, timed events,
  operator actions, expected transitions, resource accounting and diagnostics.
- Expand from the baseline to five scheduled flights, the sixth-flight
  disruption and transit only as their lifecycle and fuel assumptions are agreed.
- Review missing-data and infeasible-demand cases, conservation, current-service
  preservation and proposal rejection/outdated results alongside success cases.
- Verify behaviour and presentation separately; later tests or viewport checks
  should record evidence against approved requirements, not against these drafts
  as if their unresolved choices were settled.
