# Case 04 — Asset Register

**Status:** Working draft for scope and production review

This register groups the visual assets and spatial elements considered for the
airfield. It combines the existing hero-asset and reference categories with the
proposed environment groups. Items are candidates for review, not a commitment
to model every item, a production schedule or evidence of completed work.

Asset boundaries, quantities, detail levels and reuse will be selected during
layout and production planning. A group may include geometry, materials, markings
or reusable assemblies; it does not prescribe a USD layer or application module.
Reference-gathering tasks do not by themselves establish production scope.

The register separates an asset's **purpose** in the airfield and visual story
from its **functional role** in the planned prototype. A functional role may be a
location, interaction point, route or state to represent; it is not evidence of
implemented behaviour. Background and visual-context items do not automatically
need simulated behaviour.

## Hero vehicles and aircraft

These assets already have dedicated production epics in the implementation plan.
The TZ-22 uses KrAZ-221 visuals; its adopted simulation capabilities are a
separate KrAZ-258B1-based profile.

**Purpose:** Give the airport its recognisable aircraft and refuelling vehicles.

**Functional role:** Refuellers are planned to move, deliver fuel and replenish
stocks; aircraft carry flight and fuel-service state. The role of each aircraft
type, including the helicopter, remains to be selected.

| Asset | Existing epic | Notes |
| --- | --- | --- |
| KrAZ-221 + TZ-22 aircraft refueller | AF-1 | Two refuellers are required for the planned demonstration; a reusable asset can represent both. |
| Tu-104 aircraft | AF-5 | Aircraft types and scenario allocation remain subject to review. |
| Il-18 aircraft | AF-6 | Aircraft types and scenario allocation remain subject to review. |
| Mi-4 helicopter | AF-7 | Its role and inclusion in the final scenario remain to be reviewed. |

## Airfield asset groups

### 01 — Runways and Taxiways

**Purpose:** Establish the airfield's aircraft movement surfaces and visual
context for arrivals and departures.

**Functional role:** Support spatial placement and possible visualisation of
aircraft movement. Aircraft routing, interactions and movement simulation are
not yet defined by this register; landing control is outside the project scope.

- Runway and taxiway surfaces, connecting sections and shoulders.
- Pavement slabs, joints, surface variation and repairs.
- Runway and taxiway markings, signs and edge lights.
- Aircraft movement paths as spatial-layout candidates; their representation
  and simulation scope remain open.

### 02 — Terminal and Airport Facilities

**Purpose:** Establish the airport's architecture, period and visual identity.

**Functional role:** Primarily scene context and spatial landmarks. Passenger
handling and airport-building operations are outside the refuelling scenario.

- Passenger terminal and entrances.
- Control tower and associated structures.
- Hangars, workshops, technical and administrative buildings, garages and
  airport-service buildings.
- Building signage and other architectural details.

The terminal's form and period details require reference review. Passenger
handling is outside the logistics simulation scope.

### 03 — Aircraft Stands and Aprons

**Purpose:** Provide the setting where aircraft park and ground servicing is
visible.

**Functional role:** Represent aircraft stand locations, service positions and
refueller waiting space needed to relate assignments and movements to the scene.
Precise positions, access and occupancy rules remain under review.

- Apron surfaces, aircraft stands, stand numbers, markings and stopping points.
- Aircraft parking and refueller service positions; final clearances and
  equipment connections require review for each selected aircraft.
- Ground-service props such as wheel chocks, passenger stairs, carts, cones and
  fire-extinguisher stands, subject to the visual and scenario scope.
- Field parking or waiting space for refuellers, with its location and layout
  still to be resolved.

### 04 — Fuel Depot

**Purpose:** Provide a recognisable base for refueller replenishment and parking.

**Functional role:** Represent the two exclusive posts, replenishment of the
three onboard stocks, depot circulation and reserve parking in the planned
scenario. Equipment, positions and timing remain subject to detailed review.

- Aviation-fuel storage and dispensing facilities, associated pipework and
  technical buildings; physical equipment and period design require review.
- One aviation-fuel loading post and one auxiliary post with separate diesel
  and pump-petrol dispensers, each accommodating one vehicle at a time.
- Depot circulation, space for both refuellers to park, entrances, exits and
  fencing. Holding positions and detailed post geometry remain open.

The existing scenario defines post operations and reserves; the precise visual
design is not yet specified. The approach road belongs to the shared service-road
network below, with its connection to the depot designed as part of the layout.

### 05 — Service Roads and Ground Infrastructure

**Purpose:** Visually connect the airfield's operational locations.

**Functional role:** Support refueller movement between depot, field parking
and service positions. Routes, travel distances and connectivity must correspond
to the spatial model; graph representation and traffic rules remain open.

- Service-road surfaces and connections between stands, field parking and depot.
- Junctions, markings, signs, barriers and waiting or holding areas as needed.
- Initial vehicle-network paths and interaction points as spatial-layout
  candidates. The relationship between road geometry, routing data and any
  future editing workflow remains under review.

### 06 — Environment and Vegetation

**Purpose:** Establish the landscape, scale and atmosphere around the airfield.

**Functional role:** Primarily visual context. Any effect on movement or access
would need an explicit spatial requirement; none is assumed for these assets.

- Terrain, ground surfaces, grass, shrubs and trees.
- Forest edges, drainage features and distant landscape.
- Peripheral buildings and other background elements where needed for the shot.

## Layout and production review

A metre-scale greybox can establish the spatial relationship between stands,
service roads, depot, terminal and runway before final asset production. It can
also provide placeholder positions and paths for later integration. Asset
replacement, spatial identifiers, coordinate mapping and the authoritative
source of routing data need their own reviewed contracts; this register does not
settle them.

The planned scenario has three aircraft initially on stands, two inbound flights
and a possible sixth unscheduled arrival, with two refuellers. The number and
arrangement of stands, vehicle paths and supporting assets are not fixed here.
A one-refueller fixture may isolate an early check but cannot establish the
two-refueller handover and field-coverage behaviour.

Next review: decide which groups and individual assets are required for the
first visible scenario, which can remain greybox or background, and which need
separate production tasks. Do not treat this list order as an approved schedule.

## Related documents

- [Fuel logistics requirements and scope](requirements/README.md)
- [Scenario and validation requirements](requirements/scenarios_and_validation.md)
- [Refueller visual and simulation profiles](requirements/reference_profiles.md)
- [USD project contract](../knowledge_base/usd_architecture/00_project_usd_contract.md)
- [Example USD grouping](../knowledge_base/usd_architecture/03_instancing_and_scopes.md)
