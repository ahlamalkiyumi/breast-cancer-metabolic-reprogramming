# breast-cancer-metabolic-reprogramming: A TCGA-BRCA RNA-seq Analysis

## Background

Cancer cells don't just grow uncontrollably—they often rewire how they produce and use energy. Back in the 1920s, Otto Warburg noticed that tumor cells tend to rely heavily on glycolysis (fermenting glucose) for energy, even when there's plenty of oxygen available for the more efficient mitochondrial pathway instead. This shift, now known as the **Warburg effect**, has been observed across many cancer types since.

But cancer metabolism isn't simply a switch from glycolysis to mitochondrial respiration. Tumor cells can rewire multiple metabolic pathways depending on their needs and environment. I wanted to see if I could uncover some of these metabolic changes myself, starting from raw public RNA-seq data rather than just reading about them.

Metabolic health and mitochondrial function are areas I'm especially interested in, so **breast cancer**, where metabolic rewiring is well documented but still an active area of research, felt like a natural place to start. As I explored the data, my focus expanded from metabolism alone to asking how these metabolic states might relate to other features of the tumor, particularly its immune state and molecular subtype.


## The question

How is metabolism rewired in breast cancer, and which metabolic pathways are most strongly altered compared with normal breast tissue?

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

The co-expression network resolved into distinct modules aligned with proliferation (cell cycle genes), hormone signaling (estrogen response), immune activity, and stromal/ECM remodeling — showing that metabolic signal in this cohort is not an isolated program but distributed across multiple co-expression modules. Module-trait correlation showed proliferation and hormone-signaling modules each had a real, moderate association with mitochondrial pathway scores.

In Basal-like TCGA-BRCA tumors, a mitochondrial fatty acid oxidation (FAO) score built from MitoCarta3.0 correlated positively with the ICR immune signature (Spearman ρ=0.31, p<0.0001), and this relationship held after controlling for immune/stromal infiltration (ESTIMATE ImmuneScore), suggesting it isn't simply explained by immune cell composition. A non-circular GSEA (excluding both the ICR and FAO gene sets from the ranking) showed FAO-high Basal tumors carry a coherent interferon/antigen-presentation and oxidative metabolism signature, while FAO-low tumors trend toward hypoxia, glycolysis, angiogenesis, and EMT — with zero gene overlap confirming the pattern isn't an artifact of the scoring itself.


## Tools used

- Python: pandas, PyDESeq2, gseapy, matplotlib/seaborn, kruskal-wallis, dunn's test, pingouin, scipy
- R: clusterProfiler, msigdbr, ggplot2, WGCNA, estimate
- other: MitoCarta3.0, ICR 20-gene signature
  
Notebooks written in Quarto (.qmd), mixing both languages in a
single reproducible workflow
