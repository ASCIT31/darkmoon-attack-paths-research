# ADR-A02: Darkmoon 2027 causal-path research programme

- **Date:** 2026-11-08
- **Status:** Rejected (NO-GO for flag promotion); security fixes kept
- **Evidence label:** `VERIFIED`

## Context (problem)

Almost no proven paths. Root causes: specialists were never asked to fill causal fields (C1); pivot evidence is truncated or masked (C2).

## Hypothesis

Collecting causal fields, value fingerprints, ranking completion and counterfactual replay would restore correct proven paths.

## Proposal

Flag-gated fixes and a preregistered black-box benchmark on an internal composite lab (dmlab), with graph benchmark, red team, tool-layer provenance, counterfactual replay CF1–CF4, blind adjudication, passive (E3) and active (E4) evidence engines.

## Implementation

DarkMoon Pro, internal research branches `exp/d27-*`, all behind default-OFF flags.

## Tests

[EXP-011](../experiments/adversarial/EXP-011.md); [EXP-013](../experiments/adversarial/EXP-013.md); [EXP-015](../experiments/failed/EXP-015.md); [EXP-017](../experiments/failed/EXP-017.md); [EXP-019](../experiments/failed/EXP-019.md); [EXP-020](../experiments/failed/EXP-020.md); [EXP-021](../experiments/adversarial/EXP-021.md); [EXP-022](../experiments/failed/EXP-022.md); [EXP-024](../experiments/failed/EXP-024.md); [EXP-031](../experiments/failed/EXP-031.md).

## Results

Causal-edge recall 0/14 on every dmlab run; counterfactual variants failed their held-out criteria; one verified path among 18 candidates; no multi-finding (N4) chain on real data anywhere.

## Decision

NO-GO for flag promotion. Kept: RT-04 (`1f464407`, "declared causal fields never make a proven path without server corroboration"), RT-02 and two benchmark-validity fixes.
