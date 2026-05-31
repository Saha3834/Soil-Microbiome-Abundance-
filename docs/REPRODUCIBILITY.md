# Reproducibility guide

## Environment

Recommended Python version: 3.10 or later.

Install dependencies using:

```bash
pip install -r requirements.txt
```

or:

```bash
conda env create -f environment.yml
conda activate soilmind-benchmark
```

## Data preparation

Place the dataset file in the working directory:

```text
SoilMind_v3_with_coordinates_and_nasa.csv
```

The notebook can also upload the file interactively in Google Colab.

## Reproducibility controls

The notebook uses the following controls:

- fixed random seed;
- identical target definition across all models;
- leakage-aware feature list;
- preprocessing inside each split or fold;
- GroupKFold by site for spatial transfer;
- time-aware holdout for temporal transfer;
- stacking trained only within outer training splits.

## Expected main outputs

After successful execution, the notebook writes outputs to:

```text
outputs_leakage_aware_extended_benchmark/
```

Important files:

```text
tables/global_extended_benchmark_results.csv
tables/global_generalisation_gaps.csv
tables/feature_block_ablation.csv
tables/subgroup_extended_benchmark_results.csv
tables/subgroup_generalisation_gaps.csv
tables/combined_global_subgroup_summary.csv
tables/fungi_xgboost_shap_importance.csv
figures/global_model_r2_heatmap.png
figures/feature_block_ablation.png
figures/subgroup_model_r2_heatmap.png
figures/fungi_xgboost_top_shap_features.png
```

## Notes on runtime

The full benchmark can take time because it evaluates several tree-based and boosting models across multiple validation strategies. To reduce runtime, run a smaller subset of models or set subgroup benchmarking to selected advanced models only.
