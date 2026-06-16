# Low-Level Design

```mermaid
sequenceDiagram
    participant Dev as Engineer
    participant Data as Dataset Builder
    participant EvalBase as Baseline Eval
    participant Planner as Cost Planner
    participant Smoke as Smoke Test
    participant Train as LoRA/QLoRA Trainer
    participant Eval as Frozen Test Eval
    participant Registry as Artifact Registry
    participant Serve as Inference Server

    Dev->>Data: Prepare task examples
    Data->>Data: Validate schema and split data
    Dev->>EvalBase: Measure base model
    Dev->>Planner: Estimate tokens, VRAM, GPU-hours
    Planner-->>Dev: Run/no-run decision
    Dev->>Smoke: Train tiny sample
    Smoke-->>Dev: Config validation
    Dev->>Train: Run adapter training
    Train->>Registry: Store adapter checkpoint
    Dev->>Eval: Compare adapter vs baseline
    Eval->>Registry: Store metrics and model card
    Dev->>Serve: Load base model + adapter
```

## Key Controls

| Control | Purpose |
| --- | --- |
| Frozen test set | Prevent fake progress from overfitting. |
| Smoke test | Catch broken data or config cheaply. |
| Cost plan | Avoid uncontrolled GPU spend. |
| Model card | Document intended use, limits, and metrics. |
| Serving check | Confirm training format works at inference time. |
