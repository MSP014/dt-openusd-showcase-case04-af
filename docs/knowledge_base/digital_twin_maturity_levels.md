# Case 04 — Digital Twin Classification and Claim Boundaries

**Status:** Source-grounded terminology note; runtime capability and operational
maturity have not been validated

**Last updated:** 29 October 2026

**Public description:** Synthetic-data-driven L2-style Decision-Support Prototype

## Source and interpretation

The repository's [reference paper](digital_twins_a_maturity_model_for_their_classification_and_evaluation.pdf)
is **Uhlenkamp et al. (2022), Digital Twins: A Maturity Model for Their
Classification and Evaluation**, IEEE Access 10, pp. 69605–69635,
[DOI: 10.1109/ACCESS.2022.3186353](https://doi.org/10.1109/ACCESS.2022.3186353).
The previous attribution to Tao et al. was incorrect for this paper.

The paper assesses 31 dimensions grouped into context, model, computing
capabilities, data, control, integration and human–machine interaction. Relevant
discussion appears on pp. 69615–69616 (models/analytics and interaction) and in
Section IV/Figure 6; the detailed dimensions are in Tables 12–18. It distinguishes
monitoring/diagnosis, prediction and prescriptive analysis, and considers human
and automatically triggered operation separately.

This multidimensional model must not be presented as a universal L0–L5 ladder.
The shorthand below is a **project communication convention**, not a quotation
of the paper's levels, a calculated maturity score or a standards assessment.
The application of that convention to Case 04 is a project interpretation.

## Working distinction between monitoring, decisions and prediction

| Project shorthand | Main question | Capability evidence needed |
| --- | --- | --- |
| L0-style representation | What does the system look like? | Structured assets and scene representation; no operational data or decision loop implied |
| L1-style monitoring | What is happening? | Data-driven state/status presentation and traceable updates |
| L2-style decision support | What action should be taken, given current inputs and constraints? | Calculated alternatives, feasible service/route decisions, an execution policy and measurable comparison with a baseline |
| Predictive capability, sometimes called L3 in project shorthand | What is likely to happen next? | An explicitly defined forecasting model, horizon and validation against appropriate reference outcomes |

L2-style here adds calculated decisions to state visualisation. Prediction is
a separate capability: decision support can use forecasts, but the L2 label
does not itself prove forecasting. Using announced arrival times, simulating a
candidate route or estimating its completion time does not establish a validated
model of future real-world events.

Likewise, operator involvement and automation must be described explicitly.
Decision support does not by definition require a person to approve every
dispatch. Automatic dispatch inside a synthetic simulation does not establish
autonomous control of physical airport equipment. The [operating-mode contract](../plans/requirements/planning_and_operator_workflow.md#operator-acceptance-and-unresolved-result-handling)
remains under review.

## Case 04 scope and current evidence

The intended demonstration requires two identical refuellers for active/standby
duty rotation and fault tolerance, three aircraft initially on stands, two
inbound flights and an optional sixth unscheduled arrival. The planned system will calculate service order and
ground routes using readiness, deadlines, travel/service time, aviation fuel,
tractor diesel and independent pump-petrol constraints.
Automatic dispatch is a required capability; the detailed operator modes remain
open.

The present repository contains requirements, reference material and development
utilities. The logistics runtime and domain validation remain unimplemented:
`src/` is a placeholder and `tests/test_dummy.py` is an infrastructure smoke test.
The L2-style description therefore states the intended capability, not an
already demonstrated operational maturity.

| Dimension | Current boundary | Planned evidence |
| --- | --- | --- |
| Data origin | No operational airport feed; synthetic provider still planned | Deterministic inputs, declared assumptions, scenario/event provenance and replay |
| Decision logic | No implemented logistics solver/model | Physical validation and cuOpt-versus-EDF comparisons |
| State and control | Requirements only; mode semantics open | Consistent state transitions, automatic dispatch and operator intervention under reviewed rules |
| Physical connection | No live sensor or actuation link to an airport | Separate integration, calibration and operational validation would be required |
| Presentation | Assets/pipeline preparation and planned runtime | Visible routes, fuel, assignments and reasons for changes tied to the same state/plan |
| Prediction | No validated forecasting claim | Define a separate forecasting model and evidence if this enters scope |

## Why synthetic data is a deliberate trade-off

No operational feed from an airport such as Zvartnots has been made available to
this project. Access to schedules, vehicle telemetry and service records would
require cooperation, access agreements, integration and data-quality work.
This statement does not imply that access has been requested or refused.

Synthetic scenarios make it possible to develop and reproduce schedule changes,
resource shortages and routing choices without that dependency. Once implemented
and tested, resource conservation, feasible planning, state ownership, USD
projection and operator/automation behaviour remain useful engineering evidence.

The limitation is that success on those scenarios does not demonstrate airport
calibration, real-data robustness, physical control or operational safety. The
data source, input assumptions and validation domain must remain visible.

Use **Synthetic-data-driven L2-style Decision-Support Prototype — Under
Development** in public descriptions. Describe individual capabilities as
implemented only when there is corresponding evidence. Avoid an unqualified
claim of a live operational L2 Digital Twin or globally optimal real-time routing.

## Next evidence to establish

1. Implement the agreed state/resource model and document scenario provenance.
2. Compare EDF and cuOpt on the [six-flight scenario](../plans/requirements/scenarios_and_validation.md),
   reporting deadlines, coverage, distance, estimated tanker diesel/cost and
   calculation overhead, including equal or worse results.
3. Resolve the automatic/assisted/manual operating-mode questions and demonstrate
   the adopted execution and intervention behaviour.
4. Validate presentation and replay against the same calculation state.
5. Assess real-data integration and forecasting separately if later authorised.
