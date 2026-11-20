# Attack-path experiments: Juice Shop, crAPI, dmlab, multi-protocol lab (c6/c7), arch-D V2/V3, micro-tests, compaction, final validation, DVGA

Status: public research documentation, version 1.0.0, 2026-11-20. Reconstructed read-only from code and internal reports. English. Conventions and labels are as in [`ATTACK-PATHS-ARCHITECTURE`](../docs/architecture/README.md) §0.
- `EXP-nnn` = the experiment registry ([`experiments/`](README.md)). A registry status is repeated here unless this documentation re-checked it.
- `R/` = internal report archive; `D27/` = internal R&D programme.

## 0. Reading rules (binding for every table below)

1. **Never compare finding counts across targets.** Juice Shop, crAPI, DVGA, dmlab and the multi-protocol lab differ in size, planted content and agent dispatch. Counts are given per run only.
2. **Never read a deterministic-harness or proxy-simulation score as agent-declaration precision.** Those rows are labelled SIMULATED.
3. **n = 1 almost everywhere.** Wilson intervals are given where the source gives them. Most real-run comparisons have overlapping intervals and are **not conclusive**.
4. **"Proven" changed meaning over time** ([`CAUSALITY-AND-EVIDENCE`](../docs/causal-model/README.md) §3). Older "proven" figures are reported with the definition in force at the time.
5. **Costs.** Costs are USD actually billed as recorded in the source. Many earlier runs have no recorded cost (UNKNOWN).

---

## 1. Master table of runs relevant to attack paths

| EXP | Date | Target | Kind | Model | Cost USD | Findings | `enabled_by` | Paths | Server-qualified / proven | Status |
|---|---|---|---|---|---|---|---|---|---|---|
| [EXP-004](inconclusive/EXP-004.md) | 08-02 | Juice Shop | real, public README claim | Claude (cloud) | — | 57 | n/a | n/a | n/a | UNKNOWN (marketing claim) |
| [EXP-010](successful/EXP-010.md) | 11-08 | 18 production campaigns | replay of real data | none | 0 | 340 | n/a | 5 proven (all R3), 62 inferred | ranking allowed 0/18 | VERIFIED |
| [EXP-015](failed/EXP-015.md) | 11-08 | dmlab (15 services, 14 truth links) | real ×3 arms | opus-4-6 | 116.95 total | per run | n/a | causal-edge recall **0/14** every run | — | VERIFIED |
| [EXP-016](inconclusive/EXP-016.md) | 11-08 | Juice Shop | real | opus-4-6 | 8.65 + 8.59 | 9/24, 13/24 TP | n/a | — | — | SUPERSEDED (contaminated: agent hit scoring endpoints) |
| [EXP-024](failed/EXP-024.md) | 11-08 | dmlab ×2 | real + frozen harness | opus-4-6 | 22.37 | 23, 85 | n/a | 1 verified path of 18 candidates; 0/14 multi-finding | criterion NOT MET | VERIFIED |
| [EXP-029](inconclusive/EXP-029.md) | 11-09 | crAPI | real | opus-4-6 | 0.5967 | 0 | n/a | — | — | SUPERSEDED (token cap misconfigured, 103 s) |
| [EXP-030](inconclusive/EXP-030.md) | 11-09 | crAPI | real | opus-4-6 | 21.98 (INFERRED sum) | 43 claimed (≈15–18 distinct by judgement) | n/a | product 3 declared / 1 derived / 4 inferred | E3 corroborates 1/43 passively | OBSERVED |
| [EXP-031](failed/EXP-031.md) | 11-09 | crAPI (isolated) | active replay, 12 requests | none | 0 | — | n/a | 2 direct (N3 → N5); **0 N4** | — | SIMULATED (harness) |
| capstone | 11-11 | dm-lab 3 hosts | real | opus-4-6 | 28.73 | 33 (18 exploited) | n/a | "2 proven cross-host chains" (declared, every step exploited) + 4 derived + 10 inferred | necessity not adjudicated | SUPERSEDED definition (see §3) |
| [EXP-032](inconclusive/EXP-032.md) | 11-10/11 | own HTTP + PostgreSQL lab | real ×4 micro | opus-4-6 | 0.5447, 5.82, … (series ≈ 16.5–20.6) | — | n/a (annotations: 0 / 9 / 5 / 8) | — | — | OBSERVED |
| [EXP-035](inconclusive/EXP-035.md) | 11-16 | Juice Shop | real smoke, stopped | opus-4-6 | 1.29 | 5 | — | — | — | OBSERVED |
| [EXP-037](failed/EXP-037.md) | 11-16 | Juice Shop | real | opus-4-6 | 13.96 (internal note) | 51 (35 exploited) | — | 13, all inferred | 0 | OBSERVED (internal note only) |
| [EXP-041](successful/EXP-041.md) c4 | 11-17 | Juice Shop | real | opus-4-6 | 13.24 | 36 (22 exploited) | 4 | 12 (3 agent-declared, 3 finalize-declared wrongly counted proven → fixed `f7d97ab4`, 6 inferred) | — | VERIFIED cost; paths from an internal note |
| [EXP-042](successful/EXP-042.md) c5 | 11-17 | Juice Shop | real, USD 15 cap | opus-4-6 | 15.11 (cap, 53 min) | 39 | 15/39 | — | "0 → 2 proven" by R4 endpoint rule, later 0 under C/D | VERIFIED |
| [EXP-043](inconclusive/EXP-043.md) c6 | 11-17 | multi-protocol lab | real, orchestrator only | opus-4-6 | 6.05 | 11 | 7 findings, 9 links | — | 0/9 qualified (replay) | VERIFIED |
| [EXP-050](failed/EXP-050.md) c7 | 11-18 | multi-protocol lab | real, arch-D image | opus-4-6 | 9.2707 | 28 | 19 findings, 19 links | 3 complete chains | **0/19** | VERIFIED |
| [EXP-055](inconclusive/EXP-055.md) | 11-18 | lab-multi | real | opus-4-6 | 1.92 | 1 (false positive) | 0 | — | — | VERIFIED (inconclusive: scanner defect) |
| [EXP-056](successful/EXP-056.md) | 11-18 | lab-multi | real, final validation | opus-4-6 | 8.46 | 29 (8 exploited) | 19 links | — | **0 promoted** | VERIFIED |
| [EXP-057](successful/EXP-057.md) | 11-18 | DVGA, official install | real | opus-4-6 | 3.697 (+ ≥ 2.12 unreported 2nd run) | 13 (5 exploited) | 0 | 2 inferred, 0 declared | 0 | VERIFIED |
| [EXP-060](successful/EXP-060.md) | 11-19 | DVGA, Community port | real | opus-4-6 | 19.64 (+ ≈ 1 aborted) | 68 (34 exploited) | 16 findings, 16 links, 0 rejected | 17 (11 declared, 6 inferred) | **0 by construction** | VERIFIED |
| [EXP-061](successful/EXP-061.md) run 1 | 11-20 | DVGA (Pro, sandboxed install) | real, monitored | opus-4-6 | 5.5818 | 23 (13 exploited) | 5 findings, 6 links | see §8.2 | see §8.2 | VERIFIED (cost/findings) |
| [EXP-061](successful/EXP-061.md) run 2 | 11-20 | DVGA (Pro, after fix) | real, monitored | opus-4-6 | 5.231 | 23 (13 exploited) | 6 findings, 6 links | see §8.2 | see §8.2 | VERIFIED (cost/findings) |

Sources: the registry entries named, plus:
- capstone: commit `2fe1c38f` `REPORT_CAPSTONE_N4.md`;
- [EXP-061](successful/EXP-061.md): R/67 11-DOCKER-INSTALL-E2E.md;
- `enabled_by` counts for [EXP-061](successful/EXP-061.md): counted for this documentation from the archived findings files of the two runs (fields only, no evidence read).

---

## 2. Juice Shop

| Item | Result | Label |
|---|---|---|
| Public claim (README) | 57 findings in 28.5 min; single run, no sealed truth, backing benchmark repo not available locally | UNKNOWN, [EXP-004](inconclusive/EXP-004.md) |
| d27 P6 arm A | 9/24 and 13/24 TP but **invalid**: the agent made 5 and 35 attempts on the target's scoring endpoints (contamination) | SUPERSEDED, [EXP-016](inconclusive/EXP-016.md); D27/19_design/DATA_VALIDITY.md |
| Expert engine (rules vs observation-only arm) | 0 verified paths in both arms; removed from evidence (contaminated, not preregistered) | SUPERSEDED, [EXP-025](inconclusive/EXP-025.md) |
| Capability graph (MulVAL-like) on 51 real findings | 10/78 edges correct (12.8 %), 9/87 paths fully correct, recall 10/22 | SIMULATED, [EXP-039](failed/EXP-039.md) |
| 2026-11-16 full campaign | 51 findings, 13 paths all inferred, 0 proven → owner refused push → proof-chain and method-watch rounds | OBSERVED (internal note), [EXP-037](failed/EXP-037.md) |
| c4 | 4 of 36 findings declared `enabled_by`. Cause: only `pentest.md` mentioned it; sub-agents had no instruction | VERIFIED cost / OBSERVED cause, [EXP-041](successful/EXP-041.md); an internal topic-evidence note §3 |
| c5 (after lever 1) | `enabled_by` 15/39; oracle strict precision 0.6 (5 decidable of 16), recall 2/6; "2 proven" by R4 endpoint rule | VERIFIED, [EXP-042](successful/EXP-042.md) |
| c5 under C/D | 0 proven; 16 declared links unverified (no execution ids); D replay 0/16 qualified (15 `no_shared_value`, 1 `session_unknown`) | SIMULATED, [EXP-045](inconclusive/EXP-045.md), [EXP-047](adversarial/EXP-047.md) |

**Limits.**
- Juice Shop solutions are public, so contamination is a risk. The registry and DATA_VALIDITY.md exclude P6 Juice runs from detection scores.
- c5's oracle has only 5 decidable declarations.

---

## 3. crAPI, dmlab, capstone: the strict-causality track (Darkmoon 2027)

| Item | Result | Label |
|---|---|---|
| dmlab P6 (arms A / B+C / A2) | causal-edge recall **0/14** on every run; causal-field share B+C 0.435 vs A2 0.01; **9/11 agent-declared links correct but not corroborated by the server**; per-run precision 0.39–0.77; strict FP 5–28 per run | VERIFIED, [EXP-015](failed/EXP-015.md) |
| CF1 (counterfactual replay) | primary 9/11 correct, **2 false proven links**; wire flows 20/20 correct but recall 4/29 | SIMULATED, [EXP-019](failed/EXP-019.md) |
| CF2 | 34/40 correct, **6 false** (all confirmed false by blind ADJ1); Juice held-out 0 candidates (design error) | SIMULATED, [EXP-020](failed/EXP-020.md), [EXP-021](adversarial/EXP-021.md) |
| CF3 | dev 111/112; Juice held-out 3/9, **6 false** | SIMULATED, [EXP-022](failed/EXP-022.md) |
| CF4 | Juice V1 9/9, V2 8/9; V3 not measurable (lab torn down by another harness) | SIMULATED, [EXP-023](inconclusive/EXP-023.md) |
| CF-CONFIRM (fresh real runs) | 2 single-finding proven reach paths, **0/14 multi-finding links**, 1 verified path of 18 candidates; image lacked P6 fixes | VERIFIED, [EXP-024](failed/EXP-024.md) |
| E3 passive engine (falsification) | synthetic: 0/1,387 false chains, 0/511 false authorisations; real taps: 18–27 direct exploitations per dmlab run, **0 chains**; 1,627 cases vs 1,700 preregistered (no deviation note) | SIMULATED, [EXP-027](inconclusive/EXP-027.md) |
| Offline A/B current engine vs E3 (9 stored runs) | current 264 paths (246 inferred, 3 proven); E3 155 direct, 0 chains | SIMULATED, [EXP-028](inconclusive/EXP-028.md) |
| crAPI-2 full run | 43 findings claimed; E3 corroborates 1/43; product 3 declared / 1 derived / 4 inferred; no sealed truth | OBSERVED, [EXP-030](inconclusive/EXP-030.md) |
| E4 active replay on crAPI (C1, C2, C4) | C1 `/.env` direct disclosure; C2 BOLA direct (needed a lab-declared permission reference); C4 legitimate; **0 N4** | SIMULATED, [EXP-031](failed/EXP-031.md) |
| V1 `DM_ATTACK_CHAINS` flag ON on crAPI | 2 confirmed direct exploitations, 0 N4 chains, 2 ranked corrections; the product owner refused automatic BOLA promotion | OBSERVED, an internal topic-evidence note §1 |
| **Capstone N4** (3-host lab, 11-11) | 33 findings; the agent's **finalisation-declared** chains rendered proven because every step was `exploited`; 1 negative control; E4 gated OFF | OBSERVED (`2fe1c38f`); **SUPERSEDED** as "proven" by `9a5b6bc2` (11-12) and `f7d97ab4` (11-17) |

**Conclusion of the track.** No real N4 chain under the strict definition. [VERIFIED]

**Contradiction recorded.** The capstone report says "two genuinely proven multi-step cross-host attack chains". The registry says "no multi-finding (N4) chain was ever proven on real data". Both are true only under their own definitions. The capstone's own "Honest limit" section states that per-step necessity was not shown. The later product rule makes such chains hypotheses.

---

## 4. Multi-protocol lab: c6, c7, [EXP-055](inconclusive/EXP-055.md), [EXP-056](successful/EXP-056.md) (final real validation)

The lab chain is unauthenticated Redis → DB credentials → PostgreSQL access/superuser → RCE / file read → SSH key → SSH. The later steps (.env, SMB with credentials, MQTT) were never reached in any run. [VERIFIED: R/54 §9; R/60 LAB-PENTEST.md]

| Metric (oracle, strict) | c6 (integ, orchestrator only) | c7 (arch-D + wording `e44ceba8`) | [EXP-056](successful/EXP-056.md) final (arch-D V0 + scanner fix) |
|---|---|---|---|
| Cost / duration | 6.05 / — | 9.2707 / 26.6 min | 8.46 / 26.6 min (budget guard stop) |
| Sessions | 1 | orchestrator + 3 sub-agents | orchestrator + 3 sub-agents (sql-databases, storage, messaging-cache) |
| Findings | 11 | 28 | 29 (8 exploited) |
| Declared links | 9 | 19 | 19 |
| TP / FP / FN | 5 / 4 / 1 | 6 / 13 / 3 | ≈ 7 correct / ≈ 12 same-cause (approximate) |
| Strict precision | **55.6 % [26.7–81.1]** | **31.6 % [15.4–54.0]** | **≈ 37 %** (approximate) |
| Strict recall | 83.3 % [43.6–97.0] | 66.7 % [35.4–87.9] (9/9 by reachability) | — |
| FP classes | 3 transitive + 1 not-a-path | 12 same-cause facets + 1 wrong parent, 0 transitive | 6 Redis facets + 6 SMB facets |
| Missed spine link | PG access → SSH key | same | DB credentials ← Redis |
| Complete chains | 2 | 3 | — |
| Server-qualified links | 0/9 (replay) | **0/19** | **0/19** |
| Privacy | lab SSH key reached provider ×4 | ×1 (fixed `63164ac9`) | 0 hits in 54 live scans |

[VERIFIED: [EXP-043](inconclusive/EXP-043.md), [EXP-050](failed/EXP-050.md) (R/54 FINAL-AUTONOMOUS-VALIDATION.md §8, CYCLE2-CAMPAIGN7.md §1); [EXP-056](successful/EXP-056.md) (R/60 LAB-PENTEST.md; "approximate" per the source)]

**Notes.**
- **Oracle sensitivity.** One mapping choice in c7 (M2: PG access vs superuser as separate types) moves the headline by about 10 points, to 42 %. [VERIFIED: CYCLE2-CAMPAIGN7.md "M2 sensitivity"]
- **Wording change.** It eliminated transitive errors (3 → 0), but a new error class appeared with sub-agents, so the net effect is not attributable. It was reverted (`cbc4df04`). [VERIFIED]
- **[EXP-055](inconclusive/EXP-055.md)** (same lab, earlier image): 1 false finding ("No Network Services Running"), caused by a naabu DNS / port argument defect. Fixed by `c4a85d5d`. [VERIFIED]
- **Metadata contradiction, [EXP-056](successful/EXP-056.md).** `kpi.json` has `stopped_by_guard: null`, while `budget_guard.log` and LAB-PENTEST.md report a guard stop. [VERIFIED by registry; noted in forensic timeline §14.2]

---

## 5. Architecture-D studies: V2 (autonomous mission), V3 (agent reasoning), harnesses

| Study | What | Result | Label |
|---|---|---|---|
| harness-d (R/51) | 89 scenarios / 102 edges, independent oracle | 0 critical false promotions; precision 1.0, recall 0.829; mutation 90.5 → 94.4 %, 0 permissive | SIMULATED, [EXP-047](adversarial/EXP-047.md) |
| Real replay c5/c6 under D | 25 declared links | **0/25 qualified** (masked evidence; no MCP session identity) | SIMULATED, [EXP-047](adversarial/EXP-047.md) |
| Final review / closure (R/52, R/53) | 100 scenarios / 117 edges | 0 false promotions, recall 0.867; mutation run 3 80.3 % (38 permissive, in R2 neighbours and masks) → run 4 95.2 %, 0 permissive; 6 "MAJOR" missed promotions = prudent abstentions; log re-mask migration stopped (regression) | SIMULATED, [EXP-049](adversarial/EXP-049.md) |
| V2 mission (R/54) | c7 real + privacy fixes | see §4; GO technique, merge conditional, **product NO** | VERIFIED, [EXP-050](failed/EXP-050.md) |
| V3 (R/55) phase 1 | per-link analysis of the 12 c7 same-cause errors | all from sub-agents; causes: loose sub-agent wording ("finding … access"), precondition ≠ progression, revealed ≠ enabled, state finding cited, content anchoring | VERIFIED (analysis of the c7 DB) |
| V3 simulation | proxy sub-agents, c7 replay ×4 + hard independent corpus | c7: V0 27 % → B 78 %; hard corpus: V0 91.2 %, B 79.4 %, C 82.6 % (one C run declared 0 links) | SIMULATED, [EXP-051](failed/EXP-051.md) |
| V3 privacy audit | 1,009 synthetic cases | 219 leaked on `63164ac9` → 0 after `44a20255` (+11 documented xfail residuals); sanitizer fail-closed `64d72147` | SIMULATED, [EXP-051](failed/EXP-051.md)/052 |
| Prod-readiness E2E (sim provider) | synthetic secrets | run 1: 42 key fragments reached the model; final: 0 of 136 tool results | SIMULATED, [EXP-052](adversarial/EXP-052.md) |

---

## 6. Micro-test V0 / V1 / V2 (R/57, 2026-11-18)

**Design.** 3 variants × 2 sets × 2 passes = 12 Claude Code sub-agent simulations, run against sealed V3 oracles.
- **Variants:**
  - V0 = the current text;
  - V1 = "mental qualification" wording (+72 chars);
  - V2 = an isolated typed label `DIRECT / SAME_CAUSE / CONTEXT / NONE`.
- **Cost:** about 858 k Claude Code tokens; 0 Darkmoon-provider calls.

[SIMULATED, EXP-053]

| Pooled over 2 passes | V0 | V1 | V2 |
|---|---|---|---|
| Independent subset: strict precision / recall | 100 % / 30/30 | 100 % / 30/30 | 100 % / 30/30 |
| c7 replay: strict precision | 36 % (8/22) | 33 % (6/18) | 40 % (8/20) |
| c7 replay: recall | 8/18 | 6/18 (2 true links lost, incl. cross-agent) | 8/18 |
| c7 replay: same-cause errors | 14 | 10 (−29 %) | 11 (−21 %) |
| Pass-to-pass variance | ±20 pt | ±20 pt | ±20 pt |

**Decision.** Keep V0; the experiment is closed. Neither variant reached the −50 % same-cause criterion, and the variance exceeded the differences. [VERIFIED: R/57 RECOMMENDATION.md]

---

## 7. Compaction research (R/58, 2026-11-18)

**Hypothesis.** Earlier native compaction could cut campaign cost by ≥ 10 %.

**Result.** A calibrated counterfactual model reproduced c5 at USD 15.11 and predicted −1 % to +2 % at the best threshold. The reasons:
- c5–c7 had **0 compactions** (peak context ≈ 185 k vs trigger ≈ 968 k);
- the fork sends the compaction request without tools or system prompt, so it misses the prompt cache.

**Decision.** NO-GO; 0 USD spent. [SIMULATED, EXP-054]

**Relevance to attack paths.**
- Compaction would drop finding ids from the agent's context and break `enabled_by` recall.
- The engine abstains on `run_compacted`.
- Since no compaction happened, this effect is **UNKNOWN** in practice. [VERIFIED: `edge_verifier.D_REASONS`; R/50 MINIMAL-ARCHITECTURE.md §1]

---

## 8. DVGA campaigns

### 8.1 Earlier DVGA runs

| Item | [EXP-057](successful/EXP-057.md) (Pro official install, 11-18) | [EXP-060](successful/EXP-060.md) (Community port, 11-19) |
|---|---|---|
| Cost | 3.697 (+ second run ≥ 2.12, missing from the report) | 19.64 (+ ≈ 1 aborted launch with the wrong agent) |
| Findings | 13 (5 exploited) | 68 (34 exploited) |
| `enabled_by` | 0 | 16 findings, 16 links, 0 rejected |
| Paths | 2 inferred (1 critical), 0 declared, 0 proven | 17: 11 declared (8 critical) + 6 inferred; 0 proven by construction |
| Incidents | graphql agent called a state-changing mutation, froze DVGA and reported its own side-effect as DoS (INFERRED FP) → TARGET SAFETY rules (PRs #49, #50, #52) | self-SSRF froze DVGA 4×; `/start_over` reset; destructive IDOR proofs; target IP decoded from `/proc/net/tcp` (privacy, fixed) |
| Label | VERIFIED | VERIFIED |

[R/63 PROD-INSTALL-CAMPAIGN.md; R/66 07-DVGA.md]

**Contradiction.** An internal topic-evidence note gives [EXP-057](successful/EXP-057.md) as USD 3.60 / 80 calls / 95 % cache. The registry and kpi give USD 3.697 / 83 calls. The registry figure is kept.

### 8.2 Today's two monitored DVGA campaigns (R/67 11-DOCKER-INSTALL-E2E.md, 2026-11-20)

**Context.** Pro `feat/executive-security-v2` image, sandboxed official install in Docker-in-Docker, local DVGA lab. The run used focus `graphql` plus 4 rules: lab only, no destructive or DoS mutations, `--max-time 10`, finalise within 20 min. A budget guard was active.

| | Run 1 | Run 2 (after fix `b4b38f60`) |
|---|---|---|
| Cost / calls | **5.5818 USD** / 85 (graphql 3.3776, orchestrator 2.0128, explore 0.1914) | **5.231 USD** / 91 (graphql 3.3631, orchestrator 1.6542, explore 0.2136) |
| Duration | 18.95 min, ended by itself; guard never stopped it | server-measured activity 16 min (executive coverage sentence) |
| Findings | 23: 6 critical, 4 high, 9 medium, 4 low; 13 exploited, 10 confirmed; graphql 22, orchestrator 1 | 23: 5 critical, 7 high, 9 medium, 2 low; 13 exploited, 10 confirmed; all graphql |
| Run binding | 3 `run-binding ok … same_run`, 0 refused | same |
| Forbidden operations executed | none | none |
| Defect found | High: MCP (uid 10001) could not read root-0600 run sidecar/policies → wrong coverage signals; fixed `b4b38f60` | coverage signals correct (`campaign_kind: short`, source `launcher`) |
| `enabled_by` (counted for this documentation) | **5 findings, 6 links**, all exact vuln ids, all from `graphql` | **6 findings, 6 links**, all exact vuln ids, all from `graphql` |

[VERIFIED: R/67 §6, §8; `enabled_by` counts VERIFIED from the archived findings files, fields only]

**Attack paths for these runs.** R/67 does not report attack-path figures [VERIFIED: report text]. This phase recomputed them offline:
- Method: the Pro `origin/dev` engine (`attack_paths.build_paths`, copied read-only) on the archived campaign, infrastructure and findings files. No run ledger was available, so every D link abstains with `missing_data`. This is a recomputation, not the production document. [SIMULATED]
- **Run 1** (11 infrastructure nodes):
  - 5 paths: **1 `observed` (Evidence-Backed)** + 4 `agent-declared` (3 critical);
  - links: 6 D `declared_by_agent` + 1 **R3** "system account file read on the host";
  - the Evidence-Backed path rests on the R3 pattern found in the agent-pushed evidence of an exploited finding. It is not an independent capture, and it is not an agent-declared link (see [`CAUSALITY-AND-EVIDENCE`](../docs/causal-model/README.md) §4 item 2).
- **Run 2:** no infrastructure copy is archived. The recompute had 0 nodes, so its path count is not meaningful: **UNKNOWN**. Its 6 D links stay `declared_by_agent`.
- The production qualification state of these 12 links (with the real ledger) is **UNKNOWN**. Every previous real run gave 0 qualified.

**Same configuration, run to run.** Both runs had the same target, rules and image family. The headline counts are close: 23 vs 23 findings, 13 vs 13 exploited, 5 vs 6 declaring findings. The severity mix and cost still differ (5.58 vs 5.23 USD). Two runs are not enough to estimate variance. [VERIFIED counts; HYPOTHESIS on stability]

---

## 9. Cross-cutting observations

### 9.1 Common-root-cause ("same cause") errors

- **Dominant error class with sub-agents.**
  - c7: 12 of 13 false links (4 Redis facets, 4 version-matched CVEs, 2 SMB facets, 2 state findings).
  - Final lab run: ≈ 12 of 19.
  - 0 of them came from the orchestrator. [VERIFIED: R/55 PHASE1; R/60]
- **Not reproduced by the proxy model on synthetic corpora** (0 same-cause errors). It depends on real delegation prompts and long sessions. [SIMULATED: R/55 §6]
- **No fix was adopted.** Wording was reverted; variants B, V1 and V2 were not applied. [VERIFIED]

### 9.2 Missing inter-agent relations

| Campaign | Observation | Label |
|---|---|---|
| c5/c6 | **0 of 25** cited ids were cross-agent; all were intra-session | VERIFIED: R/50 |
| c7 | 7 of 9 expected links crossed agents; 4 declared; the critical spine link PG access → SSH key was missed (also missed in c6 and the final run) | VERIFIED: R/54 |
| Mechanism | ids cross agents only through orchestrator prompts and task results. The server cannot attribute MCP calls to sessions (no session id per call), so it cannot add the missing relations itself | VERIFIED: R/51 SERVER-ATTRIBUTION.md |

### 9.3 Wrong links (server side, historical)

| Engine | Wrong links | Label |
|---|---|---|
| Architecture A | c6 `pg_hba` finding bound to a refused `cat /etc/shadow`; c5 links bound by declared URL only | VERIFIED: R/40 00-baseline.md |
| R4 own-command | 5 families, then 7 new false links | VERIFIED: R/14 |
| CF1 / CF2 / CF3 | 2 / 6 / 6 false links | SIMULATED |
| Community engine (pre-merge review) | JWT co-occurrence in agent text marked "PROVEN · corroborated" (HIGH) → evidence text withheld | OBSERVED: R/66 03 |
| Architecture D harnesses | 0 false promotions | SIMULATED |

### 9.4 Declared vs corroborated paths

Every real run under C/D has **0 corroborated (server-qualified) declared links**:
- c5/c6 replay: 0/25;
- c7: 0/19;
- final lab run: 0/19;
- DVGA Community: 0/16 by construction;
- today's DVGA runs: 0/12 in the offline recompute without the ledger; production state UNKNOWN.

Declared paths are therefore always shown as Possible.

Evidence-backed paths, where they exist, come from server pattern rules (R3/R1) on pushed evidence, not from declarations:
- 5 in 18 production campaigns;
- 1 in today's DVGA run 1 (recompute).

[VERIFIED / SIMULATED as marked]

### 9.5 Campaigns with zero corroborated or proven paths

| Campaign | Result | Label |
|---|---|---|
| 13 of 18 production campaigns (paths v1 diagnosis) | no proven path | VERIFIED: D27/02 |
| Juice Shop 11-16 | 13 paths, all inferred | OBSERVED, internal note |
| c5 and c6 under C/D | 0 | SIMULATED |
| c7, final lab run, DVGA [EXP-057](successful/EXP-057.md), DVGA Community [EXP-060](successful/EXP-060.md) | 0 | VERIFIED |
| dmlab P6 | causal-edge recall 0/14 on every run | VERIFIED |
| Today's DVGA run 2 | UNKNOWN (no infrastructure archive) | — |

### 9.6 Run-to-run variability

- **Cost.** c6 → c7 +53 % with similar prompts, driven by 3 sub-agents. [VERIFIED: R/54 §6; R/58]
- **Proxy model.** The same variant on the same corpus gave V0 82.6 % vs 100 % (V3), and ±20 pt between passes (micro-test). [SIMULATED]
- **Token overhead vs noise.** Run-to-run variance (≈ 20 %) is far larger than the overheads being measured (≤ 1 %). A behavioural A/B would need ≥ 5 real campaigns per variant (≈ USD 60); it has not been run. [OBSERVED: R/50 FINAL-RECOMMENDATION.md §7]
- **Statistical consequence.** No pair of real runs in this programme separates configurations with non-overlapping Wilson intervals. [VERIFIED: R/54 §8 "Not conclusive"]

---

## 10. Inconsistencies between sources (not silently resolved)

1. c6 recompute under C: "1 → 0 proven" (R/40 DECISION.md:70, RESULTS.json) vs "3 → 0" (R/40 RECOMPUTE-5-6.md:16).
2. [EXP-057](successful/EXP-057.md) cost and calls: an internal topic-evidence note (3.60 / 80) vs registry and kpi (3.697 / 83). A second run ≥ USD 2.12 is absent from R/63.
3. [EXP-056](successful/EXP-056.md): `kpi.json` `stopped_by_guard: null` vs the guard log stop.
4. Capstone "2 proven chains" vs the later invariant (§3).
5. [EXP-027](inconclusive/EXP-027.md): 1,627 synthetic cases vs 1,700 preregistered, with no deviation note.
6. [EXP-030](inconclusive/EXP-030.md) cost: summed by the extraction agent (INFERRED), not stated in a report.
7. c4 path breakdown and [EXP-037](failed/EXP-037.md) figures exist only in internal notes. The primary run logs were not located.
