# Installation and Setup Instructions

## Prerequisites

- Python 3.10 or newer (developed on 3.12)
- ~10 GB free disk space (raw WESAD data + processed outputs)

---

## 1. Get the raw data

This repo uses the WESAD dataset from the Schmidt et al. (2018):
https://ubicomp.ifi.lmu.de/pub/datasets/WESAD/

The directory looks like:

```
data/
└── WESAD/
    ├── S2/
    │   ├── S2.pkl
    │   ├── S2_E4_Data/
    │   └── ...
    ├── S3/
    ├── S4/
    ...
    └── S17/
```

Subjects included: 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 13, 14, 15, 16, 17 (no S12).

Each subject folder contains the `.pkl` file (combined chest + wrist data).

---

## 2. Create a virtual environment

```bash
python3 -m venv .venv
source .venv/bin/activate      # Windows: .venv\Scripts\activate
```

---

## 3. Install dependencies

```bash
pip install -r requirements.txt
```

Key packages:

| Package | Purpose |
|---|---|
| `numpy`, `scipy` | Array ops, signal resampling, Welch PSD |
| `pandas` | Feature tables, results DataFrames |
| `neurokit2` | ECG R-peak detection, EDA decomposition, RSP processing |
| `scikit-learn` | LOSO cross-validation, metrics |
| `interpret-core` | Explainable Boosting Machine (EBM) |
| `matplotlib`, `seaborn` | Plots and confusion matrices |
| `notebook`, `ipykernel` | Jupyter notebook runner |

---

## 4. Register the kernel

```bash
python3 -m ipykernel install --user --name wesad --display-name "WESAD"
```

---

## 5. Run the notebooks in order

Launch Jupyter:

```bash
jupyter notebook
```

Then run the notebooks in this order:

### Step 1 — `notebooks/01_preprocessing.ipynb`

Reads raw `.pkl` files for all 15 subjects, extracts 60-second sliding windows (0.25 s shift), computes 119 features per window, and saves processed arrays.

**Output** (written to `data/WESAD/processed_engineered_dropnan/`):
- `X.npy` — feature matrix, shape `(121808, 119)`
- `y.npy` — binary labels: 1 = stress, 0 = baseline + amusement
- `groups.npy` — subject IDs per window
- `feature_cols.json` — ordered list of 119 feature names

This step is slow (~40 min depending on hardware) due to per-subject ECG peak detection and EDA decomposition via neurokit2.

### Step 2 — `notebooks/02_ebm.ipynb`

Trains an Explainable Boosting Machine with leave-one-subject-out cross-validation over the 14 dev subjects (S3–S17), then evaluates once on the held-out test subject (S2).

Sections in order:
1. Load processed data
2. Train/test split (S2 locked out)
3. LOSO cross-validation
4. Final model training + S2 evaluation + confusion matrix
5. Feature importance extraction
6. Ablation experiments (7 conditions)
7. Global and local EBM explanations

**Output:** `data/WESAD/processed_engineered_dropnan/final_ebm.pkl`

### Optional — `notebooks/00_eda.ipynb`

Exploratory data analysis: label distributions, signal visualizations, per-subject window counts. Safe to run at any time after preprocessing.

---

## 6. Processed data layout

If you want to skip preprocessing and use pre-built arrays:

```
data/WESAD/
├── processed_engineered_dropnan/   ← used by ebm_final.ipynb
│   ├── X.npy
│   ├── y.npy
│   ├── groups.npy
│   └── feature_cols.json
├── processed_engineered/           ← intermediate (before NaN drop)
└── processed_basic/                ← earlier 30-feature version
```

---

## Signal reference

| Sensor | Device | Sampling rate | Signals |
|---|---|---|---|
| Chest | RespiBAN | 700 Hz | ECG, EDA, EMG, Resp, Temp, ACC (x/y/z) |
| Wrist | Empatica E4 | BVP @ 64 Hz, EDA/Temp @ 4 Hz, ACC @ 32 Hz | BVP, EDA, Temp, ACC (x/y/z) |

**Label mapping:**  
- `1` (baseline) + `3` (amusement) → class 0 (non-stress)  
- `2` (stress) → class 1 (stress)  
- `0` (transient) + `4` (meditation) → excluded

**Test subject:** S2 is held out for final evaluation and never seen during model development or cross-validation.
