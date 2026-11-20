# Experiments

One page per entry of the DarkMoon experiment registry (61 entries, 2026-07-02 → 2026-11-20). The registry itself is private; these pages are its public, redacted form. Facts are unchanged; local paths, run identifiers, internal hostnames and private-repository links were removed.

## How to read a page

Each page carries two independent tags.

**Evidence label** (from the registry, unchanged):

| Label | Meaning |
|---|---|
| `VERIFIED` | Checked in code, or in a primary report cross-checked with a second artefact (commit, data file). |
| `OBSERVED` | Stated in one primary source and not re-executed. |
| `SIMULATED` | Deterministic harness, replay, synthetic corpus or proxy model. **Never** agent-declaration precision on a real run. |
| `HYPOTHESIS` | An interpretation, or a claim no source tested. |
| `SUPERSEDED` | Replaced by later work; kept for history. |
| `UNKNOWN` | The evidence was not available. |

**Outcome class** (the folder; an editorial classification made for this repository):

| Folder | Meaning |
|---|---|
| [`successful/`](successful/) | The experiment met its own stated goal or decision criterion (often narrowly, often n = 1). |
| [`failed/`](failed/) | The hypothesis was rejected or the preregistered criterion was not met (NO-GO). Negative results are kept on purpose. |
| [`inconclusive/`](inconclusive/) | Contaminated, stopped, unscored, single-source or otherwise not decisive. |
| [`adversarial/`](adversarial/) | Red-team reviews, sealed harnesses with independent oracles, mutation testing, blind adjudication, falsification benches. |

**Reading rules.**
1. Finding counts are **never comparable across targets** (Juice Shop, crAPI, DVGA, dmlab, multi-protocol lab).
2. A deterministic-harness or proxy-simulation score is **not** agent-declaration precision.
3. n = 1 almost everywhere; most real-run comparisons have overlapping intervals.
4. "Proven" changed meaning over time (see [`../docs/causal-model/`](../docs/causal-model/README.md)).
5. Costs are USD actually billed as recorded in the source.

**Sources.** "internal report R/nn-…" and "internal R&D programme report D27/…" are private engineering reports, cited by identifier for traceability. Unlinked short commit hashes refer to **DarkMoon Pro, internal** (private repository). Linked hashes point to the public [Dark-Moon](https://github.com/ASCIT31/Dark-Moon) repository. Campaign aliases c4–c7 are the four real campaigns of 2026-11-17/18 used throughout the documentation.

## Index

| Experiment | Date | Class | Label | Hypothesis |
|---|---|---|---|---|
| [EXP-001](successful/EXP-001.md) | 2026-07-02 | successful | `OBSERVED` | A scaffold + small local model (7B) can reproduce most finding families of an Opus 4.6 Juice Shop run. |
| [EXP-002](inconclusive/EXP-002.md) | 2026-07-03 | inconclusive | `OBSERVED` | A local 14B model acting only as selector/orchestrator (proxy = state engine) can drive Darkmoon agents on ... |
| [EXP-003](inconclusive/EXP-003.md) | 2026-08-01 | inconclusive | `UNKNOWN` | Pro stack agents work end to end on seeded known-CVE labs. |
| [EXP-004](inconclusive/EXP-004.md) | 2026-08-02 | inconclusive | `UNKNOWN` | Public claim: Darkmoon finds 57 real vulnerabilities on Juice Shop in 28.5 min. |
| [EXP-005](failed/EXP-005.md) | 2026-09-24 | failed | `SUPERSEDED` | Cache/context engineering alone can cut real LLM cost >=30% with no degradation. |
| [EXP-006](successful/EXP-006.md) | 2026-09-24 | successful | `OBSERVED` | 'Cache hit -100%' alert is caused by routing Anthropic through the openai-compatible provider. |
| [EXP-007](successful/EXP-007.md) | 2026-10-25 | successful | `OBSERVED` | A generic vulnx-first -> targeted nuclei block can replace hard-coded CVE checks across 36 web/service agen... |
| [EXP-008](successful/EXP-008.md) | 2026-10-27 | successful | `OBSERVED` | Pro can be driven headless from the UI for an E2E pentest; investigate empty 2nd campaign / missing report. |
| [EXP-009](successful/EXP-009.md) | 2026-11-08 | successful | `VERIFIED` | Descriptive baseline |
| [EXP-010](successful/EXP-010.md) | 2026-11-08 | successful | `VERIFIED` | Q1-Q10: why does the product almost never show proven attack paths? |
| [EXP-011](adversarial/EXP-011.md) | 2026-11-08 | adversarial | `SIMULATED` | Engine AND/OR claims are exact and top-k remediation ranking is optimal. |
| [EXP-012](failed/EXP-012.md) | 2026-11-08 | failed | `SUPERSEDED` | Flag-gated fixes (causal-field prompt block, fingerprints, hints, completion, stable ids) restore proven pa... |
| [EXP-013](adversarial/EXP-013.md) | 2026-11-08 | adversarial | `VERIFIED` | P7 flags are safe to promote. |
| [EXP-014](failed/EXP-014.md) | 2026-11-08 | failed | `SIMULATED` | Cut cloud tokens 20% per campaign at equal coverage. |
| [EXP-015](failed/EXP-015.md) | 2026-11-08 | failed | `VERIFIED` | H1: P7 fixes (arm B+C) yield proven correct causal paths vs arm A. |
| [EXP-016](inconclusive/EXP-016.md) | 2026-11-08 | inconclusive | `SUPERSEDED` | Same as P6 on Juice Shop |
| [EXP-017](failed/EXP-017.md) | 2026-11-08 | failed | `SIMULATED` | R4 tool-layer observed dataflow links findings correctly. |
| [EXP-018](failed/EXP-018.md) | 2026-11-08 | failed | `SIMULATED` | Small local models can do attribution / secret extraction on an i3 8 GB CPU profile. |
| [EXP-019](failed/EXP-019.md) | 2026-11-08 | failed | `SIMULATED` | CF1: counterfactual active replay verifies causal links. |
| [EXP-020](failed/EXP-020.md) | 2026-11-08 | failed | `SIMULATED` | CF2 widened candidates fix CF1 |
| [EXP-021](adversarial/EXP-021.md) | 2026-11-08 | adversarial | `VERIFIED` | Blind adjudication confirms ground truth and claims |
| [EXP-022](failed/EXP-022.md) | 2026-11-08 | failed | `SIMULATED` | CF3 artefact/privilege-aware verified paths |
| [EXP-023](inconclusive/EXP-023.md) | 2026-11-08 | inconclusive | `SIMULATED` | CF4 |
| [EXP-024](failed/EXP-024.md) | 2026-11-08 | failed | `VERIFIED` | CF verification confirmed on fresh real campaigns |
| [EXP-025](inconclusive/EXP-025.md) | 2026-11-09 | inconclusive | `SUPERSEDED` | Rule-based expert engine finds verified paths |
| [EXP-026](adversarial/EXP-026.md) | 2026-11-09 | adversarial | `VERIFIED` | Synthetic noise controls of the falsification bench are legitimate |
| [EXP-027](inconclusive/EXP-027.md) | 2026-11-09 | inconclusive | `SIMULATED` | H-F: E3 passive evidence engine emits no chain on legitimate-only inputs |
| [EXP-028](inconclusive/EXP-028.md) | 2026-11-09 | inconclusive | `SIMULATED` | E3 vs current engine on the same 9 stored runs |
| [EXP-029](inconclusive/EXP-029.md) | 2026-11-09 | inconclusive | `SUPERSEDED` | Black-box run on an independent target (crAPI) |
| [EXP-030](inconclusive/EXP-030.md) | 2026-11-09 | inconclusive | `OBSERVED` | Same, crAPI full run |
| [EXP-031](failed/EXP-031.md) | 2026-11-09 | failed | `SIMULATED` | Active counterfactual replay yields N4 chains on crAPI |
| [EXP-032](inconclusive/EXP-032.md) | 2026-11-10 | inconclusive | `OBSERVED` | Opus annotates evidence natively during a real pentest; deterministic oracles decide (advisory only). |
| [EXP-033](successful/EXP-033.md) | 2026-11-11 | successful | `OBSERVED` | Full chain campaign -> paths -> fix first -> simulation -> Angular works end to end. |
| [EXP-034](successful/EXP-034.md) | 2026-11-16 | successful | `SIMULATED` | Integrate export into dev and validate end to end |
| [EXP-035](inconclusive/EXP-035.md) | 2026-11-16 | inconclusive | `OBSERVED` | Real LLM smoke on integration |
| [EXP-036](inconclusive/EXP-036.md) | 2026-11-16 | inconclusive | `OBSERVED` | Integration changes token cost |
| [EXP-037](failed/EXP-037.md) | 2026-11-16 | failed | `OBSERVED` | Full real campaign validates integration incl. attack paths |
| [EXP-038](failed/EXP-038.md) | 2026-11-16 | failed | `SUPERSEDED` | R4 'own command' attribution proves chains |
| [EXP-039](failed/EXP-039.md) | 2026-11-16 | failed | `SUPERSEDED` | Round 1: MulVAL-like capability graph from CWE/CAPEC/ATT&CK |
| [EXP-040](failed/EXP-040.md) | 2026-11-16 | failed | `SUPERSEDED` | Round 2: rules on finding text vs server wire-flow observation |
| [EXP-041](successful/EXP-041.md) (c4) | 2026-11-17 | successful | `VERIFIED` | enabled_by + ATT&CK filter yield agent-declared chains |
| [EXP-042](successful/EXP-042.md) (c5) | 2026-11-17 | successful | `VERIFIED` | Prompt lever + recent_exploited raise enabled_by fill; pcap tap enables necessity |
| [EXP-043](inconclusive/EXP-043.md) (c6) | 2026-11-17 | inconclusive | `VERIFIED` | Agent-declared links on non-web multi-protocol chain |
| [EXP-044](adversarial/EXP-044.md) | 2026-11-17 | adversarial | `SUPERSEDED` | Architecture C: agent cites execution ids, server verifies edges |
| [EXP-045](inconclusive/EXP-045.md) | 2026-11-17 | inconclusive | `SIMULATED` | Recompute real c5/c6 under arch C |
| [EXP-046](successful/EXP-046.md) | 2026-11-17 | successful | `SIMULATED` | Agent-native enabled_by under +1% token cap |
| [EXP-047](adversarial/EXP-047.md) | 2026-11-17 | adversarial | `SIMULATED` | Arch D: agent-declared paths qualified only by exact value attribution |
| [EXP-048](inconclusive/EXP-048.md) | 2026-11-17 | inconclusive | `OBSERVED` | Observation-only proof works across AD, IoT, cloud and databases without per-protocol logic |
| [EXP-049](adversarial/EXP-049.md) | 2026-11-18 | adversarial | `SIMULATED` | Arch D final review and closure |
| [EXP-050](failed/EXP-050.md) (c7) | 2026-11-18 | failed | `VERIFIED` | Arch D on a real multi-protocol campaign |
| [EXP-051](failed/EXP-051.md) | 2026-11-18 | failed | `SIMULATED` | Prompt variants B/C reduce same-cause false links |
| [EXP-052](adversarial/EXP-052.md) | 2026-11-18 | adversarial | `SIMULATED` | No secret reaches the model after privacy V3 |
| [EXP-053](failed/EXP-053.md) | 2026-11-18 | failed | `SIMULATED` | Rewording enabled_by (V1) or relation labels (V2) cut same-cause errors |
| [EXP-054](failed/EXP-054.md) | 2026-11-18 | failed | `SIMULATED` | Earlier native compaction cuts campaign cost >=10% |
| [EXP-055](inconclusive/EXP-055.md) | 2026-11-18 | inconclusive | `VERIFIED` | First authorised live campaign on arch D |
| [EXP-056](successful/EXP-056.md) | 2026-11-18 | successful | `VERIFIED` | Arch D + scanner fix on lab-multi |
| [EXP-057](successful/EXP-057.md) | 2026-11-18 | successful | `VERIFIED` | Official install + real campaign works |
| [EXP-058](successful/EXP-058.md) | 2026-11-18 | successful | `VERIFIED` | Read-only Terminal Explorer plugin in the OpenCode TUI without model calls |
| [EXP-059](successful/EXP-059.md) | 2026-11-18 | successful | `VERIFIED` | install-dev works on a clean environment |
| [EXP-060](successful/EXP-060.md) | 2026-11-19 | successful | `VERIFIED` | Port Pro arch-D paths + explorer to Community |
| [EXP-061](successful/EXP-061.md) | 2026-11-20 | successful | `VERIFIED` | Executive Security V2 (Cyber Score v1, treatment plan, framework mapping) works on real data |

Totals: {'successful': 17, 'inconclusive': 17, 'failed': 19, 'adversarial': 8}; labels: {'OBSERVED': 12, 'UNKNOWN': 2, 'SUPERSEDED': 9, 'VERIFIED': 18, 'SIMULATED': 20}.
