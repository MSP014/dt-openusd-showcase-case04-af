# Fuel Logistics — Persistence and Data Storage

**Status:** Draft; single-server MySQL and database separation proposed during
architecture review. Names and physical schemas are working proposals, not
created databases or an installed integration.
**Date:** 1 November 2026, Asia/Yerevan.
**Review question:** Q-16; dependencies Q-01/Q-02/Q-05/Q-10/Q-11/Q-13.

## Storage contour

Use one proposed MySQL server with separate Case 04 databases, rather than
separate database products or a database for every module. Their working names
are namespaced independently of SPLit or any other project on a shared server.

| Proposed database | Stored information | Authority and consumers |
| --- | --- | --- |
| `af_core` | Runs, effective profile/configuration versions, simulation time, current entity state/positions, route progress, assignments, request/commitment records, actual fuel balances and transfer ledger, input/applied-event and decision history | Durable backing for the single logical WorldState and actual service facts; execution/recovery/history queries |
| `af_telemetry` | Time-stamped synthetic GPS-like position history and selected measurement samples, tied to entity/run identity, state revision, source provenance and spatial reference | Recorded observations/history of the same simulation; replay, path inspection and analysis, without a second movement or balance authority |
| `af_analytics` | Derived fuel statistics, reporting-period aggregates, route/diesel/petrol summaries and EDF/cuOpt comparison results | Recomputable results with source run/revision/time and metric definitions; UI reports and benchmark inspection |
| `af_settings` | Primary settings for all application modules, selected profile/policy parameters, settings revisions and change/activation history | Settings Manager validates and versions changes; modules receive effective configuration; the administration surface uses this manager |

Fuel requests and actual fuel transfers are operational facts in `af_core`,
not facts stored only as analytics totals. Current position is part of the
consistent core state; its historical samples belong in `af_telemetry`.
Duplicating a value into a telemetry sample or report does not give it a second
independent update owner. Analytics must not write back authoritative stocks.

USD geometry, textures and other heavy visual assets remain files. Scenario,
graph and other input datasets may remain versioned files selected by settings.
Primary module settings belong in `af_settings`. Preserve effective values or
immutable revision references in each run's core state so later admin edits
cannot alter the meaning of historical data.

## Module settings, bootstrap files and administration

The proposed Settings Manager owns loading, validation, versioning and change
history for all application modules' settings. It accesses the database through
Persistence. An administration page/panel inspects values/history and submits
changes through this manager; its UI technology and access policy remain open.

Duplicate minimal startup-critical settings in local bootstrap files, sufficient
to initialise storage access before reading `af_settings`. These include the
connection location and references to locally managed credentials. Credentials
remain subject to the existing secret policy, not ordinary admin-page fields.
When storage is available, `af_settings` is the primary settings source. Define
bootstrap-copy revisions, update and override policy; stale files must not
silently overwrite newer database values. Fallback can support initialisation
or diagnostics, but cannot authorise fuel operations without persistent storage.

Admin edits create validated revisions. Distinguish configured, pending and
effective settings. Declare live-applicable, replan-required and restart-required
changes; activation affecting simulation state passes through execution
authorisation/application. Persist the effective version with its run/state.
Each module declares its editable keys, types and update policy; validate
ranges and incompatible combinations before activation.

Preserve D-034 service commitments and D-037/D-040 flight/schedule authority.
An edit cannot rewrite completed transfers, historical coordinates, an active
delivery obligation or an existing flight's ETD. Retain previous settings
versions for recovery/replay. Exact activation/restart rules remain Q-13/Q-16.

## Persistence module and transaction boundary

Add an explicit Persistence / Data Access module to the architecture. The
execution engine owns the meaning of a state change; Persistence owns its
database transaction, repositories and restoration access. WorldState remains
the single logical current state, represented in memory and durably backed by
committed records. The database module is not a second dispatcher or simulator.

The proposed write path is:

```text
Execution calculates and validates a change
    -> Persistence commits the coherent state/facts
    -> Execution publishes the committed WorldState revision
    -> Presentation and metrics consume that revision
```

Related request updates, actual fuel debit/credit, service history, state,
position and time must not become independently successful writes. Define a
transaction boundary using transactional InnoDB tables before implementation;
where a core change requires a matching telemetry sample, include its consistency
and commit handling explicitly. Separate database names do not remove this need.
Do not report Completed or advance to a newly accepted state after a failed
commit. Ambiguous commit outcomes require identification/readback rather than
repeating a fuel transfer blindly.

History recording passes through this persistence boundary. Records of input,
rejected commands and solver results must remain distinguishable from actual
applied changes. Derived analytics can be updated separately and reconstructed;
their run/revision/time must make any lag visible.

## Coordinates, retention and recovery

Retain all requests, including completed/cancelled records and actual partial
transfer/executor history under D-041. Store sufficient current state to recover
moving entities at their committed positions, with route/progress, assignments,
stocks, service commitments, clock and event-processing position.

Position history needs entity/run identity, simulation time, recording time,
coordinate reference and source provenance. Synthetic samples must agree with
the same WorldState movement and infrastructure mapping under D-047. Preserve
the relevant airfield/graph/reference versions so past samples retain meaning.

Select telemetry sampling/checkpoint frequency separately from render FPS and
declare the maximum permitted unsaved tail. Significant arrival, stopping,
rerouting, transfer and request-state changes need explicit persistence rules.
The runtime must not reset objects to initial positions, recreate completed
requests or re-credit fuel after restart.

Recovery must restore the last coherent committed state and provide historical
queries for requests and coordinates from earlier in the run. Distinguish
simulation time from actual recording time. A retained event/position history
supports inspection without requiring the user to rerun the entire scenario.

For power-loss resilience, verify the selected MySQL version, InnoDB durability
settings and backup/restore procedure. The official documentation discusses
`innodb_flush_log_at_trx_commit=1` and, when binary logging is used,
`sync_binlog=1`; actual durability also depends on the storage stack correctly
honouring flushes. Database separation alone does not provide crash recovery
or backups. See [MySQL durability settings](https://dev.mysql.com/doc/refman/8.4/en/replication-options-binary-log.html)
and [backup/recovery guidance](https://dev.mysql.com/doc/refman/8.4/en/backup-strategy-summary.html).

## Open review items

- **Q-16:** Confirm the database split, schemas/repository contracts, server
  version and transactional storage configuration. Define coherent commit,
  idempotency and ambiguous-outcome handling, startup restoration, telemetry
  sampling/checkpoint interval, retention and backup/restore validation.
  Include settings revisions, bootstrap-copy precedence/update, administration
  access, validation and effective/restart activation policy.
- Q-01/Q-02 retain mutation ownership, event ordering and replay semantics;
  Q-05 retains request/commitment identity and atomic domain outcomes.
- Q-10/Q-11 retain field/metric meanings and spatial/profile bindings. Storage
  placement must not silently choose their unresolved semantics.

Database schemas, repositories, configuration administration and recovery remain
unimplemented. Real airport-system bridges are outside the agreed scope.
