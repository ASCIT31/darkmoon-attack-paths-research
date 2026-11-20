# Security properties of the attack-path pipeline

Status: public research documentation, version 1.0.0, 2026-11-20. Labels as in [architecture §0](../architecture/README.md#0-conventions). This page describes design properties and their evidence. It is not a security advisory; to report a vulnerability, use the process in the [Dark-Moon repository](https://github.com/ASCIT31/Dark-Moon/security).

## 1. Non-promotion of agent declarations

| Property | Mechanism | Evidence | Label |
|---|---|---|---|
| A declared link is never shown as proven | provenance `agent-declared` ⇒ `hypothesis: true`; weakest-link path level | code | VERIFIED |
| Structured claims never make a proven path without server corroboration (RT-04) | `attack_paths._uncorroborated` | red-team finding RT-04 fixed | VERIFIED, [EXP-013](../../experiments/adversarial/EXP-013.md) |
| Finalisation chains declared by the agent are report text only | skipped unless a rollback flag is set (forced off in Community) | code | VERIFIED |
| No false promotion on adversarial scenarios | sealed harness, independent oracle, mutation testing | 0 false promotions on 100 scenarios / 117 edges; mutation 95.2 %, 0 permissive survivors | SIMULATED, [EXP-049](../../experiments/adversarial/EXP-049.md) |
| Community cannot be fooled by agent text | evidence text withheld before the engine | a pre-merge review found a path marked "PROVEN · corroborated" from one JWT repeated in two agent texts; fixed before merge | OBSERVED, [`attack_paths_local.py`](https://github.com/ASCIT31/Dark-Moon/blob/10eaab0/mcp/api/attack_paths_local.py) |

A forging agent that writes a fake value into two findings cannot promote a link in Pro either, because qualification requires the value to have been **produced and consumed by the server's own recorded executions** (§2 of [provenance](../provenance/README.md)). Adversarial agents that try to forge execution lineage have **not** been tested. [HYPOTHESIS / UNKNOWN]

## 2. Privacy

- Reversible local tokenisation (Privacy Gateway) keeps secrets, hosts and URLs out of the model context. [VERIFIED: Dark-Moon [`037bcfd`](https://github.com/ASCIT31/Dark-Moon/commit/037bcfd), [`c7e24e2`](https://github.com/ASCIT31/Dark-Moon/commit/c7e24e2)]
- A real defect was found during this research: a lab-only SSH private key reached the model provider in c6 (×4) and c7 (×1) through database table cells. Key blocks and table-cell secrets are now tokenised and the output sanitiser fails closed ("[privacy filter error: output withheld]"). [VERIFIED: [ADR-B01](../../decisions/ADR-B01-privacy-gateway.md)]
- After the fix: 0 of 136 tool results leaked synthetic secrets end to end; 0 hits in 54 live scans. [SIMULATED: [EXP-052](../../experiments/adversarial/EXP-052.md); VERIFIED: [EXP-056](../../experiments/successful/EXP-056.md)]
- The ledger stores salted HMAC fingerprints only, never values; `evidence_refs` expose execution ids and a 12-character fingerprint prefix, never a value. [VERIFIED]
- Residual masking gaps for some identifier and key shapes are documented and tracked internally. [OBSERVED]

## 3. Run isolation (Pro)

Each MCP call is bound to its run by a header; reads of another run's campaign are refused. In the two monitored DVGA runs of 2026-11-20: 3 `same_run` bindings, 0 refused, no forbidden operation executed. [VERIFIED: [EXP-061](../../experiments/successful/EXP-061.md)]

## 4. Target safety

An agent once froze the DVGA target with a state-changing mutation and reported its own side-effect as a DoS finding; TARGET SAFETY prompt rules followed in both editions. [VERIFIED: [ADR-E01](../../decisions/ADR-E01-agent-target-safety.md)]

## 5. Terminal Explorer

The read-only TUI makes 0 model calls and neutralises ANSI/OSC injection in displayed data. [VERIFIED: [EXP-058](../../experiments/successful/EXP-058.md)]
