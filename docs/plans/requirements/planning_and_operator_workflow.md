# Fuel Logistics — Planning and Operator Workflow

**Status:** Working draft; queue and non-interruption principles agreed in
discussion; policy, solver interface and acceptance mechanics open

**Last updated:** 29 October 2026

**Primary decision:** D-011

**Review questions:** Q-08, Q-09; state/clock dependencies Q-01, Q-02

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
trigger, revised priorities, proposed plan, feasibility and operator decision for
replay. A proposal must not appear to have executed before acceptance.

## Operator acceptance and unresolved result handling

The recorded architecture requires an operator to accept or reject proposed
changes, including the sixth-flight disruption scenario. The precise boundary
between accepting a complete plan, accepting an override and automatically
executing already accepted future actions remains **open**.

Further review must define the input state, command recipient, allowed side
effects and visible outcome of each action: manual request, Serve next, accept,
reject and any later revise/cancel/reset controls. These are contract questions,
not additional approved UI features.

Review findings to address before implementation:

- A demand, resource or schedule change may invalidate a pending solver result.
  Define how result/state revisions are compared and whether recalculation or
  rejection is required before acceptance.
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
  Define queue-position semantics, infeasible-override feedback and operator
  approval boundaries without interrupting committed service.
- **Q-09:** Select a cuOpt interface, verify required modelling features and
  identify application-side validation. Specify result identity, acceptance
  checks and failure/outdated-result behaviour.
- **Q-01/Q-02 dependencies:** Bind accepted commands/results to the state owner,
  simulation time and replay sequence.

First clarify the policy intent, then verify its solver representation. Use the
[scenarios](scenarios_and_validation.md) to define observable success and failure
outcomes. Do not infer working controls from UI labels or a proposed route.
