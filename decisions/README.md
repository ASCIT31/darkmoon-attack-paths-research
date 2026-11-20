# Decisions

Architecture decision records (ADR style) reconstructed from an internal decision-traceability file. Each record follows the chain **problem → hypothesis → proposal → implementation → tests → results → decision**. Facts are unchanged; internal file paths and private notes were removed. Unlinked commit hashes are **DarkMoon Pro, internal** (private repository); linked hashes are in the public [Dark-Moon](https://github.com/ASCIT31/Dark-Moon) repository.

A-series: attack paths. B: privacy. C: token cost. E: agent safety. F: visualisation tooling. Gaps in the series are decisions not related to this research topic.

| ADR | Date | Decision | Status |
|---|---|---|---|
| [ADR-A01](ADR-A01-observed-path-engine.md) | 2026-10-29 | Observed-path engine (paths v1) | Accepted, later refined (R2 demoted, see A09) |
| [ADR-A02](ADR-A02-d27-causal-programme.md) | 2026-11-08 | Darkmoon 2027 causal-path research programme | Rejected (NO-GO for flag promotion); security fixes kept |
| [ADR-A03](ADR-A03-agent-evidence-tagging.md) | 2026-11-10 | Agent-native evidence tagging | Code present, flag OFF |
| [ADR-A04](ADR-A04-r4-own-command.md) | 2026-11-16 | R4 "own command" proof chain | Rejected twice; branch frozen, not merged |
| [ADR-A05](ADR-A05-method-watch.md) | 2026-11-16 | Method watch: capability graph vs rules vs server observation | Accepted (R4 observation + agent-declared links) |
| [ADR-A06](ADR-A06-enabled-by.md) | 2026-11-16 | `enabled_by`: the agent declares the finding it used | Accepted (core of architecture D) |
| [ADR-A07](ADR-A07-observation-only-proof.md) | 2026-11-17 | Corroboration by endpoint observation ("observation only") | SUPERSEDED by A08/A09 |
| [ADR-A08](ADR-A08-architecture-c.md) | 2026-11-17 | Architecture C (paths-v2): execution ids | Engine technical GO; agent surface rejected |
| [ADR-A09](ADR-A09-architecture-d.md) | 2026-11-17 | Architecture D: declared, then qualified by exact value attribution | Accepted (current) |
| [ADR-A10](ADR-A10-release-arch-d.md) | 2026-11-18 | Release with architecture D | Accepted |
| [ADR-A11](ADR-A11-community-port.md) | 2026-11-19 | Port of attack paths to DarkMoon Community | Accepted |
| [ADR-B01](ADR-B01-privacy-gateway.md) | 2026-07-08 | Privacy Gateway and tokenisation | Accepted; hardened 2026-11-18 |
| [ADR-C01](ADR-C01-token-cost.md) | 2026-09-24 | Token-cost reduction targets | Rejected (targets not reachable) |
| [ADR-E01](ADR-E01-agent-target-safety.md) | 2026-11-18 | Agent safety on targets | Accepted |
| [ADR-F01](ADR-F01-terminal-explorer.md) | 2026-11-18 | Terminal Explorer (read-only TUI view of findings and paths) | Accepted |
