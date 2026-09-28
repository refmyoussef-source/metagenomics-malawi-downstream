# Metagenomics Downstream Analysis

🔗 **[View live report](https://refmyoussef-source.github.io/metagenomics-malawi-downstream/)**

**Diversity analysis of the gut microbiome in Malawian adults exposed to antibiotics**

---

## Overview

This repository contains the **downstream analysis** of a pilot metagenomics study investigating the **bystander effect of antimicrobial use on the gut microbiome** in Malawian adults.

We analyze 24 stool and rectal swab samples collected from **6 patients across 4 timepoints** (pre-, during-, and post-antibiotic exposure), using deep shotgun metagenomic sequencing.

The raw sequencing data was processed with an upstream pipeline (fastp → Kraken2 → Bracken). **This repository focuses on the downstream diversity analysis in R.**

---

## Study Background

Antibiotic treatment for sepsis has an unintended consequence: it exerts a **bystander effect** on the gut microbiome, altering bacterial composition and the resistome. Understanding these dynamics is critical for antimicrobial stewardship, especially in low-income settings where antimicrobial resistance is disproportionately prevalent.

This pilot project is part of a larger study conducted in **Blantyre, Malawi**, aiming to quantify these changes using longitudinal sampling and Bayesian modelling.

## Data

### Samples

| Item | Details |
|---|---|
| **Cohort** | 6 Malawian adults |
| **Samples** | 24 (paired-end shotgun metagenomics) |
| **Timepoints** | 4 per patient (v0 to v4, days 0–196) |
| **Sample types** | 21 stool, 3 rectal swabs |
| **Sequencing** | Illumina, ~10 Gbp/sample |

### Input files

- `data/metadata.csv` — Sample metadata (patient ID, visit, time, sample type, clinical variables)
- `data/bracken/` — 24 Bracken output files (`*.bracken`), one per sample

Each `.bracken` file contains species-level abundance estimates produced by Bracken (after Kraken2 classification).

## Methods

### Upstream pipeline (not included)

| Step | Tool | Purpose |
|---|---|---|
| 1. Download | SRA Toolkit | Retrieve raw reads from NCBI (BioProject PRJNA869071) |
| 2. QC & Trimming | fastp v1.3.7 | Adapter removal, quality filtering (Q20, min length 50 bp) |
| 3. Taxonomic Profiling | Kraken2 v2.1.2 | Classify reads against Standard database |
| 4. Abundance Correction | Bracken v3.0.1 | Bayesian re-estimation of species abundances (k=150, level=Species) |

### Downstream analysis (this repository)

All downstream analysis is implemented in **`scripts/analysis.Rmd`** using R:

- **Community matrix construction** — samples × species matrix from Bracken `new_est_reads`
- **Alpha diversity** — Observed richness, Shannon, Simpson, Chao1 (`vegan`)
- **Beta diversity** — Bray-Curtis distance + PCoA ordination (`vegan`)
- **Statistical testing** — PERMANOVA (`adonis2`) to compare visit vs patient effects
- **Visualization** — Boxplots, time-trajectories, PCoA plots (`ggplot2`)

## Key Results

### Alpha diversity decreases over time

Shannon diversity declined from a median of **~4.4 at baseline (v0)** to **~2.8 at the final visit (v4)**. Five out of six patients showed a decrease over time.

### Patient effect dominates over visit effect

| Factor | R² (%) | p-value |
|---|---|---|
| Visit | 6.4 | 0.084 (n.s.) |
| Patient | 30.3 | 0.002 (**) |

PERMANOVA shows that **host-specific factors** explain ~5× more variance than antibiotic-driven temporal shifts in this pilot. This is consistent with the known inter-individual variability of the gut microbiome.

### Interpretation

The strong patient effect suggests that factors specific to each individual — such as diet, lifestyle, genetics, and other unmeasured host variables — shape the microbiome more than short-term antibiotic exposure in a small pilot cohort. A larger sample size (as in the full Malawi cohort, n = 425) is needed to detect significant antibiotic effects.

## Repository Structure

```
metagenomics-malawi-downstream/
│
├── README.md
├── .gitignore
│
├── data/
│   ├── metadata.csv              # Sample metadata
│   └── bracken/                  # 24 Bracken output files
│
├── scripts/
│   └── analysis.Rmd              # Main downstream analysis
│
├── results/
│   ├── analysis_report.html      # Rendered report (figures + code)
│   ├── alpha_diversity.csv       # Alpha diversity metrics per sample
│   ├── beta_diversity_pcoa.csv   # PCoA coordinates
│   └── permanova_summary.csv     # PERMANOVA results
│
└── docs/
    └── interpretation.md         # Extended interpretation of results
```

## How to Reproduce

1. **Clone the repository**
   ```bash
   git clone https://github.com/<your-username>/metagenomics-malawi-downstream.git
   cd metagenomics-malawi-downstream
   ```

2. **Open the R Markdown file**
   - Open `scripts/analysis.Rmd` in RStudio

3. **Install required packages** (if not already installed)
   ```r
   install.packages(c("tidyverse", "vegan", "ggplot2"))
   BiocManager::install("phyloseq")
   ```

4. **Knit the document**
   - Click the **Knit** button in RStudio
   - This will regenerate `analysis_report.html` and all figures

---

## Requirements

- **R** ≥ 4.3
- **RStudio** (recommended)
- R packages:
  - `tidyverse`
  - `vegan`
  - `ggplot2`
  - `phyloseq` (optional, from Bioconductor)
```

---
## Author

**Youssef**  
Bioinformatics / Metagenomics analysis

---

## Acknowledgments

- Original study: *Quantifying the bystander effect of antimicrobial use on the diversity and resistome of the gut microbiome in Malawian adults* (Nature Communications, 2025)
- Data source: NCBI BioProject [PRJNA869071](https://www.ncbi.nlm.nih.gov/bioproject/PRJNA869071)
- Tools: fastp, Kraken2, Bracken, vegan, phyloseq

---

## License

This project is for educational and portfolio purposes. The original data is publicly available via NCBI.
