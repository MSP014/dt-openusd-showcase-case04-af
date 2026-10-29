# Case 04 — Fuel Logistics Requirements

**Status:** Working drafts; requirements review in progress

**Last updated:** 29 October 2026 (Asia/Yerevan)

**Source discussions:** 28–29 October 2026

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
two identical refuellers in active/standby duty rotation (D-016). A one-refueller,
three-aircraft fixture may help isolate a check but does not replace that
demonstration scope. Compare EDF with cuOpt on deadline performance and total
refueller route/diesel cost; an advantage has not yet been demonstrated.

**Required fleet and fault tolerance:** Two refuellers are mandatory for the
demonstration. Normal fuel checks must preserve enough tractor diesel for a
protected return to the depot. If a vehicle nevertheless cannot reach it, the
second must supply enough diesel from its own tractor tank to restore that
return, while retaining its own feasible return reserve (D-017). The assistance
mechanism and exception handling still need detailed requirements.

**Depot and third-stock clarification:** D-018–D-020 record transitions 5–7:
arrival starts `Loading`; required post visits and internal travel take time;
completion leads either to depot parking and `Idle` or directly to `ToAircraft`.
The two posts have no occupancy queues, and separate depot parking holds both
refuellers. D-021 adds pump petrol as the third independent refueller stock,
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
| [Domain and state machines](domain_and_state_machines.md) | Seven states, transitions 1–3, transition 4 aviation-fuel branch, depot transitions 5–7, two-refueller rotation and assistance recorded; aircraft/request lifecycles incomplete | Review remaining fuel-shortage returns and recovery (Q-03), then waiting movements and reassignment, including depot parking (Q-04); track Q-05/Q-13 | Three-resource feasibility; depot operations; operator commands |
| [Fuel and service model](fuel_and_service_model.md) | Three independent refueller stocks, timed depot operations, protected return and assistance requirements recorded; units/rates open | Define pump-petrol profile/reserve and depot service/visit policy; detail diesel return/donation and fuel checks (Q-06, Q-07) | Equipment profiles; domain lifecycle |
| [Planning and operator workflow](planning_and_operator_workflow.md) | Shared queue and uninterrupted current service agreed; automatic dispatch required; operator modes open | Resolve operating modes (Q-13), “serve next” and cost/deadline policy (Q-08); verify the cuOpt interface (Q-09) | Domain states; physical constraints; EDF comparison |
| [USD presentation contract](usd_presentation_contract.md) | One-way projection agreed in discussion; conflicts with existing schema v1.0 unresolved | Design ID bindings and field/status mappings, then review a versioned migration (Q-10) | Reviewed domain fields and ownership |
| [Scenarios and validation](scenarios_and_validation.md) | Demonstration, depot transitions 5–7, three-stock guards, rotation and diesel assistance cases recorded; EDF benchmark specified; no measured solver advantage | Fix common EDF/cuOpt inputs, pump/depot/rotation/recovery fixtures and thresholds (Q-12); retain separate diesel and petrol accounting | Fuel profiles; lifecycle; objective and operating modes |
| [Reference profiles](reference_profiles.md) | Source claims retained; separate pump drive corroborated; exact early variant and numerical performance unverified | Verify pump-engine/tank variant and select capacities, consumption, rates and reserves; continue aircraft/destination review (Q-11) | Intended scene configuration and fuel model |

## Resume checkpoint

The decision register now extends through D-021. New recorded agreements are
**D-018 / depot arrival and operations**, **D-019 / completion without an assignment**, **D-020 / direct
assigned departure** and **D-021 / three independent refueller fuel stocks**.
Transitions 5–7 are now recorded rather than waiting for first discussion.
The individual statuses of D-001–D-017 still apply; this does not approve remaining
resource-shortage branches. Discuss **one transition/resource case at a time**;
a table row for an open transition is not an agreed rule.

**Additional review required:** Q-13 records the unresolved operating modes and
the required automatic-dispatch capability. Resolve its authorisation and
intervention rules before implementing dispatch; the earlier blanket
operator-confirmation wording is reopened. This does not pre-approve any further
tanker transition or choose the final mode set. D-016's duty rotation must follow
the same operating-mode contract.

**Next discussion: remaining fuel-shortage return transitions and the detailed
assistance contract (Q-03).** Add pump-petrol-only and mixed-shortage cases to
the existing diesel review. The agreed
aviation-fuel-only branch sends the relieved tanker straight to Fuel Depot if
it can safely reach it with diesel reserve. The standby tanker can depart even
before the current refuelling ends **only when** the next departing flight's
fuel-check window has opened, an actual deficient-fuel request exists, and the
active tanker cannot feasibly serve it. The ongoing refuelling is never
interrupted. The depot now replenishes aviation fuel at one post and tractor
diesel/pump petrol at the other, with no post queues and timed operations under
D-018–D-021. D-017 requires the second refueller to assist a
tractor that cannot return for lack of diesel; equipment, rates, timing,
recovery lifecycle and donor-unavailable cases remain open. Do not infer
permission for either vehicle to travel without sufficient diesel.
See [the transition table](domain_and_state_machines.md#tanker-transitions) and
[resource questions](fuel_and_service_model.md#open-questions-and-next-steps).

Then continue, individually: remaining waiting-location arrivals and Staging
Point/Main Parking movements; a new assignment during `Repositioning` or the
`Loading` movement to depot parking; entry/exit from `Maintenance`; remaining
exceptions. For recorded transitions 5–7, detail post order/skipping, auxiliary
diesel/petrol service sequence, durations, rates and readiness targets without
reopening their agreed state boundaries. Then complete request and aircraft
lifecycles and the remaining contracts.

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
transitions 5–7 and separate pump-petrol accounting. These are provenance IDs,
not ADR numbers. Each has one primary home; other documents link to that definition.

| ID | Recorded status | Decision and primary home |
| --- | --- | --- |
| D-001 | Agreed in discussion | [WorldState owns current state](architecture.md#state-ownership) |
| D-002 | Agreed in discussion; existing-contract conflict open | [One-way USD presentation](usd_presentation_contract.md#ownership-and-direction) |
| D-003 | Direction agreed | [Separate lifecycles](domain_and_state_machines.md#entities-and-lifecycles) |
| D-004 | Agreed in discussion | [Unlimited aviation-fuel supply at depot](fuel_and_service_model.md#depot-and-resource-accounting) |
| D-005 | Numerical separation agreed in direction; units proposed; extended by D-021 | [Separate numerical fuel balances](fuel_and_service_model.md#quantities-and-units) |
| D-006 | Provisional; blanket operator-confirmation assumption reopened by Q-13 | [Aircraft lifecycle and arrival type](domain_and_state_machines.md#aircraft-lifecycle); use the clarified 3 + 2 + optional 1 schedule |
| D-007 | Proposed | [Pre-departure fuel check](domain_and_state_machines.md#fuel-check-and-request-lifecycle) |
| D-008 | Direction agreed | [Yerevan, destinations and next flight leg](reference_profiles.md#destinations-and-flight-legs) |
| D-009 | Provisional user proposal | [Scheduled transit fuel stop](scenarios_and_validation.md#scheduled-transit-fuel-stop) |
| D-010 | Basic model agreed; transitions incomplete | [Seven tanker states and sequential service](domain_and_state_machines.md#tanker-states) |
| D-011 | Agreed in discussion; mechanism open | [Shared queue and operator override](planning_and_operator_workflow.md#requests-and-serve-next); transition 1 links to this rule |
| D-012 | Agreed in discussion | [Arrival and start of refuelling](domain_and_state_machines.md#tanker-transitions), transition 2 |
| D-013 | Agreed in discussion; locations/threshold open | [Depot, waiting locations and Repositioning](domain_and_state_machines.md#locations-and-repositioning) |
| D-014 | Agreed in discussion; remaining shortage-transition details open | [Post-service branching](domain_and_state_machines.md#tanker-transitions), transition 3 |
| D-015 | Agreed in discussion; remaining diesel-shortage transitions open | [Aviation-fuel-shortage return to Fuel Depot](domain_and_state_machines.md#tanker-transitions), transition 4 |
| D-016 | Two refuellers required; rotation agreed; mechanism open | [Two-refueller duty rotation](domain_and_state_machines.md#two-refueller-rotation-d-016--agreed-in-discussion) |
| D-017 | Protected diesel return and exceptional assistance required; detailed recovery contract open | [Diesel reserve and tractor-to-tractor assistance](domain_and_state_machines.md#diesel-reserve-and-exceptional-assistance-d-017) |
| D-018 | Agreed in discussion; post rates/order open | [Transition 5: ToDepot to Loading](domain_and_state_machines.md#transition-5-todepot-to-loading); two posts and separate two-vehicle depot parking |
| D-019 | Agreed in discussion; assignment during parking movement open | [Transition 6: Loading to Idle](domain_and_state_machines.md#transition-6-loading-to-idle); no assigned request, Idle on parking arrival |
| D-020 | Agreed in discussion; numerical guards open | [Transition 7: Loading to ToAircraft](domain_and_state_machines.md#transition-7-loading-to-toaircraft); direct departure, partial stocks permitted if feasible |
| D-021 | Three independent refueller stocks and guards agreed; exact equipment/parameters open | [Aviation fuel, tractor diesel and pump petrol](fuel_and_service_model.md#three-refueller-fuel-stocks-d-021) |

## Open review register

These questions consolidate the original open items and review findings from
29 October. Their inclusion does not decide the answer. Detailed next steps
live in the owning document; this register provides a single continuation index.

| ID | Unresolved question | Owner |
| --- | --- | --- |
| Q-01 | Who applies events and commands to WorldState, and who publishes snapshots? Reconcile SDP publication wording with D-001 | [Architecture](architecture.md#open-questions-and-next-steps) |
| Q-02 | What defines simulation time, event ordering, replay, resets and the relationship to USD time? | [Architecture](architecture.md#open-questions-and-next-steps) |
| Q-03 | How do diesel-only, pump-petrol-only and mixed-shortage returns and D-017 assistance work, including an unavailable or infeasible donor? | [Domain](domain_and_state_machines.md#open-questions-and-next-steps) |
| Q-04 | Does Repositioning include leaving an aircraft stand? What are waiting arrivals/timeouts and reassignment rules, including a new request during Loading movement to depot parking? | [Domain](domain_and_state_machines.md#open-questions-and-next-steps) |
| Q-05 | How do aircraft, request and tanker lifecycles stay consistent, especially request closure versus the final fuel check? What exactly does Ready mean? | [Domain](domain_and_state_machines.md#open-questions-and-next-steps) |
| Q-06 | Which units, fuel calculation, reserves and fuel-check timing rules are canonical? How are late arrivals and ETD changes handled? | [Fuel model](fuel_and_service_model.md#open-questions-and-next-steps) |
| Q-07 | What pump-petrol capacity/consumption/reserve, depot visit/skip order, auxiliary diesel/petrol service sequence, rates/quantities and D-017 assistance calculations apply? | [Fuel model](fuel_and_service_model.md#open-questions-and-next-steps) |
| Q-08 | What does “serve next” guarantee? How are priorities, queue position, deadline performance and tanker route/diesel cost balanced? | [Planning](planning_and_operator_workflow.md#open-questions-and-next-steps) |
| Q-09 | Which cuOpt interface/features represent the chosen constraints, and how are infeasible, failed or outdated proposals handled? | [Planning](planning_and_operator_workflow.md#open-questions-and-next-steps) |
| Q-10 | How are logical IDs, separate aircraft/refueller fuel balances and state fields projected into USD, and how is schema v1.0 reconciled/versioned? | [USD contract](usd_presentation_contract.md#open-questions-and-next-steps) |
| Q-11 | Which equipment variants, operating profiles, destinations, source editions and asset-swap metadata are verified and selected? | [Reference profiles](reference_profiles.md#open-questions-and-next-steps) |
| Q-12 | Which common EDF/cuOpt fixtures, two-refueller rotation/recovery cases, fleet route/diesel-cost model and deadline/coverage criteria establish useful routing benefit on the 3 + 2 + optional 1 schedule? | [Scenarios](scenarios_and_validation.md#open-questions-and-next-steps) |
| Q-13 | How can the system dispatch automatically, when is operator confirmation required, and how do intervention, overrides and mode switching work? | [Planning](planning_and_operator_workflow.md#operator-acceptance-and-unresolved-result-handling) |

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
