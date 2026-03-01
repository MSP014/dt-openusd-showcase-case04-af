# Guideline 01: Scale, Axis, and World Origin

Unlike enclosed factory rooms or dense server racks, an Air Field is a massive, sprawling environment. This necessitates strict adherence to absolute world scales and transformation rules to ensure vehicles navigate accurately across the VRP logic paths.

## 1. The Real-World Scale Rule

* All assets must be modeled in **Meters** (`metersPerUnit = 1.0`).
* A Boeing 737 is roughly 35 meters long. A TZ-22 is roughly 15 meters long. Do not scale root transforms in the layout stage to "make things look right." If an asset is the wrong size, fix the geometry at the source (Houdini SOPs).

## 2. World Origin (0,0,0) and the "Stage"

The Air Field layout (`/World/Stage/`) constitutes the absolute coordinate system for the simulation.

* **Terrain/Tarmac:** The central tarmac surface should intersect `Y=0`.
* **Runways and Taxiways:** Defined by splines or navmeshes sitting directly on or infinitesimally above `Y=0` to prevent Z-fighting in the viewport.

## 3. Asset Pivot Points (The "Contact Point")

For all dynamic agents (Vehicles, Aircraft, Characters):

* **The Pivot MUST be at the lowest contact point** (the bottom of the tires where they touch the ground).
* **The Pivot MUST rest exactly at `Y=0`** in the local asset file.
* **The Asset MUST be oriented facing `+Z`** (Forward axis).

> [!CAUTION]
> If a TZ-22 refueler's local origin is at its center of mass instead of the ground, any orientation or terrain-following logic will cause the wheels to sink into or float above the tarmac.

## 4. Transform Hierarchy

* Keep hierarchies shallow. Deep nesting of `Xform` nodes leads to performance degradation and difficulty in extracting world-space coordinates for the VRP solver.
* The VRP script will expect the `/World/Factory/Vehicles/TZ22_01` transform to represent the literal world-space location of the vehicle.

---

## ✅ Definition of Done (DoD)

* [ ] The asset's local origin `(0,0,0)` sits exactly at the ground contact plane.
* [ ] The asset faces `+Z`.
* [ ] The scale is checked mathematically against real-world dimensions (Meters).
