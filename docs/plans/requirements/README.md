# Case 04 — Fuel Logistics Requirements

**Status:** Working drafts; requirements review in progress

**Last updated:** 29 October 2026 (Asia/Yerevan)

**Source discussions:** 28 October 2026

**Implementation status:** This collection describes intended behaviour. It does
not establish a working provider, fuel model, priority engine, cuOpt integration
or Omniverse operator workflow.

## Purpose and scope

Develop a synthetic-data-driven L2 decision-support prototype for airfield
refuelling. The demonstration should make a schedule change, its consequences
and a feasible proposed service sequence visible to an operator. Houdini prepares
assets and data; Omniverse presents the scene and operational state; cuOpt is the
planned routing solver. The operator accepts or rejects proposed plan changes.

The scope covers aircraft fuel requests, refueller movements and replenishment
at one aviation-fuel depot. It excludes baggage, catering, passenger handling,
aircraft towing, landing clearances and wider airport operations. A diversion is
an input event, not a claim to manage landing traffic or aviation emergencies.
Synthetic telemetry does not establish a live airport integration.

Period visual replicas and contemporary planning concepts are separate from
configurable equipment capabilities. Preserve the cinematic presentation and
asset reuse while making constraints, assumptions and results inspectable.

## Document status and next steps

All documents below remain drafts. Their individual statements retain the
decision statuses recorded in the source notes; restructuring is not approval.

| Document | Current state | Next requirements step | Depends on |
| --- | --- | --- | --- |
| This README | Navigation, decision provenance and review queue assembled | Keep this table, the checkpoint and decision register aligned after each reviewed change | All topic documents |
| [Architecture](architecture.md) | Ownership principles agreed in discussion; application boundaries and time contract incomplete | Resolve Q-01 and Q-02: event application, snapshot publication, simulation clock and replay | Domain entities; operator acceptance |
| [Domain and state machines](domain_and_state_machines.md) | Seven tanker states, transitions 1–3, transition 4 aviation-fuel branch and two-refueller rotation recorded; aircraft model provisional; request lifecycle undefined | Resume transition 4 diesel-shortage cases (Q-03), then review remaining transitions one at a time; track Q-04 and Q-05 | Resource feasibility; operator commands |
| [Fuel and service model](fuel_and_service_model.md) | Separate fuel balances agreed conceptually; units, calculation profiles and numerical parameters open | Define the resource cases needed for transition 4; then settle units, replenishment and fuel-check rules (Q-06, Q-07) | Reference profiles; domain lifecycle |
| [Planning and operator workflow](planning_and_operator_workflow.md) | Shared queue and uninterrupted current service agreed in discussion; solver and acceptance details proposed | Clarify “serve next”, approval boundaries and priority policy; verify the selected cuOpt interface (Q-08, Q-09) | Domain states; physical constraints |
| [USD presentation contract](usd_presentation_contract.md) | One-way projection agreed in discussion; conflicts with existing schema v1.0 unresolved | Design ID bindings and field/status mappings, then review a versioned migration (Q-10) | Reviewed domain fields and ownership |
| [Scenarios and validation](scenarios_and_validation.md) | Minimum, expanded and disruption scenarios collected; numerical examples illustrative | Fix reproducible inputs, reference sequence and acceptance thresholds (Q-12) | Fuel profiles; lifecycle; solver objective |
| [Reference profiles](reference_profiles.md) | Equipment choices and source links retained; exact applicability not independently verified during restructuring | Verify variants and sources; select aircraft profiles, reserve framework and destinations (Q-11) | Intended scene configuration and fuel model |

## Resume checkpoint

The discussion has now agreed **D-015 / transition 4's aviation-fuel shortage
branch** and **D-016 / two-refueller duty rotation**, in addition to D-001–D-014.
Transitions 1–3 retain their recorded agreement. Discuss and record **one
transition/resource case at a time**; a table row for an open transition is not
an agreed rule.

**Next discussion: transition 4, diesel-shortage cases (Q-03).** The agreed
aviation-fuel-only branch sends the relieved tanker straight to Fuel Depot if
it can safely reach it with diesel reserve. The standby tanker can depart even
before the current refuelling ends **only when** the next departing flight's
fuel-check window has opened, an actual deficient-fuel request exists, and the
active tanker cannot feasibly serve it. The ongoing refuelling is never
interrupted. The depot is now assumed to replenish tractor diesel as well as
aviation fuel during rotation, while physical facilities, rates and diesel
failure/recovery rules remain open. Do not infer permission for a vehicle to
travel without sufficient diesel.


Then continue, individually: arrival at the depot; loading completion and exit;
arrival at waiting locations; Staging Point/Main Parking movements; a new
assignment during `Repositioning`; entry/exit from `Maintenance`; remaining
exceptions. Resolve shared resource questions when needed for each transition.
Then complete request and aircraft lifecycles and the remaining contracts.

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

D-001–D-014 originate in the 28 October notes; D-015–D-016 record the later
29 October follow-up discussion. These are provenance IDs, not ADR numbers. Each has one primary home; other documents link to that definition.

| ID | Recorded status | Decision and primary home |
| --- | --- | --- |
| D-001 | Agreed in discussion | [WorldState owns current state](architecture.md#state-ownership) |
| D-002 | Agreed in discussion; existing-contract conflict open | [One-way USD presentation](usd_presentation_contract.md#ownership-and-direction) |
| D-003 | Direction agreed | [Separate lifecycles](domain_and_state_machines.md#entities-and-lifecycles) |
| D-004 | Agreed in discussion | [Unlimited aviation-fuel supply at depot](fuel_and_service_model.md#depot-and-resource-accounting) |
| D-005 | Direction agreed; units proposed | [Three separate numerical fuel fields](fuel_and_service_model.md#quantities-and-units) |
| D-006 | Provisional | [Aircraft lifecycle and arrival type](domain_and_state_machines.md#aircraft-lifecycle) |
| D-007 | Proposed | [Pre-departure fuel check](domain_and_state_machines.md#fuel-check-and-request-lifecycle) |
| D-008 | Direction agreed | [Yerevan, destinations and next flight leg](reference_profiles.md#destinations-and-flight-legs) |
| D-009 | Provisional user proposal | [Scheduled transit fuel stop](scenarios_and_validation.md#scheduled-transit-fuel-stop) |
| D-010 | Basic model agreed; transitions incomplete | [Seven tanker states and sequential service](domain_and_state_machines.md#tanker-states) |
| D-011 | Agreed in discussion; mechanism open | [Shared queue and operator override](planning_and_operator_workflow.md#requests-and-serve-next); transition 1 links to this rule |
| D-012 | Agreed in discussion | [Arrival and start of refuelling](domain_and_state_machines.md#tanker-transitions), transition 2 |
| D-013 | Agreed in discussion; locations/threshold open | [Depot, waiting locations and Repositioning](domain_and_state_machines.md#locations-and-repositioning) |
| D-014 | Agreed in discussion; shortage branch open | [Post-service branching](domain_and_state_machines.md#tanker-transitions), transition 3 |
| D-015 | Agreed in discussion; diesel shortage open | [Aviation-fuel-shortage return to Fuel Depot](domain_and_state_machines.md#tanker-transitions), transition 4 |
| D-016 | Agreed in discussion; mechanism and exception handling open | [Two-refueller duty rotation](domain_and_state_machines.md#two-refueller-rotation-d-016--agreed-in-discussion) |

## Open review register

These questions consolidate the original open items and review findings from
29 October. Their inclusion does not decide the answer. Detailed next steps
live in the owning document; this register provides a single continuation index.

| ID | Unresolved question | Owner |
| --- | --- | --- |
| Q-01 | Who applies events and commands to WorldState, and who publishes snapshots? Reconcile SDP publication wording with D-001 | [Architecture](architecture.md#open-questions-and-next-steps) |
| Q-02 | What defines simulation time, event ordering, replay, resets and the relationship to USD time? | [Architecture](architecture.md#open-questions-and-next-steps) |
| Q-03 | Transition 4: aviation-fuel-only depot return agreed; what if diesel is insufficient or both fuel guards fail? | [Domain](domain_and_state_machines.md#open-questions-and-next-steps) |
| Q-04 | Does Repositioning include departure from an aircraft stand? What are its arrival, timeout and reassignment rules? | [Domain](domain_and_state_machines.md#open-questions-and-next-steps) |
| Q-05 | How do aircraft, request and tanker lifecycles stay consistent, especially request closure versus the final fuel check? What exactly does Ready mean? | [Domain](domain_and_state_machines.md#open-questions-and-next-steps) |
| Q-06 | Which units, fuel calculation, reserves and fuel-check timing rules are canonical? How are late arrivals and ETD changes handled? | [Fuel model](fuel_and_service_model.md#open-questions-and-next-steps) |
| Q-07 | Diesel replenishment at the depot now assumed for rotation; what facility, rates, travel, pumping, reserve and reachable-return rules apply? | [Fuel model](fuel_and_service_model.md#open-questions-and-next-steps) |
| Q-08 | What does “serve next” guarantee? How are priorities, queue position and operator acceptance defined? | [Planning](planning_and_operator_workflow.md#open-questions-and-next-steps) |
| Q-09 | Which cuOpt interface/features represent the chosen constraints, and how are infeasible, failed or outdated proposals handled? | [Planning](planning_and_operator_workflow.md#open-questions-and-next-steps) |
| Q-10 | How are logical IDs and reviewed state fields projected into USD, and how is schema v1.0 reconciled/versioned? | [USD contract](usd_presentation_contract.md#open-questions-and-next-steps) |
| Q-11 | Which equipment variants, operating profiles, destinations, source editions and asset-swap metadata are verified and selected? | [Reference profiles](reference_profiles.md#open-questions-and-next-steps) |
| Q-12 | Which fixed scenarios, comparison routes, measurements and thresholds establish useful decision support? | [Scenarios](scenarios_and_validation.md#open-questions-and-next-steps) |

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
