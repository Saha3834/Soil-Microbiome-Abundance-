# A Leakage-Aware Soil Microbial Abundance Benchmark

This repository contains a reproducible machine learning benchmark for predicting soil microbial abundance from integrated ecological data. The project focuses on **leakage-aware feature governance** and **spatial/temporal transfer evaluation**, rather than only reporting random train-test performance.

The central research question is:

> How can an integrated soil–microbe dataset be structured as a leakage-aware benchmark for evaluating microbial abundance prediction under spatial and temporal transfer?

## Project overview

The notebook builds a model-ready benchmark dataset from an integrated soil–microbe table and evaluates multiple tabular regression models under four validation settings:

| Component | Description |
|---|---|
| Target | `log_meanCopyNumber = log1p(meanCopyNumber)` |
| Dataset size | 47,435 observations |
| Subgroups | Fungi: 22,776 rows; Bacteria/Archaea: 24,659 rows |
| Predictors | 20 approved ecological predictors |
| Model families | Linear, bagging ensembles, boosting models, stacking ensemble |
| Validation | Random split, 5-fold CV, GroupKFold by site, time-aware holdout |
| Main metrics | R², MAE, RMSE, site generalisation gap, temporal generalisation gap |
| Interpretability | SHAP analysis for fungi-specific XGBoost |

## Why this benchmark matters

Random splits can overestimate ecological model performance because soil samples from the same site, plot, or time period may be highly related. This project therefore compares ordinary interpolation performance with stricter transfer-aware validation.

The benchmark shows that advanced models can improve random and K-fold performance, especially for fungi and bacteria/archaea subgroup tasks. However, performance drops under site-aware and time-aware validation, showing that high random-split accuracy does not necessarily mean ecological generalisation.

## Repository structure

```text
.
├── code/
│   └── Soil_Microbe_leakage_aware_benchmark.ipynb
├── docs/
│   ├── NOTEBOOK_WALKTHROUGH.md
│   └── REPRODUCIBILITY.md
├── README.md
├── requirements.txt
├── environment.yml
├── .gitignore
```

## Notebook workflow

The notebook follows this pipeline:

1. Install optional packages if missing.
2. Import libraries and set reproducibility seeds.
3. Load the integrated soil–microbe dataset.
4. Audit dataset shape, missing values, and column inventory.
5. Create the log-transformed microbial abundance target.
6. Remove leakage-prone variables and create temporal features.
7. Define the approved ecological feature set.
8. Identify site and year variables for transfer-aware validation.
9. Define benchmark models, including a leakage-safe stacking ensemble.
10. Run global benchmark across eight model families.
11. Generate global model ranking and transfer-gap summaries.
12. Run XGBoost feature-block ablation.
13. Prepare fungi and bacteria/archaea subgroup datasets.
14. Run subgroup benchmark using advanced models.
15. Combine global and subgroup benchmark summaries.
16. Run SHAP analysis for the fungi XGBoost model.
17. Export tables and figures for reporting.

A full cell-by-cell explanation is available in [`docs/NOTEBOOK_WALKTHROUGH.md`](docs/NOTEBOOK_WALKTHROUGH.md).

## Models included

The global benchmark compares:

- Ridge Regression
- ElasticNet
- Random Forest
- Extra Trees
- XGBoost
- LightGBM
- CatBoost
- Stacking Ensemble

The stacking ensemble is fitted inside the validation pipeline to avoid leakage. Its meta-learner is trained only using training data within each split, not using outer validation or test data.

## Key findings from the executed notebook

### Global task

The Stacking Ensemble achieved the strongest random and K-fold performance, but it performed poorly under site-aware and time-aware validation. CatBoost was generally more transfer-stable, although its transfer score was still negative.

| Dataset | Best interpolation model | Main transfer observation |
|---|---|---|
| Global | Stacking Ensemble | CatBoost had the least negative mean transfer R² |
| Fungi | Stacking Ensemble | CatBoost was more transfer-stable than stacking |
| Bacteria/Archaea | Stacking Ensemble | CatBoost had the least severe transfer degradation |

### Main interpretation

> Stronger models improve interpolation, but they do not automatically solve ecological transfer.

## Main outputs

The notebook exports outputs to:

```text
outputs_leakage_aware_extended_benchmark/
├── tables/
├── figures/
└── models/
```

Important outputs include:

- `dataset_summary.csv`
- `column_inventory.csv`
- `benchmark_model_list.csv`
- `global_extended_benchmark_results.csv`
- `global_generalisation_gaps.csv`
- `feature_block_ablation.csv`
- `subgroup_extended_benchmark_results.csv`
- `subgroup_generalisation_gaps.csv`
- `combined_global_subgroup_summary.csv`
- `fungi_xgboost_shap_importance.csv`
- `global_model_r2_heatmap.png`
- `feature_block_ablation.png`
- `subgroup_model_r2_heatmap.png`
- `fungi_xgboost_top_shap_features.png`

## Data availability

The raw CSV dataset is not included in this repository because it's  large and derived from multiple external ecological sources. Name of the used dataset is

```text
SoilMind_v3_with_coordinates_and_nasa.csv
```

## How to run

### Option 1: Google Colab

1. Open the notebook in Google Colab.
2. Run the package setup cell.
3. Upload `SoilMind_v3_with_coordinates_and_nasa.csv` when prompted.
4. Run all cells from top to bottom.

### Option 2: Local Jupyter

Create a Python environment:

```bash
python -m venv .venv
source .venv/bin/activate   # macOS/Linux
# .venv\Scripts\activate    # Windows

pip install -r requirements.txt
```

Then start Jupyter:

```bash
jupyter notebook
```

Open:

```text
notebooks/Soil_Microbe_leakage_aware_benchmark.ipynb
```

## Reproducibility notes

- Random seed is fixed in the notebook.
- Preprocessing is fitted inside each training split or fold.
- Site identifiers are used only for validation grouping, not as predictors.
- Time-aware validation uses earlier periods for training and the most recent period for testing.
- Stacking is trained inside each outer split to avoid meta-learner leakage.
- SHAP is used only as supporting interpretation, not as evidence of transferability.


