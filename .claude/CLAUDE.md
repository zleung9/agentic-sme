# CLAUDE.md

> Companion file: see `MEMORY.md` for the full project history, the original
> manuscript reference, and the source conversation that motivated this rebuild.

## Project identity

This repository is evolving from the original **SMEGen** project into a
2026-ready, agentic discovery project for single-molecule electronics (SME).

The old project framed the work mainly as:

> **SMEMol dataset + SMEPre predictor + LSTM molecular generator + DFT/NEGF
> validation + limited EC-STMBJ experiment.**

That framing is now considered **legacy**. It should be preserved as historical
context and as an important baseline, but it is **not** the final scientific
story.

The new project framing is:

> **A physics-grounded agentic molecular co-scientist for quantum-interference
> single-molecule electronics.**

The project should move from a conventional generative–predictive–validation
loop to an **agentic, hypothesis-driven discovery system** that reads
literature, formulates quantum-transport hypotheses, designs molecules to test
those hypotheses, critiques candidates, validates them computationally,
prioritizes synthesis, and supports prospective experimental validation.

---

## Core scientific thesis

Single-molecule electronics is not simply a molecular optimization task. It is a
sparse-data, mechanism-rich, experimentally expensive quantum-transport
discovery problem.

The new manuscript should argue that:

> We develop an agentic molecular co-scientist for single-molecule electronics
> that integrates literature-derived knowledge, quantum-transport reasoning,
> conditional molecular generation, uncertainty-aware DFT+NEGF validation,
> retrosynthetic assessment, and prospective EC-STMBJ experiments. Unlike
> conventional closed-loop molecular optimization, the system formulates and
> tests explicit quantum-interference hypotheses, enabling the discovery of
> molecular junctions that are not simple extrapolations of known expert design
> rules.

The key advance is **not** merely using a newer generator. The key advance is
converting sparse SME knowledge into testable, prospectively validated
molecular-device hypotheses.

---

## Why the old framing is insufficient in 2026

By 2026, the simple loop below is no longer methodologically novel enough by
itself:

```text
Generate → Predict → Validate → Update
```

This has become standard in AI-for-science, self-driving-lab, and molecular
discovery work.

The project must instead emphasize:

```text
Read → Hypothesize → Design → Critique → Simulate → Synthesize → Measure → Explain → Revise
```

The system should be judged not only by hit rate, but also by whether it can:

1. propose explicit quantum-interference hypotheses;
2. design molecules that test those hypotheses;
3. distinguish new mechanisms from known expert rules;
4. select positive, negative, and control molecules prospectively;
5. explain successes and failures;
6. build an auditable reasoning trail.

---

## Legacy project context

The existing project and manuscript use older conventions. The agent should be
aware of these names and should not delete them blindly.

| Legacy name | Meaning | New role |
|---|---|---|
| `SMEMol` | Literature-derived SME molecule dataset | Seed dataset / knowledge-graph source |
| `SMEPre` | Predictor for SME properties | Uncertainty-aware surrogate ensemble component |
| `SMEGen` | LSTM-based generator and pipeline | Historical baseline and ablation |
| LSTM generator | Original molecular generator | Baseline, not final model |
| DFT+NEGF validation | Computational validation | Multi-fidelity quantum-transport agent tool |
| EC-STMBJ validation | Experimental validation | Prospective experimental validation module |
| 171 original molecules | Initial curated dataset | Tier A seed data |
| 338 mixed molecules | Original + generated/validated molecules | Active-learning history, **not** independent random split |
| Gen1 / Gen9 / SME1 | Existing experimental molecules | Legacy experimental anchor, not sufficient final validation |

### Important rule

Do **not** treat the legacy 338-molecule mixed set as a clean independent
dataset. Generated molecules that were later validated are part of the
active-learning history and must be handled carefully to avoid train/test
leakage.

---

## New project name

Recommended working name:

```text
SME-Agent
```

Alternatives if needed: `QISM-Agent` (Quantum-Interference Single-Molecule
Agent), `Agentic-SME`, `SME-CoScientist`. Use the old name `SMEGen` only when
referring to the legacy baseline.

---

## Target manuscript framing

**Avoid** the outdated claim:

> We developed a closed-loop generative AI framework for SME molecular design.

**Avoid** the weak claim:

> We replaced the LSTM with a more advanced generative model.

**Use** the stronger claim:

> We developed a physics-grounded, hypothesis-driven multi-agent discovery
> framework for single-molecule electronics that generates, critiques,
> validates, and experimentally prioritizes molecular junctions to test
> quantum-interference mechanisms.

Strong title candidates:

1. An Agentic Molecular Co-Scientist for Quantum-Interference Single-Molecule Electronics
2. Agentic Discovery of Quantum-Interference Molecular Junctions through Physics-Grounded Generative Design
3. A Physics-Grounded Multi-Agent Framework for Small-Data Molecular Electronics Discovery
4. From Generative Molecular Design to Agentic Quantum-Device Discovery

---

## Innovation target

| Version | Method novelty | SME-domain novelty | Top-journal attractiveness |
|---|---:|---:|---:|
| Legacy LSTM + SMEPre + DFT loop | 2/10 | 5–6/10 | 3/10 |
| Graph diffusion + active learning | 4–5/10 | 7/10 | 5–6/10 |
| Agentic hypothesis-driven SME discovery | 6–7/10 | 8/10 | 7–8/10 |
| Agentic discovery of a new SME design rule | 7–8/10 | 9/10 | Nature / Nat. Materials / Nat. Nanotech competitive |

The highest-value result is not simply a better molecule. The highest-value
result is a **new or counterintuitive SME design principle** supported by
prospective DFT and EC-STMBJ validation.

---

## System architecture

Organize the new project around cooperating agents, even if some are initially
implemented as scripts or modules rather than fully autonomous LLM agents.

### 1. Literature–Knowledge Agent

Purpose: build an auditable SME knowledge graph.

Responsibilities: mine literature-derived molecule records; extract molecular
structures, anchor groups, scaffold types, junction geometries, conductance
behavior, Seebeck values, gating behavior, and QI labels; detect duplicate
structures and inconsistent labels; link molecules to publications and evidence;
flag unsupported claims or missing metadata; maintain a structured record of
known SME design rules.

Target output:

```text
molecule → scaffold → anchor group → QI motif → junction geometry → DFT label → experimental evidence → source
```

### 2. Hypothesis Agent

Purpose: propose and rank mechanistic quantum-transport hypotheses, for example:

- asymmetric heteroatom placement shifts antiresonance closer to the Fermi level;
- cross-conjugated donor–acceptor motifs amplify gate sensitivity;
- non-classical anchor alignment creates hidden destructive quantum interference channels;
- heteroaromatic fusion breaks expected magic-ratio behavior;
- anchor-induced level alignment can dominate over classical connectivity rules.

Score each hypothesis by: novelty, physical plausibility, testability, DFT cost,
synthetic feasibility, experimental discriminability, and ability to distinguish
from known expert rules.

### 3. Generative Design Agent

Purpose: generate molecules to test hypotheses, not just maximize a black-box
score.

| Role | Model |
|---|---|
| Main model | Conditional graph diffusion / graph Transformer generator |
| Strong practical baseline | REINVENT4-style Transformer/RL or scaffold-decorating workflow |
| Low-data baseline | Original LSTM / CLM |
| Non-AI baseline | Random SME-like enumeration |
| Expert baseline | Rule-based quantum-interference analogs |

Generation should support conditioning on: DQI presence; high Seebeck
coefficient; large regulation range; high regulation efficiency; two valid
anchor groups; synthetic accessibility; scaffold novelty; redox/electrochemical
constraints; hypothesis label.

### 4. Quantum-Transport Agent

Purpose: manage multi-fidelity computational validation.

```text
graph/descriptor surrogate
↓
semi-empirical or tight-binding transport, if available
↓
DFT+NEGF
↓
geometry/conformer sensitivity analysis
↓
Fermi-level alignment uncertainty
↓
gate-response simulation
```

Required metadata for every computed label: functional; basis set;
pseudopotential; electrode model; Au–anchor geometry; conformer sampling rule;
Fermi-level alignment method; transmission-dip definition; gate-voltage mapping;
convergence thresholds; failure/exclusion criteria. No result is final unless
the computational protocol is reproducible.

### 5. Synthesis Agent

Purpose: ensure that generated molecules are experimentally realistic.

Responsibilities: calculate SAscore; assess retrosynthetic accessibility
(e.g. RAscore, AiZynthFinder); identify commercial precursors; estimate number
of synthesis steps; check anchor compatibility; check redox stability and
electrochemical window; flag purification risks; prioritize molecules with
feasible routes. Do not let beautiful but unsynthesizable molecules dominate the
story.

### 6. Reviewer / Critic Agent

Purpose: protect the work from obvious reviewer attacks. The critic should ask:
Is this candidate just a known QI rule in disguise? Is the scaffold genuinely
new relative to the training data? Is the predicted gain larger than DFT
uncertainty? Is the molecule experimentally distinguishable from controls? Are
there positive, negative, isomeric, and expert-designed controls? Would a
reviewer call this cherry-picking? Is the claim stronger than the evidence? Is
train/test leakage possible? Are failed DFT or failed synthesis cases
documented? Produce an audit trail usable in the Supplementary Information.

---

## Dataset strategy

### Data tiers

| Tier | Content | Use |
|---|---|---|
| Tier A | Original SMEMol literature molecules (171) | Seed dataset |
| Tier B | Legacy generated + DFT-validated molecules | Active-learning history |
| Tier C | Existing experimental molecules (Gen1/Gen9/SME1) | Legacy experimental anchors |
| Tier D | Newly mined literature molecules | Dataset expansion |
| Tier E | Newly generated prospective candidates | Active-learning candidates |
| Tier F | Locked prospective validation set | Final test set |

### Dataset rules

1. Never use a simple random split over mixed legacy + generated data as the main result.
2. Prefer scaffold splits, source splits, time splits, and locked prospective splits.
3. Generated molecules from prior loops are not independent external test data.
4. Preserve full provenance for every molecule.
5. Record failed extractions, failed DFT jobs, failed retrosynthesis, and failed syntheses.
6. Do not hide negative or borderline candidates.
7. Standardize molecule representation using canonical SMILES and SELFIES.
8. Store anchor definitions explicitly.
9. Store scaffold and analog-series labels explicitly.
10. Treat all labels as protocol-dependent unless computed with the standardized DFT+NEGF workflow.

### Suggested molecule record schema

```yaml
molecule_id:
legacy_id:
canonical_smiles:
selfies:
inchi_key:
scaffold_id:
analog_series_id:
anchor_group_type:
anchor_positions:
substituents:
source_type: literature | legacy_generated | new_generated | experiment
source_reference:
generation_round:
generator_model:
hypothesis_id:
dft_status:
dft_protocol_id:
dft_transmission_features:
dqi_label:
seebeck_coefficient:
regulation_range:
regulation_efficiency:
synthetic_accessibility_score:
retrosynthesis_status:
experimental_status:
ec_stmbj_summary:
split_assignment:
notes:
```

---

## Model and benchmark plan

### Predictor plan

Make the predictor an uncertainty-aware ensemble rather than a single point
estimator.

| Task | Type |
|---|---|
| DQI / transmission dip | Classification |
| Seebeck coefficient | Regression |
| Regulation range | Regression |
| Regulation efficiency | Regression |
| DFT failure probability | Classification |
| Experimental priority | Ranking |

Recommended baselines: descriptor + Random Forest; descriptor + XGBoost;
Chemprop / message-passing neural network; GIN/GAT; GraphGPS or equivalent graph
Transformer; Uni-Mol or 3D-aware model if 3D inputs are reliable; legacy SMEPre.

Required outputs: point prediction; uncertainty estimate; calibration
diagnostics; scaffold-split performance; locked-test performance; ablation table.

### Generator plan

Main generator: conditional graph diffusion / graph Transformer.

Baselines: legacy LSTM; SELFIES Transformer; REINVENT4-style Transformer/RL;
random SME-like enumeration; expert-rule enumeration; Bayesian optimization over
fragments.

Generative metrics: validity; uniqueness; novelty; diversity; scaffold novelty;
anchor validity; synthetic accessibility; property controllability; hypothesis
alignment; DFT hit rate under matched budget; experimental priority rate.

### Essential ablations

| Ablation | Question |
|---|---|
| No active learning | Does the loop matter? |
| No uncertainty | Does uncertainty improve discovery? |
| No DFT feedback | Are gains just generator bias? |
| No SMEPre/surrogate filter | Does screening help? |
| LSTM vs graph diffusion | Does the new generator matter? |
| Random vs AI | Is AI better than brute force? |
| Expert rules vs AI | Is AI discovering beyond known design rules? |
| Agentic vs non-agentic generator | Does hypothesis-guided reasoning help? |
| With vs without critic agent | Does critique reduce false positives and reviewer weaknesses? |

The most important comparison, under **matched DFT+NEGF budgets**, is:

```text
agentic hypothesis-guided design
vs non-agentic graph diffusion
vs legacy LSTM SMEGen
vs expert-designed/rule-based analogs
vs random SME-like enumeration
```

---

## Active-learning workflow

```text
Generate 50,000–200,000 molecules
↓ Validity / anchor / chemistry filters
↓ Surrogate prediction with uncertainty
↓ Diversity and novelty selection
↓ Hypothesis and critic-agent review
↓ Retrosynthesis / synthetic feasibility filter
↓ DFT+NEGF for 50–150 molecules
↓ Update dataset
↓ Retrain predictor and generator
↓ Revise hypotheses
```

### Selection policy (per DFT round)

| Class | Approximate fraction | Purpose |
|---|---:|---|
| Exploitation | 40% | Highest predicted performance |
| Exploration | 40% | Novel scaffolds or high uncertainty |
| Controls | 20% | Negative, borderline, expert, or isomeric controls |

### Recommended rounds

| Round | DFT+NEGF candidates | Goal |
|---|---:|---|
| Round 0 | audit/recompute current molecules | establish clean baseline |
| Round 1 | 100–150 | test new agentic design |
| Round 2 | 100–150 | show learning improvement |
| Round 3 | 50–100 | locked prospective validation |

The final round must be frozen before DFT/experiment. Do not retrospectively
modify the validation set.

---

## Experimental validation plan

The old three-molecule validation is not enough for the new claim.

### Minimum validation (8–10 molecules)

| Category | Number |
|---|---:|
| High-confidence AI positives | 4 |
| Novel-scaffold AI positives | 2 |
| AI negatives / weak-response controls | 2 |
| Expert-designed controls | 2 |

### Strong validation (12–15 molecules)

| Category | Number |
|---|---:|
| AI positives | 6 |
| AI negatives | 3 |
| Expert controls | 3 |
| Isomer/control series | 3 |

### Required reporting per molecule

Conductance histogram; 2D conductance–gate histogram; transfer curves; trace
number; junction yield; batch repeatability; uncertainty/error bars; cyclic
voltammogram; electrochemical window; molecule purity; calculated vs measured
trend; failed molecules, if any. The story is stronger if it reports successes,
failures, negative controls, and borderline predictions.

---

## Manuscript structure

**Figure 1 — From closed loop to agentic discovery.** Show the shift from
`Generate → Predict → Validate → Update` to
`Literature Agent → Hypothesis Agent → Design Agent → Quantum-Transport Agent →
Synthesis Agent → Experiment → Critic Agent → Revised Hypotheses`. Message: the
system does not only optimize molecules; it proposes and tests SME design
hypotheses.

**Figure 2 — SME knowledge graph and hypothesis generation.** Show
`molecule → scaffold → anchor → QI motif → DFT transmission → experimental
evidence`, then 3–5 agent-generated hypotheses and their rankings. Message:
sparse literature and DFT data are converted into explicit, testable transport
hypotheses.

**Figure 3 — Prospective agentic design benchmark.** Compare random enumeration,
expert rules, legacy LSTM SMEGen, graph diffusion only, and agentic
hypothesis-guided design. Metrics: DFT hit rate; novel scaffold rate; hypothesis
confirmation rate; synthetic accessibility; experimental discriminability;
new-mechanism score. Message: hypothesis-guided agentic design discovers
high-value candidates more efficiently under the same DFT budget.

**Figure 4 — Prospective experimental validation.** Show AI positives,
AI negatives, expert-designed controls, isomer controls, DFT predictions,
EC-STMBJ results, and prediction–experiment agreement. Message: prospective
molecules validate the agent's quantum-transport hypotheses experimentally.

---

## Implementation guidance for Claude Code

### First action in a new session

1. Read `MEMORY.md` first for full project history and source files.
2. Inspect the repository tree.
3. Identify legacy SMEGen files, data, notebooks, and manuscript files.
4. Find existing naming conventions.
5. Avoid destructive edits.
6. Create a migration map from the legacy pipeline to the new agentic architecture.
7. Propose small, reviewable commits.

### Coding rules

- Prefer modular, testable Python code.
- Keep legacy code working unless explicitly asked to remove it.
- Put new agentic code in clearly separated modules.
- Use configuration files for model choices, thresholds, and dataset paths.
- Keep all random seeds explicit.
- Log all generated, rejected, failed, and selected molecules.
- Store audit trails for agent decisions.
- Never overwrite raw data.
- Never fabricate results.
- Mark unknown results as `TBD`, `pending`, or `not_yet_run`.
- Do not make claims in manuscript text unless the supporting data exists.

### Suggested repository structure

```text
.
├── CLAUDE.md
├── MEMORY.md
├── README.md
├── pyproject.toml
├── configs/
│   ├── dataset.yaml
│   ├── dft_protocols.yaml
│   ├── models.yaml
│   ├── active_learning.yaml
│   └── manuscript_claims.yaml
├── data/
│   ├── raw_legacy/
│   ├── raw_literature/
│   ├── processed/
│   ├── splits/
│   ├── generated/
│   ├── dft_results/
│   ├── experiment/
│   └── audit_trails/
├── notebooks/
│   ├── legacy_reproduction/
│   ├── dataset_audit/
│   ├── model_benchmarks/
│   └── manuscript_figures/
├── src/
│   ├── sme_agent/
│   │   ├── data/
│   │   ├── knowledge/
│   │   ├── hypotheses/
│   │   ├── generation/
│   │   ├── prediction/
│   │   ├── quantum_transport/
│   │   ├── synthesis/
│   │   ├── critic/
│   │   ├── active_learning/
│   │   └── evaluation/
│   └── legacy_smegen/
├── scripts/
│   ├── audit_dataset.py
│   ├── build_knowledge_graph.py
│   ├── generate_candidates.py
│   ├── score_candidates.py
│   ├── select_dft_batch.py
│   ├── ingest_dft_results.py
│   ├── run_benchmarks.py
│   └── build_manuscript_tables.py
├── tests/
├── manuscript/
│   ├── outline.md
│   ├── abstract.md
│   ├── figures/
│   ├── tables/
│   └── supplementary/
└── docs/
    ├── migration_from_smegen.md
    ├── dataset_protocol.md
    ├── dft_protocol.md
    ├── agent_architecture.md
    └── reviewer_risk_register.md
```

---

## Near-term task queue

1. **Migration inventory** → `docs/migration_from_smegen.md`: old files, data objects, model artifacts, naming conventions; which parts become baselines, are deprecated, or preserved.
2. **Canonical dataset schema** → `configs/dataset.yaml`, `docs/dataset_protocol.md`: all molecule fields, data tiers, provenance rules, split rules, leakage warnings.
3. **Reviewer risk register** → `docs/reviewer_risk_register.md`: each risk with mitigation and required evidence.
4. **Manuscript outline** → `manuscript/outline.md`: 4-figure structure, claims, evidence needed, status.
5. **Data audit utilities** → `src/sme_agent/data/`, `scripts/audit_dataset.py`: duplicates, invalid SMILES, missing anchor labels, missing DFT metadata, missing provenance, inconsistent splits, train/test leakage, generated molecules in independent test set.
6. **Agent interfaces** → `src/sme_agent/*/interfaces.py`, `docs/agent_architecture.md`: input schema, output schema, uncertainty field, evidence/provenance field, audit log field.
7. **Baseline benchmark plan** → `configs/models.yaml`, `docs/benchmark_plan.md`: legacy LSTM, graph diffusion, REINVENT-style baseline, random enumeration, expert rules.
8. **Active-learning protocol** → `configs/active_learning.yaml`, `docs/active_learning_protocol.md`: candidate generation size, filters, selection fractions, DFT budget, frozen validation, retraining rules.
9. **Claim-to-evidence table** → `configs/manuscript_claims.yaml`, `manuscript/tables/claim_evidence_table.md`.
10. **Experimental validation matrix** → `manuscript/tables/experimental_validation_matrix.md`: AI positives, AI negatives, expert controls, isomer controls, rationale, predicted behavior, synthesis status, measurement status.

---

## Claim discipline

Do **not** write, until the matching evidence exists:

- "outperforms expert design" — needs expert-rule vs AI comparison under matched budgets;
- "discovers new mechanism" — needs a hypothesis, controls, DFT support, and experimental support;
- "robust predictor" — needs uncertainty/calibration/scaffold-split results;
- "experimentally validated platform" — needs enough prospective molecules measured;
- "autonomous" — needs true planning/tool use without hidden human selection.

Safer wording before evidence exists: "candidate framework", "working
hypothesis", "prospective validation target", "preliminary DFT support",
"designed to test", "aims to distinguish", "pending experimental validation."

---

## Reviewer risk register summary

1. **The system is just a standard closed loop.** → Emphasize agentic hypothesis generation, critique, and test design.
2. **The AI did not contribute causally.** → Compare to random, expert, LSTM, and non-agentic graph diffusion baselines.
3. **The dataset is too small.** → Expand literature data and DFT-labeled prospective candidates; report uncertainty.
4. **Train/test leakage.** → Use tiered data, scaffold/source/time/prospective splits.
5. **DFT+NEGF labels are not reproducible.** → Standardize and document the full computational protocol.
6. **Generated molecules are not synthesizable.** → Include retrosynthesis and experimental feasibility filters.
7. **Experimental validation is too thin.** → Include 8–15 molecules with positives, negatives, expert controls, and isomer controls.
8. **The mechanism is not new.** → Require hypothesis novelty analysis and controls against known QI rules.
9. **The manuscript overclaims.** → Maintain claim-to-evidence tracking.

---

## Definition of done

Not ready for a top-journal submission until all of the following exist:

- clean standardized dataset with provenance;
- leakage-safe train/validation/test splits;
- reproducible DFT+NEGF protocol;
- uncertainty-aware predictor ensemble;
- modern generator plus strong baselines;
- agentic hypothesis generation and critique logs;
- matched-budget comparison against random, expert, LSTM, and non-agentic baselines;
- at least one prospective DFT round;
- experimental validation with positives, negatives, and controls;
- claim-to-evidence table;
- code/data availability plan;
- reviewer risk register;
- manuscript rewritten around agentic hypothesis-driven discovery.

---

## Preferred working style

Be conservative about scientific claims; be aggressive about identifying
methodological weaknesses; preserve useful legacy work as baselines; separate
evidence from aspiration; ask whether each result proves model novelty, domain
novelty, or mechanism novelty; prioritize reproducibility and auditability;
prefer small, reviewable changes; write documentation alongside code; treat
failed candidates as scientifically useful data.

The goal is not to make the old SMEGen manuscript look newer. The goal is to
rebuild the project so that the final paper is genuinely aligned with the 2026
agentic AI-for-science era.

## Language

All communication, code, comments, documentation, and commit messages must be
in English only.

## Memory policy

- **Canonical project memory:** `./claude/MEMORY.md` — read and edit this file
  directly for all project-level memory.
- **Do NOT write project memory to** `~/.claude/projects/` unless explicitly
  asked by the user.
- User profile, preferences, and non-project reference memories remain in
  `~/.claude/projects/-Users-zliang/memory/`.

