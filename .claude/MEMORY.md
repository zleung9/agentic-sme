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
