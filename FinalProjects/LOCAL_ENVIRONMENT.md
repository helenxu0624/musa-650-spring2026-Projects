# Local Environment Setup

This project has two supported local setup paths. The conda path is recommended
for the Nairobi geospatial workflow because `rasterio`, `geopandas`, `rioxarray`,
and `odc-stac` are easier to install consistently through conda-forge.

## Option A: Conda/Mamba Recommended

From the repository root:

```bash
conda env create -f FinalProjects/environment.yml
conda activate musa650-final
python -m ipykernel install --user --name musa650-final --display-name "Python (musa650-final)"
jupyter lab
```

If the environment already exists:

```bash
conda env update -f FinalProjects/environment.yml --prune
conda activate musa650-final
python -m ipykernel install --user --name musa650-final --display-name "Python (musa650-final)"
```

## Option B: Existing `.venv`

This is lighter, but geospatial packages may be more fragile on macOS.

```bash
source FinalProjects/.venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r FinalProjects/requirements.txt
python -m ipykernel install --user --name musa650-final-venv --display-name "Python (musa650-final-venv)"
jupyter lab
```

## Notebook Order For Nairobi DL-Only Transfer

Use `notebooks/0303_DL_Application_on_Nairobi.ipynb` for the final deep-learning transfer workflow.
Run the notebook from top to bottom:

1. Environment, configuration, and model-selection table.
2. Sentinel-1 preprocessing and before/after compositing.
3. Deep-learning inference with the selected Sen1Floods11 checkpoint.
4. DL-only diagnostics against the Groundsource/media and UNStats references.
5. Separate report figure export.

The current default deep-learning checkpoint is:

```text
FinalProjects/results/outputs_DL/exp06_resattn_auto_wce_dice025_lr8e-4_base32_e80_pat20/sen1floods11_small_unet_best.pt
```

with:

```python
BEST_WATER_THRESHOLD = 0.45
```

## Notes

- Local PyTorch will run on CPU or Apple Silicon MPS when available. Colab should
  keep using its preinstalled CUDA PyTorch.
- RAPIDS/cuML from `notebooks/sen1floods11_rf_comparison.ipynb` is optional and Colab-only
  for this project. Locally, the RF code falls back to scikit-learn.
