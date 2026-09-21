# LLM Security: Malicious Instruction Defense

## Overview
This project focuses on LLM Safety, specifically designing defense mechanisms against Prompt Injection attacks. The goal is to prevent the model from generating harmful, unethical, or illegal content when prompted by malicious users.

**The defense prompt itself was submitted to the course's external judging system and is not in this repository.** `hw4_code.ipynb` holds only the course-supplied token counter; see [`../docs/known-issues.md`](../docs/known-issues.md) D-3.

## Tech Stack
* **Language:** Python 3
* **Model:** Llama-3 / Gemma
* **Techniques:** Defense Prompting, Input Filtering, Safety Guardrails

## Key Features
* **Attack Simulation:** Analyzed various prompt injection techniques used to bypass safety guardrails.
* **Defense Implementation:** Designed system-level defense prompts to filter out malicious instructions while maintaining model utility.
* **Evaluation:** Assessed the defense mechanism's success rate against a dataset of adversarial prompts.

## How to Run
1. Load the dataset of malicious instructions.
2. Run hw4_code.ipynb to evaluate the model's response with and without defense prompts.
