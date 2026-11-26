# Generalizable Wearable Stress Detection from Heart-Rate Variability:
# A Cross-Subject Study with Consumer-Device Validation

## Problem Statement
Psychological stress detection from wearable sensors is valuable for digital health,
but models frequently fail to generalize: (1) across people, because physiology varies
subject-to-subject, and (2) across devices, because research-grade lab sensors differ
from consumer wearables. Heart-rate variability (HRV) is a physiologically grounded,
low-cost signal that is now available on consumer devices such as the Apple Watch.
This work asks: can an HRV-based stress detector trained on public, lab-collected data
generalize to unseen subjects, and does the learned representation transfer to a
consumer wearable (Apple Watch Ultra)?

## Core Hypothesis
Time- and frequency-domain HRV features carry enough information that a
*subject-independent* classifier trained on a public multimodal stress dataset can
detect acute stress with meaningful accuracy (above chance and above a resting-HR-only
baseline), and that this HRV representation transfers — with degraded but non-trivial
performance — to HRV recorded on a consumer Apple Watch, despite device and protocol
differences.

## Proposed Methodology (High-Level Technical Approach)
1. **Public dataset.** Use WESAD (Wearable Stress and Affect Detection), which provides
   wrist and chest physiological signals with labeled conditions (baseline / stress /
   amusement). Derive inter-beat intervals and HRV from the *wrist* signal to stay close
   to consumer-wearable conditions.
2. **Features.** Compute standard HRV metrics over sliding windows: SDNN, RMSSD, pNN50,
   mean HR, and frequency-domain LF, HF, LF/HF power.
3. **Models.** Compare interpretable baselines (logistic regression, random forest)
   against a small neural network. Include a trivial baseline (resting HR / mean HR only)
   to show HRV adds value.
4. **Evaluation protocol.** Leave-One-Subject-Out (LOSO) cross-validation — this is the
   core of the "generalizable" claim. Report accuracy, macro-F1, AUROC, and per-subject
   variance (not just the mean).
5. **Ablations.** Window length; time- vs. frequency-domain features; feature subsets.
6. **Consumer-device validation (case study).** Export Apple Watch Ultra HRV together
   with self-reported stress labels, apply the model trained on public data, and report
   agreement. Personal health data stays local / de-identified; it is a validation
   case study, not training data.

## Expected Contribution
- A rigorous *subject-independent* benchmark of HRV-only stress detection (many papers
  leak subject identity and overstate accuracy; LOSO corrects this).
- Evidence on whether and how lab-trained HRV models transfer to a consumer wearable.
- An open, reproducible pipeline (runnable in Google Colab) plus a documented protocol
  for consumer-device validation.
