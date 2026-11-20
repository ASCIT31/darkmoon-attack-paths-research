# 01 — System overview

```mermaid
flowchart LR
  subgraph AG["LLM agents (OpenCode sessions)"]
    O["Orchestrator<br/>conf/agents/pentest.md"]
    SP["Specialist sub-agents<br/>conf/agents/*.md"]
  end
  subgraph MCP["MCP server"]
    EX["execute_command<br/>mcp/src/server.py"]
    SAN["output sanitised for the model<br/>server.py:_sanitize_for_model"]
    PU["dashboard_push_finding (enabled_by)<br/>mcp/src/server.py"]
    LED["run ledger: HMAC fingerprints<br/>tool_provenance (Pro only)"]
  end
  subgraph ST["Storage"]
    FJ["findings per campaign<br/>live_push._push_finding_unlocked"]
  end
  subgraph AN["Analysis (no model call)"]
    AP["attack_paths.build_paths"]
    EV["edge_verifier.qualify_edge"]
  end
  subgraph OUT["Outputs"]
    TUI["Terminal Explorer<br/>conf/plugins/darkmoon-explorer"]
    REP["Report annex<br/>report_annex / report_annex_local"]
    UI["Web dashboard (Pro only)"]
  end
  O --> EX
  SP --> EX
  EX --> SAN --> O
  EX -.-> LED
  O --> PU
  SP --> PU
  PU --> FJ
  PU -.-> LED
  FJ --> AP
  LED -.-> EV
  AP --> EV
  AP --> TUI
  AP --> REP
  AP --> UI
```

Dashed arrows exist only in Pro: Community has no run ledger (`DM_TOOL_PROVENANCE` is in `FORCED_OFF`, [`mcp/api/feature_flags.py`](https://github.com/ASCIT31/Dark-Moon/blob/10eaab0/mcp/api/feature_flags.py)), so `qualify_edge` finds no facts and every declared link stays `declared_by_agent`.
