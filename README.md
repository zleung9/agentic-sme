# SME-Agent

A physics-grounded, hypothesis-driven multi-agent discovery framework for
quantum-interference single-molecule electronics.

## Overview

This project is evolving from the legacy **SMEGen** closed-loop generative
pipeline into **SME-Agent**, an agentic molecular co-scientist that:

- reads literature and builds an auditable SME knowledge graph,
- formulates explicit quantum-transport hypotheses,
- designs molecules to test those hypotheses,
- critiques candidates before and after computational validation,
- validates prospectively through DFT+NEGF and EC-STMBJ experiments,
- explains successes and failures, and revises hypotheses.

## Documentation

| Document | Purpose |
|---|---|
| [`.claude/CLAUDE.md`](.claude/CLAUDE.md) | Full project specification, architecture, dataset strategy, and agent instructions |
| [`.claude/MEMORY.md`](.claude/MEMORY.md) | Project history, source documents, and 3-month research plan |
| [`docs/research-plan.html`](docs/research-plan.html) | Visual research plan |

## Legacy baseline

The original SMEGen pipeline (SMEMol dataset → LSTM generator → SMEPre
predictor → DFT+NEGF validation → EC-STMBJ experiment) is preserved as a
historical baseline. See `MEMORY.md` for legacy metrics and the rationale for
the rebuild.

## Quick start

```bash
# Install (when source code is available)
pip install -e .

# Audit the legacy dataset
python scripts/audit_dataset.py

# Run the evaluation harness
python scripts/run_benchmarks.py
```

## License

TBD
