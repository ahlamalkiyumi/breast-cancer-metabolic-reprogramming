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

Do breast tumors show a measurable shift away from mitochondrial
energy production (oxidative phosphorylation, fatty acid metabolism)
and toward glycolysis, compared to normal breast tissue and can I
recover this signature independently, using two different statistical
methods, from real patient data?

## Data


Source: TCGA-BRCA (The Cancer Genome Atlas, Breast Cancer cohort),
accessed via recount3
Samples: 1,109 primary tumor samples, 112 solid tissue normal samples
Genes: protein-coding genes only, filtered from the full annotation
No metastatic samples were included, this is a primary tumor vs.
matched normal tissue comparison only


## Method, in plain terms


QC and filtering — checked for zero-expression genes, filtered
low-expression genes using a CPM-based threshold (rather than a raw
count cutoff, since library sizes vary a lot across this many
samples), confirmed sample IDs matched correctly between the
expression matrix and clinical metadata, and ran PCA as a sanity
check that tumor and normal samples actually separate from each
other before doing any statistical testing.
Differential expression used PyDESeq2 to compare tumor vs.
normal, with normal tissue set as the reference group so fold
changes read as "tumor relative to normal."
Pathway enrichment, done two ways:

A quick over-representation check (Python, gseapy/Enrichr) on
the significantly changed genes
A full rank-based GSEA (R, clusterProfiler + msigdbr) using
the entire tested gene list, ranked by DESeq2's test statistic, 
this doesn't depend on picking an arbitrary significance cutoff,
so it's the more rigorous of the two


I used both deliberately, so I could check whether a simpler method
and a more careful one actually agreed with each other, rather than
trusting a single approach.


## What I found

Both methods pointed the same direction:



## What's next

This is one part of a larger project. Still to come:




## Tools used


Python: pandas, PyDESeq2, gseapy, matplotlib/seaborn
R: clusterProfiler, msigdbr, ggplot2
Notebooks written in Quarto (.qmd), mixing both languages in a
single reproducible workflow
