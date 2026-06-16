# SMEMol Dataset Audit Report

**Date:** 2026-06-09  
**Dataset version:** 0.1.0  
**Audit type:** Initial structural audit from manuscript metadata

---

## Summary

This document records the findings of the initial audit of the SMEMol dataset,
performed as part of Month 1, Week 1–2 of the SME-Agent generative research plan.

**Key finding:** The SMEMol dataset is **not available as structured data** in
this repository. The original 171-molecule dataset was extracted from published
literature figures using PDFigCapX + DECIMER (optical chemical structure
recognition) in the legacy SMEGen project, whose codebase (`/Users/zliang/smegen`)
no longer exists. Only manuscript and SI DOCX files are available on disk.

---

## What IS available

| Item | Status | Source |
|------|--------|--------|
| Manuscript text | ✅ Available | `Manuscript-SMEGen_0324_LZ.docx` |
| Supplementary Information | ✅ Available | `SI-SMEGen_0323_LZ.docx`, `SI-SMEGen_0316_LZ.docx` |
| Figure PPTX | ✅ Available | `Figure.pptx` (WPS Sync) |
| Structured SMILES data | ❌ Missing | Not in repo or disk |
| Anchor group labels | 🟡 Partial | Only known for Gen_1–Gen_13 (all thiol-anchored) |
| DFT property values | 🟡 Partial | Aggregate statistics only (no per-molecule values) |
| Scaffold labels | ❌ Missing | Not assigned |
| Split assignments | 🟡 Partial | 3 molecules assigned to `test` tier |

## Known molecule IDs (26 total)

### Tier A — Original SMEMol literature molecules

| ID | Type | Notes |
|----|------|-------|
| SME_1 – SME_13 | 13 comparative reference molecules | Used as structural analogs for Gen_1–13 comparisons |

### Tier B — Legacy generated + DFT validated

| ID | Type | Cycle | Notes |
|----|------|-------|-------|
| Gen_1 – Gen_4 | 4 DQI-enhanced molecules | Cycle 1 | Gen_1 EC-STMBJ validated |
| Gen_5 – Gen_13 | 9 DQI-enhanced molecules | Cycle 2 | Gen_9 EC-STMBJ validated |

### Tier C — Experimental molecules

| ID | Type | Notes |
|----|------|-------|
| Gen_1, Gen_9, SME_1 | 3 EC-STMBJ validated | split=test |

## Known legacy SMEMol dataset statistics

| Metric | Value |
|--------|-------|
| Total literature-derived molecules | 171 |
| Evaluation set (literature + generated) | 338 |
| Cycle 1 DFT candidates | 89 |
| Cycle 1 DQI yield | 27 (30.3%) |
| Cycle 1 enhanced DQI | 4 |
| Cycle 2 DFT candidates | 77 |
| Cycle 2 DQI yield | 30 (39.0%) |
| Cycle 2 enhanced DQI | 9 |
| Combined DQI molecules | 57 |
| Total enhanced DQI | 13 |
| Scaffold 1 frequency | ~26.3% (meta-phenylene/mOPE) |
| Scaffold 2 frequency | ~13.7% |
| Generated unique scaffolds | 57.9% |

## SMEPre predictor performance

| Metric | Value |
|--------|-------|
| DQI AUC | 0.784 |
| DQI ACC | 0.765 |
| Seebeck RMSE | 0.325 |
| Regulation space RMSE | 2.15 |
| Regulation efficiency RMSE | 2.31 |

## Missing labels and gaps

1. **SMILES strings**: 0 of 171 molecules have SMILES in this repo
2. **SELFIES strings**: 0 of 171
3. **InChI keys**: 0 of 171
4. **Anchor group labels**: Only 13 Gen molecules confirmed as thiol-anchored
5. **Scaffold IDs**: None assigned
6. **Per-molecule DFT properties**: Only aggregate statistics available
7. **Provenance records**: No literature source mapping per molecule
8. **Analog series labels**: None assigned

## Recommended next steps

1. **Retrieve the original SMEMol CSV/data** from the SMEGen project authors
   (Xiamen University / Peking University)
2. **Re-extract SMILES** using chemical OCR (DECIMER, OSRA) on the SI figures
   which show the full molecule structures
3. **Manually curate** from literature references if automated OCR fails
4. **Populate** the standardized schema: SMILES → canonical SMILES → SELFIES →
   InChI key → anchor groups → scaffolds → data tiers → split assignments
5. **Re-run** `scripts/audit_dataset.py` to verify the complete standardized dataset

## Data quality checklist (for when SMILES arrive)

- [ ] All 171 SMILES parseable by RDKit
- [ ] All SMILES canonicalized (RDKit)
- [ ] SELFIES generated for all molecules
- [ ] InChI keys generated for all molecules
- [ ] No duplicate molecules (by InChI key)
- [ ] Anchor group labels assigned for all molecules
- [ ] Anchor group validity: exactly 2 valid anchor groups per molecule
- [ ] Scaffold IDs assigned (Bemis-Murcko or custom)
- [ ] Data tiers tagged (A/B/C)
- [ ] Split assignments: scaffold split preferred
- [ ] Provenance tracked: literature source DOI/PMID for each molecule
- [ ] DFT properties recorded for each molecule
- [ ] No train/test leakage (tier B molecules not in training set)
