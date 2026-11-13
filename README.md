# Robust Non-Motor Symptom Modeling for Parkinson's Disease Differential Diagnosis

https://doi.org/10.5281/zenodo.24202188

Code for the paper *Robust Non-Motor Symptom Modeling for Parkinson's Disease Differential Diagnosis*.
The study uses the 30-item non-motor symptoms questionnaire in the PADS dataset to separate Parkinson's
disease (PD, n = 276) from other movement and neurological disorders (DD, n = 114), with patient-level,
leakage-controlled validation.

## Data

The code uses the **PADS – Parkinson's Disease Smartwatch dataset, version 1.0.0** from PhysioNet:
https://doi.org/10.13026/m0w9-zx22

The dataset is not included in this repository. Download the zip file from PhysioNet and upload it to
`/content` in Google Colab. All notebooks expect the data at:

```
/content/pads_dataset/pads-parkinsons-disease-smartwatch-dataset-1.0.0/
```

## Environment

The analyses were run in Google Colab (Python 3.13; NVIDIA T4 GPU for the GPU models).
Package versions are listed in `requirements.txt`.

**Exact reproduction requires XGBoost 3.3.0.** Newer XGBoost releases build trees slightly differently and
change the results. Each notebook starts with `!pip install -q xgboost==3.3.0`; restart the runtime once
after installing. All random seeds are fixed at 42.

## Run order

Run the notebooks in this order. Each notebook reads the files written by the earlier ones under
`project_ready_data/`.

| Notebook | Contents |
|---|---|
| `01_data_preparation.ipynb` | Data consolidation, sensor feature engineering, fixed patient-level outer folds |
| `02_baseline_models.ipynb` | Logistic regression, RBF-SVM, random forest, XGBoost, 1D-CNN, feature-set ablation |
| `03_sensor_and_multimodal_models.ipynb` | Sensor models (incl. NTR-XGB, Model D), robustness, calibration, multimodal stacking |
| `04_questionnaire_models.ipynb` | Questionnaire baselines, **primary NMS-XGB model (Model J)**, calibration / decision curve / referral (Model K), questionnaire reduction (Model L), paired bootstrap, repeated-seed stability |
| `05_tables_and_figures.ipynb` | Final consistency audit and manuscript figures |
| `06_revision_analyses.ipynb` | Analyses added during peer review: DD subgroup characterization and performance, disease duration / phenotype / treatment, comparison with the questionnaire total score and routine clinical features |

Notes:
- Notebook 06 only needs notebook 01 and Model J from notebook 04.
- Model L (questionnaire reduction) saves checkpoints to Google Drive so it can resume after a disconnect.
  Change `DRIVE_OUTPUT_DIR` in that cell if you do not use Google Drive.
- The notebooks are saved with their outputs, so the results can be inspected without re-running them.

## Cohort and folds

`data/cohort_and_folds.csv` lists the 390 PADS participant identifiers used in the analysis, their diagnostic
group and their fixed outer fold (1–5). Folds were created once with stratified sampling
(`StratifiedKFold`, shuffled, seed 42) on the PD-versus-DD label and are used by every model.

## Validation design

All preprocessing, imputation, feature selection, hyperparameter tuning, threshold selection and probability
calibration are fitted on the outer-training participants only. Every participant is tested exactly once.
This is internal cross-validation; the model has not been validated on an independent cohort and is not
intended for clinical use.

## Citation

If you use this code, please cite the paper (citation will be added after publication) and the PADS dataset:

Varghese J, Brenner A, Fujarski M, Alen CM, Plagwitz L, Warnecke T. Machine learning in the Parkinson's
disease smartwatch (PADS) dataset. npj Parkinson's Disease 10, 9 (2024). https://doi.org/10.1038/s41531-023-00625-7
