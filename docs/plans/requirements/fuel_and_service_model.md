# Fuel Logistics — Fuel and Service Model

**Status:** Working draft; three stocks and numerical refueller profile/reserves
adopted; depot operations and normal completion clarified; aircraft conversions,
setup/depot rates and remaining event contracts open

**Last updated:** 31 October 2026

**Primary decisions:** D-004, D-005, D-021, D-042; depot D-018–D-020/D-043

**Review questions:** Q-06, Q-07; assistance Q-03; equipment evidence Q-11

This document owns resource quantities and feasibility rules. Lifecycles belong
to [domain and state machines](domain_and_state_machines.md); selected constants
and evidence status belong to [reference profiles](reference_profiles.md).
These are demonstrator requirements, not a validated runtime or operational
flight-dispatch procedure.

## Quantities and units

**D-005 — direction agreed:** Numerical telemetry is separate from discrete
state enums. Keep at least these distinct current quantities in `WorldState`:

| Recorded field example | Quantity | Meaning |
| --- | --- | --- |
| `aircraft.fuel_onboard` | Aviation fuel | Fuel already aboard the aircraft |
| `tanker.aviation_fuel_remaining` | Aviation fuel | Deliverable stock in the TZ-22 semi-trailer |
| `tanker.tractor_diesel_remaining` | Diesel | Propulsion fuel under the KrAZ-258B1 capability profile; KrAZ-221 visual asset |
| `tanker.pump_gasoline_remaining` | Petrol (Pump Gasoline) | Fuel for the semi-trailer's separate pump engine under D-021 |

The simulation calculates balance changes; USD presents the resulting values.
Keep aircraft aviation fuel and all three refueller stocks distinct. None of
the three fuel types substitutes for another; these field names are examples,
not an approved serialisation or USD schema.

**D-042 — adopted refueller profile:** Account for each refueller stock and
aircraft-service transfer in litres, retaining separate capacities and exact
balances with `0 <= current <= capacity`. Aircraft performance calculations may
use mass (kg), but their canonical schema, usable-stock definition, density and
conversion boundary remain Q-06. Never compare a mass requirement directly with
a litre-valued stock.
Conversion requires a configurable density with declared units; converting
volume into mass does not create additional fuel.

Distinguish the required departure stock from the **uplift demand**, which is
the amount still to deliver. Capacity, usable/unusable stock definitions, fuel
type compatibility and rounding/tolerance rules need explicit agreement.

## Three refueller fuel stocks (D-021)

D-021 adopts the semi-trailer's autonomous petrol pump engine. Aviation fuel,
tractor diesel and pump petrol remain independent; none substitutes for another.
D-042 selects the [simulation profile](reference_profiles.md#adopted-simulation-profile-d-042)
with separate historical-evidence limits.

Every service assignment, including direct continuation, replacement and depot
departure, must pass three guards: deliverable aviation fuel covers the assigned
uplift; diesel covers counted travel/manoeuvres and protected depot return;
pump petrol covers pumping plus its reserve. A replacement uses only the
still-valid undelivered remainder, not the original already-partly-delivered total.
Eligibility, availability, access and timing remain additional guards.

### Adopted resource checks (D-042)

```text
dieselReserve = 330 L * 0.125 = 41.25 L
petrolReserve = 90 L * 0.125 = 11.25 L
countedTravelDiesel = 0.401 L/km * countedDistanceKm
pumpingMinutes = assignedUpliftL / (1,000 L/min)
pumpingPetrol = (15 L/h) * (pumpingMinutes / 60)
```

Require enough stock for the operation and its protected residual reserve, not
merely a current gauge reading above the threshold. For an aircraft assignment,
counted distance includes approach from actual position, modelled manoeuvres and
the feasible return to depot; internal depot movements are excluded from diesel
consumption. Remaining diesel must be at least 41.25 L after that protected
journey; remaining pump petrol at least 11.25 L after pumping. Reaching the
threshold requires replenishment before further consumption of that resource.
Numerical tolerances and any additional pump-running/setup allowances remain open.

A full 22,000 L uplift takes 22 minutes of pumping and consumes 5.5 L petrol.
It therefore needs at least 16.75 L petrol before pumping under this reserve
model, before any separately selected extra engine-running allowance. Setup,
disconnection and release add service time; the 22 minutes is not total service time.
The aircraft gains exactly the aviation fuel actually removed from the tanker.

Tractor diesel consumption in aircraft Refueling is zero: the tractor engine is
off and pumping uses the separate petrol engine. Internal depot moves also use
zero diesel by explicit simplification, while retaining distance/location and
elapsed travel. Other counted ground travel uses the selected distance model.
No independent idle or depot-pump petrol rate is invented by this agreement.

At the auxiliary post, replenish diesel and pump petrol through separate
dispensers with independent balances/durations. Dispensing order remains open.
D-020/D-043 permit partial-stock departure for a feasible assignment. Standby
instead requires all three stocks at 100%, technical readiness, completed
operations and actual arrival/stopping at depot reserve parking. Internal depot
travel does not debit diesel after the final fill.

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

The [D-007 fuel check](domain_and_state_machines.md#fuel-check-and-request-lifecycle)
uses this demand model. Its reviewed triggers include the current-flight window,
an immediate due check after late stand arrival and D-041 normal-completion
accounting, with current-flight readiness checks retained for changed/new flights.
Numerical lead time and rescheduling after an accepted ETD change
remain open; an earlier flight's completed delivery does not open a new window.

## Depot and resource accounting

**D-004 — agreed in discussion:** Aviation fuel is always available at the one
Fuel Depot. Do not track base inventory, supply shortages or replenishment of the
base itself, and do not create a separate depot status machine. D-043's
RefillTank/RefillServiceFuel are states of the refueller. This simplification
does not remove tanker capacity, travel distance, loading time or service constraints.

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
clearance, movement and necessary coordination waits consume time; D-043 separates
RefillTank, RefillServiceFuel and internal Repositioning.
Do not assume equal service durations, overlapping occupancy or instantaneous
swaps. Exact paths/holding positions, first/second tie-breaking, other visit/skip
rules and depot rates remain to be selected; full Standby targets are fixed
by D-043 and assignment targets must meet all guards. The specified
simultaneous-arrival order is agreed.

Resource-accounting requirements, retaining the source architecture principles:

- Every delivery decreases the tanker aviation-fuel stock and increases the
  served aircraft stock consistently, using the agreed units/conversions.
- Depot reloads are explicit actions with duration, not instantaneous invented
  fuel. D-042 fixes the aircraft pumping rate; depot loading, setup and release
  durations still need profiles.
- Diesel is accounted for separately through travel and any explicitly selected
  operating-mode model; aviation-fuel loading alone does not reset the tractor's
  diesel balance. Record diesel replenishment separately under D-016.
- Pump petrol is consumed by its own engine model and replenished separately
  under D-021. Filling any one stock does not reset either of the others.
- Negative stock, over-capacity stock, service before readiness, service after
  departure and movement without elapsed travel are invalid outcomes.

The **D-016 duty-rotation agreement**, extended by D-018–D-021, replenishes the
three stocks as required by the next assignment or the fully replenished Standby
condition. D-042 selects the model's capacities, movement/pumping rates and
numerical reserves; exact depot rates, supply quantities and setup/visit details
remain open. Unlimited aviation-fuel supply
(D-004) and operational depot posts do **not** establish unlimited diesel or petrol stock.

[D-015 / transition 4](domain_and_state_machines.md#transition-4-refueling-to-todepot)
first requires D-027 assignment review when **any** of the three stocks is
insufficient for a candidate, including mixed shortages. A feasible alternative
can keep the vehicle on the field. Use the depot-return branch if no alternative
is selected or replenishment is chosen by the reviewed fleet plan. Insufficient
diesel for the original assignment can coexist with sufficient diesel for the
shorter protected return. D-042 supplies the selected reserve/consumption inputs;
route details and remaining allowances still require definition.

The [D-017 diesel-return and assistance requirement](domain_and_state_machines.md#diesel-reserve-and-exceptional-assistance-d-017)
requires normal checks to preserve a feasible return to the depot with reserve.
If that invariant nevertheless fails, the second tractor supplies diesel from
its own tank. Calculate the amount needed for the recipient's protected return
and check tank headroom, donor approach and the donor's remaining return reserve.
Record donor/recipient balances, elapsed transfer time and any modelled
consumption explicitly. Diesel assistance does not transfer aviation fuel or
create an unaccounted fuel supply. Assistance equipment and transfer rate/setup
parameters remain open; D-042's tractor capacity/road consumption/reserve apply.

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
separate checks. Apply D-042's reserves and define remaining readiness/handover timing
without counting a depot vehicle as physical field coverage.

A single job exceeding a full tanker load needs an agreed partial-delivery or
multiple-visit rule; otherwise report it as infeasible. Never silently truncate
the request. Restricting each initial job to one full load is a scope-control
proposal, not a proven cuOpt feature or an agreed general split-delivery policy.

For D-014's direct next-aircraft branch, sufficient diesel must cover travel to
the next aircraft, service and a subsequent return to Fuel Depot while retaining
the minimum reserve. D-042 fixes the selected minima and distance/pumping model;
remaining setup/engine allowances and aircraft conversions are still open.
Check that any proposed replenishment location is itself reachable.

Tractor diesel consumption covers the adopted movement and operating model,
excluding internal depot movement under the adopted zero-diesel simplification.
Pump-engine petrol consumption is calculated
independently from its running time and operating profile for the required
pumping; do not charge that same engine use to tractor diesel. D-042 selects pump
delivery and petrol consumption rates; any extra
setup/idle engine-running allowance still needs an explicit profile. Whether depot loading uses an external pump or the onboard pump,
and any resulting petrol use, remain open. D-017 diesel-transfer equipment must
be specified separately; do not assume it uses the aviation-fuel pump.
Setup/hose handling is included in service duration under D-012; the detailed
time model remains to be selected.

## Open questions and next steps

D-036 preserves actual aircraft fuel and outstanding committed delivery during
an interruption in aircraft Alert. A replacement supplies only that undelivered
quantity, unless D-039 cancels it before replacement arrival. Cancellation
retains issued fuel in aircraft stock and transfer history; it removes only the
old outstanding obligation, not the physical delivery already made.
D-037 uses retained onboard fuel at the changed/new flight's check window to
determine Ready or additional demand while Parked. No synthetic event may revoke
Ready after it has been granted, and execution rejects equivalent operator
commands. Delivery completion before a changed/new flight's check window leaves
the aircraft Parked, even if its stock is sufficient. A late stand arrival gets
its due check immediately; a window opening during running service does not
interrupt it or duplicate its demand. D-040 retains scheduled ETD separately
from prediction/actual departure when readiness is late. Numerical lead times
and ETD-change rescheduling details remain open.

D-034 retains the agreed delivery quantity/conditions when aircraft service
starts. Later cancellation/revision does not reduce or rewrite that active
commitment. Account for actual transferred fuel even when the flight is cancelled;
finish running committed service in normal operation; D-035 detected-fault
withdrawal stops it regardless of whether some equipment remains functional.
D-039 permits cancellation of the old undelivered obligation while Alert before
replacement arrival. For an 8-tonne operation interrupted after 3 tonnes, record
3 tonnes actually issued and cancel the remaining 5 tonnes if that flight is
cancelled in the reviewed window. A new flight requiring 6 tonnes onboard then
needs 3 tonnes of uplift if there has been no intervening consumption. Never
count the cancelled 5 tonnes as delivered or revive it as the new flight's demand.
The fuelled aircraft remains physically parked. A new flight uses the same
aircraft's actual stock and its own required departure fuel; do not reset fuel
or repeat the original delivery. D-041 closes normal delivery without recalculating unchanged-flight demand;
remaining release/identity/event sequencing is Q-05. D-033 cancellation before
service removes pending demand without inventing
a delivery or undoing prior physical transfers.

D-031/D-035/D-044 interruption handling must preserve actual balances and cumulative
delivery at the event time. For the adopted D-044 pump-failure event, stop
further aviation-fuel transfer when pumping capability is lost; do not credit
the aircraft with the original full request or reset either vehicle's stocks.
The replacement serves the still-valid undelivered committed quantity; current
flight sufficiency is a separate check and D-039 may cancel the old remainder.
Define any fuel consumption during failure/return,
partial-service accounting and repair duration explicitly; maintenance completion
is not replenishment. Pump failure does not itself prove loss of tractor mobility.

- **Q-03 dependency:** Detail D-017/D-032 equipment/setup, rate/amount calculations,
  event timing and records; the assistance state sequence and busy-donor waiting
  are agreed. D-015's return branch for any single-stock or
  mixed shortage follows D-027 assignment review. Quantify its guards under Q-06/Q-07 without
  reopening that behaviour or weakening the protected-return invariant.
- **Q-06:** D-042 fixes litre-valued refueller stocks/capacities and transfer
  rates. Choose aircraft calculation units, density/conversion boundaries,
  tolerances and usable-fuel definitions. Select the departure-fuel calculation and reserve
  framework, numerical check lead time and rescheduling after accepted ETD
  changes, preserving the agreed immediate check on a late stand arrival.
- **Q-07:** Apply D-042's reserves and movement/pumping model; detail the depot
  replenishment capability, reachable-return geometry and additional allowances,
  loading rates and setup/completion durations. Define tractor-to-tractor
  transfer equipment/rate, quantity calculation and donor/recipient constraints.
  Retain D-042's pump-petrol capacity/consumption/reserve; define depot
  replenishment rates for all three stocks,
  remaining post visit/skip rules, first/second tie-breaking, safe holding and
  movement geometry, and diesel/petrol dispensing sequence within the auxiliary
  post. Preserve D-018's agreed simultaneous-arrival order and separate dispensers.
- **Q-11 dependency:** Keep D-042's adopted proxy/synthetic inputs separate
  from equipment evidence; verify historical variant/applicability without
  claiming that adoption establishes measured performance.

Use the [replenishment examples](scenarios_and_validation.md#illustrative-replenishment-cases)
to review resource accounting, then agree fixtures and tolerances using D-042 and labelled aircraft/scenario assumptions.
The examples alone establish neither a validated fuel model nor a complete route.
