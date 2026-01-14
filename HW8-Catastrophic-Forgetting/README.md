# Analysis of Catastrophic Forgetting in LLMs

## 📌 Overview
This research-oriented project investigates **Catastrophic Forgetting** in Large Language Models. It analyzes how fine-tuning an LLM on a new domain (Math/GSM8K) affects its previously acquired knowledge (Safety/General Chat).

## 🛠 Tech Stack
- **Language:** Python 3
- **Model:** Llama-3-8B
- **Dataset:** GSM8K (Math), Safety Benchmarks
- **Techniques:** Fine-tuning, Knowledge Retention Evaluation

## 🚀 Key Features
- **Fine-Tuning Experiments:** Fine-tuned the model on specialized math datasets.
- **Forgetting Analysis:** Evaluated the model's performance on its original capabilities (Safety) after fine-tuning.
- **Trade-off Observation:** Visualized the trade-off curve between learning new tasks and retaining old ones.

## 📖 How to Run
1. Run `hw8_code.ipynb` to perform fine-tuning.
2. Run the evaluation script to observe performance degradation in non-target domains.
