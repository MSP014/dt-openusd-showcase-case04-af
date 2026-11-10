# Fuel Logistics — Spatial Network Administration

**Status:** Separate module selected; requirements draft, not implemented or
approved for the first demonstration. **Review:** Q-17, with Q-01/Q-02/Q-09/
Q-11/Q-13/Q-15/Q-16 dependencies.

## Purpose and architectural assessment

An administrator should be able to propose changes to the permitted refueller
network in the application without opening Houdini or editing source USD files.
Houdini may prepare the initial layout and network. A route is a plan calculated
over a network; editing the network does not itself dispatch a refueller.

The C4 draft already gives the Spatial Model ground paths, distances and access;
Settings Manager validates and versions module settings; the administration
surface submits edits; Persistence supports recovery; planning proposes routes;
and execution applies authorised changes. These responsibilities provide
integration points, but do not yet specify graph editing, geometric validation,
network revision storage or safe network activation.

**Spatial Network Manager (SNM)** is a separate application module responsible
for network draft editing, validation orchestration, revision lifecycle and
requests for controlled activation. Its APIs, validation implementation,
persistence contract and collaboration with Settings Manager remain open. It is
not a separate service by implication. Spatial Model provides the effective
network to consumers; planning calculates routes; execution applies authorised
state changes. Avoid a second effective network, settings authority, planner or
execution owner. A prepared network is a possible earlier baseline; an
interactive editor is a candidate increment, not an approved first-demonstration
requirement.

## Candidate requirements

These IDs preserve the discussion as reviewable proposals, not accepted
decisions. Exact UX, representations and enforcement owners remain open.

| ID | Proposed requirement and review boundary |
| --- | --- |
| SNM-01 | An authorised administrator can create, modify and remove network nodes and segments in a draft. Identify impacted connections, prevent accidental dangling references and define concurrent-edit conflict handling. |
| SNM-02 | Distinguish refueller access from any aircraft taxi network. The need to edit aircraft routes and interaction at crossings remains open. |
| SNM-03 | Validate the full segment/trajectory against versioned permitted and prohibited areas, including between sampled points. Specify geometry, tolerance and validation method; point-only checks are insufficient. |
| SNM-04 | Validate identity, direction, connections and reachability required by the scenario. Distinguish an intentionally reserved disconnected section from an unusable required service location. |
| SNM-05 | Define applicable vehicle width, clearance, turning and articulated swept-envelope checks. An inside-area centreline alone does not establish feasibility; identify simplified checks and their limits. |
| SNM-06 | Represent applicable access restrictions and controlled crossings. Collision avoidance, traffic coordination and concurrent-vehicle conflicts need separate rules. |
| SNM-07 | Isolate draft changes from the effective network. Validate a specific draft and its referenced layout, permitted-area and vehicle-profile revisions; give actionable errors and invalidate stale validation. |
| SNM-08 | Save network revisions and provenance; distinguish draft, validated, pending and effective states and retain activation history. Restoring an older revision requires controlled activation. |
| SNM-09 | Activate only under a reviewed policy for vehicles, routes, service commitments and field coverage. Planning and execution use the same identified effective network; revalidate or recalculate as required. |
| SNM-10 | Show draft and validation feedback separately from the effective network and current/proposed routes through the chosen presentation bridge. Show revisions and outcomes; projection is not a network authority. |

The Spatial Model should provide consumers with a consistent metric spatial
reference. Geographic mapping for synthetic GPS-like telemetry follows D-047;
origin, axis orientation, conversion and precision remain open. Logical
separation of graph and visual surface does not decide whether graph data is
stored in USD, a database, files or a combination. Define stable IDs, transforms,
units, layout/area revision and compatibility with asset replacement.

## Activation, failures and recovery

Publishing a draft and making it effective may be distinct operations. Before
activation, check that validation still applies to the draft and dependencies,
and that affected routes, vehicle positions, depot access, service commitments
and field coverage have a reviewed disposition. Alternatives include deferred
activation, a controlled stop or a validated replan; selection, event ordering
and authority remain open. No option may teleport vehicles, erase delivered fuel
or silently break D-034 service commitments. A valid network does not guarantee
a feasible fleet plan.

Bind planning inputs/results and execution decisions to effective network and
state revisions. Reject or revalidate an older result under a reviewed policy;
an outdated path must not be presented as current or executed silently. On
infeasible, failed, cancelled or timed-out replanning, expose the outcome and
apply the reviewed safe disposition.

Rejected validation or activation leaves the prior effective network and current
committed state intact. Define atomic publication/activation, retries, concurrent
edits, reset/restart and ambiguous commit readback with Q-01/Q-02/Q-13/Q-16.
Preserve immutable network, layout, reference and activation identity sufficient
to recover coherent state and interpret historical routes and positions. Exact
storage placement, transaction and retention remain open.

## Candidate first increment and acceptance

One possible increment after a prepared-network baseline is editing directed
refueller nodes/segments within fixed permitted areas, topology checks, draft
validation, version persistence, controlled activation and a new route query.
Two-way travel may use directed connections without two manually drawn curves.
Lane geometry, opposing traffic and articulated manoeuvres need selected checks.
Editing area boundaries, road-surface meshes, aircraft networks or a general GIS
editor is excluded from this candidate increment; final scope remains open.

Before acceptance, demonstrate on a representative layout and vehicle profile:

1. An authorised edit creates a draft while the effective network remains
   unchanged; invalid, disconnected or out-of-area examples receive diagnostics.
2. A validated change can be saved and activated under the selected policy; the
   planner uses the new effective revision and labels its result appropriately.
3. Changed validation dependencies and older planning results are detected;
   failed activation or replanning does not silently execute an obsolete route.
4. A change affecting a moving refueller is deferred or handled under the agreed
   policy, including current service commitments and two-refueller coverage.
5. Restart and controlled restoration preserve coherent effective revision,
   vehicle state and history. Tests state geometry and vehicle-envelope limits;
   unsupported cases are not reported as proven.

An initial network test need not require Houdini or a final asset; administration
UI/scene integration and compatibility still need separate Q-15 verification. No
existing editor, solver integration or verified safety guarantee is implied.
