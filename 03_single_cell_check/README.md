# breast-cancer-single-cell-analysis

## Single-cell check: where does the fatty acid oxidation (FAO) signal come from in breast tumors?

A follow-up to the TCGA-BRCA bulk RNA-seq analysis. The bulk data suggested a link between fatty acid oxidation (FAO) and immune infiltration in Basal-like breast tumors. Bulk RNA-seq averages over all cells in a tumor, so it cannot say which cells carry the FAO signal. This folder uses public single-cell data to ask that question directly.

## Background (from the bulk analysis)

In Basal-like TCGA-BRCA tumors, the FAO score correlated with M2 macrophage infiltration (rho = 0.24, padj = 0.0066) and CD8+ T cell infiltration (rho = 0.19, padj = 0.048), and with none of 8 other immune cell types. A possible artifact is that macrophages and T cells express FAO genes themselves, so more of them raises the bulk FAO score without any change in the tumor cells.

## Questions
Is the FAO program in breast cancer predominantly associated with malignant cells, and does it differ across subtypes?

### SQ1. 
Which cell populations have the strongest FAO scores: malignant cells, macrophages, other myeloid cells, or T cells?

### SQ2. 
Across patients, is higher malignant-cell FAO associated with differences in immune-cell abundance?


## Data
- Dataset: Wu et al. 2021 breast cancer single-cell atlas, GEO accession GSE176078 (processed single-cell matrix and metadata).
- Cohort: 26 primary tumors (ER+, HER2+, TNBC). Subtype labels are the clinical labels from the dataset, not PAM50.
- Annotations used: celltype_major, celltype_minor, subtype, orig.ident (patient).
- FAO gene set: the MitoCarta3.0 / MitoPathways FAO gene set used in the bulk analysis (43 genes found in this dataset).


## Methods
- Loaded the processed counts into AnnData (Scanpy), kept raw counts in a layer, then normalized (library-size to 10,000, log1p).
- Scored FAO per cell with scanpy.tl.score_genes (mean expression of the gene set relative to matched control genes; negative values mean below background).
- Basal-like check (4-patient analysis): summed raw counts of cancer epithelial cells per TNBC patient into a pseudobulk profile, converted to log2 CPM, and called PAM50 subtypes with genefu (pam50.robust, 50/50 genes found). Four of eight TNBC patients were called Basal-like.
- All-patient analysis: averaged FAO per patient and cell type (cell types with fewer than 20 cells in a patient excluded), so that each patient counts once and cells from the same patient are not treated as independent.
- Compared malignant-cell FAO across subtypes (Kruskal-Wallis) and correlated it with immune-cell abundance across patients (Spearman). Immune abundance was computed as a fraction of non-epithelial cells, so tumor purity does not drive it.

## Results
1. In four Basal-like tumors, FAO is highest in cancer cells
Mean FAO score per cell type. CID4515 and CID44971 are confident Basal-like calls; CID4465 and CID4495 are low confidence. Within myeloid and T cells (minor labels), macrophages score slightly above monocytes and DCs in three of four patients, and CD8+ T cells score slightly above CD4+ T cells, but both T cell groups are at or below background in the three patients with enough cells.
2. Across 26 patients, FAO is an epithelial program. Epithelial cells (normal and malignant) carry the FAO program. Immune and stromal cells are at or below background.
3. No detectable difference in malignant-cell FAO between subtypes. Kruskal-Wallis p = 0.92. With three HER2+ patients this is weak evidence, not evidence of no difference.
4. No detectable association between malignant FAO and immune abundance.

## Interpretation
- The FAO signal in the bulk data most likely comes from epithelial cells, not from macrophages or T cells. This argues against the composition artifact described above, since immune cells score at or below background.
- Normal epithelial cells score higher than malignant cells, so "FAO is a tumor-cell feature" would overstate this. The accurate statement is that FAO is an epithelial program that malignant cells retain in modest form.
- The bulk correlation between FAO and M2/CD8 infiltration is neither confirmed nor refuted here.

## Limitations
- Small cohort. Only 20 patients had enough cancer cells for the correlations. At n = 20, a correlation needs to be about 0.45 in size to reach p < 0.05, while the bulk effects were 0.19 and 0.24. The analysis could not have detected an effect of that size.
- Basal-like subset. Four patients, two of them low confidence. The Basal-like analysis shows the origin of the signal and does not test the bulk correlation.
- PAM50 on pseudobulk. Calls were made on only 8 samples, where gene centering is unstable. Two patients (CID4513, CID4523) were called LumB although their markers leaned Basal.
- No M2 label. The dataset annotates "Macrophage", which includes M1-like and M2-like cells.
- Different measures. Bulk used deconvolution estimates in 100+ tumors. Here abundance is a cell fraction from dissociated tissue, which depends on sample handling.
- Score size. FAO score differences are consistent in direction but small in absolute value.
- Subtype labels are clinical (ER+/HER2+/TNBC), not PAM50, and HER2+ has only three patients.


## Tools
- Python (Scanpy, AnnData, pandas, SciPy)
- R (genefu, org.Hs.eg.db)
