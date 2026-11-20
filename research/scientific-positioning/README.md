# Scientific positioning

Status: public research documentation, version 1.0.0, 2026-11-20. Full argument and citations: [literature review](../literature-review/README.md).

## 1. The problem DarkMoon addresses

| Problem | Typical output | Ground truth | DarkMoon |
|---|---|---|---|
| (P1) Attack-graph **generation** (MulVAL, TVA) | all potential paths to a goal | correctness relative to a model | not addressed: no network model, no exhaustive enumeration |
| (P2) Attack **scenario reconstruction** from provenance (SLEUTH, HOLMES) | causal subgraph of an incident | red-team ground truth | borrowed discipline (value lineage), at tool layer only |
| (P3) **Autonomous exploitation** by LLM agents | flags, root, findings | flag or milestone | the host system |
| (P4) **Qualification of links between agent findings** | edges labelled declared / corroborated / abstained | hand-adjudicated oracle per campaign | **this research** |
| (P5) Vulnerability **prioritisation** (CVSS, EPSS, SSVC, KEV) | score or decision | exploitation in the wild | "Fix first" is downstream and conditional on edge quality |

**Research question.** When LLM agents state that one finding enabled another, how can a system decide, without trusting the agent and without extra model calls, which of those statements are supported by what the system itself recorded, and how often are they right?

## 2. Position (current evidence)

- Architecture D is a **minimal neuro-symbolic split**: the LLM proposes edges at almost no token cost; one deterministic, auditable rule (exact value lineage on server records) disposes; everything else is labelled a hypothesis.
- The graph sits **behind** the agent as an audit layer, not in front of it as planning memory (contrast: task graphs and fact–intent DAGs in recent LLM pentest agents).
- Demonstrated: non-promotion on synthetic adversarial scenarios. Not demonstrated: any real-world qualified link; any real N4 chain.

## 3. Candidate contributions (conditional, not demonstrated)

1. **An explicit epistemic status for agent-declared attack-path edges** with a non-promotion guarantee. *Needed:* an independent harness; adversarial agents that try to forge lineage.
2. **A measured error profile of LLM-declared inter-finding dependencies**, dominated by common-cause ("same root cause") confusions from sub-agents. *Needed:* more runs and models, blinded multi-annotator oracles, released traces.
3. **Negative results**, rarely published: text rules, counterfactual replay, own-command attribution, prompt rewording all failed preregistered criteria.
4. **The privacy–provenance tension**: tokenising secrets before the model removes the values lineage verification needs.

## 4. Open research directions

1. A benchmark of executed attack-chain edges with sealed edge-level truth, including decoy common-cause pairs, and released traces.
2. Telling common cause from direct enablement at token level, e.g. by making the root cause a first-class node agents can link to (untested).
3. Privacy-preserving provenance: lineage checks when values must not reach the model (keyed-hash commitments, in-vault verification).
4. Capture completeness at the tool layer (shell substitutions, cross-call variables).
5. Calibrated agent self-reports and abstention on `enabled_by`.
6. Structured priors (ATT&CK, MulVAL) kept strictly separate from the "corroborated" status.
7. Model-matched plain-agent baselines before crediting any architecture.
8. Trace-verified outcomes for findings, not only for links.
