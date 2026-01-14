# Retrieval-Augmented Generation (RAG) System

## 📌 Overview
本專案為台大「生成式人工智慧與機器學習導論」課程作業。實作了一個基礎的 RAG 系統，旨在透過檢索《紅樓夢》知識庫來增強 LLM (Llama-3) 的回答準確度，有效解決大型語言模型的「幻覺問題」。

## 🛠 Tech Stack
- **Language:** Python (Jupyter Notebook)
- **Model:** Llama-3-8B-Instruct (via Hugging Face)
- **Frameworks:** PyTorch, Sentence-Transformers
- **Techniques:** Document Chunking, Vector Embeddings, Cosine Similarity Retrieval

## 🚀 Key Features
- **Data Preprocessing:** 實作文本分塊 (Chunking) 技術，將長篇小說轉化為可檢索的向量索引。
- **Retrieval Mechanism:** 使用嵌入模型 (Embedding Model) 將問題與文本對齊，精準抓取相關段落。
- **Prompt Engineering:** 透過精心設計的指令，引導模型僅依據檢索到的資訊進行總結回答。
- [hw2_code.ipynb.ipynb](https://github.com/user-attachments/files/24608165/hw2_code.ipynb.ipynb)
