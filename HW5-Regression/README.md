# Deep Learning for Regression Analysis

## Overview
A foundational Deep Learning project aimed at solving a regression problem. The objective is to predict continuous target variables based on a set of input features using a Deep Neural Network (DNN).

## Tech Stack
* **Language:** Python 3
* **Framework:** PyTorch
* **Libraries:** Pandas, Scikit-Learn, Matplotlib, Sentence-Transformers

## Key Features
* **Data Preprocessing:** Handled missing values, feature scaling (normalization), and dataset splitting.
* **Text Embedding:** Two free-text columns (`reviews`, `short_description`) encoded with `all-mpnet-base-v2` and reduced to 100 dimensions each by PCA before concatenation onto the numeric features.
* **Model Architecture:** Designed a Multi-Layer Perceptron (MLP) with adjustable hidden layers and activation functions (ReLU).
* **Optimization:** Implemented Gradient Descent optimization (Adam/SGD) and tuned hyperparameters to minimize MSE Loss.

## How to Run
1. Run hw5_code.ipynb to train the model.
2. Visualize the loss curve to monitor convergence.
