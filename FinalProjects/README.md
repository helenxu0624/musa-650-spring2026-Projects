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

- `notebooks/0201_RF.ipynb`  
  Random forest baseline on Sen1Floods11.

- `notebooks/0202_MNDWI_RF_comparisons.ipynb`  
  Comparison notebook for traditional ML feature configurations.

- `notebooks/0301_deep_learning_baseline_local.ipynb`  
  Local debugging notebook for the Sen1Floods11 deep-learning baseline. This was used for quick iteration before full Colab runs.

- `notebooks/0302_deep_learning_baseline.ipynb`  
  Main Sen1Floods11 training notebook for the final deep-learning experiments. It contains the training loop, threshold sweep, experiment logging, and model-selection workflow.

- `notebooks/0303_DL_Application_on_Nairobi.ipynb`  
  Final Nairobi transfer notebook. It loads the selected Sen1Floods11 checkpoint, applies the model to Nairobi Sentinel-1 before/after imagery, evaluates DL-only outputs against the available Nairobi references, and exports report-ready figures.


### Deep-learning experiment summaries (`results/experiments/`)

- `results/experiments/deep_learning_experiment_summary.csv`
- `results/experiments/deep_learning_threshold_sweep.csv`
- `results/experiments/dl_experiment_report_table.csv`
- `results/experiments/dl_experiment_report_table.md`

These files summarize the final Sen1Floods11 deep-learning experiments and the threshold-sweep results used to choose the final transfer model.

### Final presentation

- `results/Final_presentation.pdf`  
  Final project presentation deck for the Nairobi flood footprint workflow.

## Analysis
### Research purpose
The main goal of this project was to compare multiple flood-mapping strategies for Nairobi under a consistent workflow and to understand how prediction quality changes as the modeling approach becomes more sophisticated. In practice, this meant comparing:

- rule-based SAR change detection,
- traditional machine-learning models trained on Sen1Floods11,
- and deep-learning segmentation models trained on the same Sen1Floods11 task and then transferred to Nairobi.

The central question was not only which method gives the strongest validation accuracy, but also which method transfers most usefully to a real Nairobi flood event setting.

### Stage 1: Sentinel-1 preprocessing and SAR baselines

In Stage 1, we focused on Sentinel-1 VV/VH preprocessing, angle-aware correction, and simple threshold-based flood masks derived from before/after SAR change. This stage was important because it established an interpretable baseline and showed what could be achieved without any learned model.

The main result of Stage 1 is that SAR change detection can identify broad flood-related patterns quickly and transparently, but it is sensitive to threshold choice, urban backscatter noise, and local scene conditions. In other words, Stage 1 gives a strong baseline for interpretation, but not the most stable final predictor.

### Stage 2: Traditional machine-learning comparison

In Stage 2, we compared traditional supervised models on Sen1Floods11 using SAR-only, optical-only, and fused feature sets. This stage tested whether feature engineering and classical ML could improve on simple thresholding while remaining easier to interpret than deep learning.

The main takeaway from Stage 2 is that learned models generally outperform rule-based thresholds, and combining richer feature sets improves separability between flooded and non-flooded pixels. SAR remains valuable because it is cloud-robust, while optical features can add discrimination when surface-water signatures are visible. This stage therefore showed the value of supervised learning and multimodal features, even before moving to deep learning.

### Stage 3: Deep-learning model selection and Nairobi transfer

In Stage 3, we trained several U-Net-style flood segmentation models on Sen1Floods11 and selected the final model using a global validation threshold sweep. The best model was the residual attention U-Net with auto weighted cross entropy plus Dice loss, which achieved the top validation results among all tested deep-learning runs.

The main result of Stage 3 is that deep learning produced the strongest validation accuracy in the project. The selected model reached `Water IoU = 0.548` and `Water F1 = 0.708`, while also keeping predicted water coverage close to the true validation water fraction. When transferred to Nairobi, the DL-only outputs produced useful before/after water probability maps and a coherent flood proxy. At the same time, the Nairobi diagnostics showed that transfer remains challenging: a model that performs well on Sen1Floods11 does not automatically become a perfect flood map in a new city. This is why the final DL outputs are most useful as calibrated evidence and report-ready diagnostics rather than as a perfect standalone ground-truth substitute.

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

### Environment and project documentation

- `README.md`  
  Project overview and final workflow summary.

- `LOCAL_ENVIRONMENT.md`  
  Extra notes for local setup and notebook execution.

- `environment.yml`  
  Recommended conda environment for local execution.

- `requirements.txt`  
  Pip-based fallback environment.



## Notes

- Sentinel imagery is loaded from Element84 Earth Search: `https://earth-search.aws.element84.com/v1`.
- AWS unsigned reads are enabled with `AWS_NO_SIGN_REQUEST=YES`.
- Local notebooks use Dask with `Client(processes=False)` where needed.
- The final report-ready Nairobi figures are stored under `results/outputs/nairobi_deep_learning_dl_only/report_figures/`.
- The google Ground truth data can be found here - https://research.google/blog/introducing-groundsource-turning-news-reports-into-data-with-gemini/
