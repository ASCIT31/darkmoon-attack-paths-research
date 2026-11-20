# ADR-A09: Architecture D: declared, then qualified by exact value attribution

- **Date:** 2026-11-17
- **Status:** Accepted (current)
- **Evidence label:** `VERIFIED`

## Context (problem)

Keep agent-declared paths, cheap, and never falsely promoted.

## Hypothesis

Qualify an agent-declared link only by exact value attribution on the server's own records; otherwise it stays `declared_by_agent`.

## Proposal

`edge_verifier.qualify_edge` with a closed set of abstention reasons, simple-path assembly, weakest-link path level, fail-closed output sanitiser.

## Implementation

DarkMoon Pro, internal `899096e0` … `64d72147`, `47f3828d`; Community: [Dark-Moon PR #59](https://github.com/ASCIT31/Dark-Moon/pull/59).

## Tests

Independent harness + mutation ([EXP-047](../experiments/adversarial/EXP-047.md), [EXP-049](../experiments/adversarial/EXP-049.md)); real c7 ([EXP-050](../experiments/failed/EXP-050.md)); proxy-model prompt variants ([EXP-051](../experiments/failed/EXP-051.md), [EXP-053](../experiments/failed/EXP-053.md)); privacy E2E ([EXP-052](../experiments/adversarial/EXP-052.md)).

## Results

Harness: 0 false promotions, recall 0.829–0.867, mutation 95.2 % with 0 permissive survivors. Real c7: declared-link strict precision 31.6 % [15–54], server-qualified 0/19, 12 of 13 false links "same root cause".

## Decision

Technical GO; product labelling: declared links are shown as Possible, never proven. A wording change was reverted (`cbc4df04`, gain not demonstrated at n = 1). No prompt variant applied.
