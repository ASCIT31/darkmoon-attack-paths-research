# 04 — Path analysis

```mermaid
flowchart TB
  IN["Community: attack_paths_local.attack_paths_doc<br/>Pro: routes_attack_paths (Pro only)"]
  BUILD["attack_paths.build_paths -> _analyse"]
  DER["structured claims + RT-04 corroboration<br/>attack_chains.derive_scenarios, attack_paths._uncorroborated"]
  OBS["server rules R1, R3 (+ R2 as inferred) and declared links<br/>attack_paths_observed.derive_observed_input"]
  DL["declared_links.resolve_one / validate"]
  Q["edge_verifier.qualify_edge<br/>(facts from attack_paths_observed._declared_ledger)"]
  DFS["simple-path assembly, depth <= 6, <= 100 chains, time budget<br/>prerequisite_steps -> edge_verifier.bounded_chains"]
  INF["possible paths from topology + confirmed findings, <= 40<br/>attack_paths_inferred.derive_inferred"]
  LVL["path level = weakest link<br/>attack_paths.path_level"]
  RANK["ranking over non-hypothesis paths + display gate<br/>attack_paths_engine.prioritize_corrections, ranking_gate"]
  FIX["Fix first / triage, fallback 'by reported severity x proof'<br/>fix_triage.fix_first"]
  KIND["Evidence-Backed vs Possible<br/>exec_core/legend.path_kind_of"]
  IN --> BUILD
  BUILD --> DER
  BUILD --> OBS
  OBS --> DL --> Q
  Q --> DFS --> LVL
  BUILD --> INF
  LVL --> RANK --> FIX
  LVL --> KIND
```

Level order: `inferred` < `declared_by_agent` < `observed_flow` < `observed` (R1/R3) < `causal_necessity_demonstrated`. A path with any `declared_by_agent` or `inferred` step is a **Possible Attack Path** (`hypothesis: true`) and is never ranked. Code: [`mcp/api/attack_paths.py`](https://github.com/ASCIT31/Dark-Moon/blob/10eaab0/mcp/api/attack_paths.py), [`attack_paths_observed.py`](https://github.com/ASCIT31/Dark-Moon/blob/10eaab0/mcp/api/attack_paths_observed.py), [`attack_paths_inferred.py`](https://github.com/ASCIT31/Dark-Moon/blob/10eaab0/mcp/api/attack_paths_inferred.py), [`fix_triage.py`](https://github.com/ASCIT31/Dark-Moon/blob/10eaab0/mcp/api/fix_triage.py), [`exec_core/legend.py`](https://github.com/ASCIT31/Dark-Moon/blob/10eaab0/mcp/api/exec_core/legend.py).
