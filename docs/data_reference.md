# Data Reference

> Describes every dataset referenced by [README.md](../README.md) and its current location in this repository.
> Generated: 2026-06-15

---

## Overview

The README references the following data artifacts:

| Artifact | README reference | Status |
|---|---|---|
| SMEMol dataset (171 molecules) | Phase 0 — seed dataset | ✅ SMILES available in legacy CSVs; not yet standardized |
| SMEPre predictor training data | Phase 2 — predictor ensemble | ✅ Raw CSVs exist under `data/raw_legacy/transformer_m/` |
| LSTM generator training data | Legacy baseline | ✅ Same CSVs as above |
| DFT+NEGF validated molecules | Phase 5 — active-learning loop | ⚠️ DFT labels exist but protocol metadata is absent |
| EC-STMBJ experimental data | Phase 5 — prospective validation | ❌ Not in structured form (only manuscript DOCX) |
| DiGress + TransformerConv training | Phase 3 — graph diffusion | ❌ Not yet implemented |

All raw data currently lives under `data/raw_legacy/`, copied from the original SMEGen repository (`/Volumes/SSD 2TB/smegen`). No data requires access to the external SSD.

---

## 1. Core SMEMol Mixed Set (Tier A + B)

Three CSV files contain the **same 303 molecules** (171 literature + 132 legacy-generated), each with a different property target.

| File | Path | Format | Target | Range | Mean |
|---|---|---|---|---|---|
| `my_reg.csv` | `data/raw_legacy/transformer_m/my_reg.csv` | `smiles,class` | Conductance / regulation range | [0.40, 13.40] | 4.88 |
| `ss_reg.csv` | `data/raw_legacy/transformer_m/ss_reg.csv` | `smiles,class` | Seebeck coefficient (log) | [0.97, 2.99] | 1.79 |
| `h_reg.csv` | `data/raw_legacy/transformer_m/h_reg.csv` | `smiles,class` | **Identical to my_reg.csv** (same SMILES, same values) | [0.40, 13.40] | 4.88 |

- **303 unique SMILES** (no duplicates within each file).
- **Tier composition:** 171 Tier A (literature) + 132 Tier B (legacy-generated). These are **not** separated in the CSV — provenance must be reconstructed from the manuscript.
- **Known issue:** The original SMEGen training used a random 70/15/15 split over this mixed set (213/46/46), which creates train/test leakage from generated molecules.

---

## 2. QI Classification Datasets

Large-scale binary DQI (destructive quantum interference) labels for training the DQI classifier.

| File | Path | Format | Rows | Unique SMILES | Positive rate |
|---|---|---|---:|---:|---:|
| `qi_classification.csv` | `data/raw_legacy/transformer_m/qi_classification.csv` | `smiles,qi` | 7,110 | 6,466 | 54.9% |
| `retrieval_qi.csv` | `data/raw_legacy/transformer_m/retrieval_qi.csv` | `smiles,label` | 5,212 | 5,212 | 45.1% |
| `qi_old.csv` | `data/raw_legacy/transformer_m/qi_old.csv` | `smiles,qi` | 236 | 235 | — |

- `qi_classification.csv` is the primary DQI training set. Contains generated/enumerated molecules with DFT-computed QI labels.
- `retrieval_qi.csv` is a separate QI dataset, possibly from a different generation round or split. Uses `label` instead of `qi` as the column name.
- `qi_old.csv` is the earlier, smaller version. Only 12 SMILES overlap with `qi_classification.csv`.
- **Overlap:** `qi_classification` ∩ `retrieval_qi` = 23 SMILES; `qi_classification` ∩ `ss_seebeck` = 144 SMILES.

---

## 3. Seebeck Regression Datasets

Seebeck coefficient regression targets at various scales.

| File | Path | Format | Rows | Unique SMILES | Range | Mean |
|---|---|---|---:|---:|---|---|
| `ss_seebeck.csv` | `data/raw_legacy/transformer_m/ss_seebeck.csv` | `smiles,class` | 1,761 | 1,608 | [3.93, 262.12] | 35.44 |
| `ss_seebeck_pn.csv` | `data/raw_legacy/transformer_m/ss_seebeck_pn.csv` | `smiles,class` | 1,761 | — | — | — |
| `ss_seebeck_old.csv` | `data/raw_legacy/transformer_m/ss_seebeck_old.csv` | `smiles,class` | 46 | — | [-262.12, 162.31] | -3.89 |

- `ss_seebeck.csv` is the expanded Seebeck dataset (likely includes many generated molecules).
- `ss_seebeck_pn.csv` has the same row count; "PN" likely refers to p-type / n-type classification.
- `ss_seebeck_old.csv` (46 molecules) likely corresponds to the original experimental Seebeck measurements. Contains signed values (positive = p-type, negative = n-type).

---

## 4. Other Regression Subsets

| File | Path | Format | Rows | Notes |
|---|---|---|---:|---|
| `mydata_1.csv` | `data/raw_legacy/transformer_m/mydata_1.csv` | `smiles,class` | 1,962 | Negative float values — likely signed Seebeck or conductance |
| `s_subset.csv` | `data/raw_legacy/transformer_m/s_subset.csv` | `smiles,class` | 1,966 | Small positive values (~1.04) |
| `ss_subset.csv` | `data/raw_legacy/transformer_m/ss_subset.csv` | `smiles,class` | 2,768 | Negative values (e.g., -28.6) — possibly expanded Seebeck or conductance |

---

## 5. DPP and CPP Series

| File | Path | Format | Rows | Notes |
|---|---|---|---:|---|
| `dpp.csv` | `data/raw_legacy/transformer_m/dpp.csv` | `idx,smiles,homolumogap` | 38 | Diketopyrrolopyrrole series + HOMO-LUMO gap |

Per `data/raw_legacy/data_read_key.txt`, indices map to:

| Index range | Series |
|---|---|
| 0–14 | DPP series molecules |
| 15–22 | CPP series, [5–12] Cycloparaphenylene |
| 23–29 | CPP series, [6–12] chemACX |
| 30–37 | CPP series, [5–12] chemACX 3D-optimized barrel structures |

---

## 6. HOMO-LUMO Gap Enumeration Datasets

Generated/enumerated molecules binned by HOMO-LUMO gap, stored under `data/raw_legacy/`.

| File | Rows | Gap range |
|---|---:|---|
| `data500.csv` | 501 | ~3–5 eV |
| `data500_1000.csv` | 500 | ~3.5–5.4 eV |
| `data1000_1500.csv` | 500 | similar range |
| `data1500_2000.csv` | 500 | similar range |
| `data2000_5000.csv` | 3,001 | wider range |

All have format: `idx,smiles,homolumogap` (or unnamed index column). These are not SME-specific but useful for pre-training or property prediction baselines.

---

## 7. SMEPre Prediction Logs

Best-model predictions from the Transformer-M predictor training run.

| File | Path | Format | Rows |
|---|---|---|---:|
| `checkpoint_best_valid_predictions.csv` | `data/raw_legacy/transformer_m/logs_reg_v1_seed2/` | `idx,y_true,y_pred` | 46 |
| `checkpoint_best_test_predictions.csv` | `data/raw_legacy/transformer_m/logs_reg_v1_seed2/` | `idx,y_true,y_pred` | 46 |

Training log summary:
- Best validation MAE: 2.4167, RMSE: 3.0216
- Best test MAE: 2.6882, RMSE: 3.0499
- Split: 213 train / 46 valid / 46 test (random split — **not scaffold split**)

---

## 8. Processed Data (in this repo)

| File | Path | Rows | Status |
|---|---|---:|---|
| `smemol_extracted.csv` | `data/processed/smemol_extracted.csv` | 26 | Named molecule IDs only — **no SMILES** (all chemical fields blank) |
| `smemol_standardized.csv` | `data/processed/smemol_standardized.csv` | 0 | **Empty** — ingest script has not been run successfully yet |
| `dataset_audit.json` | `data/audit_trails/dataset_audit.json` | — | Audit output from `audit_dataset.py` |

---

## 9. Data Not in This Repository

| Data | Location | Status |
|---|---|---|
| EC-STMBJ experimental measurements | Manuscript DOCX (figures) | ❌ Not in structured form |
| DFT+NEGF protocol metadata | Manuscript DOCX | ❌ Functional, basis set, electrode model not documented in structured form |
| Molecule name → SMILES mapping | Manuscript DOCX (SI figures) | ❌ Must be reconstructed from manuscript or original SMEGen project |
| Tier A / Tier B provenance labels | Not available | ❌ Must be reconstructed from manuscript |
| Anchor group labels | Not available | ⚠️ Can be inferred from SMILES via substructure matching |

---

## 10. Summary Statistics

### Cross-dataset SMILES overlap

```
my_reg ∩ qi_classification:  8 SMILES
my_reg ∩ retrieval_qi:       1 SMILES
my_reg ∩ ss_seebeck:        13 SMILES
qi_classification ∩ retrieval_qi:  23 SMILES
qi_classification ∩ ss_seebeck:   144 SMILES
retrieval_qi ∩ ss_seebeck:         2 SMILES
```

**Total unique SMILES across 4 main datasets:** ~13,400

### Duplicate note

`h_reg.csv` is **byte-identical** to `my_reg.csv` (same 303 SMILES, same target values). It is a duplicate and should not be treated as an independent dataset.

---

## 11. Data Inventory by README Phase

| README Phase | Data needed | Available? | Location |
|---|---|---|---|
| Phase 0 — Scaffold & data | SMEMol SMILES + metadata | ✅ Partial | `data/raw_legacy/transformer_m/my_reg.csv` (SMILES), `data/processed/smemol_extracted.csv` (IDs only) |
| Phase 1 — Baselines | Training SMILES for generators | ✅ | `data/raw_legacy/transformer_m/` (multiple files) |
| Phase 2 — SMEPre predictor | QI labels + regression targets | ✅ | `qi_classification.csv`, `my_reg.csv`, `ss_seebeck.csv` |
| Phase 3 — Graph diffusion | Training SMILES + conditioning labels | ✅ | Same as above |
| Phase 4 — Evaluation harness | Benchmark candidate sets | ❌ | Not yet generated |
| Phase 5 — Active learning | DFT+NEGF results, candidate selections | ❌ | Not yet run |

---

## 12. Known Issues

1. **No provenance separation** — the 303-molecule mixed set blends Tier A and Tier B without labels.
2. **No molecule name → SMILES mapping** — Gen_1, SME_1, etc. cannot be matched to their SMILES.
3. **Random split used historically** — the original SMEGen used random 70/15/15 over mixed data; scaffold splits are required.
4. **h_reg.csv is a duplicate** of my_reg.csv.
5. **Column name inconsistency** — `qi` vs `label` vs `class` for the target across files.
6. **DFT protocol unrecorded** — functional, basis set, pseudopotential, electrode model, convergence thresholds are not in structured form.
7. **ss_seebeck_old.csv has Windows line endings** — may cause parsing issues.
8. **Ingest script produced empty output** — `smemol_standardized.csv` has header only; the script needs debugging or re-running.
