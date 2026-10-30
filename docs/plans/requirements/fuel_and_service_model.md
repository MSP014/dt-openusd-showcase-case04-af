# Fuel Logistics — Fuel and Service Model

**Status:** Working draft; three refueller stocks and depot operations agreed in
discussion; units, consumption profiles, rates and numerical parameters open

**Last updated:** 30 October 2026

**Primary decisions:** D-004, D-005, D-021; depot transitions D-018–D-020

**Review questions:** Q-06, Q-07; assistance implementation Q-03; profiles Q-11

This document owns resource quantities and feasibility rules. Lifecycle triggers
belong to [domain and state machines](domain_and_state_machines.md); equipment
values and their evidence belong to [reference profiles](reference_profiles.md).
All formulae below are prototype proposals transferred from the source notes,
not operational flight-dispatch instructions or a validated implementation.

## Quantities and units

**D-005 — direction agreed:** Numerical telemetry is separate from discrete
state enums. Keep at least these distinct current quantities in `WorldState`:

| Recorded field example | Quantity | Meaning |
| --- | --- | --- |
| `aircraft.fuel_onboard` | Aviation fuel | Fuel already aboard the aircraft |
| `tanker.aviation_fuel_remaining` | Aviation fuel | Deliverable stock in the TZ-22 semi-trailer |
| `tanker.tractor_diesel_remaining` | Diesel | Propulsion fuel in the KrAZ-221 tractor's own tank |
| `tanker.pump_gasoline_remaining` | Petrol (Pump Gasoline) | Fuel for the semi-trailer's separate pump engine under D-021 |

The simulation calculates balance changes; USD presents the resulting values.
Keep aircraft aviation fuel and all three refueller stocks distinct. None of
the three fuel types substitutes for another; these field names are examples,
not an approved serialisation or USD schema.

**Proposed, not agreed:** Store each current quantity in litres and enforce
`0 <= current <= capacity`. The broader proposal also allows aircraft fuel-burn
calculations in mass (kg) while the tanker uses volume (L). Select canonical
state units and conversion boundaries explicitly before approving a schema.
Conversion requires a configurable density with declared units; converting
volume into mass does not create additional fuel.

Distinguish the required departure stock from the **uplift demand**, which is
the amount still to deliver. Capacity, usable/unusable stock definitions, fuel
type compatibility and rounding/tolerance rules need explicit agreement.

## Three refueller fuel stocks (D-021)

**D-021 — agreed in discussion:** The selected prototype models a separate
petrol-driven pump engine on the TZ-22 semi-trailer. Its stock, capacity and
consumption are independent of both aviation-fuel payload and tractor diesel.
The equipment evidence and early-variant verification boundary are recorded in
[reference profiles](reference_profiles.md#equipment-evidence-register).

For **every** aircraft-service assignment, independently require:

| Resource | Feasibility requirement |
| --- | --- |
| Aviation Fuel | Deliverable stock covers the assigned uplift |
| Tractor Diesel | Stock covers actual travel, manoeuvres, any applicable tractor operating consumption and a return to Fuel Depot with protected reserve |
| Pump Gasoline | Stock covers engine operation needed to pump the uplift plus the agreed operating reserve |

These guards apply to departures from `Idle` or `Loading`, direct continuation
after aircraft service and active/standby relief. Pump petrol cannot be treated
as free merely because the tractor has diesel. Numeric pump capacity, petrol
capacity, engine consumption, usable stock and reserve remain open under Q-06/Q-07.

Petrol replenishment takes place at the **Diesel/Petrol Refueling** post
(previously **Diesel Refueling**). It provides diesel and petrol through
**separate dispensers**. Petrol retains its own balance and duration; co-location
does not combine the fuels or establish simultaneous dispensing. The exact
diesel/petrol service sequence within this post remains open.
An assigned departure may use partial stocks if all guards pass (D-020).
Standby readiness retains the fully replenished condition for all three stocks.

## Aircraft demand model

The provider supplies the next destination/flight leg, route distance or duration,
operational aircraft profile, initial stock and conditions. The physical module
calculates required departure fuel and uplift; synthetic input generation must
not replace that calculation with arbitrary favourable numbers.

Transferred conceptual formulation:

```text
requiredFuel = taxiFuel
             + tripFuel
             + contingencyFuel
             + destinationAlternateFuel (when applicable)
             + finalReserveFuel
             + extraOrDiscretionaryFuel (when applicable)

upliftDemand = max(0, requiredFuel - usableFuelAlreadyOnBoard)
```

`tripFuel` depends on the selected operating profile and the next planned leg
from Yerevan to the next landing airport, not the passenger's entire itinerary.
Shorter trips may require smaller uplifts without a proportional reduction,
because reserves and other allowances remain.

The source proposal uses contemporary fuel-planning concepts. Jurisdiction,
applicability, reference edition, reserve policy and simplifications still need
selection and verification. Anticipated holding/delay, if modelled, needs a
documented allowance without double-counting alternate or final-reserve fuel.
The final reserve is protected in the proposed model, not routine holding stock.

Aircraft performance profiles must be documented representative modern profiles
or explicitly synthetic ones. The Tu-104/Il-18 visual names do not establish
historical burn, capacity or certified performance. A separately verified
historical performance study is outside the proposed core prototype.

Missing route, performance, reserve or readiness data remains unknown. The
system must not silently fabricate a feasible demand. Reject/report an aircraft
capacity violation rather than silently capping its calculated requirement.

The proposed [D-007 fuel check](domain_and_state_machines.md#fuel-check-and-request-lifecycle)
uses this demand model. Its trigger timing, ETD changes, late arrivals and
post-service result handling remain open; this document does not approve them.

## Depot and resource accounting

**D-004 — agreed in discussion:** Aviation fuel is always available at the one
Fuel Depot. Do not track base inventory, supply shortages or replenishment of the
base itself, and do not create a separate depot status machine. `Loading` is a
state of the refueller. This simplification does not remove tanker capacity,
travel distance, loading time or service constraints.

Under [D-018–D-020](domain_and_state_machines.md#depot-loading-and-departure),
the depot has an aviation-fuel loading post, a diesel/petrol replenishment post
and separate parking for two refuellers. Each vehicle's required post visits
are sequential; different posts can serve the two vehicles concurrently. A post
can hold only one vehicle. For simultaneous arrivals, the first uses aviation
loading and the second uses diesel/petrol. After the first completes all required
replenishment and departs for an assigned aircraft or proceeds to parking, the
second moves to the vacated aviation post. Any required auxiliary visit by the
first must also respect release of that post by the second.

This agreed sequence replaces the blanket no-post-occupancy-queues assumption.
No additional external queue is modelled, but actual replenishment, post
clearance, movement and necessary coordination waits consume time in `Loading`.
Do not assume equal service durations, overlapping occupancy or instantaneous
swaps. Exact paths/holding positions, first/second tie-breaking, other visit/skip
rules, rates and target quantities remain to be selected; the specified
simultaneous-arrival order is agreed.

Proposed conservation requirements from the architecture note:

- Every delivery decreases the tanker aviation-fuel stock and increases the
  served aircraft stock consistently, using the agreed units/conversions.
- Depot reloads are explicit actions with duration, not instantaneous invented
  fuel. Delivery and loading rates, setup and completion times need profiles.
- Diesel is accounted for separately through travel and any explicitly selected
  operating-mode model; aviation-fuel loading alone does not reset the tractor's
  diesel balance. Record diesel replenishment separately under D-016.
- Pump petrol is consumed by its own engine model and replenished separately
  under D-021. Filling any one stock does not reset either of the others.
- Negative stock, over-capacity stock, service before readiness, service after
  departure and movement without elapsed travel are invalid outcomes.

The **D-016 duty-rotation agreement**, extended by D-018–D-021, replenishes the
three stocks as required by the next assignment or the fully replenished Standby
condition. The facilities are defined conceptually; rates, supply quantities and
numerical reserve calculations remain open. Unlimited aviation-fuel supply
(D-004) and operational depot posts do **not** establish unlimited diesel or petrol stock.

[D-015 / transition 4](domain_and_state_machines.md#transition-4-refueling-to-todepot)
first requires D-027 assignment review when **any** of the three stocks is
insufficient for a candidate, including mixed shortages. A feasible alternative
can keep the vehicle on the field. Use the depot-return branch if no alternative
is selected or replenishment is chosen by the reviewed fleet plan. Insufficient
diesel for the original assignment can coexist with sufficient diesel for the
shorter protected return. Numerical guard calculations remain open.

The [D-017 diesel-return and assistance requirement](domain_and_state_machines.md#diesel-reserve-and-exceptional-assistance-d-017)
requires normal checks to preserve a feasible return to the depot with reserve.
If that invariant nevertheless fails, the second tractor supplies diesel from
its own tank. Calculate the amount needed for the recipient's protected return
and check tank headroom, donor approach and the donor's remaining return reserve.
Record donor/recipient balances, elapsed transfer time and any modelled
consumption explicitly. Diesel assistance does not transfer aviation fuel or
create an unaccounted fuel supply. Equipment and numerical parameters remain open.

D-032/D-038 use Alert Refueling for both donor and recipient during tractor-diesel
assistance. The operation type identifies the fuel and participant roles;
do not apply aircraft uplift or pump-petrol consumption merely because this
state is active. After transfer the recipient must return to depot. Recheck
the donor's actual aviation fuel, tractor diesel and pump petrol before its
next assignment/waiting/replenishment decision. If post-work diesel cannot cover
approach, the donation needed for the recipient's protected return and the
donor's own protected return, first use the donor's remaining diesel to reach
depot under its existing return guard. Replenish both tractor diesel and trailer
aviation fuel there, independently checking/top-up of pump petrol under D-021.
Repeat assistance checks after timed depot operations, then travel to the
recipient while it remains Alert. The scenario
assumes traversable assistance routes; busy status is handled by waiting,
without removing approach/donation/protected-return checks.

## Ground travel and service feasibility

Use reachable ground-network paths, actual location, travel distances/times,
stand readiness, vehicle availability and service durations. Configuration must
cover relevant dimensions, manoeuvring/access limits and service locations.

The physical module is intended to output uplift demands, travel/service-time
estimates, resource/capacity constraints, depot needs and feasibility diagnostics.
Validate these constraints independently of the solver objective or returned
route. Report infeasible/unserved demand explicitly.

D-023's assignment during `Repositioning` uses the vehicle's actual current
position and remaining stocks, including diesel already spent while moving
towards a waiting location. Check the replacement route, service and protected
return rather than reusing estimates from the previous stand or destination.
Under D-025/D-027, known upcoming demand first triggers assignment review;
selected replenishment can then start from `Idle` or `Repositioning` before an
aircraft-service assignment exists. The 10-tonne stock
versus 12-tonne uplift example is a synthetic mass fixture: apply the adopted
units/density profile and verify the full Standby's capacity and all resource
guards. Do not mix tonnes and litres or assume full tanks guarantee a job fits.

D-026 replacement feasibility includes the depot-to-field-parking leg and the
subsequent aircraft route/service/protected return when assigned. Full depot
stocks do not erase travel consumption. Replacement-to-parking dispatch does
not require an aircraft request/window; aircraft-bound departure from parking
does. Resource readiness, actual field presence and service assignment are
separate checks. Define numeric readiness/reserve criteria and handover timing
without counting a depot vehicle as physical field coverage.

A single job exceeding a full tanker load needs an agreed partial-delivery or
multiple-visit rule; otherwise report it as infeasible. Never silently truncate
the request. Restricting each initial job to one full load is a scope-control
proposal, not a proven cuOpt feature or an agreed general split-delivery policy.

For D-014's direct next-aircraft branch, sufficient diesel must cover travel to
the next aircraft, service and a subsequent return to Fuel Depot while retaining
the minimum reserve. No numerical minimum or complete consumption model is
agreed. Check that any proposed replenishment location is itself reachable.

Tractor diesel consumption covers the adopted movement and operating model,
including internal depot movement. Pump-engine petrol consumption is calculated
independently from its running time and operating profile for the required
pumping; do not charge that same engine use to tractor diesel. Pump delivery
rate, engine fuel consumption and any setup/idle allowance need declared
profiles. Whether depot loading uses an external pump or the onboard pump,
and any resulting petrol use, remain open. D-017 diesel-transfer equipment must
be specified separately; do not assume it uses the aviation-fuel pump.
Setup/hose handling is included in service duration under D-012; the detailed
time model remains to be selected.

## Open questions and next steps

D-036 preserves actual aircraft fuel and outstanding need during an interrupted
delivery in aircraft Alert; a replacement resumes service from those quantities.
D-037 uses retained onboard fuel at the changed/new flight's check window to
determine Ready or additional demand while Parked. No synthetic event may revoke
Ready after it has been granted; the demonstration assumes departure readiness
then remains valid. Check-window/service overlap and late readiness are queued
for the next aircraft review, not silently assigned timing rules here.

D-034 retains the agreed delivery quantity/conditions when aircraft service
starts. Later cancellation/revision does not reduce or rewrite that active
commitment. Account for actual transferred fuel even when the flight is cancelled;
finish the committed service in normal operation; D-035 detected-fault withdrawal
stops aircraft service regardless of whether some equipment remains functional.
The fuelled aircraft remains physically parked. A new flight uses the same
aircraft's actual stock and its own required departure fuel; do not reset fuel
or repeat the original delivery. Remaining completion/final-check sequencing is
Q-05. D-033 cancellation before service removes pending demand without inventing
a delivery or undoing prior physical transfers.

D-031/D-035 fault handling must preserve actual balances and cumulative
delivery at the event time. If the Q-14 pump-failure candidate is adopted, stop
further aviation-fuel transfer when pumping capability is lost; do not credit
the aircraft with the original full request or reset either vehicle's stocks.
The replacement serves the reviewed remaining deficit from actual aircraft fuel
and current requirements. Define any fuel consumption during failure/return,
partial-service accounting and repair duration explicitly; maintenance completion
is not replenishment. Pump failure does not itself prove loss of tractor mobility.

- **Q-03 dependency:** Detail D-017/D-032 equipment/setup, rate/amount calculations,
  event timing and records; the assistance state sequence and busy-donor waiting
  are agreed. D-015's return branch for any single-stock or
  mixed shortage follows D-027 assignment review. Quantify its guards under Q-06/Q-07 without
  reopening that behaviour or weakening the protected-return invariant.
- **Q-06:** Choose canonical units, density, capacities, tolerances and the
  usable-fuel definition. Select the departure-fuel calculation and reserve
  framework, and reconcile fuel-check timing with ETD changes and late arrivals.
- **Q-07:** Detail the agreed depot diesel replenishment capability, minimum reserve,
  reachable-return calculation, travel/idle/pumping consumption assumptions,
  loading rates and setup/completion durations. Define tractor-to-tractor
  transfer equipment/rate, quantity calculation and donor/recipient constraints.
  Specify pump-petrol capacity/consumption/reserve, rates for all three stocks,
  remaining post visit/skip rules, first/second tie-breaking, safe holding and
  movement geometry, and diesel/petrol dispensing sequence within the auxiliary
  post. Preserve D-018's agreed simultaneous-arrival order and separate dispensers.
- **Q-11 dependency:** Verify equipment variant and profile applicability before
  promoting candidate reference values to approved model parameters.

Use the [replenishment examples](scenarios_and_validation.md#illustrative-replenishment-cases)
to review resource accounting, then agree numerical fixtures and tolerances.
The examples alone establish neither a validated fuel model nor a complete route.
