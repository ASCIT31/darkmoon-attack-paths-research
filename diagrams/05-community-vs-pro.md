# 05 — Community vs Pro

```mermaid
flowchart LR
  subgraph PRO["DarkMoon Pro (internal)"]
    P1["run ledger ON by default"]
    P2["qualify_edge can reach observed_flow / necessity"]
    P3["R1/R3 read agent-pushed evidence text"]
    P4["HTTP API + web dashboard + Explorer HTTP client"]
  end
  subgraph COM["DarkMoon Community (public)"]
    C1["no ledger: DM_TOOL_PROVENANCE in FORCED_OFF"]
    C2["no facts: links stay declared_by_agent"]
    C3["evidence text withheld before the engine<br/>attack_paths_local._without_evidence_text"]
    C4["attack_paths_local.attack_paths_doc + Explorer file adapter"]
  end
  P1 --- C1
  P2 --- C2
  P3 --- C3
  P4 --- C4
```

Shared: schema `darkmoon-attack-paths/v1` and the engine modules (`attack_paths*.py`, `declared_links.py`, `edge_verifier.py`, `live_push.py`). Community forces off `DM_LEGACY_DERIVED_PROOF`, `DM_FINALIZE_CHAINS_AS_PATHS`, `DM_ARTIFACT_FINGERPRINTS`, `DM_TOOL_PROVENANCE`, and replaces evidence text with "[agent-written evidence: not used as proof]". Result: **`proven_paths = 0` by construction** in Community. Code: [`attack_paths_local.py`](https://github.com/ASCIT31/Dark-Moon/blob/10eaab0/mcp/api/attack_paths_local.py), [`feature_flags.py`](https://github.com/ASCIT31/Dark-Moon/blob/10eaab0/mcp/api/feature_flags.py).
