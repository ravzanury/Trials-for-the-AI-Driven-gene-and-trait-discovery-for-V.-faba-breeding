# 🌱 Faba Bean Days-to-Flowering: Genotype → Phenotype
### AI-Driven Gene & Trait Discovery in Plant Breeding
**PhD Project - Prof. Agnieszka Golicz Lab, Wageningen University & Research**

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/YOUR_USERNAME/faba-bean-dtf-gwas/blob/main/notebooks/faba_bean_DTF_project.ipynb)

---

## 🎯 Project Overview

This project links **real genotypic data** (539,643 SNP markers) to **phenotypic data** (Days to Flowering) across a diversity panel of faba bean (*Vicia faba* L.) accessions. It serves as the starter project for a PhD investigating AI-driven gene and trait discovery in plant breeding.

**Trait of focus:** Days to Flowering (DTF)
- High broad-sense heritability → strong, clean SNP signal
- Known candidate genes → results are biologically interpretable
- Multi-environment data → robust across locations and years
- Directly connected to Prof. Golicz's faba bean genome paper (Nature, 2023)

---

## 📦 Dataset

**ProFaba Diversity Panel** - Bornhofen et al. (2024)
> Zenodo DOI: [10.5281/zenodo.13908861](https://doi.org/10.5281/zenodo.13908861)

| File | Description | Size |
|------|-------------|------|
| `final_profaba_db.txt` | Phenotypic data - multiple traits across European environments | 2.8 MB |
| `ProFaba_chrAll_...vcf.gz` | 539,643 high-quality SNP markers (VCF format) | 32.8 MB |

> **No manual downloading needed.** The notebook downloads both files automatically from Zenodo.

---

## 🔬 Methods Pipeline

```
Real SNP Data (VCF)          Real Phenotype Data
     │                              │
     ▼                              ▼
  VCF Parsing               Days to Flowering
  QC & Filtering             (ProFaba DB)
     │                              │
     └──────────── Align ───────────┘
                       │
              Population Structure
                (PCA - 10 PCs)
                       │
           ┌───────────┴───────────┐
           ▼                       ▼
         GWAS                 Genomic Prediction
   (linear regression       (Ridge / Random Forest /
    + PC correction)         Gradient Boosting)
    Manhattan plot           5-fold Cross-Validation
    QQ plot                        │
           │                       ▼
           └──────── SHAP Explainability ────────┐
                     (feature importance,         │
                      direction of effects)       │
                                                  ▼
                                     GWAS vs ML Benchmarking
                                     (do both methods agree?)
```

---

## 📊 Outputs

| Output File | Description |
|-------------|-------------|
| `figures/01_phenotype_distribution.png` | DTF distribution and environment comparison |
| `figures/02_population_structure_PCA.png` | PCA coloured by DTF |
| `figures/03_manhattan_plot.png` | GWAS Manhattan plot across 6 chromosomes |
| `figures/04_qq_plot.png` | QQ plot with genomic inflation factor λ |
| `figures/05_prediction_accuracy.png` | Predicted vs actual DTF for all 3 models |
| `figures/06_shap_importance.png` | Top 20 SNPs by SHAP importance |
| `figures/07_shap_beeswarm.png` | Direction of SNP effects on DTF |
| `figures/08_gwas_vs_ml_benchmark.png` | GWAS rank vs ML rank comparison |
| `results/gwas_results_DTF.csv` | Full GWAS table with p-values |
| `results/shap_importance_DTF.csv` | SHAP importance scores per SNP |
| `results/model_performance_summary.csv` | R², Pearson r, RMSE for each model |
| `results/accession_predictions_DTF.csv` | Per-accession observed and predicted DTF |

---

## 🚀 How to Run

### Option 1 - Google Colab (recommended)
Click the **Open in Colab** badge above, or:
1. Go to [colab.research.google.com](https://colab.research.google.com)
2. File → Open notebook → GitHub tab
3. Paste this repo URL and select the notebook

### Option 2 - Local (requires ~4 GB RAM)
```bash
git clone [https://github.com/ravzanury/Trials-for-the-AI-Driven-gene-and-trait-discovery-for-V.-faba-breeding]
pip install -r requirements.txt
jupyter notebook notebooks/faba_bean_DTF_project.ipynb
```

---

## 🗂️ Repository Structure

```
faba-bean-dtf-gwas/
│
├── notebooks/
│   └── faba_bean_DTF_project.ipynb   # Main analysis notebook
│
├── data/                              # Downloaded data goes here (gitignored)
│
├── results/                           # CSV outputs from analysis
│
├── figures/                           # PNG figures from analysis
│
├── docs/
│   └── project_rationale.md          # Scientific background and PhD context
│
├── requirements.txt                   # Python dependencies
├── .gitignore
└── README.md
```

---

## 🔗 Key References

- **ProFaba dataset:** Bornhofen E. et al. (2024). Zenodo. https://doi.org/10.5281/zenodo.13908861
- **Faba bean reference genome:** Jayakodi M., **Golicz A.A.** et al. (2023). The giant diploid faba genome unlocks variation in a global protein crop. *Nature* 615, 652–659. https://doi.org/10.1038/s41586-023-05791-5
- **Flowering time GWAS:** Ohm H. et al. (2024). Novel SNP markers for flowering and seed quality traits in faba bean. *Frontiers in Plant Science* 15. https://doi.org/10.3389/fpls.2024.1348014
- **eQTL / structural variants:** Golicz A.A. et al. (2025). *Theoretical and Applied Genetics*.

---

## 📌 PhD Context

This starter project addresses three core PhD duties:

| Duty | How |
|------|-----|
| Link genotypic → phenotypic data across scales | SNP matrix aligned to field phenotype per accession |
| Interpretable, biologically grounded AI models | SHAP explainability on Random Forest |
| Benchmark against established methods (GWAS/eGWAS) | Direct GWAS vs ML SNP ranking comparison |

**Next steps in the PhD roadmap:**
1. Integrate transcriptomics (RNA-seq) as a third data layer
2. Single-cell omics → eQTL mapping at cell-type resolution
3. Graph neural network on gene–SNP–trait relationships
4. Pangenome structural variants as features (Golicz et al. 2025 approach)
5. Multi-environment G×E interaction modelling

---

*Wageningen University & Research - Plant Breeding Chair Group*
