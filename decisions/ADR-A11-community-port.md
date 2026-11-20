# ADR-A11: Port of attack paths to DarkMoon Community

- **Date:** 2026-11-19
- **Status:** Accepted
- **Evidence label:** `VERIFIED`

## Context (problem)

Community lacked attack paths, the Terminal Explorer and the latest fixes.

## Hypothesis

The Pro engine can be ported such that Community never treats agent-written evidence text as proof.

## Proposal

Port the engine; withhold evidence text before the engine (`attack_paths_local._without_evidence_text`); force off the ledger and legacy proof flags. Result: `proven_paths = 0` by construction.

## Implementation

[Dark-Moon PR #59](https://github.com/ASCIT31/Dark-Moon/pull/59) ([`85c9e87`](https://github.com/ASCIT31/Dark-Moon/commit/85c9e87)).

## Tests

2,008 MCP tests; LLM-free prevalidation 10/10; real DVGA campaign ([EXP-060](../experiments/successful/EXP-060.md)); two adversarial reviews (one HIGH: agent evidence shown as "PROVEN · corroborated", fixed before merge).

## Results

68 findings, 16 with `enabled_by`, 17 paths (11 declared, 6 inferred), 0 proven.

## Decision

GO; merged 2026-11-19.
