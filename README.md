# Llama 8B Fine-Tuning Lab

This repository documents an experimental fine-tuning workflow for an 8B-class Llama model. The focus is responsible experimentation: dataset readiness, baseline evaluation, LoRA/QLoRA planning, cost control, and serving checks.

## Design Focus

| Area | Design Point |
| --- | --- |
| Task scope | Fine-tune only for a narrow behavior such as structured extraction or classification style. |
| Dataset readiness | Clean, validate, split, and version examples before training. |
| Baseline | Measure the base model before any adapter run. |
| Cost control | Estimate tokens, VRAM, GPU-hours, and failed-run buffer before scaling. |
| Training | Prefer LoRA/QLoRA experiments before considering full fine-tuning. |
| Evaluation | Compare tuned adapter against the frozen test set and failure cases. |
| Serving | Verify the same prompt format works in inference. |

## Diagrams

| Diagram | Purpose |
| --- | --- |
| [diagrams/01-high-level-design.md](diagrams/01-high-level-design.md) | End-to-end fine-tuning workflow. |
| [diagrams/02-low-level-design.md](diagrams/02-low-level-design.md) | Training run control flow. |

## Current Status

Experimental design repository. It does not claim a completed fine-tuned model.
