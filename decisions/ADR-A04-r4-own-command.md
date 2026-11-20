# ADR-A04: R4 "own command" proof chain

- **Date:** 2026-11-16
- **Status:** Rejected twice; branch frozen, not merged
- **Evidence label:** `VERIFIED`

## Context (problem)

A real Juice Shop campaign produced 13 paths, all inferred, 0 proven ([EXP-037](../experiments/failed/EXP-037.md)).

## Hypothesis

A finding owns the observed command whose distinctive values it reuses.

## Proposal

Attribution rule R4 on a per-run ledger of commands.

## Implementation

DarkMoon Pro, internal `fac80177`, `db21ff5a`, `a7b23aa3`, `6830a324` (branch `feat/proof-chain`).

## Tests

Deterministic probes P1–P8, two independent review rounds ([EXP-038](../experiments/failed/EXP-038.md)).

## Results

Round 1: five families of false "proven" links. Round 2: seven new false links from skeleton collisions.

## Decision

NO-GO. The product owner concluded deterministic matching was the wrong methodology and asked for an adversarial method watch (A05). The run ledger itself was kept.
