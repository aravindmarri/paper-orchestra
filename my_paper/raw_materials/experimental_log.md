# Experimental Log — Wearable HRV Stress Detection
# STATUS: REAL RESULTS (run in Google Colab on the WESAD dataset, 2026-11-25).
# Produced by my_paper/experiments/wesad_hrv_stress.ipynb.

## 1. Setup
- Dataset: WESAD (15 subjects: S2-S11, S13-S17; no S1/S12).
- Signal: wrist PPG/BVP (64 Hz) -> inter-beat intervals (neurokit2 0.2.10 ppg_clean + ppg_findpeaks).
- Task: binary, baseline (label 1) vs stress (label 2). Windows kept only if >=90% one class.
- Windows: 60 s, 30 s stride (main). Beats filtered to 300-2000 ms; >=12 beats/window required.
- Features (per window): meanHR, SDNN, RMSSD, pNN50 (time domain); LF, HF, LF/HF (frequency domain,
  Welch PSD on cubic-interpolated tachogram at 4 Hz).
- Models: mean-HR-only baseline (logistic regression on meanHR), logistic regression, random forest
  (300 trees), small MLP (32-16). Features standardized on the training split only.
- Protocol: Leave-One-Subject-Out (LOSO) cross-validation, 15 folds. Seeds fixed (random_state=42).
- Feature table: 869 windows total -> 562 non-stress, 307 stress. 55-61 windows per subject.

## 2. Results — Main (LOSO, baseline vs stress)

| Model | Accuracy | Macro-F1 | AUROC | Per-subject F1 std |
|---|---|---|---|---|
| meanHR-only (baseline) | 0.689 | 0.541 | 0.668 | 0.191 |
| Logistic Regression | 0.885 | 0.873 | 0.952 | 0.094 |
| Random Forest | 0.838 | 0.823 | 0.905 | 0.159 |
| Small MLP | 0.882 | 0.870 | 0.948 | 0.116 |

Key takeaways:
- Full HRV features vastly outperform the heart-rate-only baseline (F1 0.873 vs 0.541;
  AUROC 0.952 vs 0.668) -> HRV, not mean HR, carries the stress signal.
- Logistic Regression is best and also most stable across subjects (lowest per-subject F1 std, 0.094),
  i.e., it generalizes to unseen people. MLP is essentially tied.

## 3. Ablations

Feature groups (Random Forest):
- time-only (meanHR, SDNN, RMSSD, pNN50): acc=0.848, F1=0.834, AUROC=0.922
- freq-only (LF, HF, LF/HF):              acc=0.696, F1=0.657, AUROC=0.720
- all:                                    acc=0.838, F1=0.823, AUROC=0.905
Finding: time-domain HRV alone nearly matches the full set and clearly beats frequency-domain alone;
frequency features add little here (likely because 60 s windows give poor LF resolution).

Window length (Random Forest):
- 30s:  acc=0.808, F1=0.787, AUROC=0.883
- 60s:  acc=0.838, F1=0.823, AUROC=0.905
- 120s: acc=0.869, F1=0.855, AUROC=0.918
Finding: longer windows improve detection monotonically (more beats -> more stable HRV estimates).

## 4. Figures (generated in Colab, saved to /content/outputs)
- fig_models.png        — macro-F1 by model (LOSO)
- fig_per_subject.png   — per-subject F1 spread for Random Forest
- fig_importance.png    — Random Forest feature importance
(Download from the Colab run to add to the paper's figures/ folder.)

## 5. Consumer-device validation — Apple Watch Ultra case study (PENDING)
Export Apple Health HRV (SDNN) + self-reported stress labels, window identically, apply the
public-data-trained model, and report agreement. Personal data kept local.
