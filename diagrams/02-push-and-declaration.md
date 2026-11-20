# 02 — Push and declaration

```mermaid
sequenceDiagram
  participant Ag as Agent (orchestrator or sub-agent)
  participant S as MCP server.py:dashboard_push_finding
  participant L as live_push._push_finding_unlocked
  participant D as declared_links (at analysis)
  Ag->>S: finding B + enabled_by=[ref to A]
  S->>S: _enabled_by_refs (at most 16 refs, 120 chars each)
  S->>L: store finding (refs kept as written)
  L->>L: re-push of same finding merges refs, dates late refs (enabled_by_at)
  L-->>Ag: vuln_id, enabled_by_warnings (self / ambiguous / unknown), recent exploited hint (<= 200 chars)
  Note over D: later, when paths are requested
  D->>D: resolve_one(ref) -> finding id | self | unknown | ambiguous
  D->>D: validate -> rejected_declaration | invalidated | declared_by_agent
```

References accepted: `vuln_id`, `dedup_key`, `short_title` or exact endpoint. ATT&CK tactic order is checked as a **warning only**. Code: [`mcp/src/server.py`](https://github.com/ASCIT31/Dark-Moon/blob/10eaab0/mcp/src/server.py), [`mcp/api/live_push.py`](https://github.com/ASCIT31/Dark-Moon/blob/10eaab0/mcp/api/live_push.py), [`mcp/api/declared_links.py`](https://github.com/ASCIT31/Dark-Moon/blob/10eaab0/mcp/api/declared_links.py).
