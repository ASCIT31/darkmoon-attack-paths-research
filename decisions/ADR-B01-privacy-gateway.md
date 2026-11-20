# ADR-B01: Privacy Gateway and tokenisation

- **Date:** 2026-07-08
- **Status:** Accepted; hardened 2026-11-18
- **Evidence label:** `VERIFIED`

## Context (problem)

Sensitive values (targets, credentials) were sent to cloud LLMs.

## Hypothesis

Reversible local tokenisation keeps utility while the model sees placeholders.

## Proposal

Privacy Gateway (Community v1.2.0), pre-model prompt tokenisation, later key-block and table-cell tokenisation and a fail-closed sanitiser.

## Implementation

Dark-Moon [`037bcfd`](https://github.com/ASCIT31/Dark-Moon/commit/037bcfd), [`c7e24e2`](https://github.com/ASCIT31/Dark-Moon/commit/c7e24e2); DarkMoon Pro, internal `63164ac9`, `44a20255`, `64d72147`.

## Tests

Regression reproduction ([`9bd8f68`](https://github.com/ASCIT31/Dark-Moon/commit/9bd8f68)); privacy suites grown to 1,417 tests; synthetic E2E ([EXP-052](../experiments/adversarial/EXP-052.md)); live scans ([EXP-056](../experiments/successful/EXP-056.md)).

## Results

A lab-only SSH key reached the model provider in c6 (×4) and c7 (×1) through database table cells; 219 of 1,009 synthetic cases leaked before the fix, 0 after; 0 in 136 tool results end to end; 0 hits in 54 live scans.

## Decision

Fixes shipped. Consequence for attack paths: masking removes the values exact attribution needs (see [`docs/provenance/`](../docs/provenance/README.md)).
