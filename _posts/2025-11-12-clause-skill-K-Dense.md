# Single-Cell RNA-seq Analysis using K-dense open source Claude skill.

K-Dense is multi-agent AI research system which can coordinate specialized agents that plan experiments, review literature, design analyses, execute code in secure sandboxes, and generate publication-ready reports. 
Achieved 29.2% accuracy on BixBench (the bioinformatics benchmark), outperforming GPT-5 (22.9%), GPT-4o (18%), and Claude 3.5 Sonnet (18%), there is no benchmark of 4.5 Sonnet I can find. 

---

## 📋 Executive Summary

I only need to press a tons of yes to create a whole single-cell RNA-seq analysis pipeline. Which includes 10 python scripts, one bash master script, one python dependency file and several documentation files.  

It seems provides all the methods used and even the citations of the methods, besides, list the Thresholds that used and which can be customized as needed. The only thing needs to be done is run the script and trouble shooting when the pipelines fails. This is definitely making the start point for analysis much faster if the direction is correct.  Will updates with real test results next week.     

### Analysis Scripts (10 Python Files)

| Script | Purpose | Key Outputs |
|--------|---------|-------------|
| **01_load_and_qc.py** | Quality control and filtering | Filtered dataset, QC plots |
| **02_doublet_removal.py** | Doublet detection (Scrublet) | Cleaned dataset, doublet plots |
| **03_preprocessing_and_clustering.py** | Normalization, PCA, UMAP, Leiden clustering | Processed dataset, UMAP plots |
| **04_cell_type_annotation.py** | Cell type identification | Annotated dataset, marker plots |
| **05_cellxgene_integration.py** | Public data integration | Integrated dataset, batch correction |
| **06_differential_expression.py** | DE analysis (Wilcoxon) | DE results, volcano plots, heatmaps |
| **07_grn_inference.py** | Gene regulatory networks (GRNBoost2) | TF-target networks, network graphs |
| **08_pathway_enrichment.py** | GO/KEGG/Reactome enrichment | Pathway results, enrichment plots |
| **09_opentargets_analysis.py** | Therapeutic target identification | Druggable targets, priority lists |
| **10_generate_report.py** | Report generation | README.md, PDF report |

**Total Lines of Code:** ~1,500+ lines of well-documented Python code

### Execution Scripts

- **run_complete_analysis.sh** - Master bash script to run all 10 steps sequentially
- **requirements.txt** - Complete list of Python dependencies

### Documentation Files

| File | Description | Pages/Size |
|------|-------------|------------|
| **QUICKSTART.md** | Quick start guide with installation and usage | 7.5 KB |
| **PIPELINE_OVERVIEW.md** | Visual pipeline architecture and overview | ~500 lines |
| **README.md** | Auto-generated analysis report | Generated at runtime |

---


#### Quality Control Thresholds
```python
min_genes_per_cell = 200
max_genes_per_cell = 6000
max_mitochondrial_pct = 15%
min_cells_per_gene = 3
doublet_rate = 6% (expected)
```

#### Preprocessing Settings
```python
normalization = "size_factor" (target_sum=10,000)
transformation = "log1p"
highly_variable_genes = 2000
pca_components = 50
umap_neighbors = 15
leiden_resolutions = [0.5, 0.8, 1.0]
```

#### Statistical Thresholds
```python
de_test = "wilcoxon"
significance_threshold = 0.05 (adjusted p-value)
fold_change_threshold = 0.5 (log2FC)
pathway_fdr = 0.05
```

---

```

**Estimated time:** 40-110 minutes (depending on dataset size)

```


## 🎓 Scientific Methods

### Cell Type Markers Used

The pipeline identifies the following PBMC cell types:

| Cell Type | Canonical Markers |
|-----------|-------------------|
| **CD4+ T cells** | IL7R, CD4, CD3D, CD3E |
| **CD8+ T cells** | CD8A, CD8B, CD3D, CD3E |
| **B cells** | MS4A1 (CD20), CD79A, CD79B, CD19 |
| **NK cells** | GNLY, NKG7, NCAM1 (CD56), KLRD1, KLRF1 |
| **CD14+ Monocytes** | CD14, LYZ, S100A8, S100A9 |
| **FCGR3A+ Monocytes** | FCGR3A (CD16), MS4A7, LYZ |
| **Dendritic cells** | FCER1A, CST3, CLEC10A |
| **Platelets** | PPBP, PF4, GNG11 |

### Statistical Methods

1. **Quality Control:** MAD-based filtering on QC metrics
2. **Doublet Detection:** Scrublet (simulated doublets comparison)
3. **Normalization:** Size-factor normalization (library size)
4. **Feature Selection:** Highly variable genes (mean-variance relationship)
5. **Dimensionality Reduction:** PCA → UMAP
6. **Clustering:** Leiden algorithm (graph-based community detection)
7. **Differential Expression:** Wilcoxon rank-sum test (non-parametric)
8. **GRN Inference:** GRNBoost2 (gradient boosting regression)
9. **Pathway Enrichment:** Hypergeometric test with FDR correction
10. **Batch Correction:** Harmony (linear correction) or BBKNN (graph-based)

---


### Customization
- 📝 Adjust QC thresholds in `01_load_and_qc.py`
- 📝 Modify clustering resolution in `03_preprocessing_and_clustering.py`
- 📝 Change DE thresholds in `06_differential_expression.py`
- 📝 Update marker genes in `04_cell_type_annotation.py`

---



## 📚 References & Citations

### Primary Methods

1. **Scanpy Framework**
   - Wolf, F.A., et al. (2018). "SCANPY: large-scale single-cell gene expression data analysis." *Genome Biology* 19:15.

2. **Doublet Detection**
   - Wolock, S.L., et al. (2019). "Scrublet: Computational Identification of Cell Doublets in Single-Cell Transcriptomic Data." *Cell Systems* 8(4):281-291.

3. **Gene Regulatory Networks**
   - Moerman, T., et al. (2019). "GRNBoost2 and Arboreto: efficient and scalable inference of gene regulatory networks." *Bioinformatics* 35(12):2159-2161.

4. **Leiden Clustering**
   - Traag, V.A., et al. (2019). "From Louvain to Leiden: guaranteeing well-connected communities." *Scientific Reports* 9:5233.

5. **Open Targets Platform**
   - Ochoa, D., et al. (2021). "Open Targets Platform: supporting systematic drug-target identification and prioritisation." *Nucleic Acids Research* 49(D1):D1302-D1310.

6. **CellxGene Census**
   - Chan Zuckerberg Initiative. (2023). "CellxGene Census: A versioned container of single-cell data."

### Supporting Tools

- **AnnData:** Virshup, I., et al. (2021). bioRxiv.
- **UMAP:** McInnes, L., et al. (2018). arXiv:1802.03426.
- **Harmony:** Korsunsky, I., et al. (2019). *Nature Methods* 16:1289-1296.
- **GSEApy:** Zhu, F., et al. (2020). *Bioinformatics* 36(15):4390-4392.
- **KEGG:** Kanehisa, M., et al. (2021). *Nucleic Acids Research* 49(D1):D545-D551.
- **Reactome:** Jassal, B., et al. (2020). *Nucleic Acids Research* 48(D1):D498-D503.


---


