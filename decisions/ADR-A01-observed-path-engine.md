# ADR-A01: Observed-path engine (paths v1)

- **Date:** 2026-10-29
- **Status:** Accepted, later refined (R2 demoted, see A09)
- **Evidence label:** `VERIFIED`

## Context (problem)

The infrastructure / attack-path view showed inferred or demo links as if they were real.

## Hypothesis

Links between findings can be established from fixed evidence rules: R1 (a typed secret produced by A is used as a credential by B), R2 (open registration, matched by endpoint), R3 (an exploited finding's response proves access to another node).

## Proposal

Observed-path adapter, a read-only analysis service under the rule "real = observed only, demo isolated, gated ranking", and a ranking display gate. Server engine and schema `darkmoon-attack-paths/v1` followed on 2026-11-03.

## Implementation

DarkMoon Pro, internal `4d0786f3`, `6ab37775`, `01f04104`, `a5e9cd18`.

## Tests

Proof-of-concept review plus adversarial corrections.

## Results

On 18 real production campaigns (340 findings): 0 findings carried causal fields; 5 proven paths, all R3; ranking allowed on 0 of 18 campaigns ([EXP-010](../experiments/successful/EXP-010.md)).

## Decision

Keep the engine; open a preregistered research programme to diagnose why paths are almost never proven (A02).
