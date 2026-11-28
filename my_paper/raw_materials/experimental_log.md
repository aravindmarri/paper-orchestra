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


## 6. Specificity & uncertainty (v3, run in Colab 2026-11-27)
# Produced by my_paper/experiments/wesad_hrv_stress_v3.ipynb. Windows kept: 1030 total
# (baseline 562, stress 307, amusement 161). Same PPG/HRV pipeline as section 1.

### 6a. Three-class LOSO (baseline/stress/amusement), Random Forest
- macro-F1 = 0.520, accuracy = 0.687; per-subject mean macro-F1 = 0.520 +/- 0.082.
- Majority-class baseline macro-F1 = 0.235.
- Row-normalised confusion (recall on diagonal):
    baseline : baseline 0.797, stress 0.155, amusement 0.048
    stress   : baseline 0.153, stress 0.834, amusement 0.013
    amusement: baseline 0.894, stress 0.081, amusement 0.025
- Reading: stress recovered well (0.83) and almost never called amusement (0.013);
  amusement collapses into baseline (recall 0.025) -> amusement is the hard class,
  NOT stress-vs-amusement confusion. Model is not a generic arousal detector.

### 6b. Stress vs amusement (both high-arousal), LOSO
- Logistic Regression: macro-F1 = 0.842 +/- 0.162, acc = 0.878, stress-recall = 0.941.
- Random Forest:       macro-F1 = 0.818 +/- 0.172, acc = 0.854, stress-recall = 0.915.
- Majority baseline macro-F1 = 0.396 (windows: stress 307, amusement 161).
- Reading: features separate stress from a different affective state, not just arousal.

### 6c. Baseline vs stress (headline), per-subject bootstrap (Random Forest)
- Mean per-subject macro-F1 = 0.823; bootstrap 95% CI [0.736, 0.895] (B=5000 over 15 subjects).
- Per-subject range: min 0.413 (S10), median 0.856, max 0.980 (S7). Hard subjects: S10, S15 (0.499).

### 6d. Saved artifacts (outputs_v3/)
- features_v3.csv, confusion_3class.csv, per_subject_f1.csv, fig_confusion3.png, summary_v3.md
- Tool versions: python 3.13, numpy 2.4.6, sklearn 1.6.1, scipy 1.16.3, neurokit2 0.2.10.
