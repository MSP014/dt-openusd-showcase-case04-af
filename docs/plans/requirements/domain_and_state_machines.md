# Fuel Logistics — Domain and State Machines

**Status:** Working draft; tanker transitions 1–3 and the aviation-fuel shortage
branch of transition 4 agreed in discussion; two-refueller rotation agreed;
safe diesel return and exceptional assistance required; depot transitions 5–7
and three-resource dispatch checks recorded; remaining transition details open;
aircraft lifecycle provisional; request lifecycle undefined

**Last updated:** 29 October 2026

**Primary decisions:** D-003, D-006, D-007, D-010, D-012, D-013, D-014, D-015,
D-016, D-017, D-018, D-019, D-020; resource dependency D-021

**Review questions:** Q-03, Q-04, Q-05; fuel dependencies Q-06, Q-07; modes Q-13

See the [requirements index](README.md#resume-checkpoint) for the continuation
point. Review one tanker transition at a time. The tables preserve the source
discussion's scope; open rows are not approved transitions or final enums.

## Entities and lifecycles

**D-003 — direction agreed:** Aircraft, fuel requests and refuellers need
separate lifecycles. One shared `Idle / Transit / Refueling / Maintenance` enum
does not describe them. USD statuses represent internal states; they do not
implement the state machines.

| Concept | Recorded role | Contract still needed |
| --- | --- | --- |
| Flight | Schedule, arrival type, destination/next leg and operational profile | Flight ID versus physical aircraft ID; repeated flights and reassignment |
| Aircraft | Physical service readiness and onboard aviation fuel | Final lifecycle, capacity/profile references and association with a flight |
| Fuel request | A need for fuel service arising automatically or manually | IDs, quantities, eligibility, queue/assignment, completion, rejection and cancellation |
| Refueller | Tractor plus aviation-fuel semi-trailer with its own operational state | Complete transitions, resource guards, location and current assignment |
| Depot | Two replenishment posts and a separate two-refueller parking area under D-018/D-021 | Service rates, target quantities, visit order and internal paths/timings |
| Waiting location | Main Parking or Staging Point | Selection, accessibility, dwell threshold and reassignment rules |

Arrival type, stop purpose and a fuel-check result are separate data, not extra
enum states. Numerical fuel quantities are defined in [D-005](fuel_and_service_model.md#quantities-and-units).
Exact field names and serialisation beyond the recorded examples remain open.

## Aircraft lifecycle

**D-006 — provisional:** `Scheduled` and `Diversion` describe arrival type. The
candidate aircraft sequence is:

```text
Expected -> Inbound -> Parked -> Refueling -> Ready -> Departed
                         \------------------> Ready
                           if fuel is sufficient (D-007 proposal)
```

An aircraft awaiting a tanker stays `Parked` until refuelling actually begins;
the request carries waiting/queue information. An inbound flight can be known to
the planner without being physically present or eligible for ground service.
Diversion does not itself establish highest refuelling priority.

The clarified schedule has three aircraft on stands, two inbound flights and a
possible sixth unscheduled arrival. The event can trigger a revised plan. The
earlier requirement for operator confirmation of every such change is reopened:
automatic dispatch is required, while the mode-specific confirmation and
intervention contract is open under [Q-13](planning_and_operator_workflow.md#operator-acceptance-and-unresolved-result-handling).
Taxiing visuals, additional lifecycle stages, cancellation and interruption
cases remain open. These names are not a final enum. The general meaning of
`Ready` must be narrowed or clarified: the prototype only models the fuel check,
not the complete pre-flight inspection.

## Fuel check and request lifecycle

**D-007 — proposed working formulation:** For each flight, configure ETD and
`predeparture_check_lead_time`. The proposed check time is `ETD - lead_time`;
1–2 hours is an example, not an agreed value. Compare `aircraft.fuel_onboard`
with `required_departure_fuel` from the [fuel model](fuel_and_service_model.md#aircraft-demand-model).

| Proposed outcome | Aircraft effect | Request effect |
| --- | --- | --- |
| Sufficient fuel | `Parked -> Ready` | No refueller call required |
| Insufficient fuel | Remain `Parked` until actual service starts | Create a request for the calculated deficit |
| Refuelling starts | Aircraft and refueller enter their own `Refueling` states | Active-service representation remains to be defined |
| Final check sufficient | Aircraft becomes `Ready` | Completion semantics remain to be defined |
| Final check insufficient | Return to waiting for service | Clarify/revise the request; exact lifecycle remains open |

`FuelChecked / Sufficient` describes a check result, not a persistent aircraft
state. The flight's request has its own lifecycle; no request enum has yet been
agreed. The source proposal's examples of request stages (not requested, pending,
ready, refuelling, completed) are descriptive examples only.

Lead time, ETD changes, a late diversion, early tanker arrival and cancellation
need explicit rules. For the provisional [transit fuel-stop scenario](scenarios_and_validation.md#scheduled-transit-fuel-stop),
check at actual stand/service readiness rather than mechanically waiting for a
scheduled check time that already passed; the exact trigger is still open.

**Review finding:** D-014 says the current request is closed after refuelling.
D-007 permits an insufficient final check and request revision. Decide the
ordering and consistent updates of fuel check, request closure and both vehicle
states; do not silently resolve this during restructuring.

## Tanker states

**D-010 — basic model and sequential service agreed in discussion:** Use a
separate state model for the KrAZ-221 + early TZ-22 assembly. D-013 adds the
seventh state. Full transition conditions and exceptions remain incomplete.

| Recorded state | Meaning |
| --- | --- |
| `Idle` | Available, waiting for assignment |
| `ToDepot` | Travelling to the aviation-fuel depot |
| `Loading` | Required depot replenishment and internal movement between posts or to depot parking; ends on direct dispatch or arrival at parking under transitions 6/7 |
| `ToAircraft` | Travelling to the assigned aircraft |
| `Refueling` | Servicing the aircraft, including setup within overall service duration |
| `Repositioning` | Moving to/between permitted waiting locations without an aircraft-service assignment; wording conflict below remains open |
| `Maintenance` | Unavailable for service during maintenance |

The tanker may serve several aircraft sequentially without a depot visit when
aviation fuel is sufficient and the applicable plan is feasible. Partial tank
depletion does not automatically trigger a depot return. Resource limits and
the planner can require replenishment. Current aviation fuel, tractor diesel
and pump petrol remain distinct numerical fields, not additional states.

## Locations and Repositioning

**D-013 — agreed in discussion; spatial details and threshold open:**

- **Fuel Depot:** One aviation-fuel replenishment base, separate from waiting
  locations; unlimited supply is defined by [D-004](fuel_and_service_model.md#depot-and-resource-accounting).
  D-018 now defines arrival into `Loading`, two posts and a separate depot
  parking area with capacity for both refuellers. This depot parking area is
  distinct from terminal-side Main Parking and the Staging Points below.
- **Main Parking:** One main long-term parking location near the terminal.
- **Staging Points:** One or two authorised short-term waiting locations close
  to aircraft stands, outside areas where the tanker obstructs movement/taxiing.
- With no immediate next assignment after service, release the aircraft stand,
  move to a suitable Staging Point and wait there. After a long wait, move to
  Main Parking. Approximately two hours is an illustrative dwell threshold,
  not an approved setting.
- Destination (Main Parking or a particular Staging Point) is separate state
  data. A stationary available tanker is `Idle` at either waiting location;
  a new route can start from its actual location.

**Review finding:** D-013 defines `Repositioning` strictly as movement *between*
permitted waiting locations, while D-014 also uses it to leave an aircraft stand.
Agree the complete definition before finalising the enum. Geometry, location
selection, arrival, timeout and reassignment while travelling are still open.

## Two-refueller rotation (D-016 — agreed in discussion)

- The full demonstration requires **two identical KrAZ-221 + TZ-22 refuellers**,
  including provision for fault tolerance under D-017.
  Normally one is the **Active** duty vehicle on the airfield; the other is
  fully replenished and waiting at the Fuel Depot as **Standby**. These are
  *duty roles*, not additional tanker enum states.
- After cuOpt/planning identifies the next fuel-service request, check whether
  the active vehicle can deliver its required aviation fuel and retain enough
  diesel to perform the service and reach the depot with a protected reserve.
  D-021 also requires sufficient pump petrol for the delivery with its operating
  reserve; apply all three checks to either duty vehicle.
  When it cannot, the standby vehicle takes the next feasible assignment.
- The standby vehicle may depart **before the active vehicle completes its
  current refuelling** if the next departing flight's pre-departure fuel-check
  window has already opened, the check found a deficit and created a request,
  and the active vehicle cannot feasibly serve it. Opening a check window
  alone is **not** a dispatch trigger.
- The current aircraft's refuelling remains uninterrupted. After completing
  it and disconnecting equipment, the relieved vehicle returns to the depot
  if safely reachable. There it replenishes aviation fuel, tractor diesel and
  pump petrol as required, then waits fully replenished as the new Standby.
  The two vehicles alternate these roles as needed. A partial-stock departure
  for a specific feasible assignment is allowed under D-020; it does not
  redefine the fully replenished Standby condition.
- Prevent running out of tractor diesel through dispatch guards. Exceptional
  diesel assistance from the second tractor is required under D-017 below;
  transfer equipment, access, timing and lifecycle details remain to be defined.
- Whether automatic rotation follows an accepted standing dispatch policy or
  requires acceptance of a particular revised plan remains open in the
  [operator workflow](planning_and_operator_workflow.md).

## Diesel reserve and exceptional assistance (D-017)

**D-017 — required behaviour agreed in discussion; detailed mechanism open:**

- During normal operation, fuel-level and feasibility checks must prevent a
  tractor from being left without enough diesel to reach the Fuel Depot with
  the protected reserve. Reject an assignment that cannot cover its travel,
  service and protected return. Assistance is an exceptional recovery path,
  not a substitute for those normal resource guards.
- If a tractor nevertheless cannot reach the depot because of insufficient
  diesel, the second refueller must travel to it and transfer enough diesel
  from its own tractor tank to restore the recipient's feasible return to the
  depot. This is tractor-to-tractor diesel transfer, separate from the aviation
  fuel in either semi-trailer.
- Calculate the required transfer from the recipient's actual diesel balance,
  reachable return path, adopted consumption model and minimum reserve. Respect
  its tank capacity; a guessed fixed transfer amount is not the requirement.
- Validate the assisting vehicle's approach and protected return as well. The
  donation must leave it enough diesel for its own feasible remaining movement
  and any transfer-related consumption included in the adopted model.
- Account for diesel leaving the donor and entering the recipient separately
  from any consumption. Transfer is an explicit operation with duration, not a
  free resource reset or an instantaneous scene change.
- Equipment, access/connection rules, transfer rate, assistance scheduling and
  the recovery request/lifecycle mapping remain open under Q-03, Q-05 and Q-07.
  Preserve the existing non-interruption rule for aircraft refuelling and apply
  the operating-mode authorisation contract (Q-13). Handling an unavailable or
  infeasible assisting vehicle also remains open; do not send it without
  sufficient resources or silently invent another recovery procedure.

## Depot loading and departure

### Transition 5: ToDepot to Loading

**D-018 — agreed in discussion:** Arrival at the Fuel Depot automatically
changes `ToDepot -> Loading` and starts the required replenishment operations.
Arrival itself does not refill any tank or complete those operations.

- **Aircraft Fuel Loading:** Loads aviation fuel into the semi-trailer tank.
- **Diesel Refueling:** Replenishes tractor diesel and, under D-021, the separate
  pump-petrol tank. The two fuels retain separate balances and operations.
- Required post visits occur sequentially, with physical movement and elapsed
  service time inside `Loading`. Unneeded replenishment may be skipped for a
  specific feasible assignment under D-020. The exact visit-order policy and
  diesel/petrol service sequence at the auxiliary post remain open.
- Both posts are always available in this prototype: no post-occupancy queue
  or contention delay is modelled, including when both refuellers visit the
  depot. This is a service-capacity simplification, not an approval of unlimited
  diesel or petrol inventory. Loading and travel durations still apply.
- A separate depot parking area holds **two TZ-22 assemblies simultaneously**.
  It is not either replenishment post.

The result of transition 5 is entry into depot operations. Completion is checked
before transition 6 or 7, rather than being an immediate effect of arrival.

### Transition 6: Loading to Idle

**D-019 — agreed in discussion:** Once required replenishment is complete and
there is **no assigned aircraft-service request**, send the refueller to depot
parking. It remains `Loading` during this internal movement and enters `Idle`
only on actual arrival. Both vehicles may park there concurrently.

The reserve vehicle retains D-016's fully replenished Standby condition, now
covering all three refueller stocks. With a valid assigned request, evaluate
transition 7 instead of inserting an `Idle` step. A request arriving during
movement to parking needs an explicit reassignment/event-order rule under Q-04;
this text does not silently select that rule.

### Transition 7: Loading to ToAircraft

**D-020 — agreed in discussion:** At the depot, required replenishment is complete,
an actual current aircraft-service request is assigned, and the whole assignment
is feasible. Independently check the [D-021 stocks](fuel_and_service_model.md#three-refueller-fuel-stocks-d-021):

- Aviation fuel covers the requested uplift.
- Tractor diesel covers the trip, required manoeuvres, applicable service use
  and return to the Fuel Depot with the protected reserve.
- Pump petrol covers the required aviation-fuel pumping with its operating reserve.

Full tanks are not required for this assigned departure. For example, replenish
only aviation fuel if the other stocks already cover the assignment and their
reserves. When all guards pass, assign the route, change `Loading -> ToAircraft`
and depart directly. If any stock is insufficient, remain `Loading` for the
necessary replenishment and repeat the checks. With no assigned request, follow
transition 6. Direct departure belongs to transition 7, not transition 5.

## Tanker transitions

| Transition | Trigger and conditions | Recorded action/result | Status and limits |
| --- | --- | --- | --- |
| 1: `Idle -> ToAircraft` (D-011) | An eligible next service is assigned; tanker is free, passes all three D-021 fuel checks and meets the other feasibility guards | Start travelling to the assigned aircraft | Agreed in discussion; queue, override and acceptance details are owned by [planning](planning_and_operator_workflow.md#requests-and-serve-next) |
| 2: `ToAircraft -> Refueling` (D-012) | Arrived at assigned aircraft; aircraft is on stand and ready to receive fuel | Both aircraft and tanker enter `Refueling`; fuel transfers gradually through WorldState | Agreed in discussion; hose connection/equipment preparation are included in overall service time, without a separate setup state |
| 3A: `Refueling -> ToAircraft` (D-014) | Current service finished and equipment disconnected; next request exists; aviation fuel covers that entire request; diesel covers travel, service and protected return; pump petrol covers pumping with reserve (D-021) | Travel directly to the next aircraft without visiting a Staging Point | Agreed in discussion; final request-closure semantics need Q-05 review; order follows shared queue/override |
| 3B: `Refueling -> Repositioning` (D-014) | Current service finished and equipment disconnected; no next request | Release aircraft stand and move to a permitted Staging Point | Agreed in discussion; reconcile Repositioning definition and resource feasibility of the waiting movement |
| 4: `Refueling -> ToDepot` (D-015), aviation-fuel shortage | Current service complete; next fuel request exists; tanker lacks aviation fuel for it, but has sufficient diesel to safely reach Fuel Depot with reserve | Return directly to Fuel Depot without Staging Point; a full standby tanker can take the next feasible service as part of D-016 rotation | **Agreed in discussion for aviation-fuel shortage only.** Diesel-only and combined-shortage transitions remain open; D-017 requires exceptional diesel assistance without finalising its lifecycle |
| Standby relief: `Idle -> ToAircraft` (D-016; reuses transition 1) | Active tanker cannot feasibly serve the next request; next flight's fuel-check window has opened; an actual fuel-deficit request exists; standby is free and passes all three D-021 fuel checks | Dispatch the standby from Fuel Depot to the aircraft, even before the current active tanker finishes refuelling | **Agreed in discussion.** Current service remains uninterrupted; acceptance/plan policy remains open |
| 5: `ToDepot -> Loading` (D-018) | Actual arrival at Fuel Depot for required replenishment | Start timed depot operations; two posts and internal movements are within `Loading` | Agreed in discussion; no post queues; rates, visit order and stock quantities remain open |
| 6: `Loading -> Idle` (D-019) | Required replenishment complete; no assigned request; actual arrival at depot parking | Become available at depot parking, which holds both vehicles | Agreed in discussion; internal parking movement remains `Loading`; assignment during that movement is open |
| 7: `Loading -> ToAircraft` (D-020) | Required replenishment complete; valid request assigned; all three stocks, readiness and route checks pass | Depart directly to the assigned aircraft without `Idle` | Agreed in discussion; full tanks not required; insufficient stock keeps the vehicle in `Loading` |
| Arrival at Staging Point/Main Parking | To be defined | Available stationary state described as `Idle`; transition details to be defined | Open |
| Long wait; reassignment during Repositioning | To be defined | To be defined | Open |
| Maintenance entry and exit | To be defined | To be defined | Open |

The D-014 diesel guard includes enough fuel for reaching the next aircraft,
servicing it and then returning to Fuel Depot with a minimum reserve. Numerical
consumption, reserve and pumping-energy assumptions are still open. The D-016
rotation agreement assumes tractor diesel can be replenished at the Fuel Depot;
the post is now specified by D-018/D-021, while timing and the consumption model
remain open (Q-07). D-021 adds independent pump-petrol guards to **every**
aircraft-service assignment, including transitions 1, 3A and standby relief.
Current refuelling is not interrupted merely because another request gains priority.

## Open questions and next steps

- **Q-03:** Transition 4's aviation-fuel-only return is agreed (D-015).
  Review diesel-only, pump-petrol-only and mixed-shortage return transitions. Detail
  the required D-017 assistance path, including reachability, donor availability
  and failure handling; its requirement is agreed, its full lifecycle is not.
- **Q-04:** Reconcile Repositioning wording; then review waiting-location
  arrivals, long-wait movement and assignment during travel individually,
  including a new request during the `Loading` movement to depot parking.
- **Q-05:** Complete aircraft/request lifecycles, ID relationships, cancellation
  and interruption cases. Reconcile final fuel check with request closure and
  define the exact scope of `Ready` and simultaneous state updates. Define the
  assistance-request lifecycle without conflating it with aircraft fuel demand.
- **Q-06/Q-07 dependencies:** Define the resource guards required by each
  reviewed transition using the agreed depot replenishment capability, without
  guessing units, reserve values, stock quantities or replenishment rates.
- **Q-13 dependency:** Define which dispatch/replanning actions are automatic and
  where confirmation or intervention applies without changing physical guards.

After the remaining transition-4 resource cases, follow the sequence in the [resume checkpoint](README.md#resume-checkpoint).
Only extend a state table or its diagram after the corresponding behaviour is
agreed. Keep agreed transitions distinct from provisional models and open rows.
