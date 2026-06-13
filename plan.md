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
- Cost, time, and GPU capacity estimate.
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
| VRAM failure | Estimate model, sequence length, batch size, quantization, and gradient accumulation before training. |
| Runaway cost | Track GPU-hours, failed-run buffer, and maximum experiment budget. |
| Misleading improvement | Require baseline, frozen test set, and error analysis before accepting a tuned adapter. |

## Critical Path

The project must move through these gates in order:

| Gate | Required Proof |
| --- | --- |
| Task gate | The target behavior is narrow, measurable, and better solved by fine-tuning than by prompting or RAG. |
| Data gate | Dataset has schema validation, train/validation/test split, no obvious leakage, and documented source policy. |
| Baseline gate | Base model metrics are recorded before training. |
| Capacity gate | Expected token count, sequence length, VRAM need, training time, and cost budget are estimated. |
| Smoke-test gate | Tiny training run completes and produces a loadable adapter. |
| Training gate | LoRA/QLoRA run records config, runtime, GPU type, and experiment metrics. |
| Evaluation gate | Tuned model beats baseline on frozen tests without unacceptable regressions. |
| Serving gate | Adapter runs through the inference path using the same prompt and schema rules. |

## Cost And Time Model

The repo should track cost in a repeatable way without hardcoding one cloud provider's price.

```text
estimated_cost = gpu_hour_price * (training_hours + evaluation_hours + failed_run_buffer)
```

Planning fields for every run:

| Field | Example |
| --- | --- |
| Dataset examples | `5,000` |
| Estimated train tokens | `examples * average_tokens_per_example * epochs` |
| Sequence length | `1024`, `2048`, or `4096` |
| GPU type | local RTX, L4, A10, A100, H100, etc. |
| VRAM | `24 GB`, `40 GB`, `80 GB` |
| Quantization | 4-bit QLoRA or no quantization |
| Training time | measured wall-clock time |
| Evaluation time | measured wall-clock time |
| Failed-run buffer | extra GPU-hours reserved for broken configs |
| Estimated cost | calculated from GPU-hour price |

Initial expectation:

| Run Type | Expected Time | Budget Purpose |
| --- | --- | --- |
| Smoke test | minutes to under 1 hour | Catch broken data, prompt, tokenizer, and checkpoint setup. |
| Small LoRA/QLoRA run | 1-6 hours | Check whether the task improves before scaling. |
| Serious adapter run | 6-24+ hours | Train a meaningful adapter after data and evals are proven. |
| Full fine-tune | days or more | Not part of the initial project path. |

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

### Slice 3: Baseline And Capacity Evaluation

- Run base model against frozen test set.
- Measure JSON validity, field-level accuracy, missing-field rate, and hallucinated-field rate.
- Estimate training tokens, sequence length, VRAM requirement, expected GPU-hours, and failed-run buffer.
- Document whether the first run can use local GPU, rented GPU, or CPU-only mock path.

Done when:

- Baseline metrics are recorded before tuning.
- Cost and time estimate exists before the first real training run.

### Slice 4: LoRA/QLoRA Training

- Run a smoke test on a tiny sample.
- Add SFT training script.
- Add LoRA configuration.
- Add checkpoint saving.
- Track parameters and metrics.

Done when:

- Adapter checkpoint is produced and can be loaded.
- Runtime, GPU type, VRAM, and estimated cost are recorded.

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
