---
created: 2024-02-06T16:51
updated: 2024-07-27T18:50
tags:
  - ready
modified: 2024-07-27T18:50:59+02:00
---
paper: https://genomebiology.biomedcentral.com/articles/10.1186/s13059-019-1663-x

## Summary
### Summary:
- **Objective**: The paper introduces the Partition-based graph abstraction (PAGA) method, which provides a graph-like map of single-cell RNA-seq data. This map *preserves the global topology* of the data, allowing for analysis at different resolutions and improving computational efficiency.
  
- **Background**: Single-cell RNA-seq offers a detailed molecular profile of individual cells, but analyzing the resulting datasets is challenging. Current computational methods either cluster data into distinct groups or infer [[pseudotemporal orderings]] of cells. PAGA aims to unify these two approaches.
  
- **Methodology**: PAGA creates maps of cells that *preserve both continuous and disconnected structures* in data at multiple resolutions. It uses a [[statistical model]] to determine the connectivity of groups of cells, resulting in a simpler PAGA graph. This graph can then be used to trace biological processes from progenitor cells to different cell fates.
  
- **Applications**: The paper demonstrates the application of PAGA in various datasets, including hematopoiesis and data from whole adult animals. The results show consistent topology and gene expression changes across different datasets.

> [!question] but how does topology is defined in this case ? - because if we consider topology only based on local cell densities, then it doesn't make much of the conceptual sense

- **Advantages**: PAGA-initialized manifold learning produces embeddings that are faithful to the global topology of high-dimensional data. This improves the interpretability of the data and speeds up the convergence of established algorithms.

### Step-by-step Algorithm:
1. **Data Representation**: High-dimensional gene expression data is represented as a [[kNN]] (k-nearest neighbors) graph. This *involves choosing a suitable low-dimensional representation and a distance metric* for computing neighborhood relations.
  
2. **Partitioning**: The kNN graph is partitioned at a desired resolution, where partitions represent groups of connected cells. The [[Louvain algorithm]] is commonly used for this, but other methods can also be applied.
  
3. **PAGA Graph Creation**: A PAGA graph is generated where *each node corresponds to a partition*. Nodes are connected by weighted edges that represent a statistical measure of connectivity between partitions.
  
4. **Discarding Spurious Edges**: By discarding edges with low weights, PAGA graphs reveal the denoised topology of the data at a chosen resolution, distinguishing between connected and disconnected regions.
  
5. **Ordering Cells**: Combining high-confidence paths in the PAGA graph with a random-walk-based distance measure on the single-cell graph, cells within each partition are ordered based on their distance from a root cell. This allows for tracing gene expression changes along complex trajectories at single-cell resolution.
  
6. **PAGA-initialized Manifold Learning**: The coarse-resolution embeddings of PAGA can be used to initialize established manifold learning and graph drawing algorithms. This results in single-cell embeddings that are more faithful to the global topology of the data.
  
7. **Application to Datasets**: PAGA is applied to various datasets to predict developmental trajectories and gene expression changes. The results are compared with existing algorithms to demonstrate the robustness and accuracy of PAGA.

The paper demonstrates the effectiveness of PAGA in providing a comprehensive and interpretable analysis of single-cell RNA-seq data. *The method reconciles clustering with trajectory inference*, offering a unified approach to understanding cellular heterogeneity and patterns.