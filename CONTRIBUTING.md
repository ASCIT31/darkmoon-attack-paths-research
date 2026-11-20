# Contributing

Thank you for your interest. This repository is research documentation; contributions that improve accuracy are the most valuable.

## What we welcome

- **Corrections** of facts, figures, labels or code references (please cite the commit or source).
- **Missing or mis-described literature**, with a verifiable identifier (DOI, arXiv id, standards URL).
- **Replication attempts** of the published reasoning on your own targets, reported with method and limits.
- **Diagram fixes** when the Mermaid diagrams drift from the code in [Dark-Moon](https://github.com/ASCIT31/Dark-Moon).

## Rules of evidence

1. Every new statement carries one label: `VERIFIED`, `OBSERVED`, `SIMULATED`, `HYPOTHESIS`, `SUPERSEDED` or `UNKNOWN` (defined in [docs/architecture §0](docs/architecture/README.md#0-conventions)).
2. Never compare finding counts across targets; never present a harness or proxy-simulation score as agent precision.
3. Do not claim superiority over other systems. Report n, intervals and limits.
4. Keep negative results.

## What not to submit

- Secrets, credentials, internal hostnames or IP addresses, customer data, or personal data.
- Content copied from paywalled papers beyond short quotations.
- Vulnerability reports about DarkMoon: use the security process of the [Dark-Moon repository](https://github.com/ASCIT31/Dark-Moon/security) instead.

## How

Open an issue first for anything substantial, then a pull request. By contributing you agree that your contribution is licensed under the repository licence ([LICENSING.md](LICENSING.md)).
