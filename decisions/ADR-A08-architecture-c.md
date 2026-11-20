# ADR-A08: Architecture C (paths-v2): execution ids

- **Date:** 2026-11-17
- **Status:** Engine technical GO; agent surface rejected
- **Evidence label:** `SIMULATED`

## Context (problem)

The proof path was 4,553 lines with 73 rules, 20 of them server-side guessing; 14/39 rules could be removed without failing a test; a wrong binding existed on c6.

## Hypothesis

The agent cites opaque execution ids; the server verifies each edge on its own execution records.

## Proposal

`EXEC ID` line on every command output, `evidence_execution_ids` on push, `verify_edge`.

## Implementation

DarkMoon Pro, internal `a0a1d6e9` → `37e92f12`.

## Tests

Sealed harness + mutation testing ([EXP-044](../experiments/adversarial/EXP-044.md)); recompute of c5/c6 ([EXP-045](../experiments/inconclusive/EXP-045.md)); token model ([EXP-046](../experiments/successful/EXP-046.md)).

## Results

0 false proofs on 66 critical negatives; mutation score 95.8 %; real recompute 0 proven; token add-on +1.02 % / +1.75 % (above the +1 % cap).

## Decision

Technical GO, NO-GO for the agent surface → architecture D. The `verify_edge` code is present but has no runtime caller.
