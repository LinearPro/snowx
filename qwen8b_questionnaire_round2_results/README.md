# Qwen8B questionnaire round 2: lightweight results

This directory contains the reports and selected result data for the second
Qwen8B questionnaire prediction experiment.

Included:

- `RESULTS_SUMMARY.md`, `IMPLEMENTATION_AUDIT.md`, and `MODEL_LOADING.md`;
- experiment configuration, validation metadata, and environment metadata;
- aggregate metrics, paired comparisons, calibration curves, stress tests,
  item importance, question-budget, runtime, and training-history tables;
- PDF/SVG figures and their source CSV files;
- input and implementation audit summaries.

Excluded to keep the repository compact:

- model weights and checkpoints;
- frozen Qwen embedding caches;
- person-level predictions, labels, and training-subset membership tables;
- full train/validation prediction tables and stress-prediction tables;
- duplicated per-run artifacts and logs;
- the 4.4 GB all-results ZIP archive.

The complete local delivery remains under
`/home/amax/lingp/code/snowx/qwen8b_questionnaire_round2`.
