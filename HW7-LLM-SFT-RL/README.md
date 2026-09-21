# LLM Alignment: SFT & RLHF

## Overview
An advanced project exploring the full pipeline of Large Language Model (LLM) Alignment. The task involves fine-tuning a base model to follow instructions and aligning it with human preferences using Reinforcement Learning.

**The DPO stage is present as code and was never executed.** Only supervised fine-tuning has a recorded run; see [`../docs/known-issues.md`](../docs/known-issues.md) C-4.

## Tech Stack
* **Language:** Python 3
* **Model:** Gemma-3-4B, 4-bit NF4 (`jaxon3062/gemma-3-4b-pt-chat`)
* **Frameworks:** PyTorch, Hugging Face transformers, trl (Transformer Reinforcement Learning)
* **Techniques:** LoRA (Low-Rank Adaptation), SFT, DPO (Direct Preference Optimization)

## Key Features
* **Supervised Fine-Tuning (SFT):** Fine-tuned the pre-trained model on an instruction dataset.
* **Preference Optimisation (DPO):** A `HW7DPOTrainer` subclass and trainer configuration are implemented but the stage was never run — no result is recorded.
* **Parameter Efficiency:** Utilized LoRA/QLoRA to fine-tune the model on consumer-grade GPUs (T4).

## How to Run
1. Install bitsandbytes, peft, and trl.
2. Run hw7_code.ipynb to execute the SFT training loop followed by the RL alignment step.
