# ADR-A03: Agent-native evidence tagging

- **Date:** 2026-11-10
- **Status:** Code present, flag OFF
- **Evidence label:** `OBSERVED`

## Context (problem)

Agent text is unusable as proof and the server cannot attribute evidence (9/11 declared links correct but uncorroborated in dmlab).

## Hypothesis

The acting model can annotate evidence during the run with enum-only labels; deterministic oracles keep authority.

## Proposal

One MCP tool, `record_evidence_annotations`, behind flag `DM_AGENT_EVIDENCE_TAGGING`.

## Implementation

DarkMoon Pro, internal `8662460e` → `16fed7ce`.

## Tests

Four real micro-campaigns on an own HTTP + PostgreSQL lab, plus three audit-remediation rounds ([EXP-032](../experiments/inconclusive/EXP-032.md)).

## Results

Annotations: 0 (tool buried in the prompt), 9, 5 (one false positive caused by a permissive lab stub), 8.

## Decision

GO on the tested perimeter; shipped inert (flag OFF). The principle "the agent declares, the server never promotes on declaration alone" carried into later work (inferred continuity, HYPOTHESIS).
