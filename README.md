# snowx Qwen8B questionnaire experiment delivery

The complete delivery is stored as a single Git LFS archive:

- `qwen8b_questionnaire_experiment_delivery_20261107.zip`
- Size: 224,508,925 bytes
- SHA-256: `dda71c7d662836b251ad0c8e0bd07f64adfeb288681716c9dfc515d832c64021`

The result data is also available as a separate results-only archive:

- `qwen8b_questionnaire_all_results_20261107.zip`
- Size: 87,261,927 bytes
- SHA-256: `850125bb2a9dd84390cd6e7a47d4a472a6da637777cbbcd2dabaab049c584a00`
- Contains the consolidated predictions, metrics, paired comparisons, calibration data, split, audits, tuning/resource records, and result summary; no code, weights, logs, or caches.

The archive contains source code, configuration, final results, 48 final model weights, audit files, and run logs. Regenerable Qwen embedding caches, debug models, and duplicate per-run prediction files are excluded. The consolidated per-person predictions are included at `results/predictions.csv`.

Verify after download:

```bash
sha256sum qwen8b_questionnaire_experiment_delivery_20261107.zip
unzip -t qwen8b_questionnaire_experiment_delivery_20261107.zip
```
