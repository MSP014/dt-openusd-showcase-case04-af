# 00. Master USD Contract: Case 04 (Air Field)

This document serves as the absolute baseline for all USD asset generation, validation, and assembly within the Case 04 Logistics Digital Twin. It defines the rigid structural naming conventions and hierarchies that downstream tools and visualizers expect.

## 1. Scene Fundamentals

* **MetersPerUnit:** `1.0` (Meters)
* **UpAxis:** `Y`
* **TimeCodesPerSecond:** `24.0` (Essential for synchronizing multi-agent logistics simulations)

## 2. Core Scene Topology

The top-level hierarchy separates the static environment from the dynamic agents:

* `/World/Stage/` - Static elements (Terrain, Terminals, Markings, Runways).
* `/World/Factory/` - Dynamic/Simulated elements (TZ-22 Refuelers, Aircraft, Ground Crew).
* `/__Class__/` or `/World/Prototypes/` - Instancing prototypes for repetitive assets.

## 3. LODs and Purpose (Geometry)

* **VariantSet Name:** Exactly `LOD` (not `lod`, `Lods`, etc.)
* **Variant Names:** `LOD0` (highest), `LOD1` (medium), `LOD2` (proxy/lowest).
* **Purpose Application:** Purpose attributes (`render`, `proxy`, `guide`) must be applied exclusively to `Geom` (Mesh/Curve) primitives, **not** to the root `Xform` of the component.

## 4. Instancing Guidelines

* **Prototypes:** Must be used for any asset appearing more than 3 times (e.g., Cones, Pallets, Fences).
* **Instance Toggles:** The `instanceable = true` metadata must be set on the leaf pointers referencing the Prototype, never on the Prototype itself.

## 5. Telemetry Schema (v1.0)

All dynamic telemetric data must use the `primvars:` namespace for Hydra compatibility.

* `primvars:telemetry:schemaVersion` = `"1.0"` (String)
* `primvars:vehicle:id` (String - e.g., "TZ22_01", "FLIGHT_773")
* `primvars:logistics:status` (String - e.g., "Idle", "Refueling", "Transit")
* `primvars:logistics:fuelNeeded` (Float - target payload to deliver)
* `primvars:logistics:fuelCurrent` (Float - current payload state)
