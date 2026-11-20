# Diagrams

Mermaid diagrams of the attack-path subsystem, matching the public code at [Dark-Moon `10eaab0`](https://github.com/ASCIT31/Dark-Moon/tree/10eaab0) (2026-11-20). Pro-only components are marked **(Pro only)** and refer to DarkMoon Pro, internal `d6bfc0d9`. GitHub renders Mermaid natively.

| File | Shows |
|---|---|
| [01-system-overview.md](01-system-overview.md) | Agents, MCP server, storage, analysis, outputs |
| [02-push-and-declaration.md](02-push-and-declaration.md) | How a finding and its `enabled_by` declaration are pushed and stored |
| [03-link-states.md](03-link-states.md) | Link state machine (rejected / invalidated / declared / observed flow / necessity) |
| [04-path-analysis.md](04-path-analysis.md) | From stored findings to paths, levels, ranking and "Fix first" |
| [05-community-vs-pro.md](05-community-vs-pro.md) | What differs between the two editions |
| [06-evidence-model.md](06-evidence-model.md) | What the server can know, and from where |
| [07-approach-history.md](07-approach-history.md) | Timeline of approaches tried, kept and abandoned |

Function names are given as `module:function`; every name was checked to exist at the commits above.
