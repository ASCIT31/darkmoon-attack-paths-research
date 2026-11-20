# Comparisons

Status: public research documentation, version 1.0.0, 2026-11-20. Labels as in [architecture §0](../../docs/architecture/README.md#0-conventions).

## 1. Agent-native declarations vs structured representations (inside DarkMoon)

| Dimension | Agent-native `enabled_by` (D) | Server-observed data flow (R1/R3, ledger) | Rule / capability graph | Structured claims (CHAIN DATA) |
|---|---|---|---|---|
| Who creates edges | acting agent, in its push | server | fixed rules over findings | agent, filling 4 typed fields |
| Fill rate on real runs | 15/39 (c5), 7/11 (c6), 19/28 (c7), 16/68 (DVGA Community) | R1 0 and R3 5 in 18 campaigns | always produces | 0/340, 0/51 |
| Precision vs oracle | 0.32–0.6 (n = 1 each) | high when it fires (harness: 0 false promotions, SIMULATED); rarely fires on real data | 12.8 % edges; 12–15 % text rules (SIMULATED) | not measurable (empty) |
| Protocol coverage | any | effectively HTTP for rules; ledger generic but needs full values | depends on rule base | any |
| Token cost | ≈ +0.5 % for the whole mechanism (SIMULATED) | 0, unless ids are shown to the agent (+1–1.75 %) | 0 | small, unused |
| Failure mode | plausible but wrong links (same cause) | silent abstention | systematic false edges | silence |
| Product status | shown at once as **Possible** | **Evidence-Backed** when every step qualifies | not used | hidden from schema |

Reading: the agent is a **high-recall, low-precision proposer**; the server is a **near-zero-recall, high-precision verifier** on real data. DarkMoon labels both separately and never adds them into one "proven" count. [HYPOTHESIS: synthesis of small samples on different targets; source: [agent-native §5](../../docs/agent-native/README.md)]

## 2. DarkMoon vs related research systems

| Family | Similar | Different |
|---|---|---|
| MulVAL / TVA attack graphs | pre/post-condition edges, AND/OR, hardening-oriented ranking | they **derive** potential edges from a model; DarkMoon tries to **certify** executed edges |
| Provenance IDS (SLEUTH, HOLMES, UNICORN) | value lineage as evidence; same failure modes (false dependencies, capture gaps) | tool-call layer inside DarkMoon's own runtime, not OS audit logs; the "adversary" is the system's own agent |
| LLM pentest agents with graphs (VulnBot, PENTESTLLMAGENT, Intentest, Excalibur) | explicit graph next to an LLM agent | their graph is **planning state**; DarkMoon's is an **attestation layer** with separate declared / corroborated statuses |
| Trajectory analysis (AgentTrails, CTF-ABACUS) | reconstruct what the agent actually did from traces | AgentTrails lets an LLM refine edges; DarkMoon forbids model output from promoting an edge |

None of the verified papers reports the precision of executed causal links between findings as a metric. [OBSERVED: [literature review §3.7](../literature-review/README.md)]

## 3. Limits of comparison

- **Targets:** DarkMoon used Juice Shop, DVGA, crAPI and self-built labs; the literature mostly uses CTFs, VulnHub/HTB machines, GOAD and CVE sandboxes. No DarkMoon run is on a public benchmark.
- **Metrics:** edge precision/recall vs a per-campaign oracle is not convertible to flag success or sub-task completion.
- **Protocols:** n = 1 per setting, budget-capped (USD 6–20 per run), one model.
- **Model confound and contamination** apply to DarkMoon as to published systems.
- **Self-evaluation:** harnesses and oracles were built by the same team.

For DarkMoon's public detection benchmarks (a different question: findings, not links), see [`../../benchmarks/`](../../benchmarks/README.md).
