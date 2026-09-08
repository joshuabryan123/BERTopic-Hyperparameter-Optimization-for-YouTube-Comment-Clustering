# BERTopic Hyperparameter Optimization for YouTube Comment Clustering 🐝🔍

This repository contains the official implementation and experimental results for optimizing **BERTopic** hyperparameters using **Artificial Bee Colony (ABC)** vs **Grid Search** to analyze public perceptions of Indonesia's Q1 2026 economic growth from YouTube comments.

---

## 📌 Background

Topic modeling using BERTopic heavily relies on the configuration of its underlying components: dimension reduction via UMAP (`n_neighbors`) and density-based clustering via HDBSCAN (`min_cluster_size`). Suboptimal hyperparameter choices can lead to incoherent topics or a high proportion of unclassified noise/outliers.

This study explores public commentary on Indonesia's reported 5.61% economic growth in Q1 2026 and provides a comparative performance analysis between an exhaustive search method (**Grid Search**) and a swarm-intelligence metaheuristic approach (**Artificial Bee Colony**).

---

## 📊 Dataset

* **Source:** YouTube comments collected via YouTube Data API v3 (May 5–12, 2026).
* **Volume:** 5,089 raw comments from 16 videos across 10 channels (including mainstream news media like CNBC Indonesia, Liputan6, KompasTV, and individual content creators like Ferry Irwandi & Raymond Chin).
* **Preprocessing Pipeline:**
* Text cleaning (URLs, punctuation, numbers, special characters, and nonverbal expressions).
* Case folding, tokenization, and custom Indonesian slang normalization.
* Stopword removal using Sastrawi & domain-specific custom stopwords.
* Multi-word expression joining (e.g., `pertumbuhan_ekonomi`, `ekonomi_indonesia`).
* Deduplication and removal of short tokens (< 3 characters).



---

## ⚙️ Methodology

1. **Text Embedding:** Indonesian SBERT (`indobenchmark/indobert-base-p2`) yielding 768-dimensional contextual vectors.
2. **Dimension Reduction:** UMAP.
3. **Clustering:** HDBSCAN.
4. **Topic Representation:** c-TF-IDF refined with Maximal Marginal Relevance (MMR).
5. **Optimization Framework:**
* **Decision Variables:** UMAP `n_neighbors` $\in [10, 30]$ and HDBSCAN `min_cluster_size` $\in [20, 50]$.
* **Constraints:** Topic count $K \ge 2$ and outlier proportion $\le 30\%$.
* **Objective Function:** Weighted sum maximizing Topic Coherence (NPMI, weight 0.7) and Topic Diversity (Refined Diversity Jaccard, weight 0.3):



$$\text{Objective} = (0.7 \times \text{Coherence}) + (0.3 \times \text{Diversity})$$

---

## 📈 Results

### Optimization Benchmark

| Metric | Artificial Bee Colony (ABC) | Grid Search |
| --- | --- | --- |
| **Optimal `n_neighbors**` | 25 | 16 |
| **Optimal `min_cluster_size**` | 33 | 36 |
| **Objective Score** | 0.6649 | **0.7101** |
| **Topic Coherence** | 0.5689 | **0.5858** |
| **Topic Diversity** | 0.8889 | **1.0000** |
| **Outlier Rate** | 5.42% | **3.48%** |
| **Computational Time** | **1,788.04s (~30 mins)** | 9,916.26s (~2.75 hrs) |

* **Trade-off:** Grid Search achieved superior topic quality and a lower outlier rate, whereas **ABC converged 5.5x faster**, proving to be an efficient metaheuristic alternative for large search spaces.

### Identified Public Sentiment Topics

The optimal BERTopic model revealed two distinct topic dynamics:

* **Topic 0: Financial Activity (76.69% / 3,705 comments)**
* *Keywords:* `rupiah`, `uang`, `belanja`, `pengeluaran`, `omongkosong`
* *Insight:* Focuses on domestic financial reality, purchasing power, spending, and daily monetary concerns.


* **Topic 1: Public Skepticism (19.98% / 958 comments)**
* *Keywords:* `bohong`, `asing`, `mimpi`, `halu`, `suka`
* *Insight:* Reflects critical, skeptical, and pessimistic sentiment toward official macro-economic statistics reported by BPS.



---

## 💡 Conclusion & Recommendations

* **Conclusion:** Grid Search yields absolute global optimum parameters within small search spaces, while Artificial Bee Colony offers high computational efficiency with highly competitive solution quality.
* **Recommendations:**
* Expand the search space to include continuous hyperparameter optimization.
* Evaluate dynamic population size and adaptability mechanisms in ABC.
* Apply this optimization pipeline to streaming text or larger cross-platform social media datasets.
