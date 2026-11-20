# ADR-F01: Terminal Explorer (read-only TUI view of findings and paths)

- **Date:** 2026-11-18
- **Status:** Accepted
- **Evidence label:** `VERIFIED`

## Context (problem)

A read-only terminal view of findings and attack paths was needed, with no model calls.

## Hypothesis

An OpenCode TUI plugin can render the same read model without calling a model.

## Proposal

Resync of the OpenCode fork to 1.19.6, plugin `conf/plugins/darkmoon-explorer/`; Community file adapter.

## Implementation

DarkMoon Pro, internal PRs #51, #53, #54; Community via [Dark-Moon PR #59](https://github.com/ASCIT31/Dark-Moon/pull/59).

## Tests

tmux harness on the real binary + unit tests ([EXP-058](../experiments/successful/EXP-058.md)).

## Results

Three journeys pass; 0 model calls (one accidental keystroke during a capture script, USD 0.09).

## Decision

GO Pro; Community after the file adapter. Screenshots: [`screenshots/`](../screenshots/README.md).
