# SME-Agent

A physics-grounded, hypothesis-driven multi-agent discovery framework for quantum-interference single-molecule electronics.

This project is evolving from the legacy **SMEGen** (closed-loop generative pipeline) into **SME-Agent**, an agentic molecular co-scientist that reads literature, formulates quantum-transport hypotheses, designs molecules, critiques candidates, validates them computationally, and supports prospective experimental validation.

---

## Generative model — architecture

The flagship generator is **conditional graph diffusion (DiGress) with a TransformerConv backbone**. This choice was made after evaluating two leading discrete diffusion approaches:

| Approach | Strengths | Weaknesses for SME-Agent |
|---|---|---|
| **DiGress** (GNN denoiser, default RGCN) | Data-efficient on small datasets (~171 molecules); good at local anchor-substructure patterns | Stepwise message passing dilutes long-range signals needed for quantum interference |
| **Graph DiT** (Transformer denoiser) | Global attention captures QI-relevant long-range effects natively; scales well with data | Poor cold-start on tiny datasets; immature codebase |

The **TransformerConv backbone** (PyG) replaces DiGress's default RGCN with local-attention GNN layers — the pragmatic midpoint. It preserves DiGress's data efficiency while enabling better long-range signal propagation. A full Graph DiT upgrade is planned once the dataset grows beyond ~500 molecules.

### Generator lineup

| Generator | Role |
|---|---|
| **Graph diffusion (DiGress + TransformerConv)** | Main model |
| **REINVENT4-style Transformer/RL** | Strong practical baseline |
| **Legacy LSTM (char-level SMILES)** | Low-data / reproducibility baseline |
| **Random SME-like enumeration** | Non-AI baseline (how hard is the task?) |
| **Expert-rule enumeration** | What would an expert chemist do? |
| **SELFIES Transformer** | Bridge between LSTM and graph diffusion |

All generators support conditioning on: DQI presence, Seebeck coefficient, regulation range, regulation efficiency, SAscore, scaffold constraints, and anchor validity.

### SMEPre surrogate predictor

Built alongside the generator as an uncertainty-aware ensemble (RDKit/XGBoost fast tier + 5-member MPNN primary tier). Handles DQI classification, Seebeck/regulation regression, and SAscore — needed for conditioning control and the REINVENT4 scoring function.

---

## Implementation plan

### Phase 0 — Scaffold & data (Weeks 1-2)
- uv + pyproject.toml package structure
- Extract SMEMol dataset from the manuscript (DOCX → parsed SMILES)
- Deduplicate, canonicalize, annotate anchors/scaffolds/tiers
- Scaffold-split train/val/test

### Phase 1 — Baselines (Weeks 3-4)
- Implement all five baselines with a common `.generate(n)` interface
- Benchmark: validity, uniqueness, novelty, diversity, scaffold novelty, anchor validity, SAscore

### Phase 2 — SMEPre predictor (Weeks 3-4, parallel)
- Build uncertainty-aware ensemble (XGBoost + 5-member MPNN)
- Scaffold-split cross-validation; calibration + uncertainty diagnostics

### Phase 3 — Graph diffusion generator (Weeks 5-8)
- Vendor DiGress under `src/sme_agent/generators/digress/`
- Add SME-specific atom featurization (anchor atoms, scaffold atoms, ring sizes)
- Replace RGCN backbone with PyG TransformerConv
- Implement classifier-free guidance for multi-property conditioning
- Train unconditional → conditional; validate quality vs baselines

### Phase 4 — Evaluation harness (Week 4, ongoing)
- Unified CLI: `scripts/run_benchmarks.py --generator <name>`
- Metrics: validity, uniqueness, novelty, diversity, scaffold novelty, anchor validity, SAscore, property controllability
- Results logged to `results/benchmark_{timestamp}.json`

### Phase 5 — Active learning loop (Weeks 9-10)
- Generate 100k-200k molecules → filter → SMEPre score → diversity select → DFT batch
- Select 50 molecules (40% exploitation, 40% exploration, 20% controls)
- Prepare DFT+NEGF input files; freeze prospective validation set

---

## Quick start

```bash
# Install (when source code is available)
uv pip install -e .

# Extract and audit the legacy dataset
python scripts/extract_smemol.py
python scripts/audit_dataset.py

# Run baseline benchmarks
python scripts/run_benchmarks.py --generator lstm --n 10000
python scripts/run_benchmarks.py --generator selfies_tf --n 10000
python scripts/run_benchmarks.py --generator random_enum --n 10000
```

---

## Documentation

| Document | Purpose |
|---|---|
| [`.claude/CLAUDE.md`](.claude/CLAUDE.md) | Full project specification, architecture, dataset strategy, agent instructions |
| [`.claude/MEMORY.md`](.claude/MEMORY.md) | Project history, source documents, 3-month research plan |
| [`docs/research-plan.html`](docs/research-plan.html) | Visual research plan |
| [`docs/literature-survey.html`](docs/literature-survey.html) | Domain literature survey |
| [`docs/literature-survey-ai-sme.html`](docs/literature-survey-ai-sme.html) | AI-for-SME literature survey |

---

## License

TBD
