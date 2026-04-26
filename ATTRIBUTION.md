# Attribution

## Dataset

**WESAD (Wearable Stress and Affect Detection)**
- Schmidt, P., Reiss, A., Duerichen, R., Marberger, C., & Van Laerhoven, K. (2018). Introducing WESAD, a multimodal dataset for wearable stress and affect detection. *Proceedings of the 20th ACM International Conference on Multimodal Interaction (ICMI '18)*, 400–408. https://doi.org/10.1145/3242969.3242985
- Downloaded from: https://ubicomp.ifi.lmu.de/pub/datasets/WESAD/
- 15 subjects (S2–S17, no S12), dual-device setup: RespiBAN chest unit (700 Hz) and Empatica E4 wrist device
- Used under the dataset's terms of use for academic research
---

## AI Assistance

**Claude (Anthropic)** was used for this project via Claude Code. Specific contributions include:
- **Exploratory data analysis** (`notebooks/00_eda.ipynb`)
- **Feature engineering pipeline** (`notebooks/01_preprocessing.ipynb`): The extraction and preprocessing of features, manual Welch LF/HF HRV computation (`_hrv_freq_manual`), EDA tonic/phasic decomposition slicing, and the NaN cleanup cell were written with Claude assistance. I refactored the code to use a full per-subject precomputation.
- **EBM training notebook** (`notebooks/02_ebm.ipynb`): LOSO cross-validation loop structure, feature importance extraction, `run_loso` helper function, ablation condition cells (simple, engineered, wrist-only, chest-only, no-ACC, baseline-normalized, chest-no-ACC, random baseline), ablation summary table and bar chart, and local/global explanation cells
- **`SETUP.md`**: Edited and formatted with Claude assistance
- **`ATTRIBUTION.md`**: Edited and formatted with Claude assistance
- **`ANALYSIS.md`**: Edited and formatted with Claude assistance
- All AI-generated code was reviewed, tested, and corrected or redirected by me

---

## External Libraries

| Library | Version | Purpose | License |
|---|---|---|---|
| [NumPy](https://numpy.org) | 2.4.4 | Array operations, numerical computing | BSD-3-Clause |
| [pandas](https://pandas.pydata.org) | 2.3.3 | DataFrames, results tables | BSD-3-Clause |
| [SciPy](https://scipy.org) | 1.17.1 | Signal resampling (`resample_poly`), Welch PSD, linear regression | BSD-3-Clause |
| [scikit-learn](https://scikit-learn.org) | 1.8.0 | `LeaveOneGroupOut`, `DummyClassifier`, metrics (`f1_score`, `accuracy_score`, `classification_report`, `confusion_matrix`) | BSD-3-Clause |
| [NeuroKit2](https://neuropsychology.github.io/NeuroKit/) | 0.2.13 | ECG R-peak detection (`ecg_peaks`), EDA decomposition (`eda_phasic`), RSP processing (`rsp_process`) | MIT |
| [InterpretML / interpret-core](https://interpret.ml) | 0.7.8 | `ExplainableBoostingClassifier`, `explain_global`, `explain_local` | MIT |
| [Matplotlib](https://matplotlib.org) | 3.10.8 | Plots, bar charts, confusion matrix figures | PSF / BSD-compatible |
| [seaborn](https://seaborn.pydata.org) | 0.13.2 | Confusion matrix heatmaps | BSD-3-Clause |
| [Jupyter Notebook](https://jupyter.org) | 7.5.5 | Interactive notebook runtime | BSD-3-Clause |
| [ipykernel](https://ipython.org) | 7.2.0 | Python kernel for Jupyter | BSD-3-Clause |

---

## Key References

**Explainable Boosting Machine:**
- Lou, Y., Caruana, R., Gehrke, J., & Hooker, G. (2013). Accurate intelligible models with pairwise interactions. *Proceedings of the 19th ACM SIGKDD International Conference on Knowledge Discovery and Data Mining*, 623–631. https://doi.org/10.1145/2487575.2487579
- Nori, H., Jenkins, S., Koch, P., & Caruana, R. (2019). InterpretML: A unified framework for machine learning interpretability. *arXiv:1909.09223*. https://arxiv.org/abs/1909.09223
