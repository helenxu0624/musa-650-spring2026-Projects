# Nairobi Flood Footprints

This folder contains the final notebooks and outputs for the Nairobi flood footprint project.

## Final Workflow

- `deep_learning_baseline.ipynb`: Sen1Floods11 deep-learning training and experiment logging.
- `dl_experiment_report_table.csv`: report-ready summary of the trained deep-learning experiments.
- `nairobi_dl_only_diagnostics.ipynb`: final Nairobi transfer notebook. It loads the experiment table, explains the selected model, applies the selected Sen1Floods11 checkpoint to Nairobi Sentinel-1 before/after imagery, evaluates DL-only outputs against the available ground-truth references, and exports separate report figures.
- `Nairobi Flood FootPrint_angle_corrected.ipynb`: SAR thresholding, ground-truth overlays, and Sentinel-2 optical context. Deep-learning diagnostics have been moved to `nairobi_dl_only_diagnostics.ipynb`.

## Deep Learning Experiment Results

All deep-learning experiments were trained on Sen1Floods11 Sentinel-1 VV/VH chips and evaluated with a global validation threshold sweep. The global threshold metric is the preferred readout because it computes one confusion matrix over all valid validation pixels, which is better aligned with transfer inference than batch-averaged argmax metrics.

| Rank | Experiment | Model | Loss | LR | Best epoch | Threshold | Water IoU | Water F1 | Precision | Recall | Pred. water frac | Collapse |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | Exp 6: Residual Attention U-Net, auto WCE + Dice 0.25 | Residual Attention U-Net | Weighted CE + Dice 0.25 | 8e-4 | 54 | 0.450 | 0.548 | 0.708 | 0.724 | 0.693 | 0.106 | No |
| 2 | Exp 2: Small U-Net, manual WCE [1,3] | Small U-Net | Weighted CE | 1e-3 | 20 | 0.550 | 0.536 | 0.698 | 0.757 | 0.648 | 0.094 | No |
| 3 | Exp 1: Small U-Net, auto WCE | Small U-Net | Weighted CE | 1e-3 | 16 | 0.450 | 0.528 | 0.691 | 0.722 | 0.662 | 0.101 | No |
| 4 | Exp 5: Residual Attention U-Net, auto WCE | Residual Attention U-Net | Weighted CE | 1e-3 | 74 | 0.550 | 0.521 | 0.685 | 0.737 | 0.639 | 0.096 | No |
| 5 | Exp 4: Small U-Net, auto WCE + Dice 0.25 | Small U-Net | Weighted CE + Dice 0.25 | 1e-3 | 20 | 0.550 | 0.513 | 0.678 | 0.724 | 0.637 | 0.097 | No |
| 6 | Exp 3: Small U-Net, auto WCE | Small U-Net | Weighted CE | 5e-4 | 20 | 0.600 | 0.491 | 0.659 | 0.773 | 0.573 | 0.082 | No |

The final selected model is **Exp 6**. It achieved the strongest validation performance (`Water IoU = 0.548`, `Water F1 = 0.708`) while keeping the predicted water fraction (`0.106`) close to the true validation water fraction (`0.110`). This indicates that the model avoided the earlier all-background collapse mode and produced a better-calibrated water probability surface for transfer to Nairobi.

## Local Environment

The notebooks were originally run in Google Colab. For local work, the recommended setup is the conda environment:

```bash
conda env create -f environment.yml
conda activate musa650-final
python -m ipykernel install --user --name musa650-final --display-name "Python (musa650-final)"
jupyter lab
```

The existing local virtual environment can also be used when conda is unavailable:

```bash
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
jupyter lab
```

See `LOCAL_ENVIRONMENT.md` for more detailed setup notes.

## Data

The notebooks use two Nairobi ground-truth references:

```text
data/groundsource__2026.parquet
unStat_groundTruth/PL_20240501_FloodExtent_Nairobi_Kiambu.shp
```

The Sen1Floods11 model checkpoints and experiment outputs are stored under `outputs_DL/`.
Nairobi DL-only outputs are stored under `outputs/nairobi_deep_learning_dl_only/`.

## Notes

- Sentinel data is loaded from Element84 Earth Search: `https://earth-search.aws.element84.com/v1`.
- AWS unsigned reads are enabled by setting `AWS_NO_SIGN_REQUEST=YES`.
- The notebooks use Dask locally with `Client(processes=False)`.
- The selected DL checkpoint is `outputs_DL/exp06_resattn_auto_wce_dice025_lr8e-4_base32_e80_pat20/sen1floods11_small_unet_best.pt`.
- The selected Nairobi transfer threshold is `BEST_WATER_THRESHOLD = 0.45`.
