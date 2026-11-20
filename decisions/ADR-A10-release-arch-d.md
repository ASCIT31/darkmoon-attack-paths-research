# ADR-A10: Release with architecture D

- **Date:** 2026-11-18
- **Status:** Accepted
- **Evidence label:** `VERIFIED`

## Context (problem)

The production branch had been reset until a real LLM campaign validated the integration.

## Hypothesis

A release branch (revert of the reset + architecture D + scanner fix) is safe to distribute.

## Proposal

Release branch and live lab runs with a budget guard.

## Implementation

DarkMoon Pro, internal `638def13`, `c4a85d5d`; internal PR #46.

## Tests

[EXP-055](../experiments/inconclusive/EXP-055.md) (inconclusive: scanner defect); [EXP-056](../experiments/successful/EXP-056.md) (29 findings, 8 exploited); clean install-dev test [EXP-059](../experiments/successful/EXP-059.md).

## Results

Scanner defect fixed; 19 declared links, 0 promoted; privacy 0 hits in 54 live scans.

## Decision

GO for documented distribution (container images + install script, no ISO).
