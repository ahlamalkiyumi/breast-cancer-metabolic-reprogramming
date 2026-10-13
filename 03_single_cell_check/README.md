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
