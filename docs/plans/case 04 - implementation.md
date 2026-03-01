# Case 04: Air Field Digital Twin (L2) — Implementation Plan

**Jira Project**: AF
**Status**: In Progress
**Last Updated**: 2026-02-01

> [!CAUTION] DISCUSSION ONLY
>
> - **We are currently in DISCUSSION MODE.**
> - **We DO NOT write code.**
> - **We DO NOT modify any code or files without explicit commands.**
> - **We ONLY discuss the proposed problem and/or task.**
> - **This state continues until Max explicitly states "Обсуждение завершено".**

---

## 🧩 EPIC: Hero Asset: Aircraft Refueler TZ-22 (AF-1)

**Status**: 🔄 In Progress | **Priority**: Medium

### ✅ [AF-17] Aircraft Refueler TZ-22 - Pre-production & References

**Status**: ✅ Done | **Priority**: Medium | **Estimate**: 1d
**Logged**: 1d

### ✅ [AF-18] Aircraft Refueler TZ-22 - Modeling

**Status**: ✅ Done | **Priority**: Medium | **Estimate**: 1d

#### ✅ [AF-37] Aircraft Refueler TZ-22 - Blocking

**Status**: ✅ Done | **Priority**: Medium | **Estimate**: 1d
**Logged**: 1w 1d

#### ✅ [AF-38] Aircraft Refueler TZ-22 - Draft (Silhouette)

**Status**: ✅ Done | **Priority**: Medium | **Estimate**: 1d
**Logged**: 3d 1h

#### ✅ [AF-39] Aircraft Refueler TZ-22 - Detailed Draft (High-Poly)

**Status**: ✅ Done | **Priority**: Medium | **Estimate**: 1d
**Logged**: 2w 1d

### 🔄 [AF-19] Aircraft Refueler TZ-22 - Retopology & UVs

**Status**: 🔄 In Progress | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-49] Aircraft Refueler TZ-22 - HP Topology (Refinement/Polish)

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-50] Aircraft Refueler TZ-22 - LP Topology

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### 🔄 [AF-51] Aircraft Refueler TZ-22 - Procedural UV Unwrapping

**Status**: 🔄 In Progress | **Priority**: Medium | **Estimate**: 1d
**Logged**: 1h

### 🔄 [AF-20] Aircraft Refueler TZ-22 - Mechanics & Rigging

**Status**: 🔄 In Progress | **Priority**: Medium | **Estimate**: 1d

### ⏸️ [AF-21] Aircraft Refueler TZ-22 - LookDev

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-61] Aircraft Refueler TZ-22 - Bake Maps and Textures

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-62] Aircraft Refueler TZ-22 - Setup Materials

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

---

## 🧩 EPIC: Pipeline & Data Integration (AF-2)

**Status**: 🔄 In Progress | **Priority**: Medium

### ✅ [AF-81] Protocol Migration Verification

**Status**: ✅ Done | **Priority**: Medium | **Estimate**: 1h
**Objective**: Restoration of artifacts and verification of environment
  integrity following system reboot. Includes pre-commit fix and dependency
  lock.
**Logged**: 30m

---

## 🧩 EPIC: References (AF-3)

**Status**: ✅ Done | **Priority**: Medium

### ✅ [AF-4] Air Field Map

**Status**: ✅ Done | **Priority**: Medium | **Estimate**: 4h
**Logged**: 4h

### ✅ [AF-69] Refs - Airfield Structures

**Status**: ✅ Done | **Priority**: Medium

#### ✅ [AF-71] Passenger Terminal Building (Murmansk / 60s Typ. Project)

**Status**: ✅ Done | **Priority**: Medium | **Estimate**: 1h
**Objective**: Gather references for the main passenger terminal of Murmansk
  Airport (Murmashi) from the 1960s or typical Soviet airport terminals of that
  era. Look for architectural style (Soviet Modernism/Stalinist Empire),
  facades, glazing, signage like "Aeroflot".Search Keywords (RU): аэропорт
  Мурманск 1960-е, аэровокзал Мурмаши фото, типовой советский аэропорт 60-х,
  архитектура аэропорта СССР.
**Logged**: 30m

#### ✅ [AF-72] ATC Tower (Civilian / Joint Use)

**Status**: ✅ Done | **Priority**: Medium | **Estimate**: 1h
**Objective**: Gather references for Soviet ATC (Air Traffic Control) towers
  from the 1960s, focusing on civilian or joint-use airfields. Look for shape,
  brutalist style, brick/concrete textures, glazing type, antenna
  placement.Search Keywords (RU): КДП аэропорта Мурманск 60-е, диспетчерская
  вышка СССР фото, КДП аэродрома СССР, аэродромная архитектура СССР.
**Logged**: 30m

#### ✅ [AF-73] Hangars & Technical Buildings (Aeroflot)

**Status**: ✅ Done | **Priority**: Medium | **Estimate**: 1h
**Objective**: Gather references for hangars used for civilian aircraft
  maintenance and related technical buildings (ATB - Aviation Technical Base) in
  the USSR, 1960s. Look for potential Aeroflot branding.Search Keywords (RU):
  ангар Аэрофлот 60-е, АТБ аэропорт СССР фото.
**Logged**: 5h

#### ✅ [AF-74] Background Buildings (Airport Services)

**Status**: ✅ Done | **Priority**: Medium | **Estimate**: 1h
**Objective**: Gather references for auxiliary airport buildings: fuel depots
  (ГСМ), administrative buildings, garages for airfield vehicles.Search Keywords
  (RU): служебные здания аэропорта СССР.
**Logged**: 1h

#### ✅ [AF-75] Building Materials & Textures

**Status**: ✅ Done | **Priority**: Medium | **Estimate**: 1h
**Objective**: Gather PBR texture references for LookDev: concrete, glass,
  possibly stone/limestone cladding, marble accents, or mosaics on the terminal
  building. Also weathered paint (protective green/grey), peeling plaster,
  concrete panels (ПП), old bricks, slate roofing (шифер).Search Keywords (RU):
  облупившаяся краска текстура, старый бетон текстура, шифер текстура, старый
  кирпич pbr, советский модернизм архитектура текстуры.
**Logged**: 1h 15m

#### ✅ [AF-76] Signage & Typography

**Status**: ✅ Done | **Priority**: Medium | **Estimate**: 1h
**Objective**: Gather references for stencil typography and signage typical for
  the 1960s USSR. Look for generic warning signs ("ОСТОРОЖНО", "НЕ КУРИТЬ"),
  hangar numbers, building plaques, Aeroflot logos, passenger navigation signs.
  Consider neutral or stylized Latin alternatives for final assets.Search
  Keywords (RU): советская типографика 1960-е, трафарет СССР шрифт, вывеска
  Аэрофлот 60-е, советские плакаты безопасность
**Logged**: 1h 30m

### ✅ [AF-70] Refs - Environment

**Status**: ✅ Done | **Priority**: Medium

#### ✅ [AF-77] Pavements (Runway, Taxiways, Apron - Concrete Slabs)

**Status**: ✅ Done | **Priority**: Medium | **Estimate**: 1h
**Objective**: Gather references for airfield concrete slabs (PAG type - ПАГ).
  Look for texture, seams between slabs, bitumen patches, cracks, tire marks,
  oil stains (especially on aprons).Search Keywords (RU): бетонные плиты
  аэродрома ПАГ, перрон аэропорта Мурманск 60-е, старая ВПП текстура,
  аэродромный бетон текстура, швы бетонных плит аэродром, следы шасси на бетоне.
**Logged**: 1h

#### ✅ [AF-78] Pavement Markings (Runway, Taxiways, Apron)

**Status**: ✅ Done | **Priority**: Medium | **Estimate**: 1h
**Objective**: Gather references for airfield pavement markings style from the
  1960s. Look for faded paint, imperfect edges. Include runway numbers
  ("14"/"32"), centerlines, taxiway lines, and typical apron markings (stand
  numbers - МС, service area lines).Search Keywords (RU): разметка перрона
  аэропорта СССР, разметка стоянки самолета 60-е, разметка советского аэродрома,
  старая разметка ВПП, аэродромная разметка 1960-е.
**Logged**: 30m

#### ✅ [AF-79] Ground Infrastructure (Lighting, Fences)

**Status**: ✅ Done | **Priority**: Medium | **Estimate**: 1h
**Objective**: Gather references for apron lighting masts, poles, typical
  concrete fences (PO-2 type - ПО-2), or chain-link fences (сетка-рабица) from
  the period.Search Keywords (RU): аэродромная мачта освещения СССР, советский
  бетонный забор ПО-2, забор сетка-рабица СССР.
**Logged**: 3h

#### ✅ [AF-80] Ground Equipment (Scatter Assets)

**Status**: ✅ Done | **Priority**: Medium | **Estimate**: 1h
**Objective**: Gather references for small details to populate the scene:
  airfield cones, windsocks (ветроуказатели/"колдуны"), signs, ground crew
  handcarts, wheel chocks (колодки), fire extinguishers on stands, passenger
  stairs (трапы), potentially period buses (LiAZ/LAZ), baggage carts.Search
  Keywords (RU): аэродромная техника СССР 60-е, аэродромное оборудование СССР,
  колодки под шасси самолета, автобус ЛАЗ аэропорт, трап самолета СССР.
**Logged**: 2h 30m

---

## 🧩 EPIC: Hero Asset: Plane Tu-104 (AF-5)

**Status**: ⏸️ To Do | **Priority**: Medium

### ⏸️ [AF-22] Plane Tu-104 - Pre-production & References

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

### ⏸️ [AF-23] Plane Tu-104 - Modeling

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-40] Plane Tu-104 - Blocking

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-41] Plane Tu-104 - Draft (Silhouette)

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-42] Plane Tu-104 - Detailed Draft (High-Poly)

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

### ⏸️ [AF-24] Plane Tu-104 - Retopology & UVs

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-52] Plane Tu-104 - HP Topology (Refinement/Polish)

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-53] Plane Tu-104 - LP Topology

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-54] Plane Tu-104 - Procedural UV Unwrapping

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

### ⏸️ [AF-25] Plane Tu-104 - Mechanics & Rigging

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

### ⏸️ [AF-26] Plane Tu-104 - LookDev

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-63] Plane Tu-104 - Bake Maps and Textures

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-64] Plane Tu-104 - Setup Materials

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

---

## 🧩 EPIC: Hero Asset: Plane IL-18 (AF-6)

**Status**: ⏸️ To Do | **Priority**: Medium

### ⏸️ [AF-27] Plane IL-18 - Pre-production & References

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

### ⏸️ [AF-28] Plane IL-18 - Modeling

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-43] Plane IL-18 - Blocking

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-44] Plane IL-18 - Draft (Silhouette)

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-45] Plane IL-18 - Detailed Draft (High-Poly)

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

### ⏸️ [AF-29] Plane IL-18 - Retopology & UVs

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-55] Plane IL-18 - HP Topology (Refinement/Polish)

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-56] Plane IL-18 - LP Topology

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-57] Plane IL-18 - Procedural UV Unwrapping

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

### ⏸️ [AF-30] Plane IL-18 - Mechanics & Rigging

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

### ⏸️ [AF-31] Plane IL-18 - LookDev

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-65] Plane IL-18 - Bake Maps and Textures

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-66] Plane IL-18 - Setup Materials

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

---

## 🧩 EPIC: Hero Asset: Helicopter Mi-4 (AF-7)

**Status**: ⏸️ To Do | **Priority**: Medium

### ⏸️ [AF-32] Helicopter Mi-4 - Pre-production & References

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

### ⏸️ [AF-33] Helicopter Mi-4 - Modeling

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-46] Helicopter Mi-4 - Blocking

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-47] Helicopter Mi-4 - Draft (Silhouette)

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-48] Helicopter Mi-4 - Detailed Draft (High-Poly)

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

### ⏸️ [AF-34] Helicopter Mi-4 - Retopology & UVs

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-58] Helicopter Mi-4 - HP Topology (Refinement/Polish)

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-59] Helicopter Mi-4 - LP Topology

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-60] Helicopter Mi-4 - Procedural UV Unwrapping

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

### ⏸️ [AF-35] Helicopter Mi-4 - Mechanics & Rigging

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

### ⏸️ [AF-36] Helicopter Mi-4 - LookDev

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-67] Helicopter Mi-4 - Bake Maps and Textures

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

#### ⏸️ [AF-68] Helicopter Mi-4 - Setup Materials

**Status**: ⏸️ To Do | **Priority**: Medium | **Estimate**: 1d

---

## 🧩 EPIC: Airfield Structures (AF-12)

**Status**: 🔄 In Progress | **Priority**: Medium

---

## 🧩 EPIC: Environment (AF-13)

**Status**: 🔄 In Progress | **Priority**: Medium

---

## 📊 Progress Summary

| Epic | Status | Priority | Completion |
| --- | --- | --- | --- |
| Hero Asset: Aircraft Refueler TZ-22 | 🔄 In Progress | Medium | 40% (2/5) |
| Pipeline & Data Integration | 🔄 In Progress | Medium | 100% (1/1) |
| References | ✅ Done | Medium | 100% (3/3) |
| Hero Asset: Plane Tu-104 | ⏸️ To Do | Medium | 0% (0/5) |
| Hero Asset: Plane IL-18 | ⏸️ To Do | Medium | 0% (0/5) |
| Hero Asset: Helicopter Mi-4 | ⏸️ To Do | Medium | 0% (0/5) |
| Airfield Structures | 🔄 In Progress | Medium | 0% (0/0) |
| Environment | 🔄 In Progress | Medium | 0% (0/0) |

---

## 🎯 Next Priorities

1. **AF-19**: Aircraft Refueler TZ-22 - Retopology & UVs (Priority: Medium)
2. **AF-20**: Aircraft Refueler TZ-22 - Mechanics & Rigging (Priority: Medium)
3. **AF-21**: Aircraft Refueler TZ-22 - LookDev (Priority: Medium)
4. **AF-22**: Plane Tu-104 - Pre-production & References (Priority: Medium)
5. **AF-23**: Plane Tu-104 - Modeling (Priority: Medium)
