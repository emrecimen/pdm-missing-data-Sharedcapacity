# Selecting Predictive-Maintenance Models Under Missing Sensor Data and Shared Repair Capacity

Code and result files for the paper *Selecting Predictive-Maintenance Models Under Missing Sensor Data and Shared Repair Capacity*.

## Overview

Predictive-maintenance models are usually chosen by their prediction error. The experiments in this repository ask whether, under missing sensor data, the model with the lowest remaining-useful-life (RUL) error also gives the lowest maintenance cost for a fleet that shares limited repair capacity.

The notebook:

1. Downloads the NASA C-MAPSS data (FD001–FD004).
2. Generates and caches the missingness masks: MCAR, MAR, MNAR, and MIXED, each at 10%, 30%, 50%, 70%, and 90% of the predictor matrix (20 settings), with five seeds. Only the sensor channels are masked; unit, cycle, and the RUL target are never masked.
3. Trains three families of XGBoost models on the same incomplete data, using XGBoost's native missing-value handling (no imputation):
   - **prediction only**: squared-error model, plus a complete-data reference;
   - **regret calibrated**: predictions shifted by a safety margin chosen on validation regret;
   - **cost-aware training**: quantile regression at a theoretical and at a validated quantile, and a hybrid squared-error + regret-surrogate loss.
4. Solves the capacity-constrained maintenance scheduling problem exactly (HiGHS via `scipy.optimize.milp`) for every set of predictions and for the perfect-information plan, and computes realized cost, failures, downtime, and **regret**.
5. Runs the distribution-shift setting (train on MCAR-10, test on MNAR-30) and the sensitivity grids for the failure cost, the early-replacement cost, and the capacity (on MNAR-30, first two seeds).
6. Writes the summary files and the table bodies and figures of the paper.

## Repository contents

| File | Description |
|---|---|
| `pdm-missing-data-Sharedcapacity.ipynb` | Experiment notebook: runs all experiments and produces the tables and figures. |
| `make_tab_uncap.py` | Produces the single-machine vs. fleet regret table from `results_raw.csv`. |
## Data

No manual download is needed. The notebook fetches C-MAPSS from the NASA Prognostics Center of Excellence repository and stores them under `./data/`.

If the automatic download fails, place `train_FD00x.txt`, `test_FD00x.txt`, and `RUL_FD00x.txt` (x = 1–4) in `./data/cmapss/`. In Google Colab the notebook opens an upload dialog instead.

## Requirements

Python 3 with `numpy`, `pandas`, `scipy` (version 1.9 or later, for `scipy.optimize.milp`), `scikit-learn`, `xgboost`, `matplotlib`, and `requests`. The notebook installs any of these that are missing.

The reported run used Python 3.13.15, NumPy 2.1.3, pandas 2.2.3, SciPy 1.16.3, scikit-learn 1.6.1, and XGBoost 3.4.1 on Google Colab.

## Running the experiments

Open the notebook in Jupyter or Google Colab and run all cells. All settings are in the `CONFIG` cell at the top; nothing else needs to be edited.

Useful options in `CONFIG`:

| Option | Default | Meaning |
|---|---|---|
| `OUT_DIR` | `./outputs` | Output folder. |
| `RESUME` | `True` | Results are saved after every block; a re-run skips the blocks already present in `results_raw.csv`. |
| `USE_DRIVE` | `True` | In Colab, mounts Google Drive and keeps data, masks, and results there. Has no effect outside Colab. |
| `SMOKE_TEST` | `False` | If `True`, runs the pipeline on small synthetic data to check that the code works. These results must not be reported. |

The main matrix has 4 data sets × 20 settings × 5 seeds = 400 blocks.

## Outputs

Everything is written to `OUT_DIR`:

## Citation

`Will be provided when the paper is accepted.`

## License

`[TO BE COMPLETED]`
