# Retrieval-Augmented Generation (RAG) System

## 📌 Overview
This project implements a **Retrieval-Augmented Generation (RAG)** pipeline using **Llama-3** to answer questions based on the classic novel *"Dream of the Red Chamber"* (Honglou Meng). It addresses the issue of LLM hallucinations by retrieving accurate context before generation.

## 🛠 Tech Stack
- **Language:** Python 3
- **Model:** Llama-3-8B-Instruct (via Hugging Face)
- **Frameworks:** PyTorch, Sentence-Transformers, LangChain (Concepts)
- **Techniques:** Document Chunking, Vector Embeddings, Cosine Similarity Retrieval

## 🚀 Key Features
- **Data Preprocessing:** Implemented text chunking strategies to convert long literary texts into retrievable vector indices.
- **Retrieval Mechanism:** Utilized embedding models to align user queries with relevant text segments.
- **Prompt Engineering:** Designed system prompts to enforce evidence-based generation.

## 📖 How to Run
1. Install dependencies: `transformers`, `torch`, `sentence-transformers`.
2. Input your Hugging Face Access Token.
3. Run the notebook `hw2_code.ipynb`.
