# cloud-itonami-isco-9629

Open Occupation Blueprint for **ISCO-08 9629**: Elementary Workers Not
Elsewhere Classified.

ISCO-08 9629 is a residual "Not Elsewhere Classified" category covering
diverse manual/elementary labour work not captured by a more specific
ISCO 9xxx code. This repository designs a forkable OSS business for a
generic elementary-labour site scheduling and logistics coordination
practice: a site scheduling and supply-coordination robot manages
crew/task records under a governor-gated actor, so an elementary-labour
crew keeps its own operating records instead of renting a closed
workforce-management SaaS.

**Maturity: `:implemented`.** `src/elementarywork/` implements the
`ElementaryWorkActor` as a `langgraph.graph/state-graph`
(`elementarywork.actor`) wired to an `Elementary Worker Advisor`
(`elementarywork.advisor`) and an independent `ElementaryWorkGovernor`
(`elementarywork.governor`), following the itonami actor pattern
(ADR-2607121000): `:intake -> :advise -> :govern -> :decide -+-> :commit
(:ok?) +-> :request-approval (:escalate?, human-in-the-loop interrupt) +->
:hold (:hard?)`. 24 tests / 52 assertions green (`clojure -M:test`). HARD
invariants (always hold, never overridable): worker provenance, site
provenance, no-actuation (`:effect` must be `:propose`), a closed
op-allowlist (`:log-work-record`, `:schedule-crew-operation`,
`:flag-safety-concern`, `:coordinate-supply-order` — nothing else may
ever be proposed), and a permanent, unconditional block on any proposal
that would directly finalize a labour-work-execution decision (e.g.
finalizing a specific labour-work operation) *or* a site-safety-clearance
decision (e.g. declaring a site safety cleared), or that would override a
site safety supervisor's judgment. Always-escalate paths (human sign-off
regardless of confidence, mapping this repo's Trust Controls in
[`docs/business-model.md`](docs/business-model.md)):
`:flag-safety-concern` (always) and `:coordinate-supply-order` above the
registered cost threshold.

## Robotics premise

All cloud-itonami verticals are designed on the premise that a **robot
performs the physical domain work**. Here a site scheduling/logistics
coordination robot performs crew scheduling, task/progress-record
logging and site-supplies procurement coordination for an
elementary-labour crew, under an actor that proposes actions and an
independent **ElementaryWorkGovernor** that gates them. The governor
never dispatches hardware itself, never performs labour work on site
itself, and never finalizes a labour-work-execution decision or a
site-safety-clearance decision, and never overrides a site safety
supervisor's judgment; `:high`/`:safety-critical` actions (such as a
flagged manual-lifting/strain, varied-site-condition or outdoor/indoor-
exposure concern, or an above-threshold supply order) require human
sign-off. Because 9629 is a residual "Not Elsewhere Classified" category
covering diverse manual/elementary labour work, no single dominant
hazard type applies — standard elementary-labour hazards apply
generically instead. **This actor coordinates SITE SCHEDULING/LOGISTICS
ONLY — it never performs labour work itself and never makes a
site-safety-clearance decision itself.**

## Core Contract

```text
worker roster + site registration + safety-reporting policy
        |
        v
Elementary Worker Advisor -> ElementaryWorkGovernor -> log/schedule/coordinate, or human sign-off
        |
        v
robot actions (gated) + operating records + audit ledger
```

No automated advice can dispatch a robot action the governor refuses,
finalize a labour-work-execution decision, finalize a site-safety-
clearance decision (e.g. declaring a site safety cleared), override a
site safety supervisor's judgment, suppress an operating record, or
disclose sensitive data without governor approval and audit evidence.

## Capability layer

Resolves via [`kotoba-lang/occupation`](https://github.com/kotoba-lang/occupation)
(ISCO-08 `9629`). Required capabilities:

- :robotics
- :identity
- :audit-ledger

See [`docs/business-model.md`](docs/business-model.md) and
[`docs/operator-guide.md`](docs/operator-guide.md).

## License

AGPL-3.0-or-later.
