---
created: 2024-07-08T09:16
updated: 2024-07-27T18:07
modified: 2024-07-27T18:07:56+02:00
tags:
  - ready
---
paper: Metacell-2 a divide-and-conquer metacell algorithm for scalable scRNA-seq analysis (2022)

Key aspects of the MC2 algorithm:

1. **Divide-and-conquer approach**:
- Splits large datasets into manageable "piles" of cells
- Processes piles independently and in parallel
- Scales as $O(N log N)$ instead of $O(N^2)$ for direct methods

2. Two-phase process:
- Phase 1: Creates *low-quality metacells* and groups them into *metagroups*
- Phase 2: Recomputes *high-quality metacells* using metagroups as piles

3. Rare cell type detection:
- Pre-process to identify *rare gene modules*
- *Outlier detection* and regrouping during main algorithm

4. Graph partitioning:
- Uses a novel "two-sided stability score" for optimizing partitions
- Balances internal connectivity and homogeneous size distribution

5. Adaptive outlier detection:
- Tunes parameters for fold change and number of outlier cells per gene

Implementation details:

1. Feature selection: Performed independently for each pile
2. Graph construction: Uses K-nearest neighbors approach
3. Partition optimization: Iterative process with cooling strategy for robustness
4. Size control: Enforces minimum and maximum metacell sizes
5. Connectivity control: Ensures metacells are well-connected subgraphs
6. Post-processing: Exports metacell UMI matrix for downstream analysis

Pros:
1. Highly scalable, can process millions of cells efficiently
2. Sensitive detection of rare cell types
3. Produces quantitative metacell states for easier downstream analysis
4. Adaptable to different dataset sizes and characteristics
5. Parallel processing for improved performance

Cons:
1. May lose some granularity compared to single-cell resolution
2. Requires careful parameter tuning for optimal results
3. Not directly applicable to other single-cell omics data without adaptation

When to use:
1. Large-scale scRNA-seq datasets (>100k cells)
2. When computational resources are limited
3. For creating quantitative models of transcriptional states
4. When rare cell type detection is important

When to avoid:
1. Small datasets where direct methods are feasible
2. When single-cell resolution is critical for the analysis
3. For non-RNA-seq single-cell data without proper adaptation
4. When the goal is direct clustering rather than creating metacells