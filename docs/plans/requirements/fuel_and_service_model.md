# Fuel Logistics — Fuel and Service Model

**Status:** Working draft; resource separation agreed conceptually; units,
calculation profiles, replenishment and numerical parameters open

**Last updated:** 29 October 2026

**Primary decisions:** D-004, D-005

**Review questions:** Q-06, Q-07; transition dependency Q-03; profiles Q-11

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

The simulation calculates balance changes; USD presents the resulting values.
Do not merge aviation fuel with tractor diesel or treat one as a substitute for
the other.

**Proposed, not agreed:** Store all three quantities in litres and enforce
`0 <= current <= capacity`. The broader proposal also allows aircraft fuel-burn
calculations in mass (kg) while the tanker uses volume (L). Select canonical
state units and conversion boundaries explicitly before approving a schema.
Conversion requires a configurable density with declared units; converting
volume into mass does not create additional fuel.

Distinguish the required departure stock from the **uplift demand**, which is
the amount still to deliver. Capacity, usable/unusable stock definitions, fuel
type compatibility and rounding/tolerance rules need explicit agreement.

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

Proposed conservation requirements from the architecture note:

- Every delivery decreases the tanker aviation-fuel stock and increases the
  served aircraft stock consistently, using the agreed units/conversions.
- Depot reloads are explicit actions with duration, not instantaneous invented
  fuel. Delivery and loading rates, setup and completion times need profiles.
- Diesel is accounted for separately through travel and any explicitly selected
  operating-mode model; aviation-fuel replenishment does not refill the tractor.
- Negative stock, over-capacity stock, service before readiness, service after
  departure and movement without elapsed travel are invalid outcomes.

The **D-016 duty-rotation agreement** now assumes tractor diesel is replenished
at the Fuel Depot together with the trailer's aviation fuel; the actual diesel
facility, refilling time, supply quantities and reachable-return rules remain
to be specified. Unlimited aviation-fuel supply (D-004) is **not** automatically
an agreed unlimited-diesel-stock model.

## Ground travel and service feasibility

Use reachable ground-network paths, actual location, travel distances/times,
stand readiness, vehicle availability and service durations. Configuration must
cover relevant dimensions, manoeuvring/access limits and service locations.

The physical module is intended to output uplift demands, travel/service-time
estimates, resource/capacity constraints, depot needs and feasibility diagnostics.
Validate these constraints independently of the solver objective or returned
route. Report infeasible/unserved demand explicitly.

A single job exceeding a full tanker load needs an agreed partial-delivery or
multiple-visit rule; otherwise report it as infeasible. Never silently truncate
the request. Restricting each initial job to one full load is a scope-control
proposal, not a proven cuOpt feature or an agreed general split-delivery policy.

For D-014's direct next-aircraft branch, sufficient diesel must cover travel to
the next aircraft, service and a subsequent return to Fuel Depot while retaining
the minimum reserve. No numerical minimum or complete consumption model is
agreed. Check that any proposed replenishment location is itself reachable.

Tractor consumption varies by configuration and operating conditions. Pumping
may involve additional fuel/energy: defer it until supported or explicitly label
an assumption. Do not silently equate auxiliary pumping energy with road-diesel
consumption. Setup/hose handling is included in service duration under D-012;
the detailed time model remains to be selected.

## Open questions and next steps

- **Q-03 dependency:** Supply explicit aviation-fuel-only, diesel-only and
  combined-shortage cases for tanker transition 4. Define feasible travel to a
  replenishment location without approving the transition in this document.
- **Q-06:** Choose canonical units, density, capacities, tolerances and the
  usable-fuel definition. Select the departure-fuel calculation and reserve
  framework, and reconcile fuel-check timing with ETD changes and late arrivals.
- **Q-07:** Detail the agreed depot diesel replenishment capability, minimum reserve,
  reachable-return calculation, travel/idle/pumping consumption assumptions,
  loading rates and setup/completion durations.
- **Q-11 dependency:** Verify equipment variant and profile applicability before
  promoting candidate reference values to approved model parameters.

Use the [replenishment examples](scenarios_and_validation.md#illustrative-replenishment-cases)
to review resource accounting, then agree numerical fixtures and tolerances.
The examples alone establish neither a validated fuel model nor a complete route.
