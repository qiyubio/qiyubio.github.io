# Single-Cell QC Analysis Summary from claude skill

## Overview
Claude release several MCPs and one skill for life science in https://github.com/anthropics/life-sciences/tree/main. 
I gave the skill a try and one of 3 inputs worked, summarizes the issues I had and lessons learned:

### QC Pipeline

**Skill:** `single-cell-rna-qc` from life-sciences plugin marketplace
**Python Environment:** System Python 3.9.6 

### Dependencies

- anndata
- scanpy
- scipy
- matplotlib
- seaborn
- numpy
- h5py

## Summary Table

| Sample | File Size | Cells | Status | Reason |
|--------|-----------|-------|--------|--------|
| 1M_neurons_filtered_gene_bc_matrices_h5.h5 | 3.9 GB | ~1,000,000 | ❌ Failed | Out of memory (insufficient RAM) |
| pbmcs_stim.h5 | 13 MB | Unknown | ❌ Failed | Incompatible file format (non-standard h5 structure) |
| raw_feature_bc_matrix.h5 | 7.6 MB | 180,440 | ✅ Success | Processed successfully, 84.7% cells retained |

---

## First there is python conflicts, will skill support container in the future? 

### Python Environment Conflicts (Pixi vs System Python)

The system had two Python environments installed:
1. **Pixi Python 3.14** (located at `/Users/nobody/.pixi/bin/python3`)
2. **System Python 3.9.6** (located at `/usr/bin/python3`)

When the default `python3` command was used, it resolved to the Pixi environment (Python 3.14), which created several issues:

#### Issues Identified

**1. Package Installation Confusion**
- Running `pip3 install` installed packages to the System Python 3.9 user directory (`/Users/nobody/Library/Python/3.9/lib/python/site-packages`)
- However, running `python3` or `python3 script.py` used Pixi Python 3.14
- This created a mismatch: packages were installed in one Python but scripts ran with another


**2. Incompatible Dependencies**
Python 3.14 was too new for some critical dependencies:
```
RuntimeError: Cannot install on Python version 3.14.0; only versions >=3.10,<3.14 are supported.
```
The `numba` package (required by scanpy) doesn't support Python 3.14 yet, making the Pixi environment unusable for this analysis.

#### The Solution

The issue was resolved by explicitly using the system Python:

## Claude do give alternative solution too:
#### Alternative Solutions

If the Pixi Python environment was preferred, these approaches could have been used:

1. **Create a Pixi environment with Python 3.12:**
   ```bash
   pixi init --python 3.12
   pixi add python=3.12 anndata scanpy scipy matplotlib seaborn
   ```
---
## Then I tested three samples
## Sample 1: 1M Neurons Dataset

**File:** `1M_neurons_filtered_gene_bc_matrices_h5.h5`
**Location:** `/Users/nobody/Downloads/1M_neurons_filtered_gene_bc_matrices_h5.h5`
**File Size:** 3.9 GB
**Dataset:** ~1,000,000 cells

### Status: FAILED ❌

### Reason for Failure

**Memory limitations** - The dataset was too large to process on the available system. Multiple attempts were made with different strategies:

1. **Standard QC pipeline** - Failed due to out of memory (OOM), process killed with exit code 137
2. **Subsampling to 100,000 cells** - Still exceeded available memory during data loading
3. **Subsampling to 10,000 cells** - Memory exhausted even with smaller subsample
4. **Ultra memory-efficient approach** (direct h5py reading with selective loading) - Still ran out of memory

The issue is that even loading the 3.9GB h5 file structure into memory to extract a subsample requires more RAM than available on the system. The sparse matrix decompression and data structures consumed all available memory before subsampling could occur.

---

## Sample 2: PBMCs Stimulated Dataset

**File:** `pbmcs_stim.h5`
**Location:** `/Users/nobody/Downloads/pbmcs_stim.h5`
**File Size:** 13 MB

### Status: FAILED ❌

### Reason for Failure

**Incompatible file format** - The file structure was not in standard 10X Genomics h5 format that scanpy expects.

---

## Sample 3: Raw Feature-Barcode Matrix

**File:** `raw_feature_bc_matrix.h5`
**Location:** `/Users/nobody/Downloads/28414916/raw_feature_bc_matrix.h5`
**File Size:** 7.6 MB

### Status: SUCCESS ✅

### QC Results

**Dataset Overview:**
- Original: **180,440 cells × 54,950 genes**
- After filtering: **152,805 cells × 1,987 genes**
- Cell retention: **84.7%**
- Gene retention: **3.6%**

**QC Metrics Identified:**
- 13 mitochondrial genes (pattern: `^mt-|^MT-`)
- 116 ribosomal genes (pattern: `^Rp[sl]|^RP[SL]`)
- 12 hemoglobin genes (pattern: `^Hb[^(p)]|^HB[^(P)]`)

**Filtering Strategy:**
- MAD-based outlier detection:
  - Total counts: 5 MADs
  - Genes detected: 5 MADs
  - Mitochondrial %: 3 MADs
- Hard mitochondrial threshold: 8%
- Gene filtering: Genes detected in <20 cells removed

**Cells Filtered:**
- Low counts outliers: 16,514 cells (9.15%)
- Low genes outliers: 15,731 cells (8.72%)
- High mitochondrial content: 13,665 cells (7.57%)
- **Total removed: 27,635 cells (15.32%)**

**Quality Observations:**
- Dataset had very low sequencing depth (median: 2 counts/cell)
- Many empty droplets or low-quality cells present
- Most cells detected very few genes (median: 2 genes/cell)
- Aggressive gene filtering due to low detection rates

### Output Files

**Location:** `/Users/nobody/.claude/plugins/marketplaces/life-sciences/single-cell-rna-qc/raw_feature_bc_matrix_qc_results/`

1. `qc_metrics_before_filtering.png` - Pre-filtering QC visualizations
2. `qc_filtering_thresholds.png` - MAD-based threshold boundaries
3. `qc_metrics_after_filtering.png` - Post-filtering quality metrics
4. `raw_feature_bc_matrix_filtered.h5ad` - Clean filtered dataset (15 MB)
5. `raw_feature_bc_matrix_with_qc.h5ad` - Original data with QC annotations (49 MB)

---
### Pros and Cons

1. Claude will write codes to fix the issues, even though they did not solve the issue, but the code is reuseable in the future. 
2. Claude will give alternative solutions, although might not be the best one. 
3. The qc step is the very basic step for single cell analysis, and sometimes the filtered inputs already included QC, so how widely it will be used is a question. 
4. Will Claude skills handle more complex situations such as alignment to genome references (computationally intensive, and large reference genome are needed)? 
5. The Python environment conflict (Pixi vs system Python) encountered during this analysis is **not an isolated incident** but reflects a common challenge, so would be nice to be able to add container in the game.
6. **IMPORTANT:** All numerical results, filtering decisions, and QC metrics reported in this analysis should be considered **preliminary and require expert validation**.

 
