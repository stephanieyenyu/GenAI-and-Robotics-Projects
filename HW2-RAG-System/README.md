# Retrieval-Augmented Generation (RAG) System

## Overview
This project implements a Retrieval-Augmented Generation (RAG) pipeline using Gemma-3-4B to answer questions based on the classic novel "Dream of the Red Chamber" (Honglou Meng). It addresses the issue of LLM hallucinations by retrieving accurate context before generation.

## Tech Stack
* **Language:** Python 3
* **Model:** Gemma-3-4B, 4-bit (`unsloth/gemma-3-4b-it`, via Hugging Face)
* **Frameworks:** PyTorch, Sentence-Transformers, rank_bm25, jieba
* **Techniques:** Fixed-width chunking, BM25 sparse retrieval over jieba tokens, dense retrieval with a multilingual E5 encoder, reciprocal rank fusion

## Key Features
* **Data Preprocessing:** Implemented fixed-width text chunking to convert long literary texts into retrievable indices.
* **Retrieval Mechanism:** Combines BM25 sparse retrieval with dense embedding retrieval, fused by reciprocal rank fusion, so neither retriever's failure mode dominates alone.
* **Prompt Engineering:** Designed system prompts to enforce evidence-based generation.

## How to Run
1. Install dependencies: transformers, torch, sentence-transformers.
2. Input your Hugging Face Access Token.
3. Run the notebook hw2_code.ipynb.
