# Llama 8B Fine-Tuning Lab Plan

This plan defines a responsible path for fine-tuning an 8B-class Llama model.

## Phase 0: Why

Fine-tuning should be used when we need a model to consistently follow a domain-specific behavior, format, classification rule, or extraction style.

Good fine-tuning use cases:

- Structured JSON extraction from messy finance or operations text.
- Incident report summarization.
- Support response style adaptation.
- Domain-specific classification.
- Policy-compliant response formatting.

Poor fine-tuning use cases:

- Memorizing frequently changing facts.
- Replacing a search or RAG system.
- Training on unreviewed sensitive data.
- Improving generic intelligence without a defined task.

## Phase 1: What

Initial target task:

```text
Extract structured JSON from finance/operations messages.
```

Example output fields:

```text
entity_type
account_reference
transaction_reference
amount
currency
event_date
risk_level
required_action
summary
confidence
```

Initial deliverables:

- Dataset schema.
- Prompt template.
- Baseline model evaluation.
- LoRA/QLoRA training script.
- Evaluation harness.
- Model card.
- Inference API.

## Phase 2: How

Core modules:

| Module | Responsibility |
| --- | --- |
| data | Raw examples, cleaned examples, train/validation/test split. |
| prompts | Instruction templates and output schemas. |
| training | SFTTrainer, LoRA/QLoRA config, checkpoints. |
| evaluation | Baseline comparison, JSON validity, field accuracy, hallucination checks. |
| serving | Load base model plus adapter for inference. |
| tracking | Experiment parameters, metrics, and artifacts. |
| governance | Model card, data statement, limitations, safety notes. |

## Phase 3: Analyze

Key risks and controls:

| Risk | Control |
| --- | --- |
| Dataset leakage | Separate frozen test set and hash dataset versions. |
| Overfitting | Validation monitoring, early stopping, small adapters, held-out evals. |
| Invalid JSON | Schema-constrained prompts and strict parser-based evaluation. |
| Hallucinated fields | Penalize unsupported fields and require null when unknown. |
| Sensitive data exposure | Synthetic or scrubbed data, no secrets, documented data policy. |
| Base model regression | Compare base vs tuned model on every evaluation run. |
| Deployment mismatch | Evaluate same prompt template used by inference server. |

## Phase 4: Act

### Slice 1: Project Foundation

- Create Python project.
- Add dependency management.
- Add dataset folder structure.
- Add baseline prompt template.
- Add README for data rules.

Done when:

- Project can run a baseline inference script against sample data.

### Slice 2: Dataset Builder

- Define JSONL training format.
- Add validation for required fields.
- Split train/validation/test.
- Hash and version dataset.

Done when:

- Dataset build is repeatable and invalid examples fail validation.

### Slice 3: Baseline Evaluation

- Run base model against frozen test set.
- Measure JSON validity, field-level accuracy, missing-field rate, and hallucinated-field rate.

Done when:

- Baseline metrics are recorded before tuning.

### Slice 4: LoRA/QLoRA Training

- Add SFT training script.
- Add LoRA configuration.
- Add checkpoint saving.
- Track parameters and metrics.

Done when:

- Adapter checkpoint is produced and can be loaded.

### Slice 5: Tuned Model Evaluation

- Evaluate tuned model against frozen test set.
- Compare against baseline.
- Generate error analysis.

Done when:

- Tuned model improvements and regressions are documented.

### Slice 6: Serving And Packaging

- Add inference API.
- Load base model plus adapter.
- Add model card.
- Add Dockerfile for inference environment.

Done when:

- Tuned model can be called through a local API with documented limitations.

