# Analysis of Catastrophic Forgetting in LLMs

## Overview
This research-oriented project investigates **Catastrophic Forgetting** in Large Language Models. It analyzes how fine-tuning an LLM on a new domain (Math/GSM8K) affects its previously acquired knowledge (Safety/General Chat).

**Neither evaluation cell has recorded output.** Fine-tuning ran and is recorded; the GSM8K accuracy and AILuminate safety comparisons this project is built to make were never executed. See [`../docs/known-issues.md`](../docs/known-issues.md) C-5.

## Tech Stack
- **Language:** Python 3
- **Model:** Llama-3.2-1B-Instruct, 4-bit NF4 (`unsloth/Llama-3.2-1B-Instruct`)
- **Dataset:** GSM8K (Math), AILuminate (Safety)
- **Techniques:** LoRA fine-tuning, Knowledge Retention Evaluation

## Key Features
- **Fine-Tuning Experiments:** LoRA fine-tuned the model on a length-filtered GSM8K subset (2,491 of 7,472 examples).
- **Forgetting Analysis (designed, not run):** Evaluation harnesses exist for GSM8K accuracy and AILuminate safety before/after fine-tuning; neither cell has recorded output.
- **Trade-off Observation:** Not produced — there is no accuracy or safety figure to compare, before or after fine-tuning.

## How to Run
1. Run `hw8_code.ipynb` to perform fine-tuning.
2. Run the evaluation script to observe performance degradation in non-target domains.
