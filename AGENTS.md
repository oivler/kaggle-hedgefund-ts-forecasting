# AGENTS.md

## Cursor Cloud specific instructions

### Overview

This repository contains a single Jupyter notebook (`model.ipynb`) for a Kaggle time-series forecasting competition. It trains LightGBM models across 4 forecast horizons (1, 3, 10, 25) with 5-seed ensembling, and produces a `submission.csv`.

### Python dependencies

All dependencies are installed via pip (no `requirements.txt` in repo):
- `jupyter`, `numpy`, `pandas`, `lightgbm`, `pyarrow`, `scikit-learn`, `kaggle`

### Dataset

The notebook expects Parquet files at `ts-forecasting/train.parquet` and `ts-forecasting/test.parquet` (relative to workspace root). These are Kaggle competition datasets and **cannot be committed to the repo** (Kaggle TOS).

**To get the real data**, use the Kaggle CLI with credentials from environment secrets:
```bash
export PATH="$HOME/.local/bin:$PATH"
kaggle competitions download -c ts-forecasting -p ts-forecasting/
```
This requires `KAGGLE_USERNAME` and `KAGGLE_KEY` secrets to be configured.

**If Kaggle credentials are unavailable**, a synthetic dataset can be generated for environment verification (see the data-generation script in the setup process). The notebook will run end-to-end on synthetic data but scores will be meaningless.

### Running the notebook

```bash
export PATH="$HOME/.local/bin:$PATH"

# Execute non-interactively:
jupyter nbconvert --to notebook --execute model.ipynb --output model_executed.ipynb --ExecutePreprocessor.timeout=600

# Or start JupyterLab for interactive development:
jupyter lab --no-browser --port=8888 --ip=0.0.0.0 --ServerApp.token='' --ServerApp.password=''
```

### Gotchas

- LightGBM's sklearn API (`LGBMRegressor`) requires `scikit-learn` — this is not declared anywhere in the repo but will fail at runtime without it.
- pip installs to `~/.local/bin` which is not on PATH by default; always prepend `export PATH="$HOME/.local/bin:$PATH"` or add it to `~/.bashrc`.
- The notebook has no formal linting or test suite. Validation is done by executing the notebook end-to-end and checking that `submission.csv` is produced with the correct shape.
- With small/synthetic datasets, LightGBM's `min_child_samples` (200–250) may cause early stopping to trigger immediately. This is expected and not an error.
