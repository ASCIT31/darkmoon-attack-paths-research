# DarkMoon attack paths: scientific positioning and literature review

Status: public research documentation, version 1.0.0, 2026-11-20. Language: English. BibTeX: [`references.bib`](../../references/references.bib). Citation keys are given in brackets, e.g. [ou2005mulval].

## 0. Scope, method and rules of evidence

**Question.** Where does DarkMoon's attack-path engine ("architecture D") sit in the research literature? What does it share with earlier work, how does it differ, what could it contribute, and what is still open?

**How each reference was verified (2026-11-20).**
- arXiv identifiers: queried the arXiv API (`export.arxiv.org`) for title, authors, first-version date and author comment. Venues taken only from an author comment are labelled "venue per arXiv comment".
- DOIs: resolved through the Crossref API (title, authors, container, year, pages).
- Standards and technical reports (W3C PROV-DM, CVSS v4.0, SSVC, ATT&CK, STIX, Attack Flow, KEV): fetched the official page and checked for HTTP 200. The CVSS v4.0 document was read for its version history.
- Evaluation protocols: taken from the abstract for every paper. For PentestGPT, AutoAttacker, VulnBot and PENTESTLLMAGENT, the evaluation section of the full text was also read; this is noted where it applies. Any limitation the authors did not state is marked **(our reading)**.
- Anything that could not be verified is excluded from the body and listed in §9 ("Not verified").
- Two earlier internal literature registers (R&D programme register L01–L45 / C01–C36, and the first method-watch proposal) were reused as lead lists only; every entry used here was checked again.

**Rules.** This document makes no claim that DarkMoon is better than any cited system. Figures quoted from papers belong to their authors, on their own benchmarks. DarkMoon figures come from the experiment registry ([`experiments/`](../../experiments/README.md)) (EXP-nnn). They are small-n, collected on different targets, and **not comparable** with each other or with published numbers.

---

## 1. What DarkMoon actually does and has measured (factual basis)

Source: the internal forensic timeline §7 and the EXP records cited here.

**Mechanism (architecture D, released 2026-11-18, Pro internal PR #46; ported to Community through [Dark-Moon PR #59](https://github.com/ASCIT31/Dark-Moon/pull/59)).**
1. LLM agents (an orchestrator plus specialist sub-agents, built on OpenCode and MCP tools) push findings. A finding can carry one optional field, `enabled_by`, which names the earlier finding(s) the agent says made it possible. This field replaced four structured "chain data" fields. Those had been filled 0/51 times [internal note], and the d27 audit found 0/340 findings with causal fields ([EXP-010](../../experiments/successful/EXP-010.md)).
2. The server treats an agent-declared link as a **hypothesis**. It is labelled `declared_by_agent` and is never counted or worded as "observed" or "proven". A link is promoted only by **exact value attribution**: a concrete value produced in the evidence of finding A (credential, token, path, etc.) must reappear in the command or evidence that demonstrated finding B. This is checked against the server's own run ledger (HMAC fingerprints of values produced and consumed by each tool execution; Pro edition only). ATT&CK tactic order is checked but produces context warnings only, never a verdict.
3. When evidence is missing or ambiguous (masked credentials, no session identity), the server **abstains**. The link stays declared.

**What has been measured.**

| EXP | Setting | Result relevant here |
|---|---|---|
| [EXP-047](../../experiments/adversarial/EXP-047.md), [EXP-049](../../experiments/adversarial/EXP-049.md) | Deterministic harness, 89–100 synthetic scenarios, 102–117 edges, plus mutation testing | 0 false promotions; recall of true edges 0.829–0.867; mutation kill rate up to 95.2% (0 permissive mutants). **Synthetic, not agent behaviour.** |
| [EXP-042](../../experiments/successful/EXP-042.md) (c5) | Juice Shop, real campaign, n=1 | `enabled_by` on 15/39 findings; oracle strict precision 0.6, but only 5 of 16 links were decidable; recall 2/6; 0 proven |
| [EXP-043](../../experiments/inconclusive/EXP-043.md) (c6) | Multi-protocol lab, real, n=1, 11 findings | Strict precision 0.556 (lenient 0.889); recall 5/6 |
| **[EXP-050](../../experiments/failed/EXP-050.md) (c7)** | Multi-protocol lab, real, n=1, 28 findings | Agent-declared link strict precision **31.6% [15–54]** (6/19); recall 66.7%; **server-qualified 0/19**; 12 of 13 false links were "same root cause" |
| **[EXP-056](../../experiments/successful/EXP-056.md)** | lab-multi (redis → PG superuser → RCE → SSH key), real, n=1 | 29 findings, 19 `enabled_by`, **0 promoted**; precision about **37% (approximate)**; about 12 same-cause links (approximate) |
| [EXP-051](../../experiments/failed/EXP-051.md), [EXP-053](../../experiments/failed/EXP-053.md) | Replays using Claude Code sub-agents as a proxy model | Prompt rewording: pass-to-pass variance of ±20 points was larger than the differences between variants → no change made |
| [EXP-015](../../experiments/failed/EXP-015.md), 017, 019–024, 027–031 | d27 programme (dmlab, crAPI, Juice Shop) | Every causal-verification approach failed its preregistered criterion; **no multi-finding (N4) chain proven on real data** |

**Dominant error mode.** The agent links two findings that are both consequences of one underlying cause (for example two exposures of the same misconfigured service). It reads this as "A enabled B". In causal-inference terms this is the classic confusion between a common cause and a direct causal edge (§3.4).

**Status.** Today the engine is a **conservative labelling system**. Its demonstrated property is the absence of false promotion on a synthetic harness, plus abstention on real data. It has not shown that it can establish multi-step chains on real campaigns.

---

## 2. Framing: which research problem is this?

Several research problems look alike on the surface. Keeping them apart matters for honest comparison.

| Problem | Typical input | Typical output | Ground truth |
|---|---|---|---|
| (P1) Attack-graph **generation** | Network model: hosts, reachability, vulnerability database, exploit pre/post-conditions | All potential paths to a goal | Correctness relative to the model |
| (P2) Attack **scenario reconstruction** from provenance | System-level audit logs (syscalls, flows) | The causal subgraph of an incident | Red-team ground truth |
| (P3) **Autonomous exploitation** by LLM agents | A target (CTF, VM, web app) | Flags, root, findings | Flag captured or milestone reached |
| (P4) **Link qualification between findings produced by an agent** (DarkMoon) | Agent findings, agent-declared dependencies, server-side tool observations | Typed edges labelled declared / corroborated / abstained | Hand-adjudicated oracle per campaign |
| (P5) Vulnerability **prioritisation** | Per-vulnerability attributes, threat intelligence | Score or decision category | Exploitation in the wild (EPSS), stakeholder decisions (SSVC) |

DarkMoon's architecture D addresses **P4**. It borrows representations from P1 (pre/post-conditions, AND/OR paths), evidence discipline from P2 (lineage of concrete values), and runs inside a P3 system. It does not currently solve P1: there is no network model and no exhaustive enumeration. It does not do P2 at system-call level either; it observes at the tool and command layer only.

---

## 3. Literature by theme

Each entry gives the claim, the evaluation protocol, its limits, and its relation to DarkMoon.

### 3.1 Attack graphs and attack trees (classic structured representation)

- **Phillips & Swiler 1998** [phillips1998graph]. Early graph-based network vulnerability analysis: nodes are attack states, edges are actions with costs. *Evaluation:* conceptual, with illustrative examples. *Limits:* manual modelling; state explosion.
- **Sheyner, Haines, Jha, Lippmann, Wing 2002** [sheyner2002automated]. Attack graphs generated automatically by model checking, plus minimal-cut style analysis. *Evaluation:* small example networks. *Limits:* graph size grows exponentially in the state space, as the later scalability literature points out.
- **Ammann, Wijesekera, Kaushik 2002** [ammann2002scalable]. Monotonicity assumption (an attacker never loses privileges) gives polynomial analysis. *Limits:* monotonicity excludes some real dynamics.
- **Noel, Jajodia, O'Berry, Jacobs 2003; Jajodia, Noel, O'Berry 2005; Wang, Noel, Jajodia 2006** [noel2003efficient; jajodia2005topological; wang2006minimum]. Exploit-dependency graphs (Topological Vulnerability Analysis) and minimum-cost hardening, i.e. choosing the cheapest set of initial conditions to remove so that the goal becomes unreachable. *Evaluation:* example networks and algorithmic analysis. *Limits:* need a complete, correct exploit pre/post-condition model.
- **Ou, Govindavajhala, Appel 2005 (MulVAL)** [ou2005mulval]; **Ou, Boyer, McQueen 2006** [ou2006scalable]. Datalog rules over scanner facts and network configuration. Logical attack graphs, polynomial in network size. *Evaluation:* real and synthetic networks, with runtime scaling. *Limits:* rule coverage limits what can be derived; output is *potential* paths, not executed ones. Successors are surveyed in [tayouri2022mulvalsurvey] and the approach is applied to supply chains in [soeiro2025supplychain].
- **Poolsappasit, Dewri, Ray 2012** [poolsappasit2012dynamic]. Bayesian attack graphs for dynamic risk. *Limits:* probabilities need calibration data that rarely exists.
- **Surveys:** Kordy et al. 2014 [kordy2014dag] (DAG-based attack/defence formalisms); Lallie et al. 2020 [lallie2020review] (visual syntax, with no consensus notation found).
- **CyGraph** [noel2016cygraph] (MITRE). Property-graph knowledge store (Neo4j) for correlating alerts to vulnerability paths. Bibliographic data partially verified (§9).

**Relation to DarkMoon.** Architecture D's edge "A `enabled_by` B" corresponds to an exploit-dependency edge: B's post-condition satisfies A's pre-condition. The basic difference is epistemic. Classic attack graphs **derive** edges from a model and do not claim the edges were executed. DarkMoon tries to **certify** edges that an agent says were *executed*, from execution evidence. MulVAL-style rules could be used as a *plausibility prior*, such as the ATT&CK tactic-order check DarkMoon keeps, as context warnings only. They cannot establish execution.

### 3.2 Provenance

- **W3C PROV-DM 2013** [w3c2013provdm]. Domain-agnostic model of entities, activities and agents, with relations such as *used*, *wasGeneratedBy*, *wasDerivedFrom* and *wasInformedBy*. *Limits:* a data model, not an inference method; it says nothing about how to obtain trustworthy records.
- **King & Chen 2003 (Backtracking intrusions)** [king2003backtracking]. OS-level dependency graphs, traced backward from a detection point. *Limits:* dependency explosion, which motivates the later work below.
- **SLEUTH 2017** [hossain2017sleuth]; **HOLMES 2019** [milajerdi2019holmes]. Real-time scenario reconstruction from COTS audit data; HOLMES maps flows to kill-chain stages. *Evaluation:* DARPA red-team engagements. SLEUTH reports reducing 38.5M events to a 130-event scenario graph (per the paper's own summary). *Limits:* trust in the kernel and audit layer; tag-propagation heuristics.
- **NoDoze 2019** [hassan2019nodoze]; **Tactical provenance 2020** [hassan2020tactical]; **UNICORN 2020** [han2020unicorn]. Triage and anomaly detection on provenance graphs. *Evaluation:* enterprise and DARPA datasets.
- **Surveys:** Zipperle et al. 2023 [zipperle2023provenance]; Inam et al. SoK 2023 [inam2023sok]. The SoK examines whether audit-based provenance is complete and sound enough for causal analysis. *Limits noted in this literature:* dependency explosion, false dependencies from long-lived processes, and the cost of capture.

**Relation to DarkMoon.** "Exact value attribution" is a narrow form of data provenance: it asks whether value *v* used by command *c_B* was *derived from* the output of command *c_A*. In PROV terms: `c_B used v`, `v wasGeneratedBy c_A`. The provenance literature gives three lessons that DarkMoon reproduced empirically:
(i) coarse dependency (shared process or session) produces false dependencies, which is what the `feat/proof-chain` NO-GO showed with "skeleton collisions" ([EXP-038](../../experiments/failed/EXP-038.md));
(ii) **sound** lineage requires capture at the point of use, so DarkMoon's R4 tap is blind to `$(...)` substitutions and cross-call shell variables ([EXP-040](../../experiments/failed/EXP-040.md) critique);
(iii) masking secrets for privacy, which DarkMoon does by tokenisation, removes the very values provenance needs. [EXP-047](../../experiments/adversarial/EXP-047.md) records "real links unqualifiable (masked credentials)".
DarkMoon observes at the **tool-call layer**, not the OS layer, so it is far cheaper to capture and far less complete.

### 3.3 Knowledge graphs for cybersecurity

- **MITRE ATT&CK** [strom2018attack]. A curated knowledge base of adversary tactics and techniques built from public reporting. It is not a causal model, and it says it is not a complete enumeration.
- **STIX 2.1** [oasis2021stix] and **Attack Flow** [ctid2025attackflow]. Exchange formats for sequences of adversary actions.
- **SEPSES KG** [kiesling2019sepses]; **BRON** [hemberg2020bron]. Link CVE/CWE/CAPEC/ATT&CK (and CPE). BRON's authors report uneven link coverage across sources.
- **AttacKG** [li2022attackg] and **AttacKG+** [zhang2024attackgplus]. Technique knowledge graphs extracted from CTI reports; the LLM version reports higher F1 on technique identification over a small (15-report) evaluation.
- **Hou et al. 2026** [hou2026reachablechains]. LLM-assisted extraction of pre/post-condition "attack units" from 20 CTI reports (334 annotated steps), compiled to Datalog. Goal reached in 19/20 reports.

**Relation to DarkMoon.** These graphs encode **type-level** knowledge ("technique X often precedes Y"). DarkMoon needs **token-level** claims ("this finding enabled that finding in this campaign"). Type-level priors can rule out implausible links, as DarkMoon's ATT&CK tactic-order check could (it is kept as warnings only, because it accepted 10 of 10 false chains in an internal review). They cannot tell a direct edge from a common cause in one campaign, and that is DarkMoon's dominant error.

### 3.4 Causal reasoning and the limits of causal discovery

- **Spirtes, Glymour, Scheines** [spirtes2000causation]; **Pearl** [pearl2009causality]. Causal graphs, d-separation, interventions. Key points that apply here:
  1. Observational co-occurrence cannot separate *A → B* from *A ← C → B* without further assumptions (causal Markov, faithfulness, no hidden confounders) or interventions.
  2. Hidden common causes are the normal case in a pentest. The underlying misconfiguration is often not a finding itself.
  3. Token-level ("actual") causation in a single run is harder than type-level causal structure.
- **DoWhy** [sharma2020dowhy]: tooling for effect estimation under an assumed graph. Effect estimation is not graph discovery from one trace.
- **Schölkopf et al. 2021** [schoelkopf2021causalrep]: causal representation learning; why i.i.d. learning does not yield causal variables.
- **LLMs and causality.** Kıcıman et al. 2023 [kiciman2023causalllm] report strong GPT-3.5/4 results on pairwise causal discovery (97%), counterfactual reasoning (92%) and event causality, argue this is not explained by memorisation alone, but note "unpredictable failure modes" and that LLMs reason over text metadata and "ignore the actual data". Jin et al. (Corr2Cause, ICLR 2024 per arXiv comment) [jin2024corr2cause] report that LLMs perform close to chance at inferring causation from correlation statements, and that fine-tuned gains do not generalise out of distribution.

**Relation to DarkMoon.** The ~32% / ~37% precision and the dominance of "same root cause" errors match what this literature predicts when an agent is asked to state edges from co-occurrence and narrative. The agent sees two findings that share a cause and reports a direct edge. Architecture D's response is effectively **interventional or mechanistic evidence**: a value must flow from A's output into B's input. That is a design choice that follows from causal theory. It is not a new causal-discovery method. The counterfactual-replay attempts (CF1–CF4, [EXP-019](../../experiments/failed/EXP-019.md), [EXP-031](../../experiments/failed/EXP-031.md)) were genuine interventions. They were accurate where they applied but had low recall (e.g. wire flows 20/20 correct, recall 4/29) and occasionally produced false proofs (2 false proven links in [EXP-019](../../experiments/failed/EXP-019.md)).

### 3.5 Expert systems and neuro-symbolic approaches

- **MYCIN** [buchanan1984mycin]. Rule-based expert system with certainty factors and an explanation facility ("why / how"). The classic lessons are the knowledge-acquisition bottleneck and brittleness outside the rule base.
- **Neuro-symbolic surveys:** Garcez & Lamb [garcez2020neurosymbolic]; Kautz [kautz2022third] (taxonomy of neural/symbolic couplings).
- **Neuro-symbolic attack-path work with LLMs:**
  - **Stevanovic & Wachter 2026** [stevanovic2026neurosymbolic]. Trivy, Semgrep and Nmap output is parsed into MulVAL predicates; an LLM helps write Datalog rules. *Evaluation:* 54 web CTF tasks from Cybench. Mean ground-truth vulnerability coverage 53.7%; **mean noise-path rate 83.9%**. The downstream agent is explicitly *not* evaluated.
  - **Prapty et al. 2024** [prapty2024ragattackgraph]. RAG-assisted LLM chaining of CVEs by pre/post-conditions. Illustrative evaluation.
  - **ATAG 2025** [gandhi2025atag]. MulVAL extended to AI-agent applications. Two case studies.
  - **ChainGraph (Barbosa 2026)** [barbosa2026chaingraph]. A Neo4j property graph wired to off-the-shelf coding agents through MCP, with pre/post-condition edges and "tamper-evident evidence receipts". *Evaluation:* one synthetic AD engagement modelled on GOAD. The author states: "illustrative single-run measurements, not a benchmark".
  - **Intentest (Wang et al. 2026)** [wang2026intentest]. A fact–intent DAG externalises long-horizon state. Verified facts are immutable nodes. Reports 88.2% success on its own CTF benchmark.

**Relation to DarkMoon.** DarkMoon's own history includes an earlier symbolic-AI prototype (2025-11, abandoned 2026-01). The d27 programme also tried an expert engine and rules G1–G5, all NO-GO ([EXP-040](../../experiments/failed/EXP-040.md): rules on finding text gave 12–15% held-out precision on dmlab). Architecture D is a **minimal neuro-symbolic split**: the LLM proposes edges and a deterministic verifier with a single, auditable rule (exact value lineage) disposes of them. Compared with ChainGraph and Intentest, which put the graph *in front of* the agent as reasoning internal note, DarkMoon puts the graph *behind* the agent as an audit layer. None of these systems, DarkMoon included, has published a controlled measure of edge precision on real executed chains.

### 3.6 Multi-agent systems

- **Classic MAS** [wooldridge2009mas]: agent communication, coordination, and the gap between what an agent asserts and what it does.
- **LLM agent frameworks:** ReAct [yao2023react]; AutoGen [wu2023autogen]; MetaGPT [hong2023metagpt].
- **Failure analysis:** MAST (Cemri et al.) [cemri2025mas]: 14 failure modes from 1,600+ annotated traces, including *task verification* failures. Who&When [zhang2025whowhen]: automated failure attribution; best method 53.5% agent-level and 14.2% step-level accuracy.

**Relation to DarkMoon.** An `enabled_by` declaration is an inter-agent *assertion*. When findings come from different sub-agents, it is an assertion about another agent's work. The MAS literature treats such assertions as claims to verify, not facts, which is the position architecture D takes. The low step-level attribution accuracy in Who&When is a warning: asking an LLM to judge which step caused which outcome is itself unreliable.

### 3.7 LLM agents for automated penetration testing

Names were checked. The system sometimes called "PentestLLMAgent" is **PENTESTLLMAGENT** (Sheng et al., IJCAI-26). **PentestAgent** (Shen et al., AsiaCCS 2025 per arXiv comment) is a different system.

| System | Ref | Evaluation protocol (verified) | Limits (stated or **our reading**) | Structured state? |
|---|---|---|---|---|
| PentestGPT | [deng2024pentestgpt], USENIX Sec 2024 | 13 HackTheBox/VulnHub targets, 182 sub-tasks; sub-task completion; plus active HTB machines | Relies on human participation and retired HTB machines are paywalled, per the follow-up critique [isozaki2024benchmark] | Three self-interacting modules to limit context loss |
| AutoAttacker | [xu2024autoattacker] | 14 ATT&CK-derived post-breach tasks in an isolated Hyper-V lab; 3 runs per task; ≤30 rounds; GPT-3.5/4, Llama2 | Deliberately insecure victims; small ATT&CK subset; 3 trials (stated) | LLM-guided modular pipeline (see paper) |
| LLMs as Hackers | [happe2026linuxprivesc] | Linux privilege-escalation benchmark VMs | Single-host privilege escalation | None (baseline harness) |
| Enterprise AD (Happe & Cito) | [happe2026enterprise], TOSEM 2026 | GOAD testbed, 5 LLMs, assumed breach | One testbed (**our reading**) | Planner–executor |
| Cochise | [happe2026cochise], ASE 2026 | GOAD; reference harness; releases JSON trajectory corpus | Not intended as state of the art (stated) | Planner keeps long-term state |
| AutoPT | [wu2024autopt] | End-to-end web benchmark; vs ReAct; completion 22% → 41% (GPT-4o mini) | Command accuracy, getting stuck (stated) | Finite-state machine |
| PentestAgent | [shen2025pentestagent] | Own benchmark; task completion and efficiency | Benchmark details not reviewed here | RAG + multi-agent |
| HackSynth | [muzsai2024hacksynth] | 200 PicoCTF / OverTheWire challenges; temperature/top-p studies | CTF-only; safety discussed | Planner + summariser |
| VulnBot | [kong2025vulnbot] | AutoPenBench (33 tasks) and 6 of 13 AI-Pentest-Benchmark VulnHub machines; 5 runs; best-of-5 for real machines | Automatic mode only; no images; exploitation is the main failure point (stated); best-of-5 is optimistic (**our reading**) | Penetration Task Graph |
| CAI | [mayoral2025cai] | CTF competitions, HTB ranking, bug-bounty exercises | Strong comparative claims ("3,600× faster") on heterogeneous, partly non-reproducible settings (**our reading**) | Modular agents, human-in-the-loop |
| xOffense | [luong2025xoffense] | AutoPenBench + AI-Pentest-Benchmark; 79.17% sub-task completion with fine-tuned Qwen3-32B | Fine-tuning data provenance and benchmark contamination not addressed in abstract (**our reading**) | Phase agents + orchestrator |
| PENTESTLLMAGENT | [sheng2026pentestllmagent], IJCAI-26 | AutoPentest-Bench (13 VulnHub, 93 sub-tasks), AutoPT web CVEs (20), hackingBuddyGPT privesc (13); 10 LLMs; ablations | Baseline numbers taken from original papers under different protocols; ~500-entry command knowledge base tuned to the services tested; best model picked on the test set (**our reading** of the full text) | Task Dependency Graph |
| D-CIPHER | [udeshi2025dcipher] | NYU CTF Bench (22.0%), Cybench (22.5%), HackTheBox (44.0%); NYU CTFs mapped to ATT&CK | CTF only | Planner + heterogeneous executors + auto-prompter |
| EnIGMA | [abramovich2025enigma], ICML 2025 per comment | CTF benchmarks with interactive tools | CTF only | Tool interfaces |
| Excalibur | [deng2026excalibur] | Analyses 28 systems; 5 implementations on 3 benchmarks; up to 91% on CTF | Separates "Type A" (tooling) from "Type B" (planning/state) failures | Evidence-Guided Attack Tree Search |
| Intentest | [wang2026intentest] | Own CTF set, >10 vulnerability types | Own benchmark | Fact–intent DAG |
| Amine & Saab | [amine2026planner], IJIS 2026 | CTF-based | Traces not published (prior method watch) | Planner + specialists |
| Fang/Kang group | [fang2024hackwebsites; fang2024oneday; zhu2024zeroday] | Sandboxed websites; 15 one-day CVEs (GPT-4: 87% with CVE description, 7% without); zero-day set | Small task sets; strong dependence on the CVE description (stated) | Teams of agents (zero-day paper) |

**Cross-cutting observation.** Every system above *maintains* structured state: a task tree, task graph, FSM, DAG or attack tree. In all of them that structure is **planning state**, used to decide what to do next. None of the verified papers reports the **precision of executed causal links between findings** as a metric. Success is measured by flags, root, sub-task milestones or CVE reproduction. DarkMoon's question, whether the agent's claim "A enabled B" is true, is mostly unmeasured in this literature. The nearest work is trace-level auditing (§3.8).

**Studies that question claims.** The SoK by Peng et al. [peng2026hallucinators] ran 13 open-source frameworks plus 2 baselines on one unified benchmark (more than 10B tokens, 1,500+ logs manually reviewed). Bouchari et al. [bouchari2026secondlook] report that a general-purpose coding agent solved 19/30 web CTFs, comparable to purpose-built architectures. Dhakal et al. [dhakal2026baselines] used the 104-task XBOW benchmark and found that plain coding CLIs with matched models solve a large share. They recommend model-matched plain baselines before crediting architecture. These results apply to DarkMoon too: any gain claimed for its agent architecture would need a model-matched plain-agent baseline.

### 3.8 Agent trajectory analysis

- **AgentTrails — verified, it exists.** Wu, Castelo, Liu, Silva, Freire, arXiv 2607.18816 (2026-07-21). A 4-page short paper at the DASHSys 2026 workshop co-located with VLDB, per the arXiv comment [wu2026agenttrails]. It converts agent trajectories into provenance graphs (tool calls = activities, inputs/outputs = artefacts), aligns several runs with a "joined quotient graph", and supports pattern extraction. *Evaluation:* a demonstration on real-world agent trajectories. Secondary summaries name scientific and biomedical agent tasks; we did not confirm this in the full text. No quantitative accuracy of the inferred dependencies was found in the abstract. *Limits:* short workshop paper; not security-specific. Secondary summaries say dependencies are inferred from evidence such as shared tokens; this is unconfirmed in the full text.
- **CTF-ABACUS (Milner et al. 2026)** [milner2026ctfabacus]. Trace-level provenance for offensive-security evaluation: 1,435 CTF attempts, 6 models, 240 challenges. It reports that **only 62–87% of recovered flags are trace-verified exploits**. The rest came through shortcuts (direct exposure, memorised recall, lookup, guessing, unsupported claims).
- **TRAIL** [deshpande2025trail]: 148 annotated traces; the best long-context model scored 11% at trace debugging. **Who&When** and **MAST**: see §3.6.
- **Cochise** [happe2026cochise] releases replay and analysis tools plus a corpus of GOAD trajectories.

**Relation to DarkMoon.** This is DarkMoon's closest scientific neighbourhood. AgentTrails and DarkMoon both turn agent tool calls into dependency graphs. AgentTrails aims at sensemaking and reuse, with an LLM refiner proposing edges; DarkMoon aims at **certification**, with the LLM proposing and a deterministic rule disposing. CTF-ABACUS is the closest *evaluation* analogue: it audits agent claims against the trace. Its finding that a material share of "successes" are not supported by the trace matches DarkMoon's experience that agent declarations need a verifier.

### 3.9 Evaluation of autonomous agents

- **CTF benchmarks:** Cybench (40 tasks, sub-task guidance, 8 models; ICLR 2025 per comment) [zhang2025cybench]; NYU CTF Bench [shao2024nyuctf]; InterCode [yang2023intercode].
- **Pentest-specific:** AutoPenBench (33 tasks, generic and specific milestones; 21% autonomous vs 64% assisted success) [gioacchini2024autopenbench]; Isozaki et al. (VulnHub-only, reproducible) [isozaki2024benchmark].
- **Real-vulnerability benchmarks:** CVE-Bench [zhu2025cvebench], CyberGym [wang2025cybergym], BountyBench [zhang2025bountybench].
- **Simulation gyms:** CybORG [standen2021cyborg].
- **Methodological critiques:** "AI Agents That Matter" [kapoor2024agentsmatter] (cost-controlled comparison, reproducibility, overfitting to benchmarks); benchmark contamination survey [xu2024contamination]; [bouchari2026secondlook]; [dhakal2026baselines]; [milner2026ctfabacus].

**What none of these benchmarks provides** for DarkMoon's question is a ground truth of **inter-finding dependency edges on executed chains**. Milestone-based scoring (AutoPenBench, Cybench sub-tasks) is the nearest. DarkMoon's oracles are hand-adjudicated per campaign: n=1 per setting, a different oracle per campaign, intervals as wide as [15–54]. They are not a benchmark.

### 3.10 Explainable AI and faithfulness

- Definitions and evaluation: Doshi-Velez & Kim [doshivelez2017rigorous]; Jacovi & Goldberg (faithfulness vs plausibility) [jacovi2020faithfully].
- Post-hoc attribution: LIME [ribeiro2016lime], SHAP [lundberg2017shap].
- High-stakes stance: Rudin [rudin2019stop] (prefer interpretable models to post-hoc explanation).
- LLM self-explanations: Turpin et al. [turpin2023unfaithful] and Lanham et al. [lanham2023faithfulness] show chain-of-thought explanations can be **unfaithful** to the actual cause of the model's output.

**Relation to DarkMoon.** An `enabled_by` field is a model-generated explanation of how the attack proceeded. The faithfulness literature predicts that such explanations can be plausible yet wrong, which matches the ~32–37% precision. Architecture D's split is in Rudin's spirit: the *displayed* status ("corroborated") comes from a transparent rule over evidence, not from the model's narrative. The narrative is shown but labelled `declared_by_agent`. The cost is recall: today, 0 real links are qualified.

### 3.11 Vulnerability management and prioritisation

- **CVSS v4.0** [first2023cvss4]: Base, Threat, Environmental and Supplemental groups. The specification says Base metrics should be enriched with Threat and Environmental values for "a more comprehensive input to risk assessment". Critiques: Spring et al. [spring2021cvss]; Allodi & Massacci (case-control study comparing CVSS severity with actual exploitation) [allodi2014comparing].
- **EPSS** [jacobs2021epss; jacobs2023epssv3]: data-driven probability of exploitation in the wild, per CVE, trained on observed exploitation data.
- **SSVC** [spring2019ssvc]: stakeholder-specific decision trees. The authors present them as hypotheses that need empirical testing.
- **CISA KEV** [cisa_kev]: an authoritative list of vulnerabilities exploited in the wild.

**Relation to DarkMoon.** These are **per-vulnerability** and mostly **type-level** signals. An attack path is a **compositional, environment-specific** object: a chain of individually medium findings can reach a critical asset. The attack-graph hardening literature (§3.1) formalised this, and ChainGraph [barbosa2026chaingraph] restates it for LLM agents. DarkMoon's "fix-first" ranking can only be as sound as its edges. While edges are declared but not corroborated, a path-based priority is a hypothesis and should be presented next to CVSS/EPSS/KEV, not instead of them. The internal Cyber Score v1 is already labelled "not scientifically validated" ([EXP-061](../../experiments/successful/EXP-061.md)).

---

## 4. Classic structured representation vs. what LLM agents add

| Dimension | Classic structured approaches (attack graphs, provenance, KGs, expert systems) | What LLM agents add | What LLM agents do *not* add (on current evidence) |
|---|---|---|---|
| Source of edges | Model-derived (rules, pre/post-conditions) or system-observed (audit logs) | Edges proposed from execution context and narrative, including on targets with no formal model (web logic, GraphQL, IoT) | Reliable edge truth: DarkMoon 31.6% [15–54] and ~37% precision on two real runs; faithfulness literature (§3.10) |
| Coverage | Bounded by rule base and vulnerability DB (e.g. 83.9% noise paths and 53.7% coverage in [stevanovic2026neurosymbolic]) | Open-world exploitation; tool use; adapting to novel targets (§3.7) | Exhaustiveness: no guarantee of finding all paths |
| Semantics | Precise (Datalog fixpoint, PROV relations, d-separation) | Natural-language rationale | Formal semantics for the rationale |
| Soundness | Sound relative to the model or capture | — | Soundness: needs an external verifier |
| Cost | Cheap once modelled; expensive to model | Cheap to start; per-run token cost (DarkMoon [EXP-046](../../experiments/successful/EXP-046.md): +0.5–1.75% token add-ons) | — |
| Explainability | Derivation trees, provenance subgraphs | Readable narratives | Faithful explanations |

The empirical literature reviewed here is consistent with a **division of labour**: the LLM explores and proposes, and a structured layer represents, checks and ranks. DarkMoon's architecture D is one point in that space, with a very strict checker. It is not unique in this respect; see Stevanovic & Wachter, ChainGraph and Intentest.

---

## 5. Similarities and differences

**Similar to:**
- MulVAL / TVA: pre/post-condition edges, AND/OR composition, hardening-oriented ranking.
- Provenance IDS: value lineage as evidence; the same failure modes (false dependencies, capture blind spots).
- PENTESTLLMAGENT, VulnBot, Intentest, Excalibur: an explicit graph alongside an LLM agent.
- CTF-ABACUS, AgentTrails: post-hoc reconstruction of what the agent actually did from traces.

**Different from:**
- Attack-graph generators: DarkMoon has no network or vulnerability model and does not enumerate potential paths. It qualifies executed links.
- Provenance IDS: DarkMoon observes at tool and command level inside its own agent runtime, not at OS level. The adversary is the system's own agent; there is no third-party intruder.
- LLM pentest agents: their structure is for *planning*. DarkMoon's verifier is for *attesting*, and it keeps "declared" and "corroborated" as separate statuses visible to the user.
- Trajectory analysis tools: AgentTrails lets an LLM refine edges, while DarkMoon deliberately forbids model output from promoting an edge.

---

## 6. Potential contributions of DarkMoon (conditional, not demonstrated)

These are **candidate** contributions. Each would need the evidence listed before it could be claimed.

1. **An explicit epistemic status for agent-declared attack-path edges** (declared / corroborated / abstained), with a non-promotion guarantee. *Evidence so far:* 0 false promotions on a synthetic harness with mutation testing ([EXP-047](../../experiments/adversarial/EXP-047.md), [EXP-049](../../experiments/adversarial/EXP-049.md)). *Still needed:* an independent harness, and adversarial agents that try to forge value lineage.
2. **A measured error profile of LLM-declared inter-finding dependencies on real campaigns,** in particular that most false links are common-cause confusions. *Evidence so far:* n=2–4 runs, one model (claude-opus-4-6), self-built labs, single adjudicator per campaign. *Still needed:* more runs, several models, blinded multi-annotator oracles with inter-annotator agreement, and released traces.
3. **Negative results**, which are rarely published in this field: rules on finding text ([EXP-040](../../experiments/failed/EXP-040.md)), counterfactual replay ([EXP-019](../../experiments/failed/EXP-019.md), [EXP-031](../../experiments/failed/EXP-031.md)), "own-command" attribution ([EXP-038](../../experiments/failed/EXP-038.md)) and prompt rewording ([EXP-051](../../experiments/failed/EXP-051.md), [EXP-053](../../experiments/failed/EXP-053.md)) all failed preregistered criteria, and rewording variance (±20 points) exceeded the differences between variants. *Still needed:* writing them up with protocols and data.
4. **The tension between privacy and provenance.** Reversible tokenisation of secrets before they reach the model removes the exact values that lineage verification relies on ([EXP-047](../../experiments/adversarial/EXP-047.md) limitation). Documenting this trade-off and possible designs, such as verifying on the server side before masking, keyed hashes, or in-vault comparison, could be useful to others.

DarkMoon has **not** shown: a proven multi-step chain on real data; higher exploitation success than any published system; or a benchmark result on any public benchmark in §3.9.

---

## 7. Limits of comparison

- **Different targets.** DarkMoon used Juice Shop, DVGA, crAPI, dmlab, lab-multi and a multi-protocol lab. The literature mostly uses CTFs (Cybench, NYU CTF, PicoCTF), VulnHub/HTB machines, GOAD and CVE sandboxes. No DarkMoon run is on a public benchmark.
- **Different metrics.** Literature: flag or root success, sub-task completion, CVE reproduction. DarkMoon: precision and recall of *edges between findings* against a per-campaign oracle. These metrics are not convertible.
- **Different protocols.** DarkMoon: n=1 per setting, budget-capped (USD 6–20 per run), one model, oracles built by the same programme, approximate figures in some runs ([EXP-056](../../experiments/successful/EXP-056.md) "~37%"). Papers vary between 1 and 5 runs per task, and some report best-of-n.
- **Model confound.** Most published gains mix architecture and model changes [dhakal2026baselines]. DarkMoon's results are with one model only.
- **Contamination.** Public CTFs and well-known vulnerable apps (Juice Shop, DVGA) are likely in training data [xu2024contamination]. This affects DarkMoon's own targets too.
- **Self-evaluation.** DarkMoon's harnesses and oracles were built by the same team that built the engine, and some replays used Claude Code sub-agents as a proxy for the campaign model ([EXP-051](../../experiments/failed/EXP-051.md), [EXP-053](../../experiments/failed/EXP-053.md)). This is a known source of optimistic bias.

---

## 8. Open research directions

1. **A benchmark for executed attack-chain edges.** Lab environments with planted multi-step chains, *sealed* edge-level ground truth (including decoy common-cause pairs), and released traces. None of the verified benchmarks provides this (§3.9). d27 "dmlab" (14 truth links) was a private start.
2. **Telling common cause from direct enablement at token level** in agent traces. Candidate methods: interventional replay with recall better than in [EXP-019](../../experiments/failed/EXP-019.md); minimal-necessity tests; and modelling the root cause explicitly as a first-class node so the agent can link two findings to a shared cause instead of to each other. This last one is a simple change in representation that has not been tested.
3. **Privacy-preserving provenance**: verifying value lineage when the values must not reach the model (keyed-hash commitments, verification inside the vault). We are not aware of work that addresses this directly in LLM-agent security tooling; a targeted search is still needed.
4. **Capture completeness at the tool layer.** Shell substitutions, cross-call variables and in-memory reuse are the tool-layer equivalent of the dependency-explosion and blind-spot problems known from OS-level provenance [inam2023sok].
5. **Calibrated agent self-reports.** Whether confidence or abstention on `enabled_by` can be calibrated against an oracle, given the faithfulness results (§3.10) and the ±20-point variance observed ([EXP-053](../../experiments/failed/EXP-053.md)).
6. **Using structured priors without leaking them into truth.** MulVAL/ATT&CK-derived plausibility as a filter or ranker, kept strictly separate from the "corroborated" status. [stevanovic2026neurosymbolic] shows that noise paths stay high when symbolic rules are generated with LLM help.
7. **Model-matched baselines** for any architectural claim: a plain coding agent with the same model and budget [dhakal2026baselines; bouchari2026secondlook].
8. **Trace-verified outcomes** in the style of CTF-ABACUS [milner2026ctfabacus] applied to DarkMoon findings, not only to links.

---

## 9. Not verified

These candidates were considered but are **not** cited in the body as verified sources, or only with a caveat:

| Candidate | Status |
|---|---|
| CyGraph (Noel et al., Handbook of Statistics vol. 35, 2016) | Existence and pages from a secondary citation and MITRE page; DOI not found through Crossref search. Cited with "partially verified" note. |
| NYU CTF Bench as NeurIPS 2024 Datasets & Benchmarks | arXiv record verified; venue not confirmed. |
| PentestAgent as AsiaCCS 2025 | Only from arXiv comment; DOI not checked. |
| Cybench ICLR 2025 Oral; EnIGMA ICML 2025; Corr2Cause ICLR 2024; ReAct ICLR 2023; Turpin NeurIPS 2023; DeMeSSAI@EuroS&P 2026 | Venue from arXiv author comments only (labelled in the bib). |
| AgentTrails evaluation domains (SciAgentGym, Discovera) and dependency-inference method (shared tokens, column names) | Seen only in search-engine and alphaXiv summaries; the VLDB workshop PDF returned HTTP 403. The paper's existence, authors and abstract are verified. |
| "Agent Trajectory Explorer" (IBM Research, AAAI 2025) | Seen only as a search result; not checked. |
| Commercial and open-source autonomous-pentest or attack-path products | Commercial or open-source products without peer-reviewed evaluation; marketing claims not verifiable. Excluded. |
| NASim paper | The prior method watch could not identify it reliably; not cited. |
| GEPA (ICLR 2026) and other prompt-optimisation work | Out of scope for this review; the prior method watch found the proceedings page unreachable. |
| Reichenbach's common-cause principle (primary source) | Concept used via Spirtes et al. and Pearl; primary text not checked. |
| Unified Cyber Ontology (UCO) | Not verified (Crossref query rate-limited); omitted. |
| Kautz 2022 page range | Two Crossref records give different pages; DOI cited without pages. |
| ZERO-APT [zheng2026zeroapt], game-theoretic CAI [mayoral2026gametheoretic], LLM-pentest survey [he2026survey] | Metadata verified; contents **not reviewed**. Listed in the bib for completeness but not used for any claim. |

---

## 10. Sources for DarkMoon facts

- Architecture history: [`docs/architecture/`](../../docs/architecture/README.md).
- the experiment registry ([`experiments/`](../../experiments/README.md)): [EXP-010](../../experiments/successful/EXP-010.md), 015, 017, 019, 024, 028, 031, 038, 040–047, 049–053, 055–057, 060, 061.
- Earlier internal literature registers (leads only; not public).
