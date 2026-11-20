# ADR-E01: Agent safety on targets

- **Date:** 2026-11-18
- **Status:** Accepted
- **Evidence label:** `VERIFIED`

## Context (problem)

A GraphQL agent froze DVGA with a state-changing mutation and reported its own side-effect as a DoS finding; in a later Community run a self-SSRF froze DVGA four times.

## Hypothesis

Explicit TARGET SAFETY rules in agent prompts prevent destructive or denial-of-service actions.

## Proposal

Prompt rules (lab only, no destructive or DoS mutations, request time limits).

## Implementation

DarkMoon Pro, internal PRs #49, #50, #52; ported to Community via [Dark-Moon PR #59](https://github.com/ASCIT31/Dark-Moon/pull/59).

## Tests

[EXP-057](../experiments/successful/EXP-057.md); [EXP-060](../experiments/successful/EXP-060.md); [EXP-061](../experiments/successful/EXP-061.md).

## Results

No forbidden operation executed in the two monitored DVGA runs of 2026-11-20.

## Decision

Shipped in both editions.
