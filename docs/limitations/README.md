# Acknowledged limitations

Status: public research documentation, version 1.0.0, 2026-11-20. Labels as in [architecture §0](../architecture/README.md#0-conventions).

## 1. What has not been demonstrated

1. **No real-world server qualification.** 0 declared links reached `observed_flow` or `causal_necessity_demonstrated` on any measured real campaign (c5/c6 replay 0/25, c7 0/19, final lab run 0/19, DVGA Community 0/16 by construction). [VERIFIED]
2. **No multi-step (N4) chain proven on real data** under the strict definition (illegitimate provenance + necessity + capability gain). [VERIFIED]
3. **No universally reliable causal model.** A graph plus an LLM does not demonstrate validated causal intelligence. [VERIFIED]
4. **Declared-link precision is modest and unstable:** 0.6 (c5, 5 decidable), 0.556 [0.27–0.81] (c6), 0.316 [0.15–0.54] (c7), ≈ 0.37 approximate (final lab run). Single runs; intervals overlap. [VERIFIED / OBSERVED]
5. **No prompt change was shown to help** outside the data that suggested it; proxy-model pass-to-pass variance (±20 points) exceeded variant differences. [SIMULATED]
6. **"Fix first" ranking** reflects the structure of declared or rule-observed links, not demonstrated causal leverage; greedy top-k had regret > 0 in 36.5 % of synthetic instances. [HYPOTHESIS / SIMULATED]
7. **No cross-target generalisation** of any figure. [UNKNOWN]

## 2. Methodological limits

- n = 1 per configuration almost everywhere; one campaign model (claude-opus-4-6).
- Oracles were built by the same programme that built the engine; one adjudicator per campaign; a single mapping choice moves c7 precision by about 10 points.
- Several replays used Claude Code sub-agents as a proxy for the campaign model (different model, shorter context).
- Targets are self-built labs or well-known vulnerable applications (Juice Shop, DVGA, crAPI) that are likely present in model training data; Juice Shop runs of the R&D programme were contaminated (the agent hit scoring endpoints) and excluded.
- No run on a public benchmark; finding counts are not comparable across targets.
- Some figures exist only in internal notes, not in primary logs (labelled OBSERVED or UNKNOWN on the experiment pages).

## 3. Engineering limits

- R1 and R3 read **agent-written** evidence text, not an independent capture. Two product strings overstate this: the Terminal Explorer's "every step backed by evidence the server recorded" and a Community docstring saying Pro evidence "comes from the server's own recording". Flagged for correction. [VERIFIED]
- The server cannot attribute an MCP call to a sub-agent session. [VERIFIED]
- Server observation is effectively HTTP-only for pattern rules; the ledger is generic but needs full values. [VERIFIED]
- Assembly is bounded (depth 6, 100 chains, time budget, simple paths only); inferred paths are capped at 40. [VERIFIED]
- Stale docstrings remain in the engine (execution ids, finalisation chains as ranked provenance, remediated links "invalidated"). [VERIFIED]

## 4. Inconsistencies between sources (not silently resolved)

1. c6 recompute under architecture C: "1 → 0 proven" vs "3 → 0" in two internal reports.
2. EXP-057 cost and calls: 3.60 USD / 80 calls vs 3.697 USD / 83 calls; a second run (≥ 2.12 USD) missing from the report.
3. EXP-056: KPI file says no guard stop; guard log says a guard stop occurred.
4. Capstone "2 proven chains" (2026-11-11) vs the later rule that declared chains are never proven → SUPERSEDED.
5. EXP-027: 1,627 synthetic cases vs 1,700 preregistered, no deviation note.
6. EXP-030 cost summed by an extraction step (inferred), not stated in a report.

Details: [`../../experiments/RESULTS.md`](../../experiments/RESULTS.md) §10, [`../architecture/README.md`](../architecture/README.md) §5.
