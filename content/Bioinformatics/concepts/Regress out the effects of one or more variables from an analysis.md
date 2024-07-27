---
created: 2024-02-06T16:51
updated: 2024-07-27T18:50
modified: 2024-07-27T18:50:04+02:00
tags:
  - ready
---
"Regressing out" or "regression" in this context refers to a statistical method used to remove the effects of one or more variables from an analysis. 

e.g. We want to regress out effects of total counts per cell and the percentage of mitochondrial genes expressed

In the context of scRNA-seq, when you regress out the effects of total counts per cell or the percentage of mitochondrial genes expressed, you're mathematically adjusting the data to remove the influence of these factors. 

*This ensures that the remaining variation in the data is due to genuine biological differences between cells and not these technical or non-specific biological factors.*

![[Pasted image 20230911182943.png]]
### Linear Regression: A Brief Overview

"Regressing out" is typically accomplished using linear regression. At its core, linear regression is a statistical method used to model and analyze the relationships between a dependent variable and one or more independent variables. The main goal of linear regression is to find the best fit straight line that accurately predict the output values within a range.

### How "Regressing Out" Works in scRNA-seq:

1. **Model Creation**: *For each gene's expression* in scRNA-seq data, a linear regression model is created where the gene's expression is the dependent variable, and the unwanted factors (like total counts per cell or percentage of mitochondrial genes expressed) are the independent variables.

2. **Fitting the Model**: The linear regression model is then "fitted" to the data. This means that the model will try to find the best line that describes the relationship between the gene's expression and the unwanted factors.

3. **Residual Calculation**: Once the model is fitted, it can predict the gene's expression based solely on the unwanted factors. The difference between the actual gene expression and the predicted expression (based on unwanted factors) is called the residual.

4. **Using Residuals**: The residuals represent the gene's expression after removing the influence of the unwanted factors. In other words, the residual captures the variation in gene expression that is not explained by the unwanted factors.

5. **Updating the Data**: The original gene expression values in the scRNA-seq data are then replaced with these residuals. This effectively "removes" the influence of the unwanted factors from the data.

### Why It Works:

By using linear regression in this manner, you're isolating the portion of the gene's expression that is due to the unwanted factors. By subtracting this portion out, you're left with the gene's expression that is independent of these factors. This allows researchers to focus on the genuine biological differences between cells without the noise introduced by these unwanted factors.

### An Analogy:

Imagine you're trying to understand the effect of exercise on weight loss, but you know that sleep also affects weight. If you were to "regress out" the effect of sleep, you'd be trying to understand the relationship between exercise and weight loss while mathematically removing the influence of sleep. This would give you a clearer picture of how exercise alone affects weight loss.

Similarly, in scRNA-seq, by regressing out unwanted factors, researchers get a clearer picture of the genuine biological differences between cells.

In summary, "regressing out" is a mathematical way to remove the influence of unwanted factors from data, allowing for a clearer and more accurate analysis of the underlying biological processes.