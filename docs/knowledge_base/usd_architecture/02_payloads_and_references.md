# Guideline 02: Payloads and References (Assembly)

Managing memory and rendering performance on an Air Field scale is accomplished using **Composition Arcs** (RAM optimization).

## 1. Composition: References vs. Payloads

This dictates *when* a file is loaded into the system's active memory.

### References (Immediate Load)

* **What it is:** The asset loads into RAM as soon as the scene opens.
* **When to use:** Use for lightweight structures, static terrain, navigation meshes, and structural layouts.
* **Rule for Case 04:** The tarmac (`/World/Stage/Terrain`), fundamental hangars, and taxiway lines. The "board" on which the game is played.

### Payloads (Deferred/Lazy Load)

* **What it is:** The asset definition is loaded, but the heavy geometry is skipped until explicitly requested.
* **When to use:** Use for ALL heavy geometry (The TZ-22 Refuelers, The Aircraft, complex ground support equipment).
* **Rule for Case 04:** The actual heavy vehicles must be saved behind Payloads. You see bounding boxes or locators until the script or user actively commands "Load Payload" for a specific vehicle executing a route.

## 2. LODs and Purpose Contract (GPU Optimization)

This dictates *what* the graphics card draws once the asset is actually loaded into RAM.

1. **VariantSet Naming:** You must use a VariantSet named exactly `LOD`.
2. **Variants:** Inside the `LOD` VariantSet, build variants like `LOD0` (highest), `LOD1`, `LOD2` (proxy box).
3. **Purpose Application:** Within any given LOD variant, attach the Purpose metadata (`proxy`, `render`, `guide`) strictly to the **Geom prims** (the actual meshes), **not** to the root Component Xform.

### The Omniverse Viewport Workflow

You load all aircraft payloads, but set the viewport display filter to **Proxy**. Omniverse instantly hides millions of polygons and displays lightweight proxy boxes for the logistics simulation.

---

## ✅ Definition of Done (DoD)

* [ ] Every complex dynamic agent (e.g., TZ-22, Jet) has its main geometry stored as a Payload.
* [ ] The VariantSet for level of detail is universally named `LOD`.
* [ ] Purpose tags (`proxy`, `render`) are applied to Geometry leaf nodes, never to root Xforms.
