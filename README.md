# AI-Driven Gene & Trait Discovery in Faba Bean
### A Prototype Pipeline for the PhD Position at Prof. Agnieszka Golicz Lab
**Wageningen University & Research — Plant Breeding Chair Group**

[![Open Module 1 in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ravzanury/Trials-for-the-AI-Driven-gene-and-trait-discovery-for-V.-faba-breeding/blob/main/faba_bean_DTF_project.ipynb)

---

## Why This Project?

This repository was built as a hands-on demonstration of the analytical skills and research vision relevant to the PhD position on *AI-Driven Gene and Trait Discovery in Plant Breeding*. Rather than describing what I would do, I wanted to show it using real, publicly available faba bean data.

The pipeline directly addresses three of the PhD's core duties:

| PhD Duty | Implementation here |
|----------|-------------------|
| Link genotypic and phenotypic data across scales | SNP matrix (539k markers) aligned to field phenotype per accession |
| Interpretable, biologically grounded AI models | Random Forest + SHAP explainability + SNP→gene annotation |
| Benchmark against established GWAS/eGWAS methods | Direct comparison of GWAS p-value rankings vs SHAP importance rankings |

---

## Trait Focus: Days to Flowering

Days to Flowering (DTF) was selected as the target trait because:
- It shows high broad-sense heritability in faba bean → strong, clean SNP signal
- Its genetic basis is partially known (FT, SOC1, TFL1 pathway homologs) → results are biologically verifiable
- A stable QTL has been identified across multiple European field seasons → robust to environment
- It is directly connected to the developmental biology studied in the Golicz lab's faba bean genome paper (Nature, 2023)

---

## Data

All data is real, publicly available, and downloaded automatically inside the notebooks — no manual setup required.

| Dataset | Source | Content |
|---------|--------|---------|
| ProFaba diversity panel | [Zenodo 10.5281/zenodo.13908861](https://doi.org/10.5281/zenodo.13908861) | 539,643 SNPs + 14 agronomic traits, multi-environment European trials |
| Faba bean reference genome | [NCBI GCA_921998355.2](https://www.ncbi.nlm.nih.gov/datasets/genome/GCA_921998355.2/) | Gene annotation for SNP→gene mapping (Jayakodi, Golicz et al., Nature 2023) |
| Vernalization RNA-seq | [NCBI PRJNA704197](https://www.ncbi.nlm.nih.gov/bioproject/PRJNA704197) | Flowering-responsive gene expression, faba bean buds (Yuan et al., 2021) |

---

## What Was Done and What Was Found

### Module 1 — GWAS & Genomic Prediction ✅ Complete

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ravzanury/Trials-for-the-AI-Driven-gene-and-trait-discovery-for-V.-faba-breeding/blob/main/faba_bean_DTF_project.ipynb)
&nbsp;|&nbsp; [Full analysis report (PDF)](docs/DTF_GWAS_FullReport.pdf)

**Method:** Per-SNP linear regression with population structure correction (top 3 PCs as covariates), Bonferroni multiple testing correction, followed by genomic prediction with Ridge Regression, Random Forest, and Gradient Boosting (5-fold cross-validation), and SHAP-based explainability.

**Key findings:**
- **9 SNPs** passed Bonferroni-corrected significance (α = 1.86 × 10⁻⁶), distributed across chromosomes 1a, 1b, 2, 3, 4, and 6
- **Chromosome 3** harbours 3 independent significant associations — consistent with conserved legume flowering-time loci potentially related to the FT pathway
- **Ridge Regression** achieved R² = 0.646 and Pearson r = 0.824, confirming that DTF variation is substantially explained by additive SNP effects
- **SHAP vs GWAS divergence:** chromosome 5 SNPs dominate the ML importance ranking despite not appearing among GWAS top hits. This likely reflects non-additive, epistatic signal that linear regression cannot capture — chromosome 5 in faba bean harbours a major photoperiod-response region (LD with *FUL*/*SOC1* homologs), and ensemble methods are sensitive to combinations of SNPs with individually sub-threshold effects. The 3 SNPs appearing in the top-50 of *both* GWAS and SHAP rankings represent the highest-confidence causal candidates
- Genomic inflation λ = 1.427 indicates that a kinship-matrix mixed model (e.g. GEMMA) will be needed for the full analysis — an important methodological finding from the prototype

### Module 2 — SNP Annotation & Biological Grounding ✅ Complete

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ravzanury/Trials-for-the-AI-Driven-gene-and-trait-discovery-for-V.-faba-breeding/blob/main/project2_snp_annotation.ipynb)

**Method:** SNP candidates from Module 1 are mapped to the nearest annotated gene in the faba bean reference genome (Jayakodi, Golicz et al., Nature 2023) using a ±100 kb window. Genes are cross-referenced against known flowering pathway genes (FT, SOC1, FLC, CO, TFL1, LFY, AP1, SVP). GO-term–style enrichment is computed via Fisher's exact test on functional descriptions, with Benjamini–Hochberg FDR correction. A SNP → Gene bipartite network is produced colour-coded by pathway category.

**Outputs:** `gwas_shap_annotated_SNPs.csv`, `go_enrichment_results.csv`, GO enrichment barplot, SNP→Gene network figure.

### Module 3 — eGWAS / RNA-seq Integration ✅ Complete

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ravzanury/Trials-for-the-AI-Driven-gene-and-trait-discovery-for-V.-faba-breeding/blob/main/project3_egwas_rnaseq.ipynb)

**Method:** Vernalization RNA-seq data (PRJNA704197, Yuan et al. 2021) is used to identify differentially expressed genes (DEGs) between vernalized and control faba bean buds (Mann-Whitney U, FDR < 0.05, |log2FC| > 1). DEGs are cross-referenced with GWAS candidate genes from Module 2. A Random Forest classifier trained on the top-500 variable genes uses SHAP to identify which genes most strongly distinguish flowering conditions. A multi-layer evidence score combines GWAS association, DEG significance, and expression SHAP to prioritise candidate genes. Full FASTQ-based pipeline code (HISAT2 → featureCounts → DESeq2) is provided as Track B for the complete PhD analysis.

**Outputs:** Expression PCA, volcano plot, SHAP expression importance figure, expression heatmap, `gwas_deg_overlap.csv`, `multi_layer_candidates.csv`.

---

## Pipeline Architecture

```
ProFaba SNPs (539k)    Days to Flowering     Vernalization RNA-seq
      +                  (phenotype)          (PRJNA704197)
Faba Bean Genome              |                      |
      |                       |                      |
      └──────────┬────────────┘                      |
                 │                                   |
    ┌────────────▼──────────────┐                    |
    │  Module 1: GWAS + ML      │                    |
    │  · Manhattan plot         │                    |
    │  · Genomic prediction     │                    |
    │  · SHAP explainability    │                    |
    │  · GWAS vs ML benchmark   │                    |
    └────────────┬──────────────┘                    |
                 │                                   |
    ┌────────────▼──────────────┐                    |
    │  Module 2: Annotation     │                    |
    │  · SNP → nearest gene     │                    |
    │  · Flowering gene flags   │                    |
    │  · GO enrichment          │                    |
    └────────────┬──────────────┘                    |
                 │                                   |
    ┌────────────▼───────────────────────────────────▼──┐
    │  Module 3: eGWAS / RNA-seq Integration             │
    │  · Differential expression (vernalized vs ctrl)    │
    │  · GWAS candidate × DEG overlap                   │
    │  · Multi-layer evidence scoring                    │
    └────────────────────────────────────────────────────┘
```

---

## How to Run

**Option A — Run each module in Colab (recommended):**

Run the notebooks in order. Each notebook reads the CSV outputs of the previous one.

```
faba_bean_DTF_project.ipynb  →  project2_snp_annotation.ipynb  →  project3_egwas_rnaseq.ipynb
```

**Option B — Local:**
```bash
pip install -r requirements.txt
jupyter notebook faba_bean_DTF_project.ipynb
```

---

## Repository Structure

```
Trials-for-the-AI-Driven.../
├── faba_bean_DTF_project.ipynb        ← Module 1: GWAS + ML (complete)
├── project2_snp_annotation.ipynb      ← Module 2: SNP annotation (complete)
├── project3_egwas_rnaseq.ipynb        ← Module 3: eGWAS / RNA-seq (complete)
├── docs/
│   ├── DTF_GWAS_FullReport.pdf        ← Full Module 1 analysis report
│   └── project_rationale.md           ← Scientific rationale
├── requirements.txt
└── README.md
```

---

## Research Directions (PhD Scope)

1. Full-panel GWAS with all 539,643 SNPs and kinship-matrix mixed model (GEMMA/BOLT-LMM)
2. Real RNA-seq alignment pipeline (HISAT2 → featureCounts → DESeq2) — Track B code in Module 3
3. Multi-environment G×E interaction analysis for DTF and other agronomic traits
4. Structural variant features following the Golicz et al. (TAG, 2025) pangenome approach
5. Integration of additional ProFaba traits (seed yield, plant height) into multi-trait models

---

## References

- Bornhofen E. et al. (2024). ProFaba dataset. Zenodo. https://doi.org/10.5281/zenodo.13908861
- Jayakodi M., **Golicz A.A.** et al. (2023). The giant diploid faba bean genome. *Nature* 615, 652–659.
- **Golicz A.A.** et al. (2025). Structural variants in *B. napus* pangenomes affect eQTL analysis. *Theoretical and Applied Genetics*.
- Yuan X. et al. (2021). Vernalization-responsive transcriptomics in faba bean. *Front. Genetics* 12:656137.
- Lundberg S.M. & Lee S.I. (2017). A unified approach to interpreting model predictions. *NeurIPS* 30.
