# Project Rationale & Scientific Background

## Why Days to Flowering?

Days to Flowering (DTF) was selected as the focus trait for this starter project based on four converging criteria:

### 1. Data Quality — High Heritability
DTF shows high broad-sense heritability in faba bean field trials, meaning a large fraction of phenotypic variation is genetically controlled. This produces a strong, interpretable SNP signal — ideal for validating the analysis pipeline on real data.

### 2. Known Biology — Results are Verifiable
Three GWAS markers for DTF have been linked to genes with homologs in other plant species:
- Dolichol kinase
- Polyadenylate binding protein interacting protein
- Chromatin-remodelling ATPase INO

When the ML model rediscovers SNPs near these genes, it confirms biological validity — the core standard for "biologically grounded AI."

### 3. Connection to Golicz Lab Research
Prof. Golicz co-led the landmark faba bean reference genome paper (Nature, 2023), which studied developmental traits including seed size and flowering-related timing. Her rapeseed pangenomics work (TAG, 2025) specifically investigated how structural variants affect gene expression in flowering-related pathways. DTF is at the heart of her research program.

### 4. Multi-Environment Robustness
A stable QTL for flowering time was identified in the CGN panel (245 accessions) consistently across two growing seasons in the Netherlands — meaning findings are not artefacts of a single environment.

---

## Dataset: ProFaba Diversity Panel

**Citation:** Bornhofen E. et al. (2024). Genotypic and phenotypic data for the ProFaba diverse panel of faba bean accessions. Zenodo. https://doi.org/10.5281/zenodo.13908861

**Scale:**
- 539,643 high-quality SNP markers
- Multiple European environments (Denmark, Germany, France, Spain, Ireland, UK)
- 14 complex traits including yield, DTF, plant height, seed weight, protein content

**Quality filters applied to VCF:**
- QUAL ≥ 20, MQ ≥ 20
- At least 2 SNPs per region
- Missingness ≤ 20%
- Heterozygosity ≤ 10%
- Minor allele frequency ≥ 0.05

---

## Methods Rationale

### Why PCA for population structure correction?
Faba bean is a partially outcrossing species with accessions from diverse geographic origins. Without correcting for ancestry, GWAS will detect SNPs that differ between populations but are not causally related to DTF — false positives. The top PCs capture the major axes of population differentiation.

### Why Ridge Regression as a baseline?
Ridge Regression (L2 penalised linear model) is mathematically equivalent to GBLUP (Genomic Best Linear Unbiased Prediction), the gold standard in plant and animal breeding. Using it as the baseline ensures our ML models are compared against the most relevant existing method.

### Why Random Forest + SHAP for interpretability?
Random Forest captures non-linear SNP×SNP interactions that linear GWAS misses. SHAP provides game-theoretic, locally accurate feature attributions — the current best practice for interpreting ML models in genomics. This directly addresses the "interpretable and biologically grounded AI" requirement.

### Why compare GWAS vs ML top SNPs?
Overlap between GWAS hits and SHAP-top SNPs provides triangulated evidence: two independent statistical approaches pointing to the same genomic region is much stronger evidence of a true effect than either method alone. This is the methodological benchmarking core of the PhD.

---

## Connection to PhD Duties

| PhD Duty | Implementation in this project |
|----------|-------------------------------|
| Design methods to link genotypic and phenotypic data | VCF parsing → SNP matrix → phenotype alignment |
| Interpretable, biologically grounded AI models | Random Forest + SHAP with biological annotation of top SNPs |
| Study genetic architecture of complex traits | Manhattan plot, effect sizes, explained variance |
| Benchmark against GWAS/eGWAS | Direct rank comparison of GWAS p-values vs SHAP importance |

---

## Planned Extensions (PhD Roadmap)

1. **Transcriptomics integration** — Add RNA-seq gene expression as features alongside SNPs
2. **eQTL mapping** — Which SNPs regulate which genes? (Golicz rapeseed 2025 approach in faba bean)
3. **Single-cell resolution** — Cell-type-specific eQTL maps using scRNA-seq
4. **Graph neural network** — Model SNP → gene → trait as a biological network
5. **Pangenome features** — Structural variants from long-read sequencing as additional inputs
6. **Multi-environment modelling** — G×E interaction, stability analysis, environment prediction
