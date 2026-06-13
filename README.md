# Llama 8B Fine-Tuning Lab

This project designs and builds a production-minded fine-tuning workflow for an 8B-class Llama model using open-source tooling. The goal is to adapt a base instruction model to a focused domain task while measuring quality, safety, cost, and deployment readiness.

This is not a one-off notebook. It is a repeatable model customization pipeline with dataset preparation, supervised fine-tuning, LoRA/QLoRA adapters, evaluation, model packaging, and inference serving.

## Why This Project Matters

RAG is often the right first choice for knowledge-heavy systems, but fine-tuning is useful when a model must learn a consistent output format, domain language, classification behavior, extraction style, or task-specific reasoning pattern.

This project demonstrates how to fine-tune responsibly:

- Define the task clearly.
- Build and version the dataset.
- Train with parameter-efficient methods.
- Compare base model vs tuned model.
- Evaluate structured outputs and failure modes.
- Package and serve the tuned adapter.

## What We Are Building

The first version will fine-tune an 8B-class Llama instruction model for a focused domain task such as structured JSON extraction from finance/operations text, incident summarization, or policy-aware classification.

Primary capabilities:

| Capability | Purpose |
| --- | --- |
| Dataset builder | Convert raw examples into train/validation/test splits. |
| Prompt template | Standardize instruction, input, and expected output format. |
| SFT training | Supervised fine-tuning with LoRA/QLoRA. |
| Experiment tracking | Record parameters, dataset version, metrics, and artifacts. |
| Evaluation harness | Compare base and tuned models. |
| Model card | Document task, data, limitations, and intended use. |
| Inference server | Serve the tuned adapter through vLLM, TGI, or Ollama where practical. |

## How We Will Build It

Open-source-first stack:

| Layer | Technology |
| --- | --- |
| Base model | Llama 8B-class instruct model |
| Training | Hugging Face Transformers, TRL SFTTrainer |
| Parameter-efficient tuning | PEFT LoRA/QLoRA |
| Quantization | bitsandbytes 4-bit where supported |
| Fast training option | Unsloth where environment supports it |
| Dataset handling | Hugging Face Datasets |
| Evaluation | lm-evaluation-harness style tasks, custom JSON/format checks, human review set |
| Tracking | MLflow or Weights & Biases |
| Serving | vLLM, Text Generation Inference, or Ollama |
| Delivery | Docker Compose locally, GPU-ready container, Kubernetes job pattern later |

## Design Principles

- **Fine-tune for behavior, not facts**: use RAG for changing knowledge; use fine-tuning for task behavior and output discipline.
- **Keep a frozen test set**: never tune against the final evaluation set.
- **Track dataset lineage**: every run must know the data version and prompt template.
- **Compare against baseline**: tuned model must beat the base model on defined metrics.
- **Prefer LoRA/QLoRA first**: full fine-tuning is expensive and unnecessary for this project start.
- **Evaluate failure modes**: invalid JSON, hallucination, refusal quality, unsafe output, and domain mistakes.

## Project Structure

```text
llama8b-finetuning-lab/
├── README.md
├── plan.md
├── diagrams/
│   └── finetuning-pipeline.md
└── docs/
```

## Current Status

This project starts with planning and architecture. The next step is to select the first task, define the dataset schema, and implement the dataset preparation and baseline evaluation scripts.

