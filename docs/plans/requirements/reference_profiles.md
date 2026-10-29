# Fuel Logistics — Reference Profiles

**Status:** Working draft; selected visual configuration and candidate source
claims retained; autonomous pump drive corroborated, exact early variant and
numerical parameters still require verification

**Last updated:** 29 October 2026

**Primary decision:** D-008

**Review question:** Q-11; fuel-model dependencies Q-06, Q-07

Source links and numerical claims below were transferred from the 28 October
[architecture proposal](../incoming/fuel_logistics_architecture.md). They were
not independently checked during restructuring. The 29 October pump-drive
addition below records a limited source check; other rows remain an evidence
queue, not newly verified equipment data or an adopted regulatory edition.

## Visual identity and operational profiles

Keep four concerns separate:

| Concern | Intended configuration | Review boundary |
| --- | --- | --- |
| Visual/cinematic identity | Period airfield, Tu-104, Il-18 and early TZ-22/KrAZ-221 replicas; Mi-4 may be added | Reusable film/showreel assets do not establish operating performance or regulatory approval |
| Aircraft operation/planning | Documented contemporary representative jet/turboprop profiles or explicitly synthetic profiles | Select actual profile, capacities, fuel/reserve rules and applicable source edition |
| Refueller capability | Independently configured tractor, semi-trailer and pumping/service profiles | Historical capacity/pump/consumption inputs require the correct variant and conditions |
| Spatial layout | Real-scale geometry, depot/stands/waiting points, road graph and service interfaces | Recalculate accessibility, distances and feasibility when affected geometry changes |

The planned reference framework for flight preparation/fuel reserves is
contemporary civil-aviation guidance with explicit jurisdiction and simplified
assumptions. Historical vehicle physics can be used independently. This is a
standards-informed demonstration, not certification, an actual historical flight
manual simulation or a claim that vintage equipment meets present-day approvals.

## Selected vehicle configuration

The source selects **KrAZ-221 6x4 tractor + early TZ-22 semi-trailer**, reported
on a **ChMZAP-5204M** chassis, for showreel use and reuse in a film set in the first
half of the 1960s. It supersedes generic references to an unspecified tractor or
KrAZ-258. The KrAZ-219 is a related general-purpose truck, not the selected
tractor. Later KrAZ-258 + TZ-22M configurations must not be conflated with it.
The as-modelled early sub-variant and details remain to be checked.

The source chronology places the tractor around 1960 and TZ-22 series production
in 1964, suggesting a 1964–1965 setting for the complete assembly. Verify this
before treating appearances in 1960–1963 as historically established. This is a
visual historical-accuracy constraint, separate from optimisation logic.

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
| Pump and service performance | No selected numeric rate/duration | Capacity sources above may include variant-specific pumping data | Verify delivery rate, pump-engine operating profile, setup/completion time and any engine use during depot loading |
| Modern fuel-planning framework | Candidate fuel categories and reserves; no adopted edition | [EASA Easy Access Rules, CAT.OP.MPA.181](https://www.easa.europa.eu/en/document-library/easy-access-rules/online-publications/easy-access-rules-air-operations?erules-id=ERULES-1963177438-12803) | Check current/applicable edition, jurisdiction and selected simplifications before adopting; not historical Soviet rules |

For illustration only, 22,000 L at an assumed 0.80 kg/L is 17,600 kg (17.6 tonnes).
This is a conversion example, not an approved density or a fixed mass/volume
equivalence. All candidate values remain configurable.

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
| Airfield | Two depot posts without occupancy queues, separate parking for two refuellers, stand/waiting nodes, reachable graph, internal/departure paths, travel times and clearances |
| Planning rules | Reserve framework, time/priority policy, objective weights and documented assumptions |
| Visual binding | Logical ID to USD asset/prim association and the required spatial/service interface metadata |

A compatible mesh-only swap should not rewrite business logic. A changed tanker
type, dimensions, aircraft service interface or airfield layout requires changed
capability/spatial inputs and renewed feasibility validation. The minimum swap
contract remains open; it is not guaranteed by having matching visual names.

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
- Verify the early pump-engine variant and petrol-tank capacity; select sourced
  or explicitly synthetic consumption/rate/reserve values for D-021. The shared
  diesel/petrol post is a scenario layout agreement, not a historical-source claim.
