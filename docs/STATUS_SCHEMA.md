# STATUS_SCHEMA.md — schema for `SYSTEM_STATUS.yaml`

`SYSTEM_STATUS.yaml` is the **single source of truth** for the state of each repository
in the Echo Labs substrate. Every count shown in the profile or any README should be
generated from, or checked against, this file — never copied from memory or an old
commit message.

## Per-component entry

```yaml
repo: echo313unfolding/<name>
role: <artifact_formation|runtime_state_compression|artifact_profiling_routing|
       plan_and_policy_ir|local_agent_runtime|safety_control|evidence_ontology|
       provenance_settlement|personal_data_receipts|cross_stack_application|
       real_industrial_application|external_runtime_integration|historical_lineage>
visibility: public | private
default_branch: main | master

observed:
  commit_sha: "<full 40-char SHA the manifest was written against>"
  observed_at: "<UTC date/time the SHA was read>"
  source: github_checkout        # how the SHA was obtained; never home-box memory

verification:
  status: verified | reported | stale | blocked | not_run
  command: "<test command, e.g. pytest -q>"
  result: "<exact output, e.g. '321 passed'>"
  verified_at: "<UTC time the command was run against observed.commit_sha, or null>"
  environment: "<where it was run, or null>"

maturity:
  component:   proven | prototype | hypothesis | historical
  integration: production | strict_demo | thin_adapter | mocked | missing

interfaces:
  consumes: []                    # named artifact/receipt schemas in
  emits:    []                    # named artifact/receipt schemas out

known_gaps: []
notes: []
```

## The one rule that matters

- A count taken from a README, a commit message, memory, or an old receipt is
  **`reported`** — never `verified`.
- A count is **`verified`** only when `verification.command` was actually run **now,
  against `observed.commit_sha`**, and `verified_at` is set.
- **Conflicting counts are recorded explicitly**, not silently reconciled (e.g. a README
  says 123 while a later push reports 321 — both are listed until one is `verified`).
- Home-box memory is **not** checkout verification. Unresolved local state goes under the
  top-level `verify_pending_on_home_box`, never asserted as a public fact.

## `maturity.integration` meanings
- `production` — wired into a real, exercised path.
- `strict_demo` — proven end-to-end in a bounded demo (e.g. devnet).
- `thin_adapter` — interface compatibility proven; not a real runtime call.
- `mocked` — the connection is a fixture/mock.
- `missing` — the component exists but is not composed into the substrate.
