# Round 2 implementation audit (P0)

## Scope and fixed inputs

- Fixed person split: `/home/amax/lingp/code/snowx/qwen8b_questionnaire_experiment/results/split_assignment.csv`; counts {'train': 110590, 'validation': 23698, 'test': 23698}. It is reused without re-randomization.
- Full input: 596 approved items after excluding the two declared aliases `['BI_ethnicity_common', 'BI_siblings_common']`. No target, other label, row identity, or post-prediction variable enters a model.
- Targets are independently filtered. Trans retains all 97,404 valid labels with class counts {'0': 62005, '1': 4209, '2': 31190}. Class 2 retains 15,255 people without SB_T1 and 15,935 with SB_T1.
- Two approved Full items are all missing: `['LGBT_K_H14', 'LGBT_K_E14']`. They remain on the item axis and receive learned missing representations.

## Corrections relative to round 1

Round-1 V encoded an entire multiselect combination as one category and fixed numeric scaling on the full training partition. Round 2 parses all 18 multiselect items as approved JSON sets (with literal `无` as an observed empty set) and refits numeric mean/SD on each actual training subset. `NA` is always distinct from valid zero, an empty set, and a negative response.

## Implemented input paths

- V: trainable raw value embedding + shared item identity + item-specific missing embedding. Categorical codes are not treated as numbers. Continuous values use a scalar channel; HH:MM uses exact cyclic sin/cos channels.
- S2: the frozen round-1 full QA Qwen vector, an item identity, explicit missing state, a trainable projection, CLS Transformer, and classifier.
- H: the same V raw branch plus separate frozen question and answer Qwen embeddings. Each semantic branch has a trainable projection; all per-item branches are fused before the questionnaire Transformer.
- R: identical to H in shape, trainable parameters, and optimization, but deterministic unit-norm random vectors replace question/answer Qwen vectors while preserving shared text IDs.
- Qwen is not instantiated during downstream training. It is frozen by construction; only cached vectors are read.

The concrete templates and identity-free examples are in `artifacts/input_template_examples.json`. S1 is retained only as a historical input template and is not a formal round-2 model.

## Qwen pooling and cache integrity

- Model: Qwen3-Embedding-8B (`Qwen3Model`, transformers 5.12.1).
- Official last-token pooling is used. Under actual left padding, every tested row's final valid token is index `sequence_length-1`; the implementation also contains the attention-mask formula needed for right padding.
- Embeddings are L2 normalized before float16 storage. Question token lengths: min/median/max 11/31.0/64; answer lengths: 2/9.0/108. No text exceeded 8192 tokens and no truncation occurred.
- 256 sampled item/answer cache mappings matched: `True`. Missing ID 0 exactly matches the raw missing matrix: `True`.

## Real Full batch shape audit

```json
{
  "batch_rows": 4,
  "category_ids": [
    4,
    596
  ],
  "numeric_values": [
    4,
    96,
    2
  ],
  "multiselect_hot": [
    4,
    18,
    16
  ],
  "item_embedding": [
    4,
    596,
    64
  ],
  "missing_mask": [
    4,
    596
  ],
  "raw_value_embedding": [
    4,
    596,
    64
  ],
  "question_embedding_lookup": [
    596,
    4096
  ],
  "answer_unique_embedding_lookup": [
    176,
    4096
  ],
  "answer_logical_lookup": [
    4,
    596,
    4096
  ],
  "question_projection": [
    4,
    596,
    64
  ],
  "answer_projection": [
    4,
    596,
    64
  ],
  "fused_item_tokens": [
    4,
    596,
    64
  ],
  "sequence_with_cls": [
    4,
    597,
    64
  ],
  "transformer_output": [
    4,
    597,
    64
  ],
  "cls_pooled": [
    4,
    64
  ],
  "logits": [
    4,
    2
  ]
}
```

The item axis remains 596 from raw lookup through fused item tokens. CLS changes the sequence length to 597, and only the encoded CLS token is passed to the output head. There is no mean/SD aggregation over questions. The unique semantic lookup is an exact compute optimization: trainable projections are applied once per unique cache ID in the batch and then restored to the original person-by-item positions.

## Missingness and type audit

Per-item, per-split missing rates and target counts are in `artifacts/input_audit_full.csv`. The source files expose `NA` but no reliable skip/nonresponse reason, so missing reasons are marked unknown rather than inferred. All multiselect source IDs passed dictionary membership checks; all HH:MM values are strictly parsed; categorical/ordinal response codes remain categorical.

## Validation status

P0 mapping, pooling, shape, type, and label-count checks passed. Round 2 is exploratory because the fixed test results were viewed in round 1. Hyperparameters, stopping, calibration, and item selection remain train/validation-only.
