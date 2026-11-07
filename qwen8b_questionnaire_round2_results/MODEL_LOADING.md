# Model loading and inference assets

All paths below are relative to the extracted delivery root. Every completed run contains `training_record.json`, a `COMPLETE` marker, predictions, calibration metadata, and either `model.pt` or `model.cbm`.

## Transformer checkpoints (V, S2, H, R)

```python
import torch

from models_and_code.model import ModelSpec, QuestionnaireTransformer

checkpoint = torch.load("models/full/H/SI_T0/fraction_100/train_20261001_sample_20261101/model.pt",
                        map_location="cpu", weights_only=False)
spec = ModelSpec(**checkpoint["spec"])
model = QuestionnaireTransformer(spec)
model.load_state_dict(checkpoint["state_dict"])
model.eval()

print(checkpoint["preprocessor"])
print(checkpoint["calibration"])
```

`models_and_code/data.py` reconstructs the raw-value and semantic inputs. The delivery includes `cache/full/`, `cache/common/`, and the required round-1 S2 cache under `cache/round1_s2/`, including prepared arrays, semantic IDs, frozen Qwen vectors, token lengths, and embedding audits. Qwen itself is never instantiated by downstream training or inference.

## CatBoost checkpoints (C and M)

```python
from catboost import CatBoostClassifier

model = CatBoostClassifier()
model.load_model("models/full/C/SI_T0/fraction_100/train_20261001_sample_20261101/model.cbm")
```

Use `models_and_code/train_catboost.py::load_catboost_features` to reconstruct the exact feature frame. Calibration parameters are adjacent in `training_record.json`.

## Integrity

`results/model_inventory.csv` lists every checkpoint, its byte size, and SHA-256 digest. `CHECKSUMS.sha256` covers the full delivered file set. The model path and Qwen `config.json` digest are recorded in `results/environment_and_model_version.json`.
