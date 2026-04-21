# Nairobi Flood Footprints

This folder contains the final notebooks for the Nairobi flood footprint project.

## Local Environment

The notebooks were originally run in Google Colab. For local work on this machine, use the Python 3.10 interpreter already available at:

```bash
/Users/helenxu/.local/bin/python3.10
```

Create and activate a local virtual environment:

```bash
cd /Users/helenxu/Desktop/Spring_2026/musa-650-spring2026-Projects/FinalProjects
/Users/helenxu/.local/bin/python3.10 -m venv .venv
source .venv/bin/activate
set -a
source .env
set +a
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
python -m ipykernel install --user --name musa650-final --display-name "Python (musa650-final)"
jupyter lab
```

If conda or mamba is available later, the same environment can be created with:

```bash
conda env create -f environment.yml
conda activate musa650-final
jupyter lab
```

## Data

The notebooks expect the Google Research flood ground-truth parquet file:

```text
data/groundsource_2026.parquet
```

The file is not currently in this repository. The checked-in notebooks still contain the original Colab/Windows paths:

- `Final_Project.ipynb`: `C:/Users/Ez/OneDrive/Work/Penn/Courses/DeepLearning/groundsource_2026.parquet`
- `Nairobi Flood FootPrint.ipynb`: `/content/drive/MyDrive/Colab Notebooks/groundsource_2026.parquet`

After downloading or copying the parquet file into `FinalProjects/data/`, update those notebook cells to:

```python
groundtruth = gpd.read_parquet("data/groundsource_2026.parquet")
```

## Notes

- Sentinel data is loaded from Element84 Earth Search: `https://earth-search.aws.element84.com/v1`.
- AWS unsigned reads are enabled by setting `AWS_NO_SIGN_REQUEST=YES`.
- The notebooks use Dask locally with `Client(processes=False)`.
- Matplotlib, IPython, and Jupyter cache/config directories are kept inside this folder through `.env`.
