# Flood Extent Mapping in Nairobi

This folder contains the final project deliverables for the Nairobi flood footprint workflow. It includes the core notebooks, the reproducible local environment files, the final deep-learning experiment summaries, and the final presentation deck.

## Folder Contents

The folder is organized into four main groups:

- `notebooks/` for all project notebooks
- `results/` for experiment tables, model outputs, figures, and the final presentation
- `data/` for local data inputs
- `unStat_groundTruth/` for the UNStats shapefile reference

### Main notebooks (`notebooks/`)

- `notebooks/0101_Sentinel1_vv_vh_angle_corrected.ipynb`  
  Sentinel-1 preprocessing, angle/stripe correction, SAR threshold baselines, ground-truth overlays, and Sentinel-2 optical context.

- `notebooks/0301_deep_learning_baseline_local.ipynb`  
  Local debugging notebook for the Sen1Floods11 deep-learning baseline. This was used for quick iteration before full Colab runs.

- `notebooks/0302_deep_learning_baseline.ipynb`  
  Main Sen1Floods11 training notebook for the final deep-learning experiments. It contains the training loop, threshold sweep, experiment logging, and model-selection workflow.

- `notebooks/0303_DL_Application_on_Nairobi.ipynb`  
  Final Nairobi transfer notebook. It loads the selected Sen1Floods11 checkpoint, applies the model to Nairobi Sentinel-1 before/after imagery, evaluates DL-only outputs against the available Nairobi references, and exports report-ready figures.

- `notebooks/sen1floods11_rf_base.ipynb`  
  Random forest baseline on Sen1Floods11.

- `notebooks/sen1floods11_rf_comparison.ipynb`  
  Comparison notebook for traditional ML feature configurations.

### Environment and project documentation

- `README.md`  
  Project overview and final workflow summary.

- `LOCAL_ENVIRONMENT.md`  
  Extra notes for local setup and notebook execution.

- `environment.yml`  
  Recommended conda environment for local execution.

- `requirements.txt`  
  Pip-based fallback environment.

### Deep-learning experiment summaries (`results/experiments/`)

- `results/experiments/deep_learning_experiment_summary.csv`
- `results/experiments/deep_learning_threshold_sweep.csv`
- `results/experiments/dl_experiment_report_table.csv`
- `results/experiments/dl_experiment_report_table.md`

These files summarize the final Sen1Floods11 deep-learning experiments and the threshold-sweep results used to choose the final transfer model.

### Final presentation

- `results/Final_presentation.pdf`  
  Final project presentation deck for the Nairobi flood footprint workflow.

## Reproducible Environment

The notebooks were developed primarily in Google Colab, but this folder also includes a local environment that can be used directly.

### Recommended: conda

```bash
conda env create -f FinalProjects/environment.yml
conda activate musa650-final
python -m ipykernel install --user --name musa650-final --display-name "Python (musa650-final)"
jupyter lab
```

### Alternative: existing `.venv`

```bash
source FinalProjects/.venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r FinalProjects/requirements.txt
jupyter lab
```

For more detail, see `LOCAL_ENVIRONMENT.md`.


## Final Deep-Learning Results

All deep-learning experiments were trained on Sen1Floods11 Sentinel-1 VV/VH chips and evaluated with a global validation threshold sweep. This global threshold readout was used as the main model-selection criterion because it computes a single confusion matrix over all valid validation pixels and directly supports transfer threshold selection.

| Rank | Experiment | Model | Loss | LR | Best epoch | Threshold | Water IoU | Water F1 | Precision | Recall | Pred. water frac | Collapse |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | Exp 6: Residual Attention U-Net, auto WCE + Dice 0.25 | Residual Attention U-Net | Weighted CE + Dice 0.25 | 8e-4 | 54 | 0.450 | 0.548 | 0.708 | 0.724 | 0.693 | 0.106 | No |
| 2 | Exp 2: Small U-Net, manual WCE [1,3] | Small U-Net | Weighted CE | 1e-3 | 20 | 0.550 | 0.536 | 0.698 | 0.757 | 0.648 | 0.094 | No |
| 3 | Exp 1: Small U-Net, auto WCE | Small U-Net | Weighted CE | 1e-3 | 16 | 0.450 | 0.528 | 0.691 | 0.722 | 0.662 | 0.101 | No |
| 4 | Exp 5: Residual Attention U-Net, auto WCE | Residual Attention U-Net | Weighted CE | 1e-3 | 74 | 0.550 | 0.521 | 0.685 | 0.737 | 0.639 | 0.096 | No |
| 5 | Exp 4: Small U-Net, auto WCE + Dice 0.25 | Small U-Net | Weighted CE + Dice 0.25 | 1e-3 | 20 | 0.550 | 0.513 | 0.678 | 0.724 | 0.637 | 0.097 | No |
| 6 | Exp 3: Small U-Net, auto WCE | Small U-Net | Weighted CE | 5e-4 | 20 | 0.600 | 0.491 | 0.659 | 0.773 | 0.573 | 0.082 | No |

### Selected final model

The final selected checkpoint is:

```text
results/outputs_DL/exp06_resattn_auto_wce_dice025_lr8e-4_base32_e80_pat20/sen1floods11_small_unet_best.pt
```

with:

```python
BEST_WATER_THRESHOLD = 0.45
```

This model was selected because it achieved the highest validation `Water IoU` and `Water F1` while keeping the predicted water fraction (`0.106`) close to the true validation water fraction (`0.110`). This indicates better calibration and a clear improvement over the earlier collapse-prone background-heavy runs.

## Data and Outputs

### Nairobi reference data

The Nairobi evaluation uses two reference sources:

```text
data/groundsource__2026.parquet
unStat_groundTruth/PL_20240501_FloodExtent_Nairobi_Kiambu.shp
```

### Output folders

- `results/outputs/nairobi_deep_learning_dl_only/`  
  Final Nairobi DL-only outputs, summary tables, raster exports, and report-ready figures.

- `results/outputs_DL/`  
  Sen1Floods11 experiment outputs and saved model checkpoints.

## Notes

- Sentinel imagery is loaded from Element84 Earth Search: `https://earth-search.aws.element84.com/v1`.
- AWS unsigned reads are enabled with `AWS_NO_SIGN_REQUEST=YES`.
- Local notebooks use Dask with `Client(processes=False)` where needed.
- The final report-ready Nairobi figures are stored under `results/outputs/nairobi_deep_learning_dl_only/report_figures/`.
