# Expert systems and rule-based attempts

Status: public research documentation, version 1.0.0, 2026-11-20. Labels as in [architecture §0](../architecture/README.md#0-conventions).

DarkMoon tried several symbolic, rule-based ways of linking findings before settling on agent declarations checked by a single deterministic rule. This page groups them. None of them produced correct multi-step chains on real data.

## 1. Rules that are still in the product

| Rule | What it matches | Status in code | Real-data result |
|---|---|---|---|
| **R1 artefact** | a typed secret appears in A's output, not in A's input, and is used as a credential in B's executable input | kept ([`attack_paths_observed.py`](https://github.com/ASCIT31/Dark-Moon/blob/10eaab0/mcp/api/attack_paths_observed.py)) | 0 links in 18 production campaigns [VERIFIED: [EXP-010](../../experiments/successful/EXP-010.md)] |
| **R2 mechanism** | open registration matched by endpoint | demoted to **historical inference** (`level: inferred`, never corroborates) | 1 link in 18 campaigns; demoted after it was shown to bind by URL only [VERIFIED] |
| **R3 reach** | an exploited finding's response proves access to another node (system account file, SQL rows + named engine) | kept | 5 links (all "proven" paths) in 18 campaigns; 1 in a 2026-11-20 DVGA recompute [VERIFIED / SIMULATED] |
| **Inferred layer** | fixed topology rules, e.g. RCE → host, credential disclosure → remote access, SQLi → database | kept, always `hypothesis: true`, never ranked, at most 40 per campaign ([`attack_paths_inferred.py`](https://github.com/ASCIT31/Dark-Moon/blob/10eaab0/mcp/api/attack_paths_inferred.py)) | descriptive only |

**Important.** R1 and R3 match patterns in the evidence fields that the agent wrote into its push. They do not read an independent capture. See [provenance](../provenance/README.md).

## 2. Rule systems that were tried and rejected

| Attempt | Method | Result | Label |
|---|---|---|---|
| Earlier symbolic-AI prototype (2025-11 → 2026-01) | symbolic reasoning prototype outside the current engine | abandoned | OBSERVED |
| Expert engine (2026-11-09) | rules arm vs observation-only arm, live lab | 0 verified paths in both arms; contaminated and not preregistered, removed from evidence | SUPERSEDED, [EXP-025](../../experiments/inconclusive/EXP-025.md) |
| R4 "own command" | a finding owns the observed command whose distinctive values it reuses | 5 families of false proven links, then 7 new false links | VERIFIED, [EXP-038](../../experiments/failed/EXP-038.md) |
| MulVAL-like capability graph | CWE/CAPEC/ATT&CK pre/post-conditions over 51 real findings | 10/78 edges correct (12.8 %), 9/87 paths fully correct | SIMULATED, [EXP-039](../../experiments/failed/EXP-039.md) |
| Rules on finding text | held-out replay on stored campaigns | 12–15 % held-out precision (dmlab), 0/10 (Juice Shop) | SIMULATED, [EXP-040](../../experiments/failed/EXP-040.md) |
| ATT&CK tactic-order filter | plausibility of declared pairs | accepted 74 % of pairs and 10 of 10 false chains → kept as **warnings only** | VERIFIED |
| CHAIN DATA (structured pre/post fields filled by agents) | four typed fields on each push | filled 0/340 and 0/51 times → hidden from the served schema | VERIFIED, [EXP-010](../../experiments/successful/EXP-010.md) |

## 3. Reading

- The failures follow the classic expert-system lessons: a knowledge-acquisition bottleneck (nobody fills the structured fields) and brittleness outside the rule base (a capability graph produces many plausible but wrong edges). [HYPOTHESIS, consistent with the literature in [research/literature-review §3.5](../../research/literature-review/README.md)]
- Architecture D keeps a **minimal symbolic core**: one auditable rule (exact value lineage on the server's own records) that can only promote, never invent, and a closed set of reasons to abstain. [VERIFIED: [`edge_verifier.py`](https://github.com/ASCIT31/Dark-Moon/blob/10eaab0/mcp/api/edge_verifier.py)]
- Rule-derived structure is still useful as **context** (inferred paths, ATT&CK warnings), clearly labelled as hypotheses. [VERIFIED]
