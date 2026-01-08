# EnCODON — From Atoms to Abstractions
### Geometric Representation Saliency in Protein-Protein Interaction Networks

This repository contains the implementation of **EnCODON**, a framework for investigating the optimal level of geometric abstraction for predicting Protein-Protein Interactions (PPI). This research evaluates whether explicit 3D molecular graph topology is necessary when high-quality sequence-derived embeddings from protein language models are available.

---

## 📖 Research Summary
Predicting PPIs is fundamental to understanding cellular function and drug discovery. While traditional models like **HIGH-PPI** use Graph Neural Networks (GNNs) to process full 3D protein structures (atomic/residue level), EnCODON demonstrates that sequence-based features from the **ESM-2** transformer significantly outperform geometric features.

### Key Findings
* **Geometric Redundancy:** Explicit molecular graph topology is largely redundant when using powerful sequence-derived embeddings.
* **Performance:** The ESM-2 + GIN architecture achieved a peak **F1-score of 0.817**, a ~30% relative improvement over traditional geometric GCN baselines.
* **Attention Failure:** The Graph Isomorphism Network (GIN) aggregator proved more robust for PPI topology than Graph Attention Networks (GAT), which tended to over-smooth abstract ESM-2 features.

---

## 🛠️ Framework Architecture



The model utilizes a hierarchical structure:

1.  **Intra-Protein Encoder (Bottom View):**
    * **Baseline:** GCN operating on residue-level physicochemical properties and contact maps.
    * **EnCODON:** Pre-trained **ESM-2 (35M parameters)** transformer. Raw sequences are tokenized and processed to produce fixed-dimensional protein representations ($\mathbf{P}_i$).



2.  **Inter-Protein Aggregator (Top View):**
    * Proteins act as nodes in a global interaction graph.
    * We utilize **Graph Isomorphism Networks (GIN)** to aggregate neighborhood information and predict the propensity of interaction between protein pairs.

---

## 📊 Experimental Results

We evaluated 32 distinct configurations across different embedding sizes ($D$) and aggregation mechanisms. 

| Intra-Protein | Inter-Protein | Emb. Size ($D$) | Batch Size | F1-Score |
| :--- | :--- | :--- | :--- | :--- |
| **ESM-2** | **GIN** | **128** | **64** | **0.817** |
| ESM-2 | GIN | 256 | 64 | 0.794 |
| GAT | GIN | 128 | 64 | 0.635 |
| GCN (Baseline) | GIN | 256 | 64 | 0.630 |
| ESM-2 | GAT | 128 | 128 | 0.575 |

---

## 🚀 Getting Started

### Installation
Clone the repository and install the required dependencies:
```bash
git clone [https://github.com/](https://github.com/)[your-username]/EnCODON.git
cd EnCODON
pip install -r requirements.txt
```
