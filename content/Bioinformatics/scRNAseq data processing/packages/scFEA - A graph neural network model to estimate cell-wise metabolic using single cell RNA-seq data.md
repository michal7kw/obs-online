---
created: 2024-02-02T17:45
updated: 2024-07-27T18:25
modified: 2024-07-27T18:25:21+02:00
tags:
  - ready
---
# Infographics

![[Network Reconstruction Process.svg]]

# Repo analysis
The [scFEA GitHub repository](https://github.com/changwn/scFEA) provides a computational framework named scFEA (single cell Flux Estimation Analysis), designed to infer cell-wise metabolic flux from single-cell RNA-sequencing (scRNA-seq) data. This framework leverages a graph neural network model to estimate metabolic activities within individual cells, offering insights into metabolic heterogeneity and interplay among cells in various conditions, including cancer.
### Key Components and Implementation Details:
1. **Objective and Methodology**:
   - scFEA aims to bridge the gap in understanding intra-tissue metabolic heterogeneity and cell-cell metabolic communications without the need for single-cell metabolomics technology.
   - It uses a reorganized human metabolic map, a probabilistic model for flux balance constraints, and a graph neural network-based optimization solver to capture the relationship between gene expressions and reaction rates.

2. **Installation and Requirements**:
   - The software is implemented in Python and requires packages such as PyTorch, NumPy, Pandas, Matplotlib, and [[MAGIC]] for imputation.
   - Installation involves cloning the repository and installing the required Python packages as listed in the `requirements` file.

3. **Usage**:
   - The main script `src/scFEA.py` can be executed with various command-line arguments to specify input directories, data files, and parameters like the number of epochs for training.
   - It supports functionalities like *single-cell data imputation using [[MAGIC]]*, and the output includes predicted metabolic flux and metabolite stress at single-cell resolution.

4. **Tutorials**:
   - Two Jupyter notebook tutorials (`scFEA_tutorial1.ipynb` and `scFEA_tutorial2.ipynb`) provide step-by-step instructions on using scFEA, including installation, testing, and examples for both human and mouse models.
   - `scFEA_tutorial2.ipynb` specifically demonstrates how to *integrate scFEA analysis into a Seurat pipeline*, a popular R package for single-cell genomics analysis.

5. **Core Implementation Files**:
   - `ClassFlux.py`: Defines the FLUX class, a *neural network model for estimating metabolic flux*.
   - `DatasetFlux.py`: Contains the `MyDataset` class for handling input data.
   - `util.py`: Includes utility functions such as `pearsonr` for calculating [[Pearson correlation]] coefficients.
   - `scFEA_grad.py`: An extension for calculating gradients by refitting the model for each cell, potentially for sensitivity analysis or further studies on model behavior.

6. **Data and Results**:
   - The `data` directory contains various CSV files with metabolic module information, compound names, and stoichiometry matrices for both human and mouse models.
   - The `input` directory is intended for single-cell RNA-seq data files that are used as input for the analysis.
   - The `output` directory is designated for the results, including predicted fluxes and balances.

7. **Supplementary Materials**:
   - Supplementary data, figures, and tables are provided to complement the research paper associated with scFEA, offering additional insights into the methodology and findings.

### Conclusion:
scFEA represents a significant advancement in computational biology, enabling researchers to explore metabolic dynamics at the single-cell level. By providing a detailed framework for analyzing scRNA-seq data in the context of metabolism, scFEA facilitates a deeper understanding of cellular heterogeneity and its implications for disease and treatment.

# Single-cell data imputation using [[MAGIC]]

"Single-cell data imputation using MAGIC" refers to a computational technique applied to single-cell RNA sequencing (scRNA-seq) data to **address the issue of [[dropouts]]**, *where some gene expression values are missing or underdetected*. This phenomenon is common in scRNA-seq datasets due to the low amount of mRNA present in each cell, leading to sparse and noisy data.

MAGIC (Markov Affinity-based Graph Imputation of Cells) is an algorithm designed to mitigate this problem by recovering the true signal from the observed data. It uses the concept of *data diffusion through a Markov chain process* to smooth out the data, enhancing the signal while preserving the data structure, including the identification of rare cell types and the delineation of continuous trajectories.

### How MAGIC Works:
1. **Construction of a Similarity Graph**: MAGIC starts by constructing a k-nearest neighbors graph to represent the similarities between cells based on their gene expression profiles.
2. **Diffusion Process**: It then simulates a Markov diffusion process over this graph, effectively allowing information (gene expression levels) to flow between similar cells. This step helps to fill in missing or unreliable values by averaging the information from similar cells.
3. **Imputation**: The result of the diffusion process is a "smoothed" dataset where the gene expression profiles of cells are adjusted based on the collective information from their neighbors. This step enhances the signal-to-noise ratio and recovers gene expression patterns that might be obscured by dropouts or technical noise.

### Applications and Benefits:
- **Enhanced Data Quality**: MAGIC improves the quality of scRNA-seq data by imputing missing values, making downstream analyses more robust and reliable.
- **Preservation of Data Structure**: Despite the smoothing, MAGIC is designed to preserve the global and local structure of the data, including the detection of rare cell populations and the inference of developmental trajectories.
- **Versatility**: It can be applied to various types of single-cell data beyond RNA-seq, including ATAC-seq and cytometry data.

By using MAGIC for single-cell data imputation, researchers can obtain a more accurate and comprehensive understanding of cellular processes, differentiation pathways, and heterogeneity within cell populations. This is particularly valuable in studies aiming to dissect complex biological systems at the resolution of individual cells.

# Pearson correlation coefficients
In the context of the scFEA (single cell Flux Estimation Analysis) framework, Pearson correlation coefficients are used to measure the *linear correlation between the predicted metabolic fluxes of different modules within single cells*. Specifically, the Pearson correlation coefficient is a statistical measure that calculates the strength and direction of the linear relationship between two variables on a scale from -1 to 1, where:
- 1 indicates a perfect positive linear correlation,
- -1 indicates a perfect negative linear correlation, and
- 0 indicates no linear correlation.

### Application in scFEA:

1. **Module-wise Variation Constraint**: The Pearson correlation coefficients are utilized as part of the *module-wise variation constraint in the loss function* during the training of the neural network model. This constraint aims to ensure that the predicted metabolic fluxes for each module within a cell are consistent with the underlying biological or experimental data.
2. **Sensitivity Evaluation**: By calculating the correlation between the predicted fluxes of different modules, scFEA can evaluate the sensitivity of enzymes (or reactions) with regards to their impact on the whole metabolic flux. This helps in identifying key enzymes or reactions that significantly influence cellular metabolism.

3. **Downstream Analysis**: Beyond the training process, Pearson correlation coefficients can be used in downstream analyses to explore the relationships between different metabolic modules or pathways across cells. For example, identifying modules that are consistently co-regulated across different cell types or conditions can provide insights into common regulatory mechanisms or metabolic states.

### Importance:

The use of Pearson correlation coefficients in scFEA serves multiple purposes:
- It helps in refining the model's predictions by incorporating biological constraints that ensure the metabolic flux predictions are biologically plausible.
- It aids in the interpretation of the model's output by highlighting significant metabolic relationships and dependencies.
- It contributes to the broader goal of understanding cellular metabolism at a single-cell level, facilitating the discovery of metabolic phenotypes and their implications for health and disease.

In summary, Pearson correlation coefficients in scFEA are crucial for enforcing biological constraints on the model, evaluating the model's predictions, and facilitating the biological interpretation of the metabolic fluxes inferred from single-cell RNA-seq data.

# `scFEA.py` code analysis
The `scFEA.py` estimates cell-wise metabolic flux from scRNA-seq data using a graph neural network model. 

Key components and functionalities of the script:

### Custom Loss Function

A custom loss function `myLoss` is defined to incorporate various constraints like [[balance constraint]], [[non-negative constraint]], [[sample-wise variation constraint]], and [[module-wise variation constraint]] using *Pearson correlation coefficients*.

```python
def myLoss(m, c, lamb1 = 0.2, lamb2= 0.2, lamb3 = 0.2, lamb4 = 0.2, geneScale = None, moduleScale = None):
    # balance constrain, non-negative constrain, etc.
    ...
    corr = torch.FloatTensor(np.ones(m.shape[0]))
    for i in range(m.shape[0]):
        corr[i] = pearsonr(m[i, :], moduleScale[i, :])
    ...
    return loss, loss1, loss2, loss3, loss4
```

### Main Function

The `main` function orchestrates the execution flow, starting from parsing command-line arguments to setting up the device (CPU or GPU), loading and processing data, defining the model, and executing the training loop.

#### Parsing Arguments

Command-line arguments for input/output directories, file names, and training parameters are parsed.

```python
parser = argparse.ArgumentParser(...)
args = parse_arguments(parser)
```

#### Data Loading and Preprocessing

Data is loaded using `pandas`, and optional single-cell data imputation using MAGIC is performed. The data is then processed to match the genes with the metabolic modules.

```python
geneExpr = pd.read_csv(input_path + '/' + test_file, index_col=0)
...
if sc_imputation == True:
    magic_operator = magic.MAGIC()
    geneExpr = magic_operator.fit_transform(geneExpr)
```

#### Model Initialization and Training

The neural network model is instantiated from the `FLUX` class, and the training loop is executed using a custom dataset class `MyDataset` and PyTorch's `DataLoader`.

```python
net = FLUX(X, n_modules, f_in = n_genes, f_out = 1).to(device)
optimizer = torch.optim.Adam(net.parameters(), lr = LEARN_RATE)
...
for epoch in tqdm(range(EPOCH)):
    for i, (X, X_scale, m_scale) in enumerate(train_loader):
        ...
        loss_batch.backward()
        optimizer.step()
```

### Saving Results

After training, the script saves the predicted metabolic fluxes and balances to specified output files.

```python
if fileName == 'NULL':
    fileName = "./" + res_dir + "/output.csv"
setF.to_csv(fileName)
```

### Conclusion

The `scFEA.py` integrates data preprocessing, a custom neural network model with a specialized loss function, and utilities for handling single-cell data.

# Video and slides
[Chi Zhang Lab, scFEA -- RECOMB2021 (youtube.com)](https://www.youtube.com/watch?v=-S8ouz2F-Rk)
```vid
https://www.youtube.com/watch?v=-S8ouz2F-Rk
```

![[Chi Zhang Lab, scFEA -- RECOMB2021 0-27 screenshot.png|500]]

![[Chi Zhang Lab, scFEA -- RECOMB2021 2-21 screenshot.png|500]]

![[Chi Zhang Lab, scFEA -- RECOMB2021 4-14 screenshot.png|500]]

![[Chi Zhang Lab, scFEA -- RECOMB2021 5-59 screenshot.png|500]]

![[Chi Zhang Lab, scFEA -- RECOMB2021 6-36 screenshot.png|500]]

![[Chi Zhang Lab, scFEA -- RECOMB2021 8-6 screenshot.png|500]]

![[Chi Zhang Lab, scFEA -- RECOMB2021 8-22 screenshot.png|500]]

![[Chi Zhang Lab, scFEA -- RECOMB2021 9-7 screenshot.png|500]]

![[Chi Zhang Lab, scFEA -- RECOMB2021 10-37 screenshot.png|500]]

![[Chi Zhang Lab, scFEA -- RECOMB2021 10-49 screenshot.png|500]]

![[Chi Zhang Lab, scFEA -- RECOMB2021 12-24 screenshot.png|500]]

![[Chi Zhang Lab, scFEA -- RECOMB2021 13-10 screenshot.png|500]]

![[Chi Zhang Lab, scFEA -- RECOMB2021 13-43 screenshot.png|500]]

![[Chi Zhang Lab, scFEA -- RECOMB2021 14-17 screenshot.png|500]]

![[Chi Zhang Lab, scFEA -- RECOMB2021 14-50 screenshot.png|500]]

![[Chi Zhang Lab, scFEA -- RECOMB2021 15-6 screenshot.png|500]]

![[Chi Zhang Lab, scFEA -- RECOMB2021 15-13 screenshot.png|500]]

![[Chi Zhang Lab, scFEA -- RECOMB2021 15-21 screenshot.png|500]]

![[Chi Zhang Lab, scFEA -- RECOMB2021 15-30 screenshot.png|500]]

![[Chi Zhang Lab, scFEA -- RECOMB2021 15-51 screenshot.png|500]]

# Usage

```shell
usage: scFEA.py [-h] [--data_dir <data_directory>]
                [--input_dir <input_directory>] [--res_dir <data_directory>]
                [--test_file TEST_FILE] [--moduleGene_file MODULEGENE_FILE]
                [--stoichiometry_matrix STOICHIOMETRY_MATRIX]
                [--cName_file CNAME_FILE] [--sc_imputation {True,False}]
                [--output_flux_file OUTPUT_FLUX_FILE]
                [--output_balance_file OUTPUT_BALANCE_FILE]
                [--train_epoch [TRAIN_EPOCH]]

scFEA: A graph neural network model to estimate cell-wise metabolic flux using
single cell RNA-seq data

optional arguments:
  -h, --help            show this help message and exit
  --data_dir <data_directory>
                        The data directory for scFEA model files.
  --input_dir <input_directory>
                        The data directory for single cell input data.
  --res_dir <data_directory>
                        The data directory for result [output]. The output of
                        scFEA includes two matrices, predicted metabolic flux
                        and metabolites stress at single cell resolution.
  --test_file TEST_FILE
                        The test SC file [input]. The input of scFEA is a
                        single cell profile matrix, where row is gene and
                        column is cell. Example datasets are provided in
                        /data/ folder. The input can be raw counts or
                        normalised counts. The logarithm would be performed if
                        value larger than 30.
  --moduleGene_file MODULEGENE_FILE
                        The table contains genes for each module. We provide
                        human and mouse two models in scFEA. For human model,
                        please use module_gene_m168.csv which is default. All
                        candidate moduleGene files are provided in /data/
                        folder.
  --stoichiometry_matrix STOICHIOMETRY_MATRIX
                        The table describes relationship between compounds and
                        modules. Each row is an intermediate metabolite and
                        each column is metabolic module. For human model,
                        please use cmMat_171.csv which is default. All
                        candidate stoichiometry matrices are provided in
                        /data/ folder.
  --cName_file CNAME_FILE
                        The name of compounds. The table contains two rows.
                        First row is compounds name and second row is
                        corresponding id.
  --sc_imputation {True,False}
                        Whether perform imputation for SC dataset (recommend
                        set to <True> for 10x data).
  --output_flux_file OUTPUT_FLUX_FILE
                        User defined predicted flux file name.
  --output_balance_file OUTPUT_BALANCE_FILE
                        User defined predicted balance file name.
  --train_epoch [TRAIN_EPOCH]
                        User defined EPOCH (training iteration).

```
