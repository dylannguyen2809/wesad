# Stress Classification from Biosensor Data

An interpretable machine learning approach to binary stress detection from wearable physiological signals, using an Explainable Boosting Machine (EBM) trained on the WESAD dataset to classify stress vs. non-stress states from features extracted from ECG, EDA, respiration, temperature, and accelerometer signals.

## What it Does

This project builds an interpretable stress classifier from multimodal wearable biosensor data. Using the publicly available 
[WESAD dataset](https://ubi29.informatik.uni-siegen.de/usi/data_wesad.html), which contains physiological recordings from 15 subjects exposed to standardized stress (Trier Social Stress Test) and non-stress (baseline, amusement) conditions, I extract a rich set of physiological features — including heart rate variability, electrodermal activity, and respiratory patterns — from 60-second sliding windows and train an Explainable Boosting 
Machine (EBM) to classify each window as stress or non-stress. Unlike black-box approaches such as Random Forests or neural networks, the EBM produces inherently interpretable shape functions that reveal exactly how each physiological feature contributes to stress predictions, providing insight into the underlying autonomic nervous system dynamics rather than just a 
classification score. The model is evaluated using leave-one-subject-out cross-validation, which measures how well the classifier generalizes to individuals it has never seen during training.

## Quick Start

1. Clone the repository and install dependencies:
```bash
   git clone <repo-url>
   cd <repo-name>
   pip install -r requirements.txt
```

2. Download the WESAD dataset from the 
   [project page](https://ubi29.informatik.uni-siegen.de/usi/data_wesad.html) 
   and place the extracted subject folders in `data/wesad/`.

3. Run the notebooks in order:
```bash
   jupyter notebook
```
   - `notebooks/preprocessing.ipynb` — reads raw `.pkl` files for all 15
     subjects, extracts 60-second sliding windows (0.25 s shift), computes
     119 features per window, and saves processed arrays to
     `data/WESAD/processed_engineered_dropnan/`. This step takes ~40 minutes.
   - `notebooks/ebm_final.ipynb` — trains an EBM with LOSO cross-validation
     over 14 dev subjects, runs ablation experiments, evaluates the final
     model on the held-out test subject, and generates global/local
     explanations.
   - `notebooks/eda.ipynb` — (optional) exploratory data analysis: label
     distributions, signal visualizations, per-subject window counts. Safe
     to run at any time after preprocessing.

## Video Links

- [Project Demo](../wesad/videos/CS_372_Demo.mp4)
- [Technical Walkthrough](../wesad/videos/technical_walkthrough.mp4)

## Evaluation

The model is evaluated on the binary stress vs. non-stress classification task using leave-one-subject-out (LOSO) cross-validation across 14 development subjects (S3–S17), with S2 held out as the final test subject. F1-score is used as the primary metric given class imbalance between stress (~30% of windows) and non-stress (~70%) conditions. The final model uses chest physiological features excluding accelerometers (57 features, `chest_no_acc` condition).

| | F1-Score | Accuracy |
|---|---|---|
| EBM chest_no_acc (this work, LOSO mean) | 0.944 ± 0.064 | 0.955 ± 0.046 |
| EBM chest_no_acc (held-out S2) | 0.970 | 0.973 |
| LDA, chest physio — Schmidt et al. (2018) | 0.915 | 0.931 |
| Majority class baseline | 0.412 | 0.699 |

Per-subject LOSO results (development set, chest_no_acc model):

| Subject | F1-Score | Accuracy |
|---|---|---|
| S3 | 0.955 | 0.964 |
| S4 | 0.903 | 0.926 |
| S5 | 0.939 | 0.947 |
| S6 | 0.766 | 0.843 |
| S7 | 0.875 | 0.887 |
| S8 | 0.926 | 0.935 |
| S9 | 0.938 | 0.951 |
| S10 | 0.985 | 0.987 |
| S11 | 1.000 | 1.000 |
| S13 | 0.988 | 0.990 |
| S14 | 0.999 | 0.999 |
| S15 | 0.969 | 0.974 |
| S16 | 1.000 | 1.000 |
| S17 | 0.970 | 0.973 |
| **Mean** | **0.944** | **0.955** |
| **Std** | **0.064** | **0.046** |

The EBM's shape functions reveal that the most predictive features are `ecg_mean_HR`, `ecg_mean_RR`, and `ecg_rmssd`, consistent with known sympathetic nervous system activation patterns during acute stress — specifically the sharp increase in heart rate above ~85 bpm characteristic of the Trier Social Stress Test.

Substantial inter-subject variability in per-fold F1 scores (std = 0.064) highlights the challenge of subject-independent stress detection, with S6 being the most difficult subject (F1 = 0.766) and several subjects achieving near-perfect classification.