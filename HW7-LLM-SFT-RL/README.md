# LLM Alignment: SFT & RLHF

## Overview
An advanced project exploring the full pipeline of Large Language Model (LLM) Alignment. The task involves fine-tuning a base model to follow instructions and aligning it with human preferences using Reinforcement Learning.

## Tech Stack
* **Language:** Python 3
* **Model:** Llama-3-8B
* **Frameworks:** PyTorch, Hugging Face transformers, trl (Transformer Reinforcement Learning)
* **Techniques:** LoRA (Low-Rank Adaptation), SFT, RLHF (PPO/DPO)

## Key Features
* **Supervised Fine-Tuning (SFT):** Fine-tuned the pre-trained model on an instruction dataset.
* **Reinforcement Learning (RL):** Implemented RLHF to align the model's outputs with a reward model, enhancing response quality and safety.
* **Parameter Efficiency:** Utilized LoRA/QLoRA to fine-tune the massive model on consumer-grade GPUs (T4).

## How to Run
1. Install bitsandbytes, peft, and trl.
2. Run hw7_code.ipynb to execute the SFT training loop followed by the RL alignment step.
