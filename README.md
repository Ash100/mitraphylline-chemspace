# Mitraphylline Chemical Space Explorer

**A scalable, open-source framework for natural-product chemical space exploration and reference-guided analogue prioritization. Applied to the oxindole alkaloid mitraphylline across the full COCONUT database (705,603 unique compounds).**

![Chemical space analysis of the COCONUT natural product database, colored by biosynthetic pathway](figures/fig6_umap_chemical_space.png)
<p align="center"><em>UMAP projection of COCONUT chemical space (30,000-compound stratified subsample), colored by NPClassifier biosynthetic pathway.</em></p>

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/YOUR_USERNAME/YOUR_REPO_NAME/blob/main/notebook/mitraphylline_coconut_chemical_space.ipynb)
![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Python](https://img.shields.io/badge/python-3.10%2B-blue.svg)

---

## Overview

This repository contains a fully reproducible cheminformatics pipeline that:

1. **Cleans and deduplicates** the entire [COCONUT](https://coconut.naturalproducts.net/) natural-product database (715,822 raw records → 705,603 unique, validated structures) using memory-safe, chunked processing.
2. **Fingerprints** every compound (Morgan/ECFP4, radius 2, 2048-bit) and computes Bemis–Murcko scaffolds.
3. **Characterizes** the physicochemical and biosynthetic landscape of the library (property distributions, drug-likeness filters, Kruskal–Wallis pathway comparisons, scaffold diversity).
4. **Maps global chemical space** via incremental PCA (full dataset) and UMAP (stratified subsample).
5. **Performs reference-guided analogue prioritization** around the oxindole alkaloid **mitraphylline** — ranking the entire library by Tanimoto similarity, statistically contextualizing hits against the full similarity distribution, and separating trivial stereoisomeric duplicates from genuinely novel structural analogues.

The entire pipeline was validated end-to-end on a **single-core, 4 GB RAM machine**, using checkpointed/resumable batch processing — no specialized computing infrastructure required.

## Key finding

A naive nearest-neighbor search around mitraphylline returns its own natural diastereomers (pteropodine, isopteropodine, speciophylline, uncarine A/F, isomitraphylline) at Tanimoto = 1.0, since standard circular fingerprints don't encode stereochemistry. This repository's analysis explicitly separates these **exact stereoisomers (Tier A)** from **genuinely distinct structural analogues (Tier B)** — surfacing real candidates like mitraphyllic acid, vineridine, and vinerine at >99.99th percentile similarity.

| | Tier A: Nearest neighbors | Tier B: Distinct analogues |
|---|---|---|
| ![Stereoisomers](figures/fig8_mitraphylline_neighbors_grid.png) | Mitraphylline's known diastereomer family (Tanimoto = 1.0) | |
| ![Distinct analogues](figures/fig8b_mitraphylline_distinct_analogues_grid.png) | | Mitraphyllic acid, vineridine, vinerine, and related structures (Tanimoto 0.80–0.82) |

## Repository structure

```
.
├── README.md
├── LICENSE
├── notebook/
│   └── mitraphylline_coconut_chemical_space.ipynb   # fully executed, Colab-ready
├── figures/                                          # all figures, 300 DPI PNG
│   ├── fig1_property_distributions.png
│   ├── fig2_correlation_heatmap.png
│   ├── fig3_pathway_boxplots.png
│   ├── fig4_sugar_scaffold_summary.png
│   ├── fig5_pca_scree.png
│   ├── fig6_umap_chemical_space.png
│   ├── fig7_umap_properties.png
│   ├── fig8_mitraphylline_neighbors_grid.png
│   ├── fig8b_mitraphylline_distinct_analogues_grid.png
│   ├── fig9_mitraphylline_similarity_ranking.png
│   ├── fig9b_mitraphylline_distinct_analogues_ranking.png
│   └── fig10_mitraphylline_similarity_distribution.png
└── results/                                          # CSV tables (descriptive stats, rankings, etc.)
    ├── table1_descriptive_stats.csv
    ├── lipinski_veber_compliance.csv
    ├── kruskal_wallis_pathway_tests.csv
    ├── mitraphylline_top10_neighbors.csv
    ├── mitraphylline_top10_distinct_analogues.csv
    └── ...
```

## Getting started

### Run in Google Colab (recommended)
Click the "Open in Colab" badge above. The notebook will prompt you to upload the COCONUT CSV export on first run (or auto-detect it if already cached in the working directory).

### Run locally
```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
cd YOUR_REPO_NAME
pip install rdkit umap-learn scikit-learn pandas numpy scipy matplotlib seaborn
jupyter notebook notebook/mitraphylline_coconut_chemical_space.ipynb
```

Download the raw COCONUT CSV export from [coconut.naturalproducts.net](https://coconut.naturalproducts.net/) and place it at `notebook/coconut_project/data/raw/coconut_csv-12-2025.zip` before running, or use the in-notebook upload cell.

**Expected runtime from a clean state:** ~35–45 minutes on constrained hardware (1 vCPU / 4 GB RAM); considerably faster on a standard machine or Colab runtime.

## Methodology highlights

- **Memory-safe by design:** chunked CSV streaming, bit-packed fingerprint storage with memory-mapped access, and incremental PCA (`partial_fit`) mean peak RAM usage stays constant regardless of dataset size.
- **Checkpointed & resumable:** long-running steps (PCA fit/transform) persist progress every 50,000 compounds, so interrupted runs resume without recomputation.
- **Statistically grounded similarity search:** analogue hits are reported with percentile rank and z-score against the *complete* empirical similarity distribution, rather than an arbitrary fixed Tanimoto threshold.
- **Reproducible:** all random operations (subsampling, UMAP) use a fixed seed (42).

## Data source

[Sorokina, M., Merseburger, P., Rajan, K., Yirik, M.A., Steinbeck, C. (2021). COCONUT online: Collection of Open Natural Products database. *Journal of Cheminformatics*, 13, 2.](https://doi.org/10.1186/s13321-020-00478-9)

## Citation

If you use this pipeline or notebook in your own work, please cite:

```bibtex
@misc{mitraphylline_chemspace_2026,
  title  = {A Scalable, Generalizable, and Reproducible Open-Source Framework for
            Natural-Product Chemical Space Exploration and Analogue Prioritization:
            A Reference-Guided Case Study on the Oxindole Alkaloid Mitraphylline},
  author = {YOUR_NAME},
  year   = {2026},
  url    = {https://github.com/YOUR_USERNAME/YOUR_REPO_NAME}
}
```

## License

This project is released under the [MIT License](LICENSE). The COCONUT database itself is subject to its own licensing terms — see [coconut.naturalproducts.net](https://coconut.naturalproducts.net/) for details.

## Contact

Questions or suggestions? Open an issue or reach out at YOUR_EMAIL.
