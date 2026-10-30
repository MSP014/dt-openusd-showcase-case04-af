# Fuel Logistics — Planning and Operator Workflow

**Status:** Working draft; automatic dispatch required; queue/non-interruption
principles retained; operator modes, cost/deadline policy and solver interface open

**Last updated:** 31 October 2026

**Primary decisions:** D-011, D-027, D-040

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

## Departure delay risk and schedule authority (D-040)

**Agreed in discussion, 31 October 2026:** `Departure Delay Risk` is a warning,
not an aircraft state. Assess it for an active flight in Parked, Refueling or
Alert, including replacement travel/waiting after interrupted service. Predict
whether service and preparation can meet the agreed departure time, try feasible
replanning and explain any predicted delay. A schedule change does not itself
put an aircraft in Alert or interrupt running service.

Keep the agreed scheduled ETD, predicted departure and actual stand-departure/
take-off times distinct. A prediction is not an accepted schedule change. The
operator decides whether to change the existing ETD; neither the provider's
runtime schedule-change input nor a solver result may bypass that decision.
Initial scenario schedules and the ETD of a newly assigned flight are inputs,
not silent rewrites of an existing flight's agreed time.

| Operator action | Required behaviour |
| --- | --- |
| Keep Schedule | Retain agreed ETD and recalculate service; preserve the warning and infeasibility/lateness if the original time cannot be met |
| Accept Proposed Time | Apply the operator-confirmed proposed ETD and recalculate using current state |
| Set Time Manually | Validate the supplied time and affected guards, then apply/recalculate; do not label an infeasible choice feasible |

ETD changes require operator confirmation; this does not impose confirmation on
every tanker dispatch or settle Q-13's remaining operating modes. Running
delivery remains committed under D-034. D-037 rejects flight changes after Ready.

If ETD is 14:00 and readiness is reached at 14:10, retain 14:00 unless the
operator has changed it. Continue Ready -> Departing -> Departed using actual
events and retain lateness; do not wait for a past timestamp, backdate movement
or automatically rewrite ETD. Exact preparation duration, the scheduled ETD's
measurement point and like-for-like delay metrics remain Q-02/Q-08/Q-12 details.
Keep scheduled, predicted and actual times labelled distinctly in the UI.

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
D-032 fixes the assistance sequence and busy-donor waiting; exact timing,
transfer equipment/rates and records remain to be specified.
All service assignments also require the independent [D-021 pump-petrol guard](fuel_and_service_model.md#three-refueller-fuel-stocks-d-021).
Keep pump petrol separate from tractor diesel in feasibility and reporting.
The route/diesel objective remains the planned comparison; combining petrol
cost with it requires a declared cost policy rather than a silent substitution.

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

## Assignment review and two-refueller field operation (D-027)

**D-027 — agreed in discussion:** Insufficient aviation fuel, tractor diesel or
pump petrol for one candidate assignment first triggers **assignment review**,
not an unconditional depot return. Examine other known upcoming requests/demand
within the planning horizon. A smaller uplift is useful only if all three fuel
guards, aircraft eligibility, route/access, service timing and protected return
also pass. Known future demand can guide planning without becoming an executable
service assignment before its check window/request conditions are met.

Compare feasible fleet plans: retain the partially loaded vehicle for suitable
work and use the second for another request, versus replenish and reassign.
Account for both vehicles' complete travel, depot/field-parking legs, service
and replenishment time, diesel cost and deadlines. A job fitting the remaining
aviation fuel does not by itself prove that retaining the vehicle is cheaper.
Do not reduce cost by silently dropping demand or worsening deadlines.
The numerical planning horizon and final cost/deadline policy remain Q-08/Q-12.

Both refuellers may operate on the airfield concurrently. D-016's one-Active,
one-Standby-at-depot arrangement is a default, not a permanent restriction or
an upper limit of one field vehicle. Do not force a useful vehicle back to the
depot solely to recreate that arrangement. Duty-role labels remain separate
from tanker states; this agreement adds no new state. D-032 separately adds Alert.

**Agreed assessment sequence (synthetic 12/10/8-tonne example):**

1. The next candidate aircraft needs 12 tonnes of uplift; the first vehicle has
   10 tonnes and cannot serve that candidate in full.
2. Examine other upcoming requests/demand: can its remaining aviation fuel,
   tractor diesel and pump petrol cover another job, service timing and all
   required reserves?
3. If suitable work is selected by the feasible fleet plan, retain the first on
   the field. For example, the second serves the 12-tonne aircraft and the first
   serves another needing 8 tonnes. Both may work on the field concurrently.
4. Include the first vehicle's depot return when replenishment is necessary or
   the complete route/timing comparison favours it, rather than automatically
   after the first candidate fails.

A suitable smaller request may be far away, conflict with more urgent service
or increase the later depot leg. Compare complete plans for **both** vehicles,
not just the immediate fitting job. Validate capacity, fuel, timings and fleet
cost. The second follows D-026's required depot-to-field-parking arrival before
aircraft service; maintain at least one ready refueller physically on the field.

This defines the planned cuOpt evaluation: **jointly choose which refueller
serves each aircraft, service order and replenishment timing** to reduce fleet
operating cost through route distance and estimated diesel consumption/cost,
subject to service deadlines and all feasibility constraints. Demonstrate any
advantage against EDF under equivalent fleet, assignment-review, coverage and
resource rules; the requirements alone establish neither savings nor a cuOpt win.

D-027 amends the earlier unconditional-shortage wording in D-015/D-025. If no
feasible alternative assignment is selected, or the fleet plan selects
replenishment, use the applicable depot-return transition with protected diesel
reserve and coordinated field coverage. Current service remains uninterrupted;
diesel assistance under D-017 is still an exceptional recovery requirement.

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
- Any next assignment must remain feasible for aviation fuel, tractor diesel,
  pump petrol, distance, readiness, availability and time. An override does not
  remove any of the three resource guards.
- If the request cannot be served without replenishment, display that limitation
  rather than sending the tanker with insufficient resources.

**D-033 — pre-service cancellation/revision agreed:** An explicit cancellation
releases the unstarted assignment, not a completed service. For a tanker already
in ToAircraft, review the fleet plan and either redirect to a feasible aircraft,
move to field waiting, or use selected replenishment, retaining actual travel,
time and stocks. A changed request repeats all feasibility checks; a changed
departure time alone does not cancel it.

**D-034 — started-service commitment agreed:** Freeze the service conditions on
entry to aircraft Refueling and complete that delivery under the original
conditions. Manual cancellation during running service is unavailable and must also be
rejected by execution if a late/stale command arrives. External request withdrawal,
flight cancellation or schedule revision does not release that active commitment.
Finish service, then release the tanker normally. A cancelled-flight aircraft
remains fuelled and parked until the provider assigns it a new flight; use its
actual onboard fuel for that new flight's demand calculation. D-035 technical
withdrawal overrides normal service continuation; Q-14 selects concrete faults.
Under D-039, flight cancellation in aircraft Alert before replacement arrival
cancels the outstanding old-request obligation, releases the replacement for
actual-position reassignment and leaves delivered fuel intact. It does not
authorise interruption of running Refueling, change the faulty tanker's return
path or create a field-parking bypass.

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

Represent all three refueller stocks and their different consumption mechanisms.
Depot visits use timed operations, internal travel and exclusive post occupancy
under [D-018](domain_and_state_machines.md#transition-5-todepot-to-loading).
Apply the agreed first-to-aviation/second-to-auxiliary simultaneous-arrival
sequence and subsequent second-to-aviation move; include necessary coordination
waits. Both EDF and cuOpt must respect the same post-release order and durations.
The auxiliary post supplies diesel and petrol through separate dispensers.
A feasible assigned departure can use partial
stocks. Without an assigned request, follow D-019's parking/Idle branch; with an
assigned request, evaluate D-020's departure guards. Verify how the selected
solver interface represents these requirements and enforce unsupported guards
in application validation;
do not assume native cuOpt support for the third resource or depot phases.

D-015/D-025's return branch follows D-027 assignment review when any fuel stock
is insufficient for a candidate assignment. A feasible alternative can retain
the vehicle on the field; a selected replenishment route preserves diesel for
return with reserve. An unexpected breach invokes D-017 assistance. Q-03 concerns
that assistance's implementation, not reopening this reviewed shortage policy.

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

**D-024/D-025 — waiting reassessment agreed:** Every new arrival/departure
schedule event and every change to an existing event triggers reassessment of
waiting/refueller allocation. Reassess also when an expected fuel-check window
opens; if no request results, inspect the next hour again. For Staging Point
waiting exceeding one hour without assignment, retain the vehicle if a flight's
check window opens within the next hour; otherwise move it to Main Parking.
The dwell-threshold crossing is itself a timed trigger. No polling frequency is
agreed. The one-hour dwell rule does not defer response to new demand/events.
For an existing flight's ETD, apply D-040: evaluating an unaccepted time proposal
does not change the authoritative check window or redirect a vehicle as if the
operator had accepted it. Reassess executable work on the applied schedule.

Distinguish anticipated demand from an eligible service request. A known upcoming
12-tonne uplift first triggers D-027 assignment review for a tanker carrying
only 10 tonnes. Retain it for other feasible work when selected by the fleet
plan; dispatch the full second vehicle to field parking for the larger request.
If replenishment is selected instead, apply `Idle -> ToDepot` or
`Repositioning -> ToDepot` under D-025. Depot departure uses
`Idle -> Repositioning` under D-026. This movement
does not wait for an aircraft request or an open check window. The replacement
must arrive and stop at field parking before a subsequent `Idle -> ToAircraft`
assignment with an open window, actual request and all guards. Account for both
vehicles' travel, parking leg and three-stock checks.

**D-026 — field coverage required:** Plan the handover so at least one
operationally ready refueller remains physically on the field. A vehicle at the
depot or still approaching from outside the field does not meet this invariant.
Use actual boundary-crossing/readiness times, not only command times; record any
gap explicitly. The both-at-depot procedure is not a feasible covered handover.
First/second readiness criteria, boundary geometry, handover timing and coverage
loss handling require detail, without weakening the coverage requirement.

**D-023 — direct in-motion assignment agreed:** A valid eligible assignment can
redirect `Repositioning -> ToAircraft` immediately. Validate all three stocks and
the route/readiness/time constraints from the actual current position. Do not
require a stop at the previous waiting destination or an intermediate Idle.
The event does not reset fuel or position. D-026's depot replacement must first
complete the required field-parking arrival, even if a request becomes eligible
on the way; it is an exception to this ordinary redirection rule.
D-030 also permits `Loading -> ToAircraft` during movement to depot parking
after required replenishment is complete and all assignment guards pass from
the actual position/stocks. Replace that parking route immediately without
parking arrival, a stop or Idle; preserve elapsed travel and fuel use. This is
distinct from D-026's required field-parking arrival for a coverage replacement.

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

### Diesel-assistance dispatch (D-032)

The recipient waits unavailable in `Alert`. If the second vehicle is busy,
finish its current committed work, then dispatch assistance in `Repositioning`;
do not treat busy status as permanent inability to assist. Prepare/replenish the
donor if required by approach, donation and both vehicles' protected-return fuel
checks. This means a depot visit on its remaining diesel, followed by timed
replenishment of **tractor diesel and trailer aviation fuel**, with pump petrol
independently checked/topped up under D-021. Retain the assistance assignment
through `ToDepot -> Loading -> Repositioning`; depart directly to the recipient
after repeated assistance checks, without a parking/Idle detour or another
aircraft job. The recipient stays Alert throughout. The scenario assumes a
traversable ground network; do not introduce impassable-road
or disaster branches as dispatch requirements.

At actual arrival/setup both vehicles use `Alert Refueling` with a tractor-diesel
assistance operation type and donor/recipient roles. After transfer the recipient
must return to depot. Recheck all three donor stocks before aircraft work,
field waiting or replenishment under D-027. Assistance targets another refueller
and does not use the ordinary aircraft-replacement parking-first trip. Preserve
actual locations, resource accounting and Q-13 authorisation. A donor already
assigned to assistance is not an ordinary waiting-location Repositioning task
that a new aircraft request can silently redirect.

D-038 reserves Refueling for aircraft service and Alert Refueling for diesel
assistance. Repositioning remains the general non-ToDepot/non-ToAircraft transit
state, with task purpose retained; internal Loading depot movement is unchanged.
Never assign aircraft work to a technically unavailable vehicle merely because
its travel state is ToDepot.

D-036 changes an interrupted **aircraft** to Alert while the failed tanker
withdraws; resume Alert to Refueling only when the replacement actually starts
service after required checks/setup. Preserve actual fuel and remaining demand.
D-039 cancels the remainder if the flight is cancelled in Alert before replacement
arrival; stale replacement assignments must not resume that work. D-037 makes
Ready irreversible for the provider, operator and execution alike. Changed/new
flights before Ready use their own check window and retained stock; completion
of an older delivery cannot grant Ready before that window. Actual departure
uses Ready -> Departing -> Departed and D-040's late-readiness rule.

## Operator acceptance and unresolved result handling

**D-031/D-035 — Maintenance and fault withdrawal agreed:** A planned withdrawal stops new
assignments, preserves completion of current service and coordinates field
coverage. Any detected technical fault immediately stops aircraft work and
removes the vehicle from assignments, even if some equipment still functions.
After necessary shutdown/release, a mobile vehicle returns unavailable in
ToDepot and enters Maintenance on arrival. A vehicle unable to move stays Alert
pending separately scoped recovery. Ordinary non-interruption rules concern
priority/replanning changes, not continued use of faulty equipment. Preserve partial delivery and unserved
demand; replan only the actual remaining work without declaring completion.

For the candidate in-service pump failure, review the fastest **feasible**
second-vehicle takeover using actual position, fuel, route/travel limits and
existing commitments. Do not assume a replacement is always idle at depot or
interrupt its own valid service just to improve a route. Whether urgent takeover
may omit D-026's field-parking stop remains Q-14, not an already approved
exception. Record fault-caused loss of coverage. Q-14 selects concrete fault
events, mobility/repair evidence and any non-mobile recovery; it does not reopen
the agreed mobile-vehicle ToDepot-to-Maintenance return sequence.

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

D-033/D-034/D-039 fix cancellation behaviour and the running-service restriction;
command transport, data/version formats and exact simultaneous-event ordering
remain implementation contracts. This does not approve unrelated revise/reset
controls or reopen the agreed restriction. D-040 fixes operator authority over
ETD changes without resolving the other Q-13 mode/command questions. Reject
cancellation, flight replacement and readiness reversal after Ready in execution,
including stale commands; disabling a UI control alone is insufficient.

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
  of deadline performance against fleet route/diesel cost. Specify D-027's
  candidate planning horizon and complete-plan comparison policy; the one-hour
  Staging Point look-ahead does not automatically define this solver horizon.
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
