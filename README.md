# 🌱 AI-Driven Gene & Trait Discovery in Faba Bean
### A Prototype Pipeline for the PhD Position at Prof. Agnieszka Golicz Lab
**Wageningen University & Research — Plant Breeding Chair Group**

[![Open Master Pipeline in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/YOUR_USERNAME/faba-bean-dtf-gwas/blob/main/notebooks/00_master_pipeline.ipynb)

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

**Method:** Per-SNP linear regression with population structure correction (top 3 PCs as covariates), Bonferroni multiple testing correction, followed by genomic prediction with Ridge Regression, Random Forest, and Gradient Boosting (5-fold cross-validation), and SHAP-based explainability.

**Key findings:**
- **9 SNPs** passed Bonferroni-corrected significance (α = 1.86 × 10⁻⁶), distributed across chromosomes 1a, 1b, 2, 3, 4, and 6
- **Chromosome 3** harbours 3 independent significant associations — consistent with conserved legume flowering-time loci potentially related to the FT pathway
- **Ridge Regression** achieved R² = 0.646 and Pearson r = 0.824, confirming that DTF variation is substantially explained by additive SNP effects
- **SHAP analysis** revealed that chromosome 5 SNPs dominate the ML importance ranking — a divergence from GWAS top hits that reflects non-linear signal captured only by the tree-based model
- **3 SNPs** appeared in the top-50 of both GWAS and SHAP rankings, representing the highest-confidence candidate loci
- Genomic inflation λ = 1.427 indicates that a kinship-matrix mixed model (e.g. GEMMA) will be needed for the full analysis — an important methodological finding from the prototype

### Module 2 — SNP Annotation & Biological Grounding 🔄 In Progress

SNP candidates from Module 1 are being mapped to the nearest annotated gene in the faba bean reference genome (Jayakodi, Golicz et al., Nature 2023) using a ±100 kb window, cross-referenced against known flowering pathway genes (FT, SOC1, FLC, TFL1, LFY, AP1), with GO-term enrichment analysis on SNP-proximal genes.

### Module 3 — eGWAS / RNA-seq Integration 🔄 In Progress

Vernalization RNA-seq data (PRJNA704197) is being integrated to identify differentially expressed genes between early- and late-flowering conditions, cross-reference GWAS candidate loci with expression changes, and build a multi-layer SNP → expression → phenotype evidence model — directly following the eQTL framework established in the Golicz lab's rapeseed pangenomics work (TAG, 2025).

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

**Option A — Full pipeline automatically (recommended):**
Open `00_master_pipeline.ipynb` → `Runtime → Run All`
All modules run in sequence. Final outputs: `master_report.html` + ZIP of all figures and results.

**Option B — Module by module (in order):**
```
01_gwas_genomic_prediction.ipynb  →  02_snp_annotation.ipynb  →  03_egwas_rnaseq.ipynb
```
Each notebook reads the CSV outputs of the previous one.

---

## Repository Structure

```
faba-bean-dtf-gwas/
├── notebooks/
│   ├── 00_master_pipeline.ipynb          ← Run All for full pipeline
│   ├── 01_gwas_genomic_prediction.ipynb
│   ├── 02_snp_annotation.ipynb
│   └── 03_egwas_rnaseq.ipynb
├── data/                                  ← Auto-downloaded by notebooks
├── results/                               ← CSV outputs
├── figures/                               ← PNG figures
├── docs/
│   └── project_rationale.md              ← Scientific rationale
├── requirements.txt
└── README.md
```

---

## Planned Extensions (PhD Roadmap)

1. Full-panel GWAS with all 539,643 SNPs and kinship-matrix mixed model
2. Real RNA-seq alignment pipeline (HISAT2 → featureCounts → DESeq2)
3. Single-cell RNA-seq integration for cell-type-specific eQTL mapping
4. Structural variant features following Golicz et al. (TAG, 2025) pangenome approach
5. Graph neural network for SNP → gene → trait relationship modelling
6. Multi-environment G×E interaction analysis

---

## References

- Bornhofen E. et al. (2024). ProFaba dataset. Zenodo. https://doi.org/10.5281/zenodo.13908861
- Jayakodi M., **Golicz A.A.** et al. (2023). The giant diploid faba bean genome. *Nature* 615, 652–659.
- **Golicz A.A.** et al. (2025). Structural variants in *B. napus* pangenomes affect eQTL analysis. *Theoretical and Applied Genetics*.
- Yuan X. et al. (2021). Vernalization-responsive transcriptomics in faba bean. *Front. Genetics* 12:656137.
- Lundberg S.M. & Lee S.I. (2017). A unified approach to interpreting model predictions. *NeurIPS* 30.

