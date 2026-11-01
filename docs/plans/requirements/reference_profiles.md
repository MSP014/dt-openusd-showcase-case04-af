# Fuel Logistics — Reference Profiles

**Status:** Working draft; visual/capability trade-off and numerical simulation
profile adopted; historical applicability and aircraft profiles remain open

**Last updated:** 1 November 2026

**Primary decisions:** D-008, D-042

**Review question:** Q-11; fuel-model dependencies Q-06, Q-07

Source links and historical claims retain their previous verification status.
Adoption as a simulation input is separate from verified equipment evidence.
D-042 selects the demonstrator profile without claiming measured airfield
performance or historical compatibility with the visual replica.

## Visual identity and operational profiles

Keep four concerns separate:

| Concern | Intended configuration | Review boundary |
| --- | --- | --- |
| Visual/cinematic identity | Period airfield, Tu-104, Il-18 and early TZ-22/KrAZ-221 replicas; Mi-4 may be added | Reusable film/showreel assets do not establish operating performance or regulatory approval |
| Aircraft operation/planning | Documented contemporary representative jet/turboprop profiles or explicitly synthetic profiles | Select actual profile, capacities, fuel/reserve rules and applicable source edition |
| Refueller capability | KrAZ-258B1 + TZ-22 logical/capability profile under D-042 | Deliberately paired with the KrAZ-221 visual replica; selected values retain separate evidence status |
| Spatial layout | Real-scale geometry, depot/stands/waiting points, road graph and service interfaces | Recalculate accessibility, distances and feasibility when affected geometry changes |

The planned reference framework for flight preparation/fuel reserves is
contemporary civil-aviation guidance with explicit jurisdiction and simplified
assumptions. Historical vehicle physics can be used independently. This is a
standards-informed demonstration, not certification, an actual historical flight
manual simulation or a claim that vintage equipment meets present-day approvals.

## Selected vehicle configuration

**D-042 — agreed in discussion, 31 October 2026:** The visual asset remains
**KrAZ-221 + early TZ-22**, with the source notes reporting a ChMZAP-5204M
semi-trailer chassis. The logical and documented operational configuration is
**KrAZ-258B1 + TZ-22**. This is a deliberate visual/capability trade-off for
asset reuse; present both identities explicitly. Do not label the visual mesh
as a historically verified KrAZ-258B1 assembly or attribute its adopted
consumption profile to a verified early KrAZ-221.

The visual source chronology places KrAZ-221 around 1960 and TZ-22 series
production in 1964, suggesting a 1964–1965 setting for the complete visual
assembly. Exact sub-variant, geometry and chronology verification remain open;
they do not change the selected synthetic demonstrator capability profile.

### Adopted simulation profile (D-042)

| Parameter | Adopted value | Evidence/applicability boundary |
| --- | --- | --- |
| Logical tractor | KrAZ-258B1 | Capability identity, deliberately different from KrAZ-221 visuals |
| Aviation-fuel tank | 22,000 L | Adopted model capacity; exact usable/nominal historical applicability remains unverified |
| Tractor diesel tanks | 2 x 165 L; 330 L total | Adopted aggregate stock/capacity; individual tank plumbing is not modelled by this agreement |
| Pump-petrol tank | 90 L | Explicitly conditional/synthetic proxy from the discussed GAZ-51A/GAZ-52-04 comparison; exact TZ-22 fitment unverified |
| Counted movement diesel | 40.1 L/100 km | Selected distance-based model; not measured slow-speed airfield consumption |
| Pump-engine petrol | 15 L/h while pumping | Selected model input; historical variant/operating conditions unverified |
| Aircraft delivery rate | 1,000 L/min through one hose | Selected pumping rate; setup/release time is additional |
| Tractor diesel in aircraft Refueling | 0 L/h | Tractor engine off; separate petrol engine powers the pump |
| Tractor diesel on internal depot moves | 0 L | Deliberate simplification; physical movement and elapsed time remain |
| Diesel and pump-petrol minimum reserves | 12.5% of their respective capacities | Chosen simulation policy; not a sourced equipment specification |

No additional source verification is implied by adoption. Mark unverified
equipment-derived values as reference proxies and the conditional 90 L tank and
model/policy simplifications as synthetic assumptions until evidence supports
a more specific classification. Source/variant applicability remains Q-11.
The reserve threshold corresponds to half the first 0–25% interval of the
discussed analogue display; internal accounting retains exact litre quantities.
It does not approve a particular UI layout or rounded feasibility calculation.

The [fuel model](fuel_and_service_model.md#adopted-resource-checks-d-042)
owns reserve calculations and assignment checks. Depot replenishment rates,
setup/teardown durations, pump use during depot loading and repair times are not
assigned invented numbers. Aircraft performance profiles and mass/volume
conversion remain separate decisions.

## Equipment evidence register

Rows record source-note claims awaiting applicability verification, except for
the explicitly bounded pump-drive check. A corroborated arrangement does not
verify the exact early sub-variant or its numerical operating parameters.

| Item | Value/claim transferred from source notes | Candidate source | Applicability and next check |
| --- | --- | --- | --- |
| TZ-22 usable capacity | 22,000 L / 22 m3, not 22 tonnes | 2003 technical compendium: [digitised reference](https://ru.djvu.online/file/wPiqc8yoKOW0h); [technical summary](https://www.kriogenavia.ru/articles/457/) | Confirm early semi-trailer variant, usable versus nominal volume and exact source location |
| Early assembly | ChMZAP-5204M semi-trailer with KrAZ-221/221B tractor | [TZ-22 description](https://aviaros.narod.ru/tz-22.htm); [historical overview](https://www.kolesa.ru/article/yaaz-kraz-i-dvukhtaktnye-dizeli-poslevoennaya-tyazhelaya-avtotekhnika-dlya-sovetskoy-armii) | Distinguish 221/221B and early TZ-22 from later TZ-22M; check against modelled geometry |
| Tractor identities | KrAZ-219 truck, KrAZ-221 tractor and later KrAZ-258 are distinct | [1967 vehicle handbook](https://ru.djvu.online/file/AijT2vN9N08xs) | Record relevant pages/variant details |
| Production chronology | KrAZ-221 around 1960; TZ-22 series production from 1964 | [2005 automotive article](https://www.autotruck-press.ru/upload/iblock/775/%D0%96%D1%83%D1%80%D0%BD%D0%B0%D0%BB%20%D0%90%D0%92%D0%A2%D0%9E%D0%A2%D0%A0%D0%90%D0%9A%20%E2%84%961%202005.pdf); [TZ-22 description](https://aviaros.narod.ru/tz-22.htm) | Confirm source passages and compatibility with the intended on-screen year |
| Road diesel norms | KrAZ-221 alone 46.5 L/100 km; with MAZ-5245 trailer 51.5 L/100 km; corresponding 221B values 45.5 and 50.5 L/100 km | [1983 vehicle fuel-norm table](https://meganorm.ru/mega_doc/dop_norm/56/metodicheskie_rekomendatsii_po_razrabotke/0/postanovlenie_gosplana_sssr_ot_17_06_1983_N_171_ob.html) | Different trailer/road conditions; possible reference proxy, not measured loaded TZ-22 airfield consumption |
| Pump drive and third fuel stock | D-021 adopts a separate petrol engine and fuel tank; a GAZ-51A pump drive is documented for TZ-22 | [RU2268203C1, description of the earlier TZ-22](https://patents.google.com/patent/RU2268203C1/ru), citing *Spetsmashiny aeroportov*, 1980, pp. 33–59; [2003 compendium](https://ru.djvu.online/file/wPiqc8yoKOW0h) also describes autonomous drives | Separate drive corroborated on 29 October; exact engine/tank arrangement for the modelled early variant, capacity, consumption and reserve remain unverified |
| Pump and service performance | D-042 adopts 1,000 L/min and 15 L/h petrol for simulation; source verification open | Capacity sources above may include variant-specific pumping data | Verify delivery rate, pump-engine operating profile, setup/completion time and any engine use during depot loading |
| Modern fuel-planning framework | Candidate fuel categories and reserves; no adopted edition | [EASA Easy Access Rules, CAT.OP.MPA.181](https://www.easa.europa.eu/en/document-library/easy-access-rules/online-publications/easy-access-rules-air-operations?erules-id=ERULES-1963177438-12803) | Check current/applicable edition, jurisdiction and selected simplifications before adopting; not historical Soviet rules |

For illustration only, 22,000 L at an assumed 0.80 kg/L is 17,600 kg (17.6 tonnes).
This is a conversion example, not an approved density or a fixed mass/volume
equivalence. The conversion density remains illustrative. D-042's adopted values are
configurable and have the separate applicability limits recorded above.

Use provenance categories `verified`, `reference_proxy` and `synthetic` for
selected inputs. A relevant-looking URL does not by itself establish `verified`
status. Record exact source/edition/location, variant, units, operating
conditions, applicability limits and any calibration. Keep verification state
separate from whether an input is ultimately chosen for the demonstration.

## Destinations and flight legs

**D-008 — direction agreed:** The notional airfield is in Yerevan; historical
appearance is an independent visual choice. The provider selects destinations
from a configurable set of real airports. Discussed directions are Tbilisi,
Ankara, Istanbul, an airport in the UAE (not selected), Berlin, London, New Delhi
and **Tashkent**. Hong Kong was excluded and Tashkent added. The intended set of
roughly ten airports is not final; exact airport identifiers are unspecified.

Calculate fuel for the next flight leg from Yerevan to the next planned landing
airport, not all later stops. The physical module uses the assigned operating
profile and reserve policy; the provider supplies route/conditions rather than
an arbitrary ready-made demand. Further stops are outside this ground-fuel
logistics scope. Reachability and sufficient capacity must be checked for each
selected leg rather than assumed from the destination list.

## Profile and asset binding requirements

The proposed configuration separates `visualAssetId`,
`aircraftPerformanceProfileId`, `refuellerCapabilityProfileId`,
`airfieldNetworkProfileId` and `planningRulesProfileId`. For the refueller, keep
independently identifiable `tractorProfileId`, `semiTrailerProfileId` and, where
needed, `pumpProfileId`. These names are conceptual interfaces, not approved
JSON keys or implemented classes.

| Profile | Information to establish |
| --- | --- |
| Aircraft operation | Fuel/performance category, tank/usable capacity, demand parameters, fuel type and applicable service constraints |
| Tractor/semi-trailer/pump | Separate aviation-fuel, tractor-diesel and pump-petrol stocks/capacities; consumption, reserves, delivery rates, service durations, dimensions and manoeuvring limits |
| Airfield | Two exclusive-occupancy posts with the agreed simultaneous-arrival sequence, separate auxiliary dispensers, depot parking for two refuellers, field boundary and replacement parking, stand/waiting nodes, reachable graph, handover/internal/holding paths, times and clearances |
| Planning rules | Reserve framework, time/priority policy, objective weights and documented assumptions |
| Visual binding | Logical ID to USD asset/prim association and the required spatial/service interface metadata |

A compatible mesh-only swap should not rewrite business logic. A changed tanker
type, dimensions, aircraft service interface or airfield layout requires changed
capability/spatial inputs and renewed feasibility validation. The minimum swap
contract remains open; it is not guaranteed by having matching visual names.

D-047 requires a common airfield reference frame for synthetic GPS-like
aircraft/refueller positions and infrastructure, plus explicit mapping to the
ground graph and Y-up/metre scene. Position samples are simulated telemetry,
not real GPS records. Select the representation/reference and binding metadata
as part of the spatial profile; reserve source-adapter interfaces without
implementing real airport-system bridges. See the [source boundary](architecture.md#input-sources-and-synthetic-position-telemetry-d-047).

## Open questions and next steps

- **Q-11:** Verify the exact assembly and chronology against cited material and
  the scene. Select sourced, proxy or synthetic values with explicit provenance;
  do not upgrade transferred claims during a documentation-only reorganisation.
- Select representative jet/turboprop operating profiles and modern reserve
  reference editions; declare the mapping from visual replicas and all synthetic
  parameters. Do not infer performance from historical aircraft names.
- Finalise real airport IDs, next-leg distances/durations and a reachability
  check; choose the UAE airport and remaining directions.
- Define the minimum binding/capability/spatial metadata for interchangeable
  assets and the validation required after a swap.
- Coordinate consumption, reserve, pumping and replenishment assumptions with
  the [fuel model](fuel_and_service_model.md) before approving numerical fixtures.
- Verify the early pump-engine variant and petrol-tank capacity. D-042 already
  selects simulation capacities, consumption, pumping and reserve values; retain
  their proxy/synthetic status separately from future source confirmation. The shared
  diesel/petrol post is a scenario layout agreement, not a historical-source claim.
