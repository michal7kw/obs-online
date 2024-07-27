---
created: 2024-01-20T20:49
updated: 2024-07-27T18:29
modified: 2024-07-27T18:29:59+02:00
tags:
  - ready
---
`doublet_scores` in the context of single-cell RNA sequencing (scRNA-seq) data analysis, particularly when using a tool like `scrublet`, represent a measure of the likelihood that each cell is a doublet. In scRNA-seq, doublets are artifacts where two or more cells are mistakenly captured and sequenced together as if they were a single cell. Identifying and removing doublets is crucial for accurate data analysis.

### How Doublet Scores are Calculated:

1. **Simulation of Artificial Doublets:**
   - `scrublet` starts by simulating artificial doublets from your data. It does this by randomly pairing cells from your dataset and merging their transcriptomes. This process mimics the kind of doublets that can occur during cell capture.

2. **Feature Selection:**
   - The algorithm selects features (genes) that are useful for distinguishing between singlets (real, individual cells) and doublets. This is often based on the variability of gene expression across cells.

3. **Embedding and Clustering:**
   - Both real and simulated cells are then projected into a lower-dimensional space (e.g., using PCA). In this space, singlets and doublets are expected to form separate clusters due to their different gene expression patterns.

4. **Training a Classifier:**
   - `scrublet` uses these embeddings to train a nearest-neighbor classifier. This classifier learns from the simulated doublets (as positive examples) and the original cells (as negative examples).

5. **Scoring Real Cells:**
   - Each real cell is then scored based on its similarity to the simulated doublets. This is typically done by looking at its nearest neighbors in the embedding space. If a cell’s nearest neighbors are mostly simulated doublets, it gets a high doublet score, indicating a high likelihood of being a doublet.

6. **Thresholding:**
   - Finally, a threshold is applied to these scores to classify cells as singlets or doublets. Cells with scores above the threshold are predicted to be doublets.

### Interpretation of Doublet Scores:

- **High Score:** A high doublet score suggests that the cell's gene expression profile is similar to that of the artificial doublets, indicating it might be a doublet.
- **Low Score:** A low score suggests the cell's profile is distinct from the artificial doublets, indicating it is likely a singlet.

### Important Considerations:

- **False Positives/Negatives:** The method isn't perfect and can produce false positives (singlets incorrectly labeled as doublets) and false negatives (doublets labeled as singlets).
- **Data Specificity:** The accuracy of doublet detection can depend heavily on the specific characteristics of the dataset, such as the cell types present and the rate of doublet formation in the experiment.
- **Thresholding:** The choice of threshold for classifying cells as doublets or singlets is critical and can vary depending on the dataset and the expected doublet rate.
