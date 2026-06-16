# Legacy SMEGen Data Inventory

> Source: `/Volumes/SSD 2TB/smegen` (old SMEGen repo on external SSD)
> Extracted: 2026-06-15
> All files copied to `data/raw_legacy/` in this repository.

---

## 1. Core SMEMol Datasets (Transformer-M format)

These are the primary SME molecule datasets used to train the SMEPre (Transformer-M) predictor.

### 1.1 Regression: `my_reg.csv` (304 molecules)

- **Path:** `data/raw_legacy/transformer_m/my_reg.csv`
- **Source:** `Transformer-M/datasets/mydata/raw/my.csv`
- **Format:** `smiles,class`
- **Columns:**
  - `smiles` — molecular SMILES string
  - `class` — regression target (likely log-conductance or regulation range)
- **Note:** This is the main 304-molecule mixed set (171 literature + generated). Per CLAUDE.md, generated molecules are part of the active-learning history and must not be treated as independent test data.

### 1.2 Regression: `ss_reg.csv` (304 molecules)

- **Path:** `data/raw_legacy/transformer_m/ss_reg.csv`
- **Source:** `Transformer-M/datasets/mydata/raw/ss.csv`
- **Format:** `smiles,class`
- **Note:** Same 304 molecules, different target (likely Seebeck coefficient values). Values are continuous floats (e.g., 1.44, 2.49).

### 1.3 Regression: `h_reg.csv` (304 molecules)

- **Path:** `data/raw_legacy/transformer_m/h_reg.csv`
- **Source:** `Transformer-M/datasets/mydata/raw/h.csv`
- **Format:** `smiles,class`
- **Note:** Same 304 molecules, different target. Values include integers and floats (e.g., 1.2, 5.2). The `class` values overlap with `my_reg.csv` — this may be the same target in different units or a binning of the same data.

### 1.4 QI Classification: `qi_classification.csv` (7,110 molecules)

- **Path:** `data/raw_legacy/transformer_m/qi_classification.csv`
- **Source:** `Transformer-M/datasets/mydata_qi/raw/qi.csv`
- **Format:** `smiles,label`
- **Columns:**
  - `smiles` — molecular SMILES string
  - `label` — binary QI classification (0 or 1)
- **Note:** This is a large dataset — likely includes DFT-computed QI labels for generated/enumerated molecules, not just the 171 literature molecules.

### 1.5 QI Retrieval: `retrieval_qi.csv` (5,212 molecules)

- **Path:** `data/raw_legacy/transformer_m/retrieval_qi.csv`
- **Source:** `Transformer-M/retrieval_qi.csv`
- **Format:** `smiles,qi`
- **Note:** Another large QI dataset, possibly a different split or generation round.

---

## 2. Seebeck Regression Variants

### 2.1 Seebeck Full: `ss_seebeck.csv` (1,760 molecules)

- **Path:** `data/raw_legacy/transformer_m/ss_seebeck.csv`
- **Source:** `Transformer-M/datasets/mydata_ss/raw/my.csv`
- **Format:** `smiles,class`
- **Note:** Large Seebeck coefficient regression dataset. Values include large floats (e.g., 162.3, 13.2). Likely includes many generated molecules.

### 2.2 Seebeck PN: `ss_seebeck_pn.csv` (1,760 molecules)

- **Path:** `data/raw_legacy/transformer_m/ss_seebeck_pn.csv`
- **Source:** `Transformer-M/datasets/mydata_ss/raw/my_pn.csv`
- **Format:** `smiles,class`
- **Note:** Same size as ss_seebeck.csv. "PN" likely refers to p-type / n-type Seebeck coefficient classification or regression.

### 2.3 Seebeck Old: `ss_seebeck_old.csv` (46 molecules)

- **Path:** `data/raw_legacy/transformer_m/ss_seebeck_old.csv`
- **Source:** `Transformer-M/datasets/mydata_ss/raw/my_old.csv`
- **Format:** `smiles,class` (Windows line endings)
- **Note:** Small subset — likely the original experimental Seebeck data before expansion.

---

## 3. Other Dataset Variants

### 3.1 mydata_1: `mydata_1.csv` (1,962 molecules)

- **Path:** `data/raw_legacy/transformer_m/mydata_1.csv`
- **Source:** `Transformer-M/datasets/mydata_1/raw/my.csv`
- **Format:** `smiles,class`
- **Note:** Regression target with negative float values (e.g., -2.01). Likely Seebeck or another signed property.

### 3.2 QI Old: `qi_old.csv` (236 molecules)

- **Path:** `data/raw_legacy/transformer_m/qi_old.csv`
- **Source:** `Transformer-M/datasets/my_old/qi.csv`
- **Format:** `smiles,qi`
- **Note:** Older/smaller QI classification dataset (236 molecules vs 7,110 in the current version).

### 3.3 DPP: `dpp.csv` (38 molecules)

- **Path:** `data/raw_legacy/transformer_m/dpp.csv`
- **Source:** `Transformer-M/datasets/mydata_old/raw/dpp.csv`
- **Format:** `idx,smiles,homolumogap`
- **Note:** DPP (diketopyrrolopyrrole) series molecules with HOMO-LUMO gap labels. Per `data/read.txt`: indices 0-14 are DPP series.

### 3.4 S Subset: `s_subset.csv` (1,966 molecules)

- **Path:** `data/raw_legacy/transformer_m/s_subset.csv`
- **Source:** `Transformer-M/datasets/s/raw/my.csv`
- **Format:** `smiles,class`
- **Note:** Regression with small positive values (e.g., 1.04). "S" likely denotes a specific property subset.

### 3.5 SS Subset: `ss_subset.csv` (2,768 molecules)

- **Path:** `data/raw_legacy/transformer_m/ss_subset.csv`
- **Source:** `Transformer-M/datasets/ss/raw/my.csv`
- **Format:** `smiles,class`
- **Note:** Regression with negative values (e.g., -28.6). Large dataset — possibly Seebeck or conductance with expanded generated molecules.

---

## 4. Binned HOMO-LUMO Gap Datasets

These appear to be from a larger enumeration or DFT screening, binned by HOMO-LUMO gap.

| File | Rows | Gap Range |
|---|---:|---|
| `data500.csv` | 501 | gap ≈ 3–5 eV |
| `data500_1000.csv` | 501 | gap ≈ 3.5–5.4 eV |
| `data1000_1500.csv` | 501 | similar range |
| `data1500_2000.csv` | 501 | similar range |
| `data2000_5000.csv` | 3,001 | wider range |

All have format: `idx,smiles,homolumogap`

- **Path:** `data/raw_legacy/data{range}.csv`
- **Source:** `data/data{range}.csv`
- **Note:** These appear to be generated/enumerated molecules with computed HOMO-LUMO gaps. Not directly SME-specific but useful for pre-training or property prediction baselines.

---

## 5. SMEPre Prediction Logs

### 5.1 Test Predictions: `checkpoint_best_test_predictions.csv` (46 rows)

- **Path:** `data/raw_legacy/transformer_m/logs_reg_v1_seed2/checkpoint_best_test_predictions.csv`
- **Format:** `idx,y_true,y_pred`

### 5.2 Validation Predictions: `checkpoint_best_valid_predictions.csv` (46 rows)

- **Path:** `data/raw_legacy/transformer_m/logs_reg_v1_seed2/checkpoint_best_valid_predictions.csv`
- **Format:** `idx,y_true,y_pred`

**Training log notes:**
- Best validation MAE: 2.4167, RMSE: 3.0216
- Best test MAE: 2.6882, RMSE: 3.0499
- Train/valid/test split: 213/46/46 (from `train_log.txt` tensor indices)
- Split was random (not scaffold split) — a known methodological weakness

---

## 6. Data Key (from `data/read.txt`)

```
0-14    DPP series molecules
15-22   CPP series, [5-12] Cycloparaphenylene
23-29   CPP series, [6-12] chemACX
30-37   CPP series, [5-12] chemACX 3D optimized barrel structures
```

---

## 7. Summary Statistics

| Dataset | Molecules | Has SMILES | Target Type | Source Tier |
|---|---:|:---:|---|---|
| my_reg.csv | 303 | ✅ | Regression (conductance?) | A+B mixed |
| ss_reg.csv | 303 | ✅ | Regression (Seebeck?) | A+B mixed |
| h_reg.csv | 303 | ✅ | Regression (same as my?) | A+B mixed |
| qi_classification.csv | 7,110 | ✅ | Binary (DQI) | A+B+generated |
| retrieval_qi.csv | 5,212 | ✅ | Binary (QI) | A+B+generated |
| ss_seebeck.csv | 1,760 | ✅ | Regression (Seebeck) | Expanded |
| ss_seebeck_pn.csv | 1,760 | ✅ | Regression (Seebeck PN) | Expanded |
| ss_seebeck_old.csv | 46 | ✅ | Regression (Seebeck) | A (literature?) |
| mydata_1.csv | 1,962 | ✅ | Regression (Seebeck?) | Expanded |
| qi_old.csv | 236 | ✅ | Binary (QI) | A+B early |
| dpp.csv | 38 | ✅ | Regression (HOMO-LUMO gap) | A (DPP series) |
| s_subset.csv | 1,966 | ✅ | Regression | Expanded |
| ss_subset.csv | 2,768 | ✅ | Regression | Expanded |
| data500–5000 | 5,005 | ✅ | Regression (HOMO-LUMO gap) | Generated/enumerated |

**Total unique SMILES across 4 main datasets: ~13,400** (very low overlap — my_reg shares only 8 SMILES with qi_classification, 1 with retrieval_qi, and 13 with ss_seebeck).

**Quantitative analysis (rdkit-validated):**

| Property | my_reg (303 mol) | qi_classification (7,110 mol) |
|---|---|---|
| Valid SMILES | 303/303 (100%) | 7,110/7,110 (100%) |
| Unique canonical SMILES | 302 (1 duplicate) | 6,466 |
| MW range | 168–977 Da | 60–2,376 Da |
| MW mean | 390 Da | 379 Da |
| Heavy atoms range | 10–72 | 4–175 |
| Heavy atoms mean | 27.1 | 26.4 |
| Rings range | 0–10 | — |
| Rings mean | 3.3 | — |
| Unique scaffolds | 158 | — |
| Top scaffold | diphenylacetylene (42 mol) | — |

**Target variable distributions:**
- `my_reg.csv` (regression): range 0.40–13.40, mean 4.88, median 3.70, stdev 3.01
- `ss_reg.csv` (Seebeck regression): range 0.97–2.99, mean 1.79, median 1.71, stdev 0.43
- `qi_classification.csv` (DQI binary): 3,210 negative (45.1%), 3,900 positive (54.9%)

**Anchor groups in my_reg (303 molecules):**
- Thioether (-SMe): 100% (every molecule — Au-contact anchor)
- Alkyne (-C≡C-): 76.2% (conjugated bridge)
- Cyano (-CN): 17.5%
- Amine (-NH₂): 4.6%
- Hydroxyl (-OH): 4.6%
- Pyridine: 1.7%

**Anchor groups in qi_classification (7,110 molecules):**
- Alkyne (-C≡C-): 54.9%
- Pyridine: 21.5%
- Cyano (-CN): 9.3%
- Amine (-NH₂): 5.5%
- Other: 33.3%

---

## 8. Critical Findings for SME-Agent Migration

### 8.1 SMILES Data is Available ✅

The critical blocker — missing SMILES for the SMEMol molecules — can be resolved from these datasets. The 304-molecule `my_reg.csv` contains SMILES for the full mixed set (Tier A + Tier B). These SMILES need to be matched back to the 26 named molecules from the manuscript (SME_1–13, Gen_1–13).

### 8.2 Large QI Classification Datasets ✅

The qi_classification.csv (7,110 molecules) and retrieval_qi.csv (5,212 molecules) represent major QI-labeled datasets that were not previously available in the agentic-sme repo. These are critical for:
- Training the DQI classifier component of SMEPre
- Pre-training the generative model with QI-conditioned data
- Understanding the distribution of QI vs non-QI molecules

### 8.3 Seebeck Regression Data ✅

Multiple Seebeck regression datasets are available (46 to 1,760 molecules). The 46-molecule `ss_seebeck_old.csv` likely corresponds to the original experimental Seebeck data, while the larger sets include generated molecules.

### 8.4 Train/Test Split Issues ⚠️

The Transformer-M training log shows a random 70/15/15 split (213/46/46) over the mixed 304-molecule dataset. Per CLAUDE.md, this is a methodological weakness — generated molecules in the test set create leakage. The new project must use scaffold/source/time/prospective splits.

### 8.5 DPP and CPP Series ✅

The `dpp.csv` file and `data/read.txt` reveal DPP (diketopyrrolopyrrole) and CPP (cycloparaphenylene) series molecules with explicit scaffold labels. These are valuable for scaffold-based analysis and splits.

### 8.6 Missing from Old Repo ❌

The following were NOT found in the old SMEGen repo:
- Explicit mapping from molecule names (SME_1, Gen_1, etc.) to SMILES
- Explicit anchor group labels
- Scaffold ID assignments
- Provenance metadata (which molecules are literature vs generated)
- DFT protocol metadata
- Experimental EC-STMBJ data in structured form

These must be reconstructed from the manuscript DOCX and supplementary information.

---

## 9. Recommended Next Steps

1. **Match SMILES to molecule names** — Read the manuscript DOCX to extract the structure→name mapping, then match against `my_reg.csv` SMILES
2. **Reconstruct provenance** — Determine which of the 304 molecules are Tier A (literature) vs Tier B (generated) using the manuscript and training logs
3. **Validate SMILES** — Run `validate_smiles()` from `src/sme_agent/data/extract.py` on all legacy SMILES
4. **Assign anchor groups** — Parse anchor groups from SMILES using known anchor substructure patterns
5. **Create scaffold splits** — Use Bemis-Murcko scaffolds to create proper scaffold-based train/valid/test splits
6. **Build QI dataset** — Process `qi_classification.csv` as the primary DQI training dataset
7. **Process Seebeck data** — Merge and de-duplicate the Seebeck regression variants
8. **Document DFT protocol** — Extract computational details from the manuscript SI
