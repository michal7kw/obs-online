---
created: 2024-01-09T14:52
updated: 2024-07-27T18:31
modified: 2024-07-27T18:31:43+02:00
tags:
  - ready
---

[Pathifier (weizmann.ac.il)](https://www.weizmann.ac.il/complex/compphys/software/yotam/pathifier/)
Pathifier is an algorithm that infers pathway deregulation scores for each tumor sample on the basis of expression data

![[Pasted image 20240116160904.png]]

Pathifier algorithm,  calculates a [[Pathway Deregulation Score]] (**PDS**) for each sample in a dataset. This score represents the extent to which the pathway is deregulated in every individual sample. The calculation of the PDS involves several steps:

1. **Representation of Samples**: Each sample is represented as a point in a multidimensional space, where each dimension corresponds to the expression level of a gene that belongs to a given pathway.

2. **Principal Curve Calculation**: A one-dimensional curve in this multidimensional space (or in a subspace of it) is determined to best describe the variability of the samples across the space. This curve is assumed to pass through the "middle of the cloud" of samples, representing the typical pathway functionality.

3. **Projection and Distance Measurement**: Each sample is projected onto this curve. The PDS is defined as the distance, measured along the curve, of the projection of a sample from the projection of the normal samples.

This method is designed to transform gene-level information into pathway-level information, generating a biologically relevant representation of each sample. It focuses on capturing the variation in pathway functionality due to disease progression or other factors, without requiring detailed knowledge of the underlying network or wiring diagram of the pathway's activity. This approach is described as "phenomenological" and context-specific, as it relies on the expression levels of genes assigned to pathways and the variability observed in the specific dataset and type of cancer being investigated.

Regarding the classification of the Pathifier algorithm as either Over-Representation Analysis (ORA) or Functional Class Scoring (FCS), it appears to align more closely with the principles of FCS. This is because Pathifier transforms gene-level data into pathway-level data and computes scores that reflect the overall deregulation of pathways in a sample, rather than simply identifying pathways that are over-represented among a set of differentially expressed genes, as in ORA.

For more detailed information, you can refer to the original document: [Pathifier Paper, page 1](https://myaidrive.com/R6QBuXTviibT4UQq/Pathifier---.pdf?pdfPage=1), [page 4](https://myaidrive.com/R6QBuXTviibT4UQq/Pathifier---.pdf?pdfPage=4), [page 5](https://myaidrive.com/R6QBuXTviibT4UQq/Pathifier---.pdf?pdfPage=5), [page 6](https://myaidrive.com/R6QBuXTviibT4UQq/Pathifier---.pdf?pdfPage=6).