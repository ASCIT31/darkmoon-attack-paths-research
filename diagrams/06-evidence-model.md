# 06 — Evidence model

```mermaid
flowchart TB
  CMD["execute_command input/output (real values)"]
  LED["run ledger: produced / consumed HMAC fingerprints, outcome + basis (Pro only)"]
  PF["pushed finding: agent-written evidence (placeholders rehydrated), status"]
  FPS["full-value fingerprints shared by finding evidence and ledger, push order (Pro only)"]
  QE["exact value attribution + abstention reasons<br/>edge_verifier.qualify_edge"]
  NEC["negative control on the same operation key<br/>(necessity)"]
  R13["pattern rules on pushed evidence<br/>attack_paths_observed R1, R3"]
  CMD --> LED
  PF --> FPS
  LED --> FPS
  FPS --> QE
  LED --> QE
  QE --> NEC
  PF --> R13
```

Success needs `http_2xx` or `exit0_expected_output`; refusal needs `http_refused` or `refusal_signature`; `exit 0` alone is `unknown`. **R1 and R3 read the agent-written evidence, not an independent capture**; only `qualify_edge` reads the independent ledger. See [`../docs/provenance/`](../docs/provenance/README.md).
