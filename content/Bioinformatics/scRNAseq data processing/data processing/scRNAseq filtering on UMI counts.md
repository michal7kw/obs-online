---
created: 2024-07-26T19:50
updated: 2024-07-27T17:46
modified: 2024-07-27T17:46:45+02:00
tags:
  - ready
---
Filtering scRNA-seq data based on UMI (Unique Molecular Identifier) counts using minimum and maximum thresholds is an important quality control step. Here are the main reasons for doing this:

1. Remove low-quality cells:
   - Cells with very low UMI counts often represent empty droplets, damaged cells, or background noise rather than actual cells. Filtering these out improves data quality.

2. Remove potential doublets:
   - Cells with unusually high UMI counts may represent doublets (two or more cells captured together). Removing these helps ensure you're analyzing single cells.

3. Reduce technical noise:
   - Cells with extremely low or high UMI counts can introduce technical noise and skew downstream analyses.

4. Improve computational efficiency:
   - Removing low-quality cells reduces the dataset size, making subsequent analyses faster and more computationally efficient.

5. Normalize for sequencing depth:
   - By setting a reasonable UMI count range, you can partially account for differences in sequencing depth between cells.

6. Focus on biologically relevant cells:
   - Cells with very low UMI counts may not have enough information for meaningful biological interpretation.

7. Remove potential contamination:
   - Extremely high UMI count cells might represent contamination from ambient RNA or other sources.

8. Improve clustering and differential expression analyses:
   - Removing outlier cells can lead to more accurate cell type identification and gene expression comparisons.