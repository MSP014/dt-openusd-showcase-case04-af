# Fuel Logistics — Domain and State Machines

**Status:** Working draft; tanker transitions 1–3 and the aviation-fuel shortage
branch of transition 4 agreed in discussion; two-refueller rotation agreed;
remaining transitions open; aircraft lifecycle provisional; request lifecycle undefined

**Last updated:** 29 October 2026

**Primary decisions:** D-003, D-006, D-007, D-010, D-012, D-013, D-014, D-015, D-016

**Review questions:** Q-03, Q-04, Q-05; fuel dependencies Q-06, Q-07

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
| Depot | Aviation-fuel replenishment location | Loading timings, travel accessibility and any separately agreed diesel facility |
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

The sixth-flight scenario proposes a revised plan after the event and requires
operator confirmation before execution. Taxiing visuals, additional lifecycle
stages, cancellation and interruption cases remain open. These names are not a
final enum. The general meaning of `Ready` must be narrowed or clarified: the
prototype only models the fuel check, not the complete pre-flight inspection.

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
| `Loading` | Replenishing aviation fuel in the semi-trailer's tank at the depot |
| `ToAircraft` | Travelling to the assigned aircraft |
| `Refueling` | Servicing the aircraft, including setup within overall service duration |
| `Repositioning` | Moving to/between permitted waiting locations without an aircraft-service assignment; wording conflict below remains open |
| `Maintenance` | Unavailable for service during maintenance |

The tanker may serve several aircraft sequentially without a depot visit when
aviation fuel is sufficient and the applicable plan is feasible. Partial tank
depletion does not automatically trigger a depot return. Resource limits and
the planner can require replenishment. Current aviation fuel and tractor diesel
remain distinct numerical fields, not additional states.

## Locations and Repositioning

**D-013 — agreed in discussion; spatial details and threshold open:**

- **Fuel Depot:** One aviation-fuel replenishment base, separate from waiting
  locations; unlimited supply is defined by [D-004](fuel_and_service_model.md#depot-and-resource-accounting).
  The source names `ToDepot -> Loading`, but its transition guards are still open.
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

- The planned full scenario has **two identical KrAZ-221 + TZ-22 refuellers**.
  Normally one is the **Active** duty vehicle on the airfield; the other is
  fully replenished and waiting at the Fuel Depot as **Standby**. These are
  *duty roles*, not additional tanker enum states.
- After cuOpt/planning identifies the next fuel-service request, check whether
  the active vehicle can deliver its required aviation fuel and retain enough
  diesel to perform the service and reach the depot with a protected reserve.
  When it cannot, the standby vehicle takes the next feasible assignment.
- The standby vehicle may depart **before the active vehicle completes its
  current refuelling** if the next departing flight's pre-departure fuel-check
  window has already opened, the check found a deficit and created a request,
  and the active vehicle cannot feasibly serve it. Opening a check window
  alone is **not** a dispatch trigger.
- The current aircraft's refuelling remains uninterrupted. After completing
  it and disconnecting equipment, the relieved vehicle returns to the depot
  if safely reachable. There it replenishes both aviation fuel in the trailer
  and diesel in the tractor, then waits fully replenished as the new Standby.
  The two vehicles alternate these roles as needed.
- Prevent running out of tractor diesel through dispatch guards. Emergency
  diesel transfer from the second tractor was discussed only as an **optional
  contingency concept**, not an approved procedure or initial-scope feature;
  transfer equipment, safe access and recovery rules remain unspecified.
- Whether automatic rotation follows an accepted standing dispatch policy or
  requires acceptance of a particular revised plan remains open in the
  [operator workflow](planning_and_operator_workflow.md).

## Tanker transitions

| Transition | Trigger and conditions | Recorded action/result | Status and limits |
| --- | --- | --- | --- |
| 1: `Idle -> ToAircraft` (D-011) | An eligible next service is assigned; tanker is free and has resources for the feasible plan | Start travelling to the assigned aircraft | Agreed in discussion; queue, override and acceptance details are owned by [planning](planning_and_operator_workflow.md#requests-and-serve-next) |
| 2: `ToAircraft -> Refueling` (D-012) | Arrived at assigned aircraft; aircraft is on stand and ready to receive fuel | Both aircraft and tanker enter `Refueling`; fuel transfers gradually through WorldState | Agreed in discussion; hose connection/equipment preparation are included in overall service time, without a separate setup state |
| 3A: `Refueling -> ToAircraft` (D-014) | Current service finished and equipment disconnected; next request exists; aviation fuel covers that entire request; diesel covers travel, service and return to Fuel Depot with minimum reserve | Travel directly to the next aircraft without visiting a Staging Point | Agreed in discussion; final request-closure semantics need Q-05 review; order follows shared queue/override |
| 3B: `Refueling -> Repositioning` (D-014) | Current service finished and equipment disconnected; no next request | Release aircraft stand and move to a permitted Staging Point | Agreed in discussion; reconcile Repositioning definition and resource feasibility of the waiting movement |
| 4: `Refueling -> ToDepot` (D-015), aviation-fuel shortage | Current service complete; next fuel request exists; tanker lacks aviation fuel for it, but has sufficient diesel to safely reach Fuel Depot with reserve | Return directly to Fuel Depot without Staging Point; a full standby tanker can take the next feasible service as part of D-016 rotation | **Agreed in discussion for aviation-fuel shortage only.** Diesel-only, combined shortage and recovery conditions remain open |
| Standby relief: `Idle -> ToAircraft` (D-016; reuses transition 1) | Active tanker cannot feasibly serve the next request; next flight's fuel-check window has opened; an actual fuel-deficit request exists; standby vehicle has adequate aviation fuel/diesel and is free | Dispatch the standby from Fuel Depot to the aircraft, even before the current active tanker finishes refuelling | **Agreed in discussion.** Current service remains uninterrupted; acceptance/plan policy remains open |
| Depot arrival and loading | Candidate `ToDepot -> Loading` | Guards and loading start conditions to be defined | Open; named in source, not detailed/approved |
| Loading completion and depot departure | To be defined | To be defined | Open |
| Arrival at Staging Point/Main Parking | To be defined | Available stationary state described as `Idle`; transition details to be defined | Open |
| Long wait; reassignment during Repositioning | To be defined | To be defined | Open |
| Maintenance entry and exit | To be defined | To be defined | Open |

The D-014 diesel guard includes enough fuel for reaching the next aircraft,
servicing it and then returning to Fuel Depot with a minimum reserve. Numerical
consumption, reserve and pumping-energy assumptions are still open. The new D-016 rotation agreement assumes tractor diesel can be replenished
at the Fuel Depot; the physical facility, timing and consumption model remain
open (Q-07). Current refuelling is not interrupted merely because another
request gains priority.

## Open questions and next steps

- **Q-03:** Transition 4's aviation-fuel-only return is agreed (D-015).
  Review the diesel-only, combined-shortage and stranded-vehicle cases next;
  establish reachability without approving any recovery behaviour by implication.
- **Q-04:** Reconcile Repositioning wording; then review waiting-location
  arrivals, long-wait movement and assignment during travel individually.
- **Q-05:** Complete aircraft/request lifecycles, ID relationships, cancellation
  and interruption cases. Reconcile final fuel check with request closure and
  define the exact scope of `Ready` and simultaneous state updates.
- **Q-06/Q-07 dependencies:** Define the resource guards required by each
  reviewed transition without guessing units, reserve values or a diesel supply.

After the remaining transition-4 resource cases, follow the sequence in the [resume checkpoint](README.md#resume-checkpoint).
Only extend a state table or its diagram after the corresponding behaviour is
agreed. Keep agreed transitions distinct from provisional models and open rows.
