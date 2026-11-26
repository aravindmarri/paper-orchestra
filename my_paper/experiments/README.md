# Experiments — WESAD HRV Stress Detection

`wesad_hrv_stress.ipynb` produces the **real results** for the paper. It runs entirely
in Google Colab, so your laptop's storage/GPU don't matter (the ~2 GB dataset downloads
to the cloud runtime).

## How to run (Colab)
1. Go to https://colab.research.google.com → **File → Upload notebook** → pick
   `wesad_hrv_stress.ipynb` (or **File → Open notebook → GitHub** and point it at this
   file in your fork).
2. **Runtime → Run all.** A free CPU runtime is enough. Expect a few minutes for the
   download and a few more for feature extraction + the window-length ablation.
3. When it finishes, download the `/content/outputs/` folder. It contains:
   - `results_main.csv`, `ablation_features.csv`, `ablation_window.csv`
   - `fig_models.png`, `fig_per_subject.png`, `fig_importance.png`
   - `results_summary.md` — paste this into `../raw_materials/experimental_log.md`,
     replacing the `PENDING` tables with the real numbers.

## What it does
- Downloads WESAD, extracts HRV features (SDNN, RMSSD, pNN50, meanHR, LF, HF, LF/HF)
  from 60 s windows of wrist PPG.
- Task: **baseline vs stress** (binary), **Leave-One-Subject-Out** cross-validation.
- Models: mean-HR-only baseline, logistic regression, random forest, small MLP.
- Ablations: feature groups (time/freq/all) and window length (30/60/120 s).

## Data caveat
The notebook's `WESAD_URL` is the commonly used public link. If the download fails,
get the current link from the official WESAD dataset page (University of Siegen, ICMI18)
and replace `WESAD_URL` in cell 2.

## Apple Watch Ultra validation (separate, local — not in this notebook)
Export Apple Health HRV (SDNN) + your self-reported stress labels, window them the same
way, apply the trained model, and report agreement. Keep that personal health data on
your own machine; do not upload it to Colab.
