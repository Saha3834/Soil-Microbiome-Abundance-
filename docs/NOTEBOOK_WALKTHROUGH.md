# Notebook walkthrough

This document explains the notebook cell by cell and how each section supports the leakage-aware benchmark goal.

## Cell summary

| Cells | Section | Purpose |
|---|---|---|
| 0–2 | Package setup | Installs optional modelling and explainability packages, including XGBoost, LightGBM, CatBoost, and SHAP if missing. |
| 3–4 | Imports and reproducibility | Imports libraries, suppresses warnings, and sets random seeds for reproducible experiments. |
| 5–6 | Output folders | Creates output directories for tables, figures, and model artifacts. |
| 7–8 | Dataset loading | Loads or uploads the integrated soil–microbe dataset. |
| 9–10 | Dataset audit | Reports dataset shape, duplicate rows, missing values, and column inventory. |
| 11–12 | Target creation | Creates `log_meanCopyNumber` from `meanCopyNumber` using `log1p`. |
| 13–14 | Leakage-aware cleaning | Removes leakage-prone and redundant columns, creates temporal features, and prepares the clean dataset. |
| 15–16 | Approved ecological features | Defines the final ecological predictor set across soil, climate, spatial, temporal, and land-cover/context groups. |
| 17–18 | Validation grouping | Identifies site, plot, and year columns used for GroupKFold and time-aware validation. |
| 19–20 | Model list | Defines eight benchmark models, including a stacking ensemble. |
| 21–22 | Reusable functions | Defines RMSE, metrics, preprocessing, model pipeline creation, and validation functions. |
| 23–24 | Benchmark runner | Defines the full benchmark execution function for random split, K-fold, GroupKFold, and time-aware holdout. |
| 25–26 | Global benchmark | Runs the benchmark on all microbial records together. |
| 27–28 | Global visualisation | Produces R² heatmaps, model rankings, and global generalisation gap summaries. |
| 29–30 | Feature-block ablation | Tests XGBoost performance using different ecological feature groups. |
| 31–32 | Subgroup preparation | Splits the dataset into fungi and bacteria/archaea subsets. |
| 33–34 | Subgroup benchmark | Runs selected advanced models on fungi and bacteria/archaea tasks. |
| 35–36 | Subgroup visualisation | Produces subgroup heatmaps and transfer-gap summaries. |
| 37–38 | Combined summary | Combines global and subgroup benchmark outputs into final summary tables. |
| 39–40 | SHAP explanation | Runs SHAP for the fungi-specific XGBoost model and exports top feature plots/tables. |
| 41–42 | Artifact inventory | Lists exported tables and figures. |

## How the notebook supports the research goal

The notebook is designed around a single benchmark question: whether microbial abundance models learn transferable ecological patterns or mainly interpolate within observed site/time conditions.

The workflow supports this goal by:

1. using only leakage-approved ecological predictors;
2. fitting preprocessing inside validation splits;
3. comparing multiple model families under identical conditions;
4. separating interpolation validation from site/time transfer validation;
5. reporting generalisation gaps, not only random-split accuracy;
6. using SHAP only as supporting interpretation.

## Main achieved outputs

The notebook produces:

- dataset audit tables;
- leakage-aware feature inventory;
- global benchmark results;
- subgroup benchmark results;
- feature-block ablation results;
- site and temporal generalisation gaps;
- SHAP feature importance table and figures.
