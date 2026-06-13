# Fine-Tuning Pipeline

```mermaid
flowchart TB
    RAW["Raw Examples\nsynthetic or scrubbed domain data"]
    CLEAN["Cleaning And Review\nPII removal, quality checks"]
    DATASET["Dataset Builder\nJSONL, schema validation,\ntrain/val/test split"]
    PROMPT["Prompt Template\ninstruction, input, output schema"]
    BASE["Base Llama 8B-Class\nInstruction Model"]
    BASEEVAL["Baseline Evaluation"]
    TRAIN["SFT Training\nTRL + PEFT LoRA/QLoRA"]
    ADAPTER["LoRA Adapter\ncheckpoint artifact"]
    EVAL["Tuned Evaluation\naccuracy, JSON validity,\nhallucination checks"]
    CARD["Model Card\nintended use, data, limits"]
    SERVE["Inference Server\nvLLM / TGI / Ollama"]
    TRACK["Experiment Tracking\nMLflow / W&B"]

    RAW --> CLEAN
    CLEAN --> DATASET
    DATASET --> BASEEVAL
    PROMPT --> BASEEVAL
    BASE --> BASEEVAL
    DATASET --> TRAIN
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
3. Base model is evaluated before tuning.
4. LoRA/QLoRA training creates a small adapter.
5. Tuned model is evaluated against the frozen test set.
6. Model card records intended use, limitations, metrics, and data notes.
7. Adapter is served with the base model through an inference server.

