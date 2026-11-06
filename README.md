# snowx Qwen8B questionnaire experiment delivery

The complete delivery is stored as a single Git LFS archive:

- `qwen8b_questionnaire_experiment_delivery_20261107.zip`
- Size: 224,508,925 bytes
- SHA-256: `dda71c7d662836b251ad0c8e0bd07f64adfeb288681716c9dfc515d832c64021`

The archive contains source code, configuration, final results, 48 final model weights, audit files, and run logs. Regenerable Qwen embedding caches, debug models, and duplicate per-run prediction files are excluded. The consolidated per-person predictions are included at `results/predictions.csv`.

Verify after download:

```bash
sha256sum qwen8b_questionnaire_experiment_delivery_20261107.zip
unzip -t qwen8b_questionnaire_experiment_delivery_20261107.zip
```
