# SMEMol Dataset Protocol

**Version:** 0.1.0  
**Date:** 2026-06-09  

---

## 1. Purpose

This document defines the protocols for constructing, standardizing, auditing,
splitting, and versioning the SMEMol dataset used in the SME-Agent project.

## 2. Data sources

### 2.1 Tier A — Original SMEMol (171 literature molecules)

- **Source:** Meta-analysis of ~100 high-impact publications from Nature
  Materials, Nature Nanotechnology, JACS, Angew. Chem., ACS Nano, Nano Letters,
  J. Phys. Chem. C, Chem. Eur. J., and others.
- **Extraction method:** PDFigCapX (figure extraction) → DECIMER (OCR to SMILES)
- **Validation:** Manual QC of all SMILES. Redundant DFT+NEGF recalculation
  under a uniform protocol.
- **Properties per molecule:**
  - Canonical SMILES (RDKit)
  - SELFIES
  - InChI key
  - Anchor group type and positions
  - Scaffold ID (Bemis-Murcko)
  - DQI label (Yes/No)
  - Seebeck coefficient (μV/K, log-transformed)
  - Regulation range (log₁₀(G/G₀))
  - Regulation efficiency (V⁻¹)
  - SAscore
  - Literature DOI/PMID reference

### 2.2 Tier B — Legacy generated (166 molecules)

- **Source:** SMEGen LSTM generator, two cycles
- **Cycle 1:** 89 DFT+NEGF validated → 27 DQI, 4 enhanced
- **Cycle 2:** 77 DFT+NEGF validated → 30 DQI, 9 enhanced
- **Use:** Active-learning history, NOT for independent test sets.
- **Important:** These molecules are products of the SMEGen loop and must not
  be treated as independent external validation data.

### 2.3 Tier C — Experimental (3 molecules)

- **Source:** EC-STMBJ at Xiamen University
- **Molecules:** Gen_1 (naphthalene-core ON/OFF ≈ 50), Gen_9 (ON/OFF ≈ 25),
  SME_1 (reference, flat response)
- **Use:** Legacy experimental anchor; split=test

### 2.4 Tiers D–F — Future data

- **Tier D:** Newly mined literature (future expansion)
- **Tier E:** Newly generated prospective candidates (future active learning)
- **Tier F:** Locked prospective validation set (future DFT batch)

## 3. Standardization protocol

### 3.1 SMILES canonicalization

1. Parse with RDKit `Chem.MolFromSmiles()`
2. Reject if parse fails (mark as invalid)
3. Canonicalize with `Chem.MolToSmiles(mol, canonical=True, isomericSmiles=True)`
4. Generate SELFIES using the `selfies` library
5. Generate InChI key using RDKit `Chem.MolToInchiKey()`

### 3.2 Duplicate detection

- **Primary key:** InChI key (most robust)
- **Fallback:** Canonical SMILES
- **Action:** Mark duplicates, flag for manual review; do not silently remove

### 3.3 Deduplication rules

1. If two records have identical InChI keys but different properties, flag
   for manual review and keep both with a provenance note.
2. If two records have identical InChI keys and consistent metadata, keep
   one record and document the duplicate in the audit trail.
3. Generated molecules (Tier B) that are identical to a literature molecule
   (Tier A) should be noted but not removed — they provide active-learning
   signal.

## 4. Split protocol

### 4.1 Primary split: Scaffold-based

1. Compute Bemis-Murcko scaffolds for all molecules.
2. Assign scaffolds to train/val/test at an 80/10/10 ratio.
3. Ensure all molecules sharing a scaffold are in the same split.
4. Record split assignment in the `split_assignment` field.

### 4.2 Fallback: Random split

- Use only for ablation studies, not for main results.
- Use a fixed seed (e.g., 42) for reproducibility.

### 4.3 Leakage prevention

1. **No Tier B in test:** Generated molecules must not appear in test splits.
2. **Scaffold separation:** All molecules sharing a scaffold co-split.
3. **Split provenance:** Record the split method and random seed in metadata.
4. **Locked test set (Tier F):** Never used for any training decision.

## 5. Anchor group annotation

### 5.1 Known anchor groups

| Anchor | SMARTS pattern (approximate) | Notes |
|--------|----------------------------|-------|
| Thiol | `[SH]` or `[S]` attached to aromatic | Most common in SME |
| Amino | `[NH2]` on aromatic | Common amine anchor |
| Pyridyl | `n` in aromatic ring | Pyridine N anchor |
| Cyano | `C#N` | Nitrile anchor |
| Isocyano | `[N+]#[C-]` | Isocyanide |
| Methylthio | `SC` | -SMe anchor |
| Carboxyl | `C(=O)O` | -COOH anchor |

### 5.2 Validation rules

1. Each molecule must have exactly **2** anchor groups.
2. Anchor groups must be terminal (at the ends of the molecular backbone).
3. Both anchor groups must be of known types.
4. Molecules without valid anchor pairs are flagged for manual review.

## 6. Computational protocol

### 6.1 DFT+NEGF workflow (from manuscript)

- **Software:** TranSIESTA (next-generation)
- **Electrode:** Au(111) surface with Au–S coordination
- **Properties computed:** Transmission spectrum, Seebeck coefficient,
  regulation (gate) range, regulation efficiency
- **DQI definition:** Presence of a transmission dip (anti-resonance) in
  the transmission spectrum near the Fermi level

*Note: Full protocol details (functional, basis set, pseudopotential,
electrode model, Au–anchor geometry, conformer sampling, Fermi-level alignment,
convergence thresholds) are not specified in the manuscript and need to be
documented as a project output.*

## 7. File format

### 7.1 Standardized CSV

Output file: `data/processed/smemol_standardized.csv`

Must contain all 28 fields defined in `MoleculeRecord` schema
(`src/sme_agent/data/schema.py`).

### 7.2 Audit trail

Audit results stored as JSON in `data/audit_trails/dataset_audit.json`.
Human-readable summary in `docs/dataset_audit.md`.

## 8. Version history

| Version | Date | Changes |
|---------|------|---------|
| 0.1.0 | 2026-06-09 | Initial scaffold: known molecule IDs, metadata extracted from manuscript |
| 1.0.0 | TBD | First complete standardized dataset after SMILES ingestion |
