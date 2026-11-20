# ADR-A07: Corroboration by endpoint observation ("observation only")

- **Date:** 2026-11-17
- **Status:** SUPERSEDED by A08/A09
- **Evidence label:** `VERIFIED`

## Context (problem)

c5 gave 0 proven paths: necessity was never demonstrated.

## Hypothesis

"Corroborated" = R4 endpoint link + demonstrated necessity + valid `enabled_by`; then the product owner dropped necessity: a step is proven by observation alone.

## Proposal

Rule change, passive pcap capture (OFF by default), universal observation tests (AD, IoT, cloud, DB).

## Implementation

DarkMoon Pro, internal `491e7385`, `39d5ec0c`, `b7b5c634`, `0a801611`, `58896bcf`.

## Tests

Synthetic pcaps; universal unit tests ([EXP-048](../experiments/inconclusive/EXP-048.md)); a blind-spot audit of 52 agent prompts.

## Results

c5 went "0 → 2 proven", but both links were bound to their command only by declared URL. No real non-web campaign was run.

## Decision

Superseded: a baseline audit judged the proof path NO-GO as "proven" (A08). R2 was later isolated as a historical inference.
