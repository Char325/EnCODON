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

EnCODON is built on a modular, hierarchical **Graph Neural Network (GNN)** framework. The architecture is split into two "views" to isolate internal protein properties from external network topology.

### 1. Intra-Protein Encoding (The "Bottom View")
This stage is responsible for generating a feature vector for each individual protein. We compare two fundamentally different methods of representation:

* **The Geometric Baseline (HIGH-PPI):** Proteins are represented as graphs $g_b = (V_b, A_b, X_b)$ where nodes are residues and edges represent physical adjacency (residues within $10\text{\AA}$ of each other). A **Graph Convolutional Network (GCN)** aggregates seven physicochemical properties per residue to create a structural embedding.
    
    

* **The EnCODON Path (Sequence-based):** Instead of using 3D coordinates, we use the raw amino acid sequence. This is processed through a pre-trained **ESM-2 (35M parameters)** transformer model. Residue-level embeddings are extracted from the final layer and pooled into a fixed-length vector $P_i$. This captures evolutionary and functional information often lost in purely geometric models.

    

### 2. Inter-Protein Aggregator (The "Top View")
Once embeddings are generated, proteins are treated as nodes in a global **PPI Interaction Graph** $g_t = (V_t, A_t, X_t)$.

* **Topology Aggregation (GIN vs. GAT):** We primarily use the **Graph Isomorphism Network (GIN)** for its robust, permutation-invariant aggregation. We also tested the **Graph Attention Network (GAT)**, which applies attention coefficients $\alpha_{ij}$ to weigh the importance of neighboring proteins. Our findings show that GIN is significantly more stable when processing abstract ESM-2 features.
* **Interaction Prediction:** For a protein pair $(i, j)$, the final representations $h_i$ and $h_j$ are combined via element-wise multiplication ($\odot$):
    $$\hat{y}_{ij} = \text{Sigmoid}(\text{MLP}(h_i \odot h_j))$$
    The result is a probability score indicating the likelihood of an interaction.

    

---

<img width="800" height="600" alt="EnCODON Architecture" src="https://github.com/user-attachments/assets/8eea420f-b08c-47c3-b4dd-0f2ff3368eb0" />

## 🧪 Experimental Combinations

We conducted an extensive grid search across **32 distinct model configurations** to identify the most salient representations for PPI prediction. 

### Architectural Matrix
We evaluated the following pairings to determine if sequence-based abstractions could outperform explicit 3D geometry:

| Configuration ID | Intra-Protein (Bottom) | Inter-Protein (Top) | Purpose |
| :--- | :--- | :--- | :--- |
| **EnCODON-GIN** | **ESM-2** | **GIN** | **Proposed optimal model** |
| EnCODON-GAT | ESM-2 | GAT | Testing attention on abstract features |
| HIGH-PPI Baseline | GCN | GIN | Traditional geometric approach |
| GAT-Hybrid | GAT | GIN | Testing residue-level attention |

### Hyperparameter Search Space
Each combination was tested across variations to ensure results were not biased by specific settings:

* **Embedding Dimension ($D$):** Tested at **128** and **256**. $D=128$ provided the best balance of capacity and efficiency for ESM-2 features.
* **Batch Size:** Evaluated at **64** and **128**. Smaller batch sizes (64) generally led to better convergence for the transformer-GNN hybrid.
* **Training Epochs:** Models were trained for **100** and **500** epochs to monitor for over-smoothing and long-term stability.

> [!IMPORTANT]
> **Result:** The **ESM-2 + GIN** combination outperformed all others, proving that explicit 3D molecular graphs are largely redundant when high-capacity language model embeddings are available.

## 📊 Experimental Results

We evaluated 32 distinct configurations across different embedding sizes ($D$) and aggregation mechanisms. 
The following are the results for the peak performing models.

| Intra-Protein | Inter-Protein | Emb. Size ($D$) | Batch Size | F1-Score |
| :--- | :--- | :--- | :--- | :--- |
| **ESM-2** | **GIN** | **128** | **64** | **0.817** |
| ESM-2 | GIN | 256 | 64 | 0.794 |
| GAT | GIN | 128 | 64 | 0.635 |
| GCN (Baseline) | GIN | 256 | 64 | 0.630 |
| ESM-2 | GAT | 128 | 128 | 0.575 |
*Please take a look at the Final Report.pdf for a more detailed overview.
---

## 🚀 Getting Started

### Installation
Clone the repository and install the required dependencies:
```bash
git clone [https://github.com/](https://github.com/)[your-username]/EnCODON.git
cd EnCODON
pip install -r requirements.txt
```
