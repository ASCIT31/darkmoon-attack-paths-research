# ADR-A06: `enabled_by`: the agent declares the finding it used

- **Date:** 2026-11-16
- **Status:** Accepted (core of architecture D)
- **Evidence label:** `VERIFIED`

## Context (problem)

Four structured CHAIN DATA fields were filled 0 of 51 times on a real campaign; a universal, protocol-agnostic mechanism was wanted.

## Hypothesis

A single optional list `enabled_by` is cheaper and actually gets filled.

## Proposal

Schema change, validation (ATT&CK order, liveness, cycles), lazy resolution, end-to-end `agent-declared` provenance, one prompt line per pushing sub-agent and a `recent_exploited` hint in the push reply.

## Implementation

DarkMoon Pro, internal `eb96ab84`, `99deb15f`, `57511aeb`, `232e5f70`, `f7d97ab4`, `33714381`, `136dbccc`; ported to Community in [Dark-Moon PR #59](https://github.com/ASCIT31/Dark-Moon/pull/59).

## Tests

Real campaigns c4, c5, c6 ([EXP-041](../experiments/successful/EXP-041.md), [EXP-042](../experiments/successful/EXP-042.md), [EXP-043](../experiments/inconclusive/EXP-043.md)); token model ([EXP-046](../experiments/successful/EXP-046.md)).

## Results

Fill: 4/36 (c4) → 15/39 (c5). Oracle strict precision 0.6 (c5, 5 decidable) and 0.556 (c6). Token overhead of the whole mechanism about +0.52–0.57 % (simulated).

## Decision

GO for agent-native declaration under a +1 % token cap.
