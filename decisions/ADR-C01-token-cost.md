# ADR-C01: Token-cost reduction targets

- **Date:** 2026-09-24
- **Status:** Rejected (targets not reachable)
- **Evidence label:** `SIMULATED`

## Context (problem)

Cost per campaign.

## Hypothesis

Cache/context engineering −30 %; lossless levers −20 %; earlier compaction −10 %.

## Proposal

Cost decomposition, offline counterfactual replays, calibrated compaction model.

## Implementation

—

## Tests

[EXP-005](../experiments/failed/EXP-005.md); [EXP-014](../experiments/failed/EXP-014.md); [EXP-054](../experiments/failed/EXP-054.md).

## Results

Baseline already about 93–95 % cache reads; none of the three targets was reached.

## Decision

Optimiser archived; compaction NO-GO. Schema-size discipline adopted as a gate (+1 % cap) for A08/A09.
