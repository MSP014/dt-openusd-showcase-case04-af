# Fuel Logistics — Planning and Operator Workflow

**Status:** Working draft; automatic dispatch required; queue/non-interruption
principles retained; operator modes, cost/deadline policy and solver interface open

**Last updated:** 29 October 2026

**Primary decision:** D-011

**Review questions:** Q-08, Q-09, Q-13; state/clock dependencies Q-01, Q-02

This document defines intended decision support, not an implemented cuOpt or UI
integration. [WorldState ownership](architecture.md#state-ownership),
[physical feasibility](fuel_and_service_model.md) and
[USD projection](usd_presentation_contract.md) have separate owners.

## Delay impact and priority policy

The proposed priority module evaluates the consequences of delaying each fuel
service. Inputs include flight/cargo category, ETA/ETD, ground readiness,
remaining time margin and documented impact assumptions.

Financial, reputational and humanitarian effects are candidate dimensions. The
proposed output is an internal numerical `priorityScore` with a factor breakdown
and explanation. Weights, scale, tie-breaking, escalation and objective mapping
remain open. An imminent ETD, diversion or medical cargo does not automatically
give maximum **refuelling** priority. This policy does not rank landing traffic.

Internal `priorityScore` is not a replacement for the existing USD integer
priority. Mapping to `1` (highest) through `5` (lowest) belongs to the reviewed
[USD presentation contract](usd_presentation_contract.md#schema-compatibility).

## Routing objective and EDF baseline

The planned routing problem includes the **tanker's own travel and diesel cost**,
alongside aircraft service deadlines, readiness, capacity, service durations and
replenishment. Deadline priority alone does not define the cost of visiting the
stands in a particular order.

The demonstration uses the [D-016 active/standby rotation](domain_and_state_machines.md#two-refueller-rotation-d-016--agreed-in-discussion).
Apply the same duty-role and relief constraints to EDF and cuOpt, and include
both vehicles' travel, depot returns and diesel use in the cost comparison.
Normal assignments must preserve each tractor's protected return. The required
[D-017 diesel-assistance fallback](domain_and_state_machines.md#diesel-reserve-and-exceptional-assistance-d-017)
needs its own feasible donor assignment and the same operating-mode contract;
assistance scheduling and its interaction with aircraft requests remain open.

Earliest Deadline First (EDF) is the required simple comparison policy. It can
meet deadlines in suitable scenarios; that outcome does not demonstrate that it
also minimises travel or diesel. Compare cost at equivalent demand coverage and
deadline performance, reporting any trade-off explicitly. Do not call a route
better merely because it burns less diesel after dropping work or missing more
departures.

Use the same physical model and feasibility checks for both EDF and cuOpt. A
distance-based objective is only a proxy for diesel unless an explicit
consumption model justifies the conversion. Monetary savings additionally need
a declared price/cost assumption; pumping/idle consumption must be treated
consistently if included. Exact objective weights and lateness policy remain
open under Q-08.

[NVIDIA's routing features](https://docs.nvidia.com/cuopt/user-guide/latest/routing-features.html)
describe configurable travel costs and a separate travel-time matrix for time
constraints (documentation checked 29 October 2026). This supports evaluating a
route-cost objective alongside schedule feasibility; it does not validate this
project's diesel model or guarantee direct support for every planned constraint.

With two refuellers and at most six flights, neither GPU necessity nor a cuOpt
advantage is established. The [benchmark contract](scenarios_and_validation.md#earliest-deadline-first-comparison)
must record calculation/integration overhead and equal or worse outcomes as
well as improvements. The integration remains planned.

## Requests and serve next

**D-011 — agreed in discussion; implementation mechanism open:**

- Automatic fuel checks and manual Omniverse UI input can both create requests.
- Both enter the same queue/planning process through cuOpt. Manual entry alone
  does not increase priority.
- The recorded UI intent is a successful queue-entry notification with current
  position, for example “Aircraft queued, position 3”. A subsequent operator
  action, **Serve next**, raises priority and triggers replanning.
- If the tanker is already `Refueling`, the next-service preference applies
  after that service completes. Current refuelling is not interrupted.
- Any next assignment must remain feasible for aviation fuel, diesel, distance,
  availability and time. An override does not remove physical constraints.
- If the request cannot be served without replenishment, display that limitation
  rather than sending the tanker with insufficient resources.

**Review finding:** “Raise priority” and “serve next” do not yet specify the
same scheduling guarantee. Decide whether the action changes a soft preference
or fixes the next eligible service order, and how that intent is represented in
the selected solver interface. No such solver capability is assumed here.

Define when a queue position is known and whether it refers to a proposal or
accepted schedule. Request receipt, optimisation completion, plan acceptance and
actual assignment are distinct outcomes. The source UI example is a requirement
intent, not evidence that an immediate position can always be provided.

## Optimisation problem and outputs

The proposed problem builder consumes current accepted state, simulation time,
priorities, uplift demand, aircraft readiness/time windows, refueller/depot state,
ground network and existing commitments.

Candidate representations include travel-time/cost matrices, capacities,
service durations, time windows, depot nodes/visits and a documented objective
or lateness-penalty formulation. Expected output includes recommended visits,
route/path identifiers, timings, depot returns, estimated impacts, unmet demand
and feasibility diagnostics.

The exact handling of priorities, repeated depot visits, multiple resources,
partial service/split deliveries and locked commitments depends on the selected
cuOpt interface and verified features. Unsupported constraints need explicit
application-level modelling or validation. Do not promise support from this
requirements list alone.

The solver does not choose business/humanitarian policy, author scenario events
or guarantee constraints omitted from its input. Validate the resulting plan
against the adopted physical and lifecycle contracts. A feasible solution is not
proof of global optimality; compare measured outcomes with a fixed reference.

## Replanning and committed actions

The architecture proposal uses event-driven rolling-horizon planning, with an
optional planning interval. Material triggers may include schedule changes,
new flights/diversions, readiness changes, service completion and replenishment.

Completed deliveries remain completed. An ongoing refuelling service is not
arbitrarily interrupted or relocated. A new plan starts from the actual current
vehicle/resource state, retains locked commitments and changes only eligible
future work. Replanning does not teleport the tanker or restore spent fuel.

Omniverse should show current and proposed routes/assignments, aircraft fuel,
anticipated delays and why the recommendation changed. Record the original plan,
trigger, revised priorities, proposed plan, feasibility, operating mode and
execution-authorisation source for replay. The source may be automatic policy
or an operator decision; a proposal must not appear to have executed before the
applicable authorisation and actual execution.

## Operator acceptance and unresolved result handling

**Q-13 — operating modes require review.** The 29 October clarification requires
the system to dispatch the refueller automatically. Earlier notes assumed
operator approval of proposed changes, including the sixth-flight scenario;
that blanket assumption is reopened. It must not prevent automatic operation.

Define whether automatic, assisted and manual operation are separate modes,
their default/selection rules and the commands allowed in each. These are
questions for agreement, not a final mode enum or approved UI layout.

| Contract question | Required clarification |
| --- | --- |
| Automatic dispatch | Which validated assignments and replans can the execution layer adopt and start without a confirmation click? |
| Operator-confirmed operation | When, if ever, must a plan, disruption response or override wait for human approval? |
| Manual intervention | How can an operator inspect, reject, override or suspend future actions, and which current commitments remain locked? |
| Mode changes | What happens to a pending calculation, adopted plan, tanker in transit and current refuelling when the mode changes? |
| Audit and feedback | How are the active mode, automatic decisions, operator actions and rejected/infeasible requests shown and replayed? |

Automatic authorisation still belongs to the execution policy: solver output
does not independently mutate WorldState or bypass resource/readiness guards.
Mode handling must preserve the already agreed non-interruption of current
refuelling. Detailed triggers and intervention behaviour remain undecided.

Further review must define the input state, command recipient, allowed side
effects and visible outcome of each action: manual request, Serve next, accept,
reject and any later revise/cancel/reset controls. These are contract questions,
not additional approved UI features.

Review findings to address before implementation:

- A demand, resource or schedule change may invalidate a pending solver result.
  Define how result/state revisions are compared and whether recalculation or
  rejection is required before automatic or operator authorisation.
- Define presentation and state effects for infeasible, failed, cancelled or
  timed-out calculations and late results from an older request/scenario.
- Define behaviour for repeated input and reset/reload during a pending result;
  do not count a duplicated command as a second accepted action.
- Define how stale or unaccepted route information is labelled so it is not
  mistaken for the current executable plan.

No concrete revision format, asynchronous API or cancellation mechanism is
selected by recording these questions.

## Open questions and next steps

- **Q-08:** Agree priority factors/weights and the exact Serve next guarantee.
  Define queue-position semantics, infeasible-override feedback and the balance
  of deadline performance against tanker route/diesel cost.
- **Q-09:** Select a cuOpt interface, verify required modelling features and
  identify application-side validation. Specify result identity, acceptance
  checks and failure/outdated-result behaviour.
- **Q-01/Q-02 dependencies:** Bind accepted commands/results to the state owner,
  simulation time and replay sequence.
- **Q-13:** Agree the operating modes, automatic-dispatch and replanning
  authorisation rules, operator intervention and mode switching. Remove blanket
  approval assumptions from the affected execution and UI contracts.

First clarify the policy intent, then verify its solver representation. Use the
[scenarios](scenarios_and_validation.md) to define observable success and failure
outcomes. Do not infer working controls from UI labels or a proposed route.
