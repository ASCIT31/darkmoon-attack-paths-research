# ADR-A05: Method watch: capability graph vs rules vs server observation

- **Date:** 2026-11-16
- **Status:** Accepted (R4 observation + agent-declared links)
- **Evidence label:** `VERIFIED`

## Context (problem)

A method that produces correct chains was needed.

## Hypothesis

(1) A MulVAL-like CWE/CAPEC/ATT&CK capability graph; (2) rules on finding text vs tool-layer wire-flow observation.

## Proposal

Five rounds of proposal and adversarial critique.

## Implementation

—

## Tests

[EXP-039](../experiments/failed/EXP-039.md); [EXP-040](../experiments/failed/EXP-040.md); code critiques on the observed-paths branch.

## Results

Capability graph: 10/78 edges correct. Text rules: 12–15 % held-out precision. Wire flow: 17/24 pairs. The ATT&CK filter accepted 74 % of pairs and 10 of 10 false chains.

## Decision

Use server observation plus agent-declared `enabled_by`; a declared link is never shown as proven. ATT&CK becomes context only.
