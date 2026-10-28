# Case 04 — Fuel Logistics Decision-Support Architecture

> **Historical source:** This proposal has been restructured into the
> [requirements collection](../requirements/README.md). Continue requirements
> review there; this retained text records the original proposal and source links.

**Status:** Proposed architecture baseline — not an implementation claim
**Date:** 28 October 2026
**Revision:** Dynamic scheduling, synthetic data, modern planning rules, historical KrAZ-221 + early TZ-22 configuration, interchangeable visual assets and depot replenishment
**Scope:** One airport fuel depot, aircraft refueller(s) and aircraft requiring or potentially requiring fuel
**Intended maturity:** L2 Digital Twin — optimisation and operator-in-the-loop decision support
**Data source:** Synthetic, deterministic scenarios; no connection to an operational airport system

## 1. Objective and boundaries

Case 04 demonstrates how an airfield fuel-logistics prototype recommends a **feasible refuelling sequence** while considering the **consequences of flight delays**, **aircraft fuel demand**, **refueller capacity**, **ground travel** and **changing schedules**. NVIDIA cuOpt is the planned Vehicle Routing Problem (VRP) solver; NVIDIA Omniverse visualises the proposed actions and the reasons for them. **The human operator makes the final decision.**

The prototype is set at a historically styled airfield and uses planned **Tu-104**, **Il-18** and an early **TZ-22 fuel semi-trailer hauled by a KrAZ-221 tractor** as **visual replicas**. These assets are also intended for reuse in a separate animated film. The digital twin uses **contemporary planning and decision-support rules**, while allowing **verified historical physical characteristics of the displayed equipment** (such as tanker capacity, pump delivery rate and tractor diesel consumption) to be selected through configuration. This hybrid demonstration does not claim that historical airframes or refuellers are certified for present-day operation. It excludes baggage, catering, passenger handling, aircraft towing, runway/air-traffic control, landing clearances and wider airport logistics. An urgent diversion is an **input event**, not a claim that this system manages aviation emergencies or prioritises landing traffic.

Synthetic telemetry is not a live airport integration. The application should be described publicly as a **synthetic-data-driven L2 decision-support prototype**, not as a validated operational dispatch or flight-planning tool.

### 1.1 Visual fidelity, planning rules and equipment capabilities

**This separation is a design principle, not an exception to be hidden in a footnote.**

| Layer | What it represents | What it does **not** imply |
| --- | --- | --- |
| **Visual / cinematic** | Period-styled airfield and 3D replicas of the Tu-104, Il-18 and **early TZ-22 semi-trailer with KrAZ-221 tractor**; reusable production assets for a future animated film set in the first half of the 1960s | Historical flight procedures, historical dispatch regulations or proof that these vehicles satisfy modern certification requirements |
| **Planning rules / decision support** | Contemporary, documented flight-fuel planning concepts, reserve categories, time windows, priority assessment and optimisation | An approved flight-dispatch system, a simulation of the actual Tu-104/Il-18 flight manuals or legal/regulatory compliance |
| **Equipment capability / physics** | Independently sourced, configurable tanker capacity, pumping performance, tractor diesel consumption, ground travel and refuelling service times; historical specifications where appropriate | A claim that a historic refueller meets today's technical approval or that a proxy vehicle value is a verified specification of the exact displayed vehicle |
| **Spatial / layout** | Real-scale geometry, stand and depot locations, accessible ground-network distances, service points and clearance constraints | The assumption that a new airport or 3D model can be swapped in without updating affected spatial inputs |

Each entity has a **visual asset identity** separate from the **operational profiles and rules** that determine its behaviour. For example, a Tu-104 replica may use a documented or explicitly synthetic representative jet-aircraft fuel profile; this is **not** a claim about actual Tu-104 performance. The Il-18 replica may use a separate turboprop profile. By contrast, the selected **KrAZ-221 tractor + early TZ-22 semi-trailer** may use **historically documented equipment specifications** (22,000 L usable aviation-fuel volume and, where matched to the specific variant, pump performance and KrAZ-221 diesel consumption) as physical model inputs. The selected values, their sources and any proxy assumptions must be inspectable and replaceable through configuration.

The project uses **modern civil-aviation standards and guidance as its reference framework for flight preparation, fuel reserves and scheduling**, with explicit jurisdiction, applicability and simplifications documented before implementation. The vehicle's **physical performance model may be historical**. The simulation is *standards-informed*, not certified or guaranteed compliant with a particular authority; it must not imply that vintage equipment is approved to perform modern refuelling operations.

### 1.2 Replaceable visuals, configurable capabilities

**Architectural principle:** The digital twin's priority assessment, feasibility rules, VRP construction and state machine must not depend on a particular aircraft mesh, airport art package or tanker model. **Visual representation is interchangeable; physical and operational inputs are explicitly configured.**

| Entity | Replaceable presentation | Independent configuration feeding the logic |
| --- | --- | --- |
| Aircraft | Tu-104, Il-18 or another USD aircraft model | Representative aircraft fuel/performance profile, onboard fuel, route, service window, refuelling interface/location |
| Refueller | **KrAZ-221 tractor + early TZ-22 semi-trailer**, or an alternative USD vehicle assembly | Separately configurable tractor, semi-trailer, pumping and service profiles: usable aviation-fuel capacity, pumping/setup rates, tractor diesel tank and consumption, dimensions, manoeuvring constraints |
| Airfield | Historical or contemporary airport USD environment | Fuel depot and aircraft stand nodes, ground road graph, route distances/travel times, accessible service locations |
| Planning policy | Not a visible asset | Modern fuel-planning reference framework, delay-impact policy, reserve rules, optimiser weights |

The solver consumes **logical entity IDs, coordinates/network data, capability profiles and current state**, not mesh names or model-specific USD paths. A binding/presentation layer associates those IDs with USD assets and renders the result. The interface and exact file schema are **proposed**, not implemented.

Changing only a compatible visual asset should not require rewriting the business logic. Changing a refueller type, its dimensions, an airport layout or an aircraft's actual refuelling interface **does require updating the corresponding capability/spatial profiles and validating feasibility**. A model swap is not a way to bypass real constraints; profiles must be explicit and provenance-labelled (`verified`, `reference_proxy`, `synthetic`).

### 1.3 Selected period vehicle assembly — KrAZ-221 + early TZ-22

**Selected visual configuration:** **KrAZ-221 6×4 tractor unit** towing the **early TZ-22 aviation fuel semi-trailer**, reported on the **ChMZAP-5204M** chassis. This is the intended historical-looking assembly for the showreel and for re-use in an animated film set in the **first half of the 1960s**. It supersedes earlier generic references to a KrAZ-258 or an unspecified tractor; the KrAZ-219 is a related general-purpose truck, **not** the selected tractor. The choice is supported by historical descriptions of the KrAZ-221/TZ-22 pairing, but the exact early sub-variant and as-modelled details still require reference validation.

**Chronology constraint:** Historical references date the **KrAZ-221 tractor to approximately 1960** and the start of **TZ-22 series production to 1964**. Therefore, the complete early TZ-22 combination is suitable for a **1964–1965** film setting; appearances in **1960–1963** should not be treated as historically verified without stronger evidence. This constraint affects visual historical accuracy, **not** the scheduling or optimisation logic.

**Capacity is volume, not mass:** The early TZ-22 is specified at **22,000 litres (22 m³) usable aviation-fuel capacity**, **not 22 tonnes of fuel**. For illustration only, at an assumed density of 0.80 kg/L this is approximately **17.6 tonnes**; model mass separately if relevant. Never hard-code a single mass/volume equivalence without a density assumption.

**Configurable historic capability:** Maintain independently identifiable `tractorProfileId` (KrAZ-221), `semiTrailerProfileId` (early TZ-22), and optional `pumpProfileId` in the refueller configuration. Use sourced KrAZ-221 diesel consumption and TZ-22 tank/pump characteristics where their **variant, operating conditions and units** match. A published **51.5 L/100 km** fuel-use norm for a **KrAZ-221 with a different MAZ-5245 semi-trailer** is a possible `reference_proxy`, **not** a verified consumption figure for the fully laden TZ-22 assembly or short airfield trips. The chosen model may also need separate stationary pumping-fuel/energy assumptions; these must not be silently charged to the tractor's road-diesel consumption.

**Swap contract:** Alternative aircraft, tractor/semi-trailer and airport art assets can be bound to the same logical entities and modules through compatible profiles. Switching to a different tractor or fuel trailer changes the physical inputs and may alter feasible routes, time windows and replenishment decisions; it does not require replacing the priority engine or cuOpt-adapter architecture.

## 2. Proposed architecture and ownership

### Module 0 — Synthetic Data Provider (SDP)

**Purpose:** Supply reproducible flight, aircraft, refueller, depot and scheduling conditions in the absence of a live airport operations feed.

**Responsibilities:**

- **Scenario configuration:** Define separate `visualAssetId`, `aircraftPerformanceProfileId`, `refuellerCapabilityProfileId` (including tractor/semi-trailer profiles where applicable), `airfieldNetworkProfileId` and `planningRulesProfileId` as applicable; reference aircraft classes, route distances, destinations and alternates, flight/cargo categories, ETA/ETD, initial fuel states, stand availability, the depot, the ground network and initial refueller state. The exact configuration schema is not yet finalised.
- **Event scheduling:** Introduce time-stamped schedule changes, arrivals, service-readiness changes, new fuel requests and unscheduled diversions. Events may be scripted or seeded, constrained variations rather than unconstrained random values.
- **State publishing:** Emit an internally consistent, time-stamped snapshot and event log. Preserve `scenario_id`, `random_seed`, simulation time and event sequence for replay and comparison.
- **Reference truth:** Supply input assumptions and authoritative simulated changes; publish actual fuel transfers and vehicle movements **only after** they have been applied by the execution/state layer.

**Outputs:** `ScenarioConfig`, `FlightEvent`, `WorldSnapshot` and a replayable event log (logical interfaces; not yet implemented or finalised as Python classes).

**Boundary:** SDP generates **conditions**, not optimal routes, aircraft uplift calculations or predetermined favourable solver results. Module 2 owns fuel-demand and physical-feasibility calculations. The execution/state layer owns accepted action outcomes and resource conservation; SDP must not independently overwrite these balances.

### Module 1 — Delay Impact and Priority Assessment

**Purpose:** Assess the consequences of delaying each aircraft's fuel service.

- **Inputs:** Flight type, cargo category, ETA/ETD, service readiness, available time margin and explicitly documented impact assumptions.
- **Evaluation dimensions:** Financial, reputational and humanitarian consequences; estimated lateness and operational urgency.
- **Outputs:** A numerical `priorityScore` with an inspectable factor breakdown and explanation.
- **Boundary:** A diversion, medical cargo or an approaching ETD does **not** automatically imply maximum refuelling priority. Scores reflect the need for ground fuel service, not landing priority. Weightings and escalation rules remain proposed policy for review, not certified real-world decision rules.

### Module 2 — Physical Fuel and Service Constraints

**Purpose:** Calculate aircraft uplift requirements and determine what a refueller can physically accomplish and when.

- **Aircraft fuel planning:** Use **contemporary reference-aircraft or explicitly synthetic performance profiles**, not unverified historical Tu-104/Il-18 flight-manual data. Consider intended route distance/time, taxi allowance, contingency allowance, destination holding/delay allowance, alternate-airfield requirement, protected final reserve and existing usable onboard fuel under the selected modern reference framework.
- **Uplift demand:** Compute fuel **to deliver**, not the aircraft's entire required fuel load; respect aircraft tank capacity, units, fuel type and availability for ground service.
- **Aviation fuel:** Track TZ-22 usable load, deliveries, depot replenishment, transfer rates and refuelling setup/completion times. Use historically documented **early TZ-22** pump/tank figures for the selected semi-trailer profile where the variant matches; store configurable alternatives with provenance labels.
- **Truck propulsion fuel:** Track diesel in the tractor's tank, travel distance/consumption and a return-to-depot reserve **separately from aviation fuel**. Select a **KrAZ-221** diesel-consumption profile; prefer data for the correct tractor/load configuration when known, otherwise use an explicitly labelled `reference_proxy` (such as the KrAZ-221 + MAZ-5245 reference) or a synthetic value, with distance, manoeuvring and operating-mode limitations documented. Additional fuel/energy used by auxiliary pumping equipment may be modelled later if supported by references.
- **Operational limits:** Use reachable ground-network paths, service durations, vehicle availability, stand readiness and depot visits. Report infeasible demand rather than inventing fuel or assuming instantaneous replenishment.
- **Outputs:** Flight uplift demands, travel and service-time estimates, capacity/resource constraints, depot replenishment requirements and feasibility diagnostics.

**Boundary:** Physical feasibility must be checked independently of any solver result. An aircraft's need exceeding a single TZ-22 load requires an explicitly supported multi-visit/partial-delivery rule or an infeasibility result; it must not be silently truncated.

### Module 3 — cuOpt Problem Construction and Decision Support

**Purpose:** Convert Module 1 and Module 2 outputs, together with the current accepted world state, into an optimisation problem for **NVIDIA cuOpt (VRP)**.

- **Inputs:** Priority scores, fuel demands, aircraft service-readiness and time windows, refueller/depot state, ground network, remaining commitments and current simulation time.
- **Solver representation:** Travel-time and travel-cost matrices, capacity constraints, service durations, time windows, depot nodes/visits and a documented objective or lateness-penalty formulation.
- **Optimisation:** Obtain a feasible suggested order, route and replenishment sequence. The exact method of encoding priorities, repeated depot visits and any split deliveries depends on the **selected cuOpt interface and its verified features**; unsupported constraints require application-level modelling or validation.
- **Outputs:** Recommended visits, route/path identifiers, timing, proposed depot returns, estimated impacts, unmet demand and feasibility evidence.
- **Presentation:** Omniverse depicts routes, current and proposed assignments, aircraft fuel states, anticipated delays and *why* the suggested plan changed. The operator explicitly accepts or rejects the recommendation.

**Boundary:** cuOpt does not decide the business/humanitarian scoring policy, author synthetic events or guarantee feasibility for constraints omitted from its input.

## 3. Data flow and event loop

```text
 Scenario config + deterministic event timeline
                     |
                     v
       [0. Synthetic Data Provider]
          |                  |
          v                  v
  Flight/schedule data    World state + events
          |                  |
          v                  v
 [1. Delay Impact]  [2. Physical/Fuel Constraints]
          |                  |
          +------ scores ----+---- demands / limits / times
                             |
                             v
                 [3. cuOpt Problem Builder]
                             |
                             v
                       NVIDIA cuOpt
                             |
                             v
                    Proposed route / plan
                             |
                             v
               NVIDIA Omniverse + operator
                             |
                  approve / reject / revise
                             |
                             v
             Execution / state accounting
                             |
                 updated state and events
                             |
                    re-evaluate if needed
```

This is an **event-driven, rolling-horizon** loop, not an instruction to rerun cuOpt on every render frame. Replanning triggers can include ETA/ETD changes, a new arrival/diversion, newly available stand, completed refuelling, refueller replenishment or another material change in feasibility. A configurable planning interval may supplement event triggers.

**Committed actions remain committed:** Completed deliveries cannot be undone; an in-progress refuelling operation must not be arbitrarily interrupted or relocated. A fresh plan starts from the vehicle's **actual current state**, retains any locked commitments and only reschedules eligible future work. The proposal and its acceptance/rejection should be recorded for replay.

## 4. Aircraft fuel-demand model (prototype, non-operational)

For each flight, Module 0 supplies a destination, intended distance or route duration, **operational aircraft profile**, visual-asset mapping and fuel state. Module 2 calculates a simplified required usable fuel budget informed by **contemporary fuel-planning standards**, not historical Soviet operating rules:

```text
requiredFuel = taxiFuel
             + tripFuel
             + contingencyFuel
             + destinationAlternateFuel (when applicable)
             + finalReserveFuel
             + extraOrDiscretionaryFuel (when applicable)

upliftDemand = max(0, requiredFuel - usableFuelAlreadyOnBoard)
```

`tripFuel` should reflect the aircraft profile and planned route; **shorter flights can require smaller uplifts, but not necessarily proportionally smaller loads** because reserves and other allowances still apply. If destination holding is explicitly anticipated, model it as a separate planned delay/extra-fuel allowance or another documented applicable category; **do not double-count it** with the alternate or final reserve. A final reserve is protected and must not be treated as routine holding allowance.

The visual **Tu-104**, **Il-18** (and **Mi-4**, if added) are **historical replicas for visual storytelling and subsequent film reuse only**. Fuel-consumption profiles and aircraft operating constraints should be chosen from documented **modern representative aircraft categories** or explicitly labelled synthetic profiles. A Tu-104-shaped model is therefore **not** assigned actual Tu-104 fuel burn, capacity or certified performance unless separately verified and explicitly selected as a historical research exercise outside the core prototype.

Use a clearly identified **contemporary reference framework** (for example, applicable EASA/ICAO fuel-planning concepts), specifying the reference edition and chosen simplified assumptions. Modern reserve categories guide the model's calculations; they are **not** presented as Soviet-era requirements, nor as a claim that historical airframes meet modern airworthiness or operational rules. Results are demonstration data, **not flight-dispatch instructions**.

**Units:** Store physical quantities with explicit units. Aircraft consumption may be modelled in mass (e.g. kg) and the TZ-22 tank in volume (litres); any conversion must use an explicitly stated, configurable assumed fuel density. A volume-to-mass conversion is not a second fuel supply.

## 5. Prototype fleet and dynamic scheduling

### 5.1 Minimum baseline — solver and state validation

- **One** **KrAZ-221 + early TZ-22** refueller assembly, **three** aircraft requiring refuelling, **one** fuel depot.
- Fixed, reproducible inputs with contrasting priorities, fuel demands and service deadlines.
- Compare a reference sequence with the proposed route, including travel time, service time, unmet demand and any depot return.
- Validate aviation-fuel capacity, propulsion-fuel reserve, service-readiness and aircraft time windows.

### 5.2 Dynamic schedule — five planned flights

The expanded operational scenario contains **five planned flights**, not necessarily five simultaneously available fuel-service tasks. Their visual models can be historical while their flight/fuel-demand profiles reflect the contemporary reference framework:

| Group | Initial condition | Refuelling eligibility |
| --- | --- | --- |
| F01–F03 | Three aircraft on stands awaiting departure | Only if a valid fuel request exists and the stand is ready |
| F04 | One aircraft expected to arrive on schedule | Not serviceable until arrival and ground readiness |
| F05 | One aircraft preparing for near-term departure | Depends on service readiness and whether uplift remains necessary |

ETA and ETD can change as the simulation progresses. **Flight lifecycle state** (inbound, on stand, preparing for departure, departed) is distinct from **fuel-service state** (not requested, pending, ready, refuelling, completed). A parked aircraft may have no outstanding fuel demand; an inbound aircraft may exist in the planning horizon without appearing physically on the apron yet.

### 5.3 Disruption scenario — unscheduled sixth flight

Introduce **F06** as an event such as `UnscheduledArrival` / `FlightDiversion`, for example when the intended destination airport becomes unavailable. The new flight is initially a **forecast arrival**, then transitions to a stand after landing and ground readiness. Its onward departure, fuel demand and service window may initially be unknown and must be supplied or updated before assigning a refuelling job.

The diversion event may require replenishment before onward flight, but **diversion urgency does not itself imply urgent refuelling**. Module 1 evaluates the resulting service consequences; Modules 2 and 3 assess whether and how it can be served. This sixth **flight record** does not require a sixth unique 3D model; USD assets may be reused.

### 5.4 Three TZ-22 replenishment cases (illustrative only)

Use **22,000 litres (22 m³), not 22 tonnes**, as the early TZ-22's documented usable tank capacity for the selected historical assembly, subject to confirmation of the exact semi-trailer variant depicted. This is a **historical physical specification**, not adoption of period aviation fuel-planning regulations. Where available and matched to the equipment variant, use documented **historic pump delivery/setup times and tractor diesel-consumption data**; otherwise mark them as an explicitly selected proxy or synthetic assumption. All such values remain overrideable in the refueller capability profile, with provenance recorded. Contemporary planning and safety concepts remain the reference for the **decision-support logic**, not a certification statement about actual historical hardware. All figures below are **synthetic test inputs, not verified uplift requirements for specific airframes**.

| Case | Example fuel deliveries | Expected behaviour |
| --- | --- | --- |
| A — two deliveries, no immediate return | 6,000 L + 8,000 L | Both feasible from a full tanker; 8,000 L remain |
| B — depot visit between deliveries | 13,000 L + 11,000 L | 24,000 L total exceeds one load; return and replenish between jobs |
| C — replenish before next assignment | 9,000 L + 10,000 L, then a new 7,000 L demand | Two deliveries leave 3,000 L; a depot visit is needed before the next full uplift |

A depot return is an **operational step**, not an automatic action whenever the tank is below full: the route should reflect the next eligible jobs, remaining fuel, travel and depot constraints. Travel-diesel feasibility and the vehicle's actual location must be checked in every case. For scope control, the initial scenarios can require each individual job to fit into one full tanker load; handling larger single-aircraft demands is a separate rule to define.

## 6. Scenario fidelity, repeatability and acceptance criteria

The provider and downstream application should support:

1. **Determinism:** A given `scenario_id`, seed, configuration and accepted action sequence reproduce the same events, demand calculations and expected outputs (subject to documented solver determinism limits).
2. **Conservation:** Every aviation-fuel delivery decreases the tanker's volume and increases the served aircraft's volume consistently; depot reloads are explicit events. Propulsion diesel is conserved separately.
3. **State integrity:** No negative stocks, impossible tank volumes, refuelling before stand readiness, service after departure or instant teleportation on replanning.
4. **Replanning traceability:** Capture the original plan, trigger event, revised priority scores, revised plan, feasibility result and operator decision. Do not present an unapproved plan as executed.
5. **Measurable proof:** Log total tanker distance/travel time, service completion and lateness, depot visits, delivered volumes, missed deadlines and infeasible/unserved jobs; compare against a fixed reference sequence.
6. **Unknowns:** Missing destination, operational-profile performance, reserve policy or arrival-readiness data remain **unknown** until supplied; never fabricate a feasible fuel-demand calculation silently. A historical model name does not fill an operational data gap.

**Minimum shippable proof:** A recruiter can follow a single visible change (schedule disruption or new flight) and its effect on refuelling order; an engineer can reproduce the scenario and inspect the constraints, resource accounting, optimisation input and resulting decision.

## 7. Compatibility with the existing repository

This note complements rather than replaces the existing [README](../../../README.md), [USD master contract](../../knowledge_base/usd_architecture/00_project_usd_contract.md), [telemetry/VRP schema](../../knowledge_base/usd_architecture/04_telemetry_and_vrp_attributes.md) and [L2 maturity definition](../../knowledge_base/digital_twin_maturity_levels.md).

The current USD schema defines `primvars:logistics:fuelNeeded`, `primvars:logistics:fuelCurrent`, `primvars:logistics:status`, `primvars:vehicle:id` and `primvars:logistics:priority` (**integer 1 = highest, 5 = lowest**). The proposed numerical `priorityScore` is **module-internal**, not an authorised replacement for that primvar. A mapping or schema revision must be explicitly designed and validated.

The schema is presently too coarse to express both **flight lifecycle** and **fuel-service status**, or to distinguish **aircraft usable fuel**, **refueller aviation fuel** and **tractor diesel**. These require future **explicit, versioned data-model decisions**; do not silently repurpose existing attributes. The repository's Y-up, metre-scale and 24 fps USD contract remains unchanged.

The standalone Python 3.11/OpenUSD inspection environment is distinct from Houdini and Omniverse Kit embedded Python/USD builds. No runtime ABI compatibility or working cuOpt integration is implied.

## 8. Decisions still open

1. **Modern reference profiles and reserve policies:** Which contemporary jet/turboprop representative profiles, standards/jurisdiction and editions will be used for fuel planning, and which numerical parameters remain explicitly synthetic? How is each visual replica mapped to the selected operational profile?
2. **Priority policy:** How are financial, reputational and humanitarian factors weighted, reviewed and mapped to the existing integer USD priority?
3. **cuOpt integration:** Which repeated-depot, multiple-capacity, time-window, partial-service and committed-route constraints are directly supported by the selected cuOpt interface? What requires application-level modelling and validation?
4. **Vehicle data and provenance:** For the **selected KrAZ-221 + early TZ-22 / ChMZAP-5204M** assembly, which year-specific details, diesel-consumption figures under relevant loads, pumping arrangements and service times are verified? Which values are labelled reference proxies or synthetic, and which can be overridden in the capability profile?
5. **State model:** What is the versioned contract for events, flight lifecycle, refuelling status, fuel quantities and operator decisions?
6. **Acceptance thresholds:** Which fixed reference routes, objective weights and measured improvements establish useful decision support without overstating optimiser performance?
7. **Swap contract:** Which minimum spatial and capability metadata must every alternative aircraft, refueller and airfield asset provide before it is interchangeable with a prepared scenario?

## 9. Reference anchors and limits

- **TZ-22 tank and pump:** *Технические средства тылового обеспечения* (2003), TZ-22 technical characteristics: **22,000 L operating capacity**, with variant-specific pump specifications; see [digitised reference](https://ru.djvu.online/file/wPiqc8yoKOW0h) and [technical summary](https://www.kriogenavia.ru/articles/457/). The exact model configuration and suitability for the selected scene still require confirmation.
- **Selected tractor + trailer pairing:** Historical [TZ-22 technical description](https://aviaros.narod.ru/tz-22.htm) identifies the original **ChMZAP-5204M semi-trailer with a KrAZ-221 tractor**; [an independent historical overview](https://www.kolesa.ru/article/yaaz-kraz-i-dvukhtaktnye-dizeli-poslevoennaya-tyazhelaya-avtotekhnika-dlya-sovetskoy-armii) also describes **KrAZ-221/221B + TZ-22** combinations. Later **KrAZ-258 + TZ-22M** arrangements must not be conflated with the chosen early vehicle. A [1967 vehicle handbook](https://ru.djvu.online/file/AijT2vN9N08xs) distinguishes the KrAZ-219 general-purpose truck, KrAZ-221 tractor and later KrAZ-258 tractor.
- **Historical chronology:** A [historical automotive article](https://www.autotruck-press.ru/upload/iblock/775/%D0%96%D1%83%D1%80%D0%BD%D0%B0%D0%BB%20%D0%90%D0%92%D0%A2%D0%9E%D0%A2%D0%A0%D0%90%D0%9A%20%E2%84%961%202005.pdf) dates production of the **KrAZ-221 to 1960**, while the [TZ-22 description](https://aviaros.narod.ru/tz-22.htm) dates series production to **1964**. The film's exact on-screen year should be checked against the full assembly; do not assert TZ-22 availability before 1964 without corroboration.
- **Historic diesel consumption (reference, not exact TZ-22 validation):** The [1983 Gosplan vehicle fuel-norm table](https://meganorm.ru/mega_doc/dop_norm/56/metodicheskie_rekomendatsii_po_razrabotke/0/postanovlenie_gosplana_sssr_ot_17_06_1983_N_171_ob.html) lists **46.5 L/100 km** for a KrAZ-221 tractor alone and **51.5 L/100 km** with a **MAZ-5245** semi-trailer (with corresponding KrAZ-221B values of 45.5 and 50.5 L/100 km). These are **historical road-norm references for different configurations**, not measured airfield consumption of a KrAZ-221 + TZ-22. Cite the selected baseline, its operating conditions, its provenance category and any calibration before use.
- **Contemporary fuel-planning reference framework:** [EASA Easy Access Rules for Air Operations, CAT.OP.MPA.181](https://www.easa.europa.eu/en/document-library/easy-access-rules/online-publications/easy-access-rules-air-operations?erules-id=ERULES-1963177438-12803). The model may use relevant modern categories and principles with explicitly documented applicability and simplifications. This is **not** a historical Soviet regulation, a claim of certification for the visual replicas or a certified flight-planning method.

**Implementation status:** Proposed architecture only. No working synthetic provider, aircraft fuel-demand model, prioritisation engine, physical-constraint engine, cuOpt integration or Omniverse operator workflow is asserted by this note.
