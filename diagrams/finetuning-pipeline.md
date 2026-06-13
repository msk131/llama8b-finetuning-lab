# Fine-Tuning Pipeline

```mermaid
flowchart TB
    RAW["Raw Examples\nsynthetic or scrubbed domain data"]
    CLEAN["Cleaning And Review\nPII removal, quality checks"]
    DATASET["Dataset Builder\nJSONL, schema validation,\ntrain/val/test split"]
    PROMPT["Prompt Template\ninstruction, input, output schema"]
    CAPACITY["Cost And Capacity Plan\nexamples, tokens, VRAM,\nGPU-hours, budget"]
    BASE["Base Llama 8B-Class\nInstruction Model"]
    BASEEVAL["Baseline Evaluation"]
    SMOKE["Smoke Test\nsmall sample, cheap failure"]
    TRAIN["SFT Training\nTRL + PEFT LoRA/QLoRA"]
    ADAPTER["LoRA Adapter\ncheckpoint artifact"]
    EVAL["Tuned Evaluation\naccuracy, JSON validity,\nhallucination checks"]
    CARD["Model Card\nintended use, data, limits"]
    SERVE["Inference Server\nvLLM / TGI / Ollama"]
    TRACK["Experiment Tracking\nMLflow / W&B"]

    RAW --> CLEAN
    CLEAN --> DATASET
    DATASET --> CAPACITY
    DATASET --> BASEEVAL
    PROMPT --> BASEEVAL
    BASE --> BASEEVAL
    CAPACITY --> SMOKE
    DATASET --> SMOKE
    PROMPT --> SMOKE
    BASE --> SMOKE
    SMOKE --> TRAIN
    PROMPT --> TRAIN
    BASE --> TRAIN
    TRAIN --> ADAPTER
    TRAIN --> TRACK
    ADAPTER --> EVAL
    BASEEVAL --> EVAL
    EVAL --> CARD
    ADAPTER --> SERVE
    CARD --> SERVE
    EVAL --> TRACK
```

## Flow Summary

1. Raw examples are cleaned, reviewed, and scrubbed.
2. Dataset builder validates schema and creates train/validation/test splits.
3. Cost and capacity plan estimates examples, tokens, VRAM, GPU-hours, runtime, and budget.
4. Base model is evaluated before tuning.
5. A smoke test catches broken data or training config cheaply.
6. LoRA/QLoRA training creates a small adapter.
7. Tuned model is evaluated against the frozen test set.
8. Model card records intended use, limitations, metrics, and data notes.
9. Adapter is served with the base model through an inference server.

## Cost Control Gates

- Do not start a real training run before the smoke test succeeds.
- Do not scale dataset size before baseline metrics exist.
- Do not accept a tuned adapter without frozen test evaluation.
- Stop failed runs early when loss, JSON validity, or validation metrics show the configuration is broken.
