# Experimental Log — Wearable HRV Stress Detection
# STATUS: PLAN ONLY. No experiments run yet.
# Every "Results" value below is marked PENDING and must be filled with REAL numbers
# produced by actually running the pipeline. Do NOT invent values here — PaperOrchestra
# reads this file as ground truth for the paper's Experiments section.

## 1. Setup (to execute in Google Colab)
- Dataset: WESAD. Download, verify subject count (S2..S17), check label scheme
  (0=undefined/transient, 1=baseline, 2=stress, 3=amusement) — keep baseline vs stress.
- Signal: wrist BVP/PPG -> inter-beat intervals (IBI). Library: neurokit2 / heartpy.
- Windows: 60 s (primary), with 30 s and 120 s as ablations; stride TBD.
- Features per window: SDNN, RMSSD, pNN50, meanHR, LF, HF, LF/HF.
- Models: logistic regression, random forest, small MLP. Trivial baseline: meanHR only.
- Protocol: Leave-One-Subject-Out CV. Fix random seeds; log library versions.

## 2. Results — Main (PENDING)
| Model               | Accuracy | Macro-F1 | AUROC | Per-subject F1 std |
|---------------------|----------|----------|-------|--------------------|
| meanHR-only (base)  | PENDING  | PENDING  | PEND. | PENDING            |
| Logistic Regression | PENDING  | PENDING  | PEND. | PENDING            |
| Random Forest       | PENDING  | PENDING  | PEND. | PENDING            |
| Small MLP           | PENDING  | PENDING  | PEND. | PENDING            |

## 3. Ablations (PENDING)
- Window length (30 / 60 / 120 s): PENDING
- Time-domain only vs. frequency-domain only vs. all: PENDING
- Feature importance (RF / permutation): PENDING

## 4. Consumer-device validation — Apple Watch Ultra case study (PENDING)
- Export Apple Health HRV (SDNN) + self-reported stress labels; windowing to match.
- Apply public-data-trained model; report agreement / confusion. Keep data local.
- Results: PENDING

## 5. Notes / observations (fill during runs)
- Failure cases, class imbalance handling, surprises: PENDING
