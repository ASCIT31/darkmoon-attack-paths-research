# Benchmarks

This repository does **not** duplicate benchmark data. DarkMoon's public measurement records live in two sister repositories (both verified public on 2026-11-20):

| Repository | What it holds | Relation to this research |
|---|---|---|
| [ASCIT31/Darkmoon-Benchmarks](https://github.com/ASCIT31/Darkmoon-Benchmarks) | Open benchmark records of autonomous AI penetration testing: datasets, scoring, reproduction notes and stated limits (reference lab: OWASP Juice Shop; multi-surface leaderboard) | **Detection** of findings. A different question from this repository, which studies **links between findings** |
| [ASCIT31/darkmoon-research](https://github.com/ASCIT31/darkmoon-research) | Evidence corpus: real reports from autonomous runs against public training labs | Example findings and evidence the attack-path engine consumes |

## Reading rules when combining them with this repository

1. Detection figures (findings per run) and link figures (precision/recall of declared edges) are **different metrics** and must not be combined.
2. Finding counts are **not comparable across targets**.
3. The Juice Shop headline figure in the Dark-Moon README (57 findings in 28.5 min) is a single run without a sealed ground truth; this repository records it as `UNKNOWN` ([EXP-004](../experiments/inconclusive/EXP-004.md)). Darkmoon-Benchmarks documents its own method and limits for its records.
4. No attack-path (edge-level) benchmark exists yet, in DarkMoon or, to our knowledge, in the verified literature. Building one is open direction 1 in [scientific positioning](../research/scientific-positioning/README.md#4-open-research-directions).

## Edge-level evaluation used in this repository

| Instrument | Nature | Where |
|---|---|---|
| harness-d (89 → 100 scenarios, 102 → 117 edges) | sealed synthetic harness, independent oracle, mutation testing | [EXP-047](../experiments/adversarial/EXP-047.md), [EXP-049](../experiments/adversarial/EXP-049.md) |
| Per-campaign oracles c5, c6, c7 | hand-adjudicated, strict and lenient scoring, Wilson intervals | [EXP-042](../experiments/successful/EXP-042.md), [EXP-043](../experiments/inconclusive/EXP-043.md), [EXP-050](../experiments/failed/EXP-050.md) |
| dmlab (15 services, 14 truth links) | internal composite lab with sealed truth grader | [EXP-015](../experiments/failed/EXP-015.md) |

These instruments are internal and not released; their results are described on the experiment pages.
