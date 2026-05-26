# MEMORY.md

This file is the durable memory for the SME-Agent project. It records where the
project came from, what was decided, and which source documents motivated the
rebuild. Read this before `CLAUDE.md` when picking up the project cold.

---

## Source documents

| Reference | File | What it is |
|---|---|---|
| Original manuscript | `Manuscript-SMEGen_0323_LZ.docx` | The legacy SMEGen paper draft. Title: *"Deep learning-driven generative molecular design for Single-Molecule Electronics."* Authors: Yuyan Liu, Haoyang Pan, Dingling Zhuang, Jie Bai, Yongfeng Wang, Haojie Liu, Wenjing Hong (Xiamen University / Peking University). Contains the SMEMol dataset, SMEPre predictor, LSTM generator, DFT+NEGF validation, and the initial EC-STMBJ experiment. |
| Conversation record | `chatgpt-raw-export_单分子论文-顶刊送审概率分析.json` | Raw export of the prior advisory conversation ("单分子论文-顶刊送审概率分析" / "single-molecule paper — top-journal send-out probability analysis"). Contains the send-out probability assessment, the list of fatal flaws, the full rebuild plan, the 2026 agentic reframing, and the innovation rating. The reasoning in `CLAUDE.md` derives directly from this record. |

> Note: if these files are not present in the working tree, ask the maintainer
> to re-upload them. The original manuscript and the conversation export are the
> primary references for all design decisions in `CLAUDE.md`.

---

## Project origin and current state

The project began as **SMEGen**, a closed-loop generative pipeline for
single-molecule electronics:

```text
SMEMol dataset → LSTM generator → SMEPre predictor → DFT+NEGF validation → dataset update
```

Key numbers from the legacy manuscript (for provenance, not for reuse as a clean
split):

- **SMEMol dataset:** ~171 curated literature-derived molecular junctions (Tier A).
- **Model evaluation set:** 338 molecules = original SMEMol + DFT-validated generated molecules (this is active-learning history, **not** an independent dataset).
- **Generation:** two rounds; ~89 then ~77 molecules DFT+NEGF-calculated; 57 DQI molecules found, 13 "enhanced."
- **Experiment:** only **3** molecules synthesized and measured by EC-STMBJ (Gen1, Gen9, SME1 — constitutional isomers on a naphthalene core).
- **Reported predictor performance:** DQI classification AUC ≈ 0.784, ACC ≈ 0.765; regression RMSEs of 0.325 (Seebeck), 2.15 (regulation range), 2.31 (regulation efficiency).

Current decision: the SMEGen framing is **legacy / baseline**. The project is
being rebuilt as **SME-Agent**, an agentic, hypothesis-driven discovery system.
See `CLAUDE.md` for the full target architecture and plan.

---

## Summary of the advisory conversation

The source conversation had three substantive exchanges. Their conclusions are
the foundation of this project.

### 1. Send-out (送审) probability and fatal flaws

The original manuscript was judged **not ready for a top journal as-is**.
Estimated send-out (not acceptance) probabilities:

- As-is to Nature / Science / Nature Materials / Nature Nanotechnology / Nature Machine Intelligence level: **~0–8%** (likely desk reject).
- Language/format cleanup only: **~10–20%**.
- Full evidence-chain rebuild (methods, external validation, ablations, statistics, open data/code, restructured story): **~30–50%** for Nature Communications / JACS / Angew / Advanced Materials / ACS Nano; **~10–25%** for Nature Materials / Nat. Nanotech.

**Fatal flaws identified:**

1. **AI causal contribution unproven** — no ablations isolating the loop (random + DFT, no fine-tune, no SMEPre filter, no iteration, expert-rule, other generators).
2. **Tiny dataset + train/test contamination risk** — 171 → 338 mixing of generated-then-validated molecules; inconsistent split description (9:1 in main text vs scaffold split in Methods).
3. **Predictor not strong enough** to support "high-reliability screener" claims (AUC 0.784 is moderate; regression lacks units, error bars, variance, tuned baselines).
4. **Experimental validation too thin / qualitative** — only 3 isomers; connection-site effects on QI are already known, so this may just rediscover known rules.
5. **DFT+NEGF method underspecified** — functional, basis set, electrode model, Au–S geometry, Fermi-level alignment, conformer sampling all missing.
6. **Dataset construction risky** — figure-to-SMILES via PDFigCapX/DECIMER without reported accuracy, manual QC, deduplication, or standardization.
7. **Generator choice under-justified** — LSTM admitted to be "a bit outdated"; placeholder "capability of XXX"; internal review note "Research LSTM's advantage over Transformers."
8. **Interpretability overclaim** — attention weights treated as causal atom contributions; needs masking / ablation / counterfactual / SHAP.

**Completeness defects that alone risk desk reject:** multiple `[citation needed]`, `XXX`, `XX`, `Figure SX` placeholders; Figure 6 reused for two different things; figure numbering inconsistencies; `DTF+NEGF` typo; duplicated paragraphs; empty Methods equations; GitHub placeholder data/code links; incomplete references; empty Author contributions / Acknowledgments.

### 2. Full rebuild plan

A staged plan was agreed: rebuild the dataset into tiers (A–F) with reproducible
DFT+NEGF labels and provenance; upgrade the generator to a conditional graph
diffusion / graph Transformer with LSTM, REINVENT4, random, and expert baselines;
turn SMEPre into an uncertainty-aware ensemble; run a genuine active-learning
loop with exploitation/exploration/control splits; add retrosynthesis (RAscore,
AiZynthFinder) beyond SAscore; expand experimental validation to 8–15 molecules
with positive, negative, and control classes; restructure the manuscript around
four figures. (Full detail lives in `CLAUDE.md`.)

### 3. 2026 agentic reframing and innovation rating

The plain generative → predictive → validation loop is **no longer novel by
itself in 2026** (it is now standard self-driving-lab / AI-for-science
vocabulary). The recommendation was to reframe to a **physics-grounded agentic
molecular co-scientist** that reads, hypothesizes, designs, critiques, simulates,
synthesizes, measures, explains, and revises. Innovation rating ladder (method /
domain / top-journal attractiveness): legacy loop 2 / 5–6 / 3; graph diffusion +
active learning 4–5 / 7 / 5–6; agentic hypothesis-driven 6–7 / 8 / 7–8; agentic
discovery of a *new SME design rule* 7–8 / 9 / Nature-competitive.

---

## Standing reminders for the agent

- Treat the 338-molecule set as active-learning history, never as a clean random split.
- Every scientific claim must map to existing evidence; otherwise use hedged wording.
- Preserve legacy SMEGen as a baseline; do not delete it.
- The headline result that matters is a **new/counterintuitive, prospectively validated SME design principle**, not just a better molecule.
- When in doubt about a design decision, re-read the relevant section of the conversation record JSON listed above.

---

## 3-month generative research plan (2026-05-26 — 2026-08-26)

This plan focuses on the generative design agent: building a conditional
molecular generator, strong baselines, and an evaluation framework that feeds
into the broader agentic discovery system. Non-generator agents (literature,
hypothesis, critic, synthesis, quantum-transport) are developed in parallel
but are not the primary deliverable of this plan.

### Month 1: Data foundation and baselines (May 26 — Jun 26)

#### Week 1–2: Dataset audit and standardization

- [ ] Audit SMEMol dataset: deduplicate, validate SMILES, standardize canonical SMILES and SELFIES
- [ ] Verify anchor-group labels (thiol, amino, pyridyl, etc.) for every molecule
- [ ] Assign scaffold IDs and analog-series labels
- [ ] Tag data tier (A/B/C) and split assignment (scaffold split preferred)
- [ ] Export to `data/processed/smemol_standardized.csv` with the schema from CLAUDE.md
- [ ] Document missing labels, unresolved duplicates, provenance gaps in `docs/dataset_audit.md`
- **Milestone:** Clean, versioned dataset with provenance — first `data/` commit

#### Week 3: Legacy baselines — reproduce and benchmark

- [ ] Re-run legacy LSTM generator on the standardized dataset; record validity, uniqueness, novelty, scaffold novelty
- [ ] Re-run SMEPre predictor on generated molecules; record DQI classification and regression metrics
- [ ] Implement SELFIES Transformer baseline (char-level Transformer on SELFIES strings)
- [ ] Implement random SME-like enumeration: fragment pool (anchor groups + scaffolds from literature) + random combination + valence/SMILES validity filter
- **Milestone:** Three baselines running (LSTM, SELFIES Transformer, random enumeration) with metrics logged

#### Week 4: Evaluation framework and expert-rule baseline

- [ ] Build evaluation harness: unified CLI script that takes a generator and outputs all metrics (validity, uniqueness, novelty, diversity, scaffold novelty, anchor validity, SAscore distribution)
- [ ] Implement expert-rule baseline: QI-motif catalog (cross-conjugated, meta-connected, quinoidal, etc.) + anchor combination enumeration
- [ ] Define property-conditioning schema: DQI yes/no, Seebeck range, regulation range, regulation efficiency, scaffold constraints
- **Milestone:** Evaluation harness usable from CLI; five baselines benchmarked

### Month 2: Conditional graph generator (Jun 26 — Jul 26)

#### Week 5–6: Molecular graph representation and diffusion setup

- [ ] Choose framework: DiGress (discrete graph diffusion) or MiDi (molecular graph diffusion) as starting point — both support conditioning
- [ ] Implement SME-specific featurization: atom types relevant to SME (S, N, O, Au-binding atoms), bond types, anchor-group tokens, scaffold-level graph features
- [ ] Set up training pipeline with scaffold-split train/val/test
- [ ] Train unconditional model; evaluate basic generation quality vs baselines
- [ ] Implement conditional generation: property regression head, classifier-free guidance
- **Milestone:** Unconditional graph diffusion running, generating valid SME molecules

#### Week 7–8: Conditioning and property control

- [ ] Train conditional model with multi-property conditioning (DQI, Seebeck, regulation range, regulation efficiency, SAscore)
- [ ] Measure property controllability: generate with conditioning label, compute property via SMEPre surrogate, measure gap
- [ ] Implement scaffold-conditioned generation (generate analogs of a given scaffold)
- [ ] Anchor-group validity filter: post-hoc check that generated molecules have exactly 2 valid anchor groups
- **Milestone:** Conditional generator with quantitative property control

#### Week 9: REINVENT4-style RL baseline

- [ ] Implement REINVENT4-style baseline: Transformer agent + scoring function (SMEPre surrogate + SAscore + anchor validity) + RL fine-tuning
- [ ] Run head-to-head against conditional graph diffusion under matched compute budget
- **Milestone:** Sixth baseline operational; first head-to-head comparison

### Month 3: Agentic generation loop and prospective validation (Jul 26 — Aug 26)

#### Week 10–11: Hypothesis-conditioned generation

- [ ] Integrate with hypothesis agent: given a hypothesis (e.g., "asymmetric heteroatom placement shifts antiresonance closer to EF"), translate to molecular constraints
- [ ] Implement constraint-to-generation mapping: hypothesis → scaffold constraints, substituent constraints, anchor constraints, property targets
- [ ] Generate molecules conditioned on 3–5 specific quantum-interference hypotheses
- [ ] Critic agent review: filter generated candidates through the reviewer checklist
- **Milestone:** End-to-end hypothesis → generation → critique pipeline working

#### Week 12: Active-learning round 1 and benchmark

- [ ] Run the full active-learning loop at small scale:
  - Generate 50k molecules (agentic + baselines)
  - Filter: validity, anchor, chemistry, SAscore < 4.5
  - SMEPre surrogate scoring + uncertainty
  - Select 50 molecules: 40% exploitation, 40% exploration, 20% controls
- [ ] Prepare DFT+NEGF batch input files for the selected 50 molecules
- [ ] Run benchmark: agentic hypothesis-guided vs non-agentic graph diffusion vs LSTM vs expert rules vs random — matched candidate budget
- **Milestone:** Benchmark table with all six generators; DFT batch ready

#### Week 13: Analysis, documentation, and manuscript Figure 3

- [ ] Analyze benchmark results: which generator finds more DQI candidates, novel scaffolds, synthesizable molecules
- [ ] Compute matched-budget efficiency: DFT hit rate per 50 candidates, scaffold novelty vs SAscore Pareto
- [ ] Draft Figure 3 (prospective agentic design benchmark) and supporting tables
- [ ] Write `docs/generator_benchmark.md`: methods, metrics, results, limitations
- **Milestone:** Figure 3 draft + benchmark report; plan DFT round 2 targets

### Deliverables at 3 months

| # | Deliverable | Status target |
|---|---|---|
| 1 | Standardized SMEMol dataset, versioned, provenance-tracked | Complete |
| 2 | Six generators benchmarked (LSTM, SELFIES TF, random, expert rules, REINVENT4-style, graph diffusion) | Complete |
| 3 | Conditional graph diffusion generator with multi-property control | Complete |
| 4 | Hypothesis-conditioned generation pipeline | Complete |
| 5 | Evaluation harness with 10+ metrics | Complete |
| 6 | First active-learning DFT batch (50 molecules) selected and input files prepared | Ready for DFT |
| 7 | Manuscript Figure 3 draft | Draft |
| 8 | Generator benchmark report | Complete |

### Dependencies and risks

| Risk | Impact | Mitigation |
|---|---|---|
| SMEMol data quality worse than expected (missing anchor labels, inconsistent SMILES) | Blocks Week 1–2 | Fall back to manual curation of a 100-molecule clean subset |
| Graph diffusion produces invalid anchor groups at high rate | Delays Week 7–8 | Add anchor-validity reward to training; post-generation filter |
| Insufficient GPU / compute | Slows training | Use cloud GPU (Lambda, Colab Pro+); REINVENT4 and LSTM baselines run on CPU |
| Hypothesis agent not ready by Week 10 | Blocks Week 10–11 | Use manually-written hypothesis → constraint templates as placeholder |
| DFT compute not available within the 3-month window | Blocks Week 12 milestone | Define the batch and freeze candidates; DFT results arrive later |

### Integration points with other agents (parallel tracks)

These are not in the generative work plan but must be coordinated:

- **Literature agent (Month 1–2):** expands Tier D molecules; generator needs updated training data
- **Predictor/SMEPre (Month 1–2):** provides surrogate scoring for generator conditioning and filtering
- **Hypothesis agent (Month 2):** provides constraints for Week 10
- **Critic agent (Month 2–3):** provides post-generation filter in Week 11
- **Quantum-transport agent (Month 3):** runs DFT on the 50-molecule batch
