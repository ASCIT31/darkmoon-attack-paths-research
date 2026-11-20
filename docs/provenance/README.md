# Provenance: the run ledger, exact value attribution and abstention

Status: public research documentation, version 1.0.0, 2026-11-20. Labels as in [architecture §0](../architecture/README.md#0-conventions).

## 1. What is recorded (Pro edition)

- The MCP server is the only component that sees both sides of every command: the real input (after placeholder resolution) and the real output (before it is re-tokenised for the model). [VERIFIED: DarkMoon Pro, internal `d6bfc0d9`]
- A per-run **ledger** records, for each `execute_command`, which sensitive artefacts the output **produced** and which earlier artefacts the input **consumed**. Only salted HMAC fingerprints are stored, never values, with file mode 0600. [VERIFIED]
- At push time the server adds, out of the agent's view: the run id, the push order, and the full-value fingerprints that the finding's own evidence shares with the ledger. [VERIFIED]
- The ledger is the only attack-path flag ON by default in Pro. **Community has no ledger** (`DM_TOOL_PROVENANCE` is forced off). [VERIFIED: [`feature_flags.py`](https://github.com/ASCIT31/Dark-Moon/blob/10eaab0/mcp/api/feature_flags.py)]

## 2. Exact value attribution (`observed_flow`)

A declared link A → B is promoted only when all of these hold (`edge_verifier.qualify_edge`, [code](https://github.com/ASCIT31/Dark-Moon/blob/10eaab0/mcp/api/edge_verifier.py)):

1. A and B are in the same run, both live, not duplicates.
2. A value fingerprint is shared **in full** by A's and B's own pushed evidence.
3. One execution `e_a` produced it; `e_a` is unique in the run, and the value was never presented before it.
4. A successful execution `e_b` consumed it from `e_a` (success = `http_2xx` or `exit0_expected_output`; exit 0 alone does not count).
5. `e_a` precedes `e_b`, and both precede their finding's push.
6. No other live finding presenting the value was pushed after `e_a`.

Session identity only restricts; it never attributes. **Necessity** additionally requires a recorded refused attempt without the value on the same operation key, in the same run, not compacted. [VERIFIED]

In W3C PROV terms: `e_b used v`, `v wasGeneratedBy e_a`; the finding-level link is accepted only when that lineage is unique and ordered. [HYPOTHESIS: mapping made by this documentation]

## 3. Abstention is the expected outcome on real data

| Run | Declared links | Qualified | Main reasons | Label |
|---|---|---|---|---|
| c5 replay | 16 | 0 | 15 `no_shared_value`, 1 `session_unknown` | SIMULATED, [EXP-047](../../experiments/adversarial/EXP-047.md) |
| c6 replay | 9 | 0 | 9 `no_shared_value` | SIMULATED, [EXP-047](../../experiments/adversarial/EXP-047.md) |
| c7 | 19 | 0 | — | VERIFIED, [EXP-050](../../experiments/failed/EXP-050.md) |
| final lab run | 19 | 0 | — | VERIFIED, [EXP-056](../../experiments/successful/EXP-056.md) |
| DVGA Community | 16 | 0 by construction | no ledger | VERIFIED, [EXP-060](../../experiments/successful/EXP-060.md) |

Whether `observed_flow` can ever trigger on real data with masked evidence is **UNKNOWN**.

## 4. Why: the privacy–provenance tension

- The Privacy Gateway replaces secrets, hosts and URLs with placeholders before the model sees them; agents additionally write `***` where a credential was (8 of 11 c6 pushes; 16 of 39 c5 pushes). [VERIFIED]
- **0 of 50** real pushes contain a non-host placeholder; the only placeholders shared across pushes are the operator's target URL and domains, which are exogenous. [VERIFIED]
- Therefore neither value-level nor placeholder-level attribution can link findings on real data. [VERIFIED]
- Stronger model-side privacy and stronger server-side attribution compete for the same evidence. No design in the sources resolves this. [HYPOTHESIS] Candidate directions (verification before masking, keyed-hash commitments, in-vault comparison, recording which execution minted a placeholder) are untested. See [research/scientific-positioning](../../research/scientific-positioning/README.md).

## 5. Capture limits at the tool layer

- The ledger observes commands, not the operating system. Shell substitutions (`$(...)`), cross-call shell variables and in-memory reuse are blind spots, the tool-layer equivalent of dependency explosion and capture gaps known from OS-level provenance. [OBSERVED: internal method-watch critique of [EXP-040](../../experiments/failed/EXP-040.md)]
- The MCP call carries **no OpenCode session identity**: the server knows the run but not which sub-agent made a given call. [VERIFIED]
- Compaction would remove finding ids from the agent's context; the engine abstains with `run_compacted`. No compaction occurred in c5–c7, so the practical effect is **UNKNOWN**. [VERIFIED / UNKNOWN]
