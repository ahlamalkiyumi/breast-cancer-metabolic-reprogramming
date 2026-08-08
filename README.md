# breast-cancer-metabolic-reprogramming: A TCGA-BRCA RNA-seq Analysis

## Background

Cancer cells don't just grow uncontrollably, they often rewire how they
produce energy. Back in the 1920s, Otto Warburg noticed that tumor cells
tend to rely heavily on glycolysis (fermenting glucose) for energy, even
when there's plenty of oxygen around to use the more efficient
mitochondrial pathway instead. This shift, now known as the Warburg
effect, has been observed across many cancer types since. I wanted to
see if I could find this same pattern myself, starting from raw public
RNA-seq data, rather than just reading about it.

Metabolic health and mitochondrial function are areas I'm especially
interested in, so breast cancer where this kind of metabolic
rewiring is well documented but still an active area of research,
felt like a natural place to start applying that interest.

## The question

What metabolic changes occur in breast tumors compared to normal tissue, and which specific pathways are most strongly affected?

## Data


Source: TCGA-BRCA (The Cancer Genome Atlas, Breast Cancer cohort),
accessed via recount3
Samples: 1,109 primary tumor samples, 112 solid tissue normal samples
Genes: protein-coding genes only, filtered from the full annotation
No metastatic samples were included, this is a primary tumor vs.
matched normal tissue comparison only


## Method

1. QC included zero-expression gene removal, CPM-based low-expression filtering (chosen over a raw count cutoff due to variable library sizes), sample ID verification against clinical metadata, and PCA to confirm tumor/normal separation before statistical testing. Differential expression was run with PyDESeq2 (tumor vs. normal, normal as reference). Pathway enrichment was done two ways: over-representation analysis (gseapy/Enrichr) on significant genes, and rank-based GSEA (R, clusterProfiler + msigdbr) on the full ranked gene list by DESeq2 test statistic, avoiding an arbitrary significance cutoff. Mitochondrial pathway involvement was assessed using the MitoCarta3.0 gene inventory.

2. WGCNA (Weighted Gene Co-expression Network Analysis): The top 5,000 most variable protein-coding genes were used to construct a signed co-expression network (soft-thresholding power selected via scale-free topology fit), followed by hierarchical clustering to detect gene modules. Each module was functionally annotated using hypergeometric over-representation analysis against Hallmark and KEGG gene sets, and module eigengenes were correlated against tumor status and MitoCarta3.0-derived mitochondrial pathway scores to examine how co-expression structure relates to metabolic signal in the data.

4. Immune Signature Scoring (ICR): Samples were scored on the 20-gene Immunologic Constant of Rejection (ICR) signature and correlated against MitoCarta-derived mitochondrial fatty acid oxidation (FAO) scores within Basal-like tumors. To identify independent pathway associations, ICR and FAO genes were excluded from a separate ranked differential expression list (FAO-high vs. FAO-low, Wilcoxon rank-sum) before running preranked GSEA (Hallmark gene sets), avoiding circular enrichment from the scoring genes themselves. ESTIMATE-derived immune/stromal scores were used to confirm the ICR-FAO correlation was not driven by immune cell infiltration alone.

## What I found

The analysis revealed a profound metabolic and cellular state transition during tumorigenesis. Normal breast tissue-associated pathways involved in adipogenesis, PPAR signaling, lipid oxidation, and differentiated metabolic functions were markedly suppressed. In contrast, tumors showed activation of glycolysis, mTORC1 signaling, estrogen-responsive pathways, and strong E2F-driven cell-cycle programs. These changes indicate a shift from a differentiated lipid-metabolic phenotype toward a proliferative anabolic state supporting tumor growth. Additionally, increased extracellular matrix remodeling, interferon signaling, and DNA repair pathways suggest substantial tumor microenvironment remodeling, immune activation, and replication stress.

Using MitoCarta3.0, Breast cancer mitochondria display a shift from metabolically versatile organelles toward a remodeled, stress-adapted state, characterized by reduced catabolic metabolism and enhanced protein import, translation, and maintenance machinery.

By doing subtype stratification of mitochondrial pathways scores, Luminal B breast tumors exhibit coordinated upregulation of mitochondrial protein import, translation, and cristae organization, supporting enhanced oxidative phosphorylation and fatty acid oxidation, while simultaneously downregulating antioxidant and iron homeostasis pathways, suggesting a high-energy but potentially redox-vulnerable mitochondrial state.



## Tools used


Python: pandas, PyDESeq2, gseapy, matplotlib/seaborn, kruskal-wallis, dunn's test
R: clusterProfiler, msigdbr, ggplot2
other: MitoCarta3.0
Notebooks written in Quarto (.qmd), mixing both languages in a
single reproducible workflow
