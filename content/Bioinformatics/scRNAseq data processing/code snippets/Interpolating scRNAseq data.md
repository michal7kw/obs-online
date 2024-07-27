---
created: 2024-07-24T06:10
updated: 2024-07-27T17:49
modified: 2024-07-27T17:47:49+02:00
tags:
  - ready
---

## Code

```python
import cupy as cp
import numpy as np
import anndata as ad
from scipy import sparse
from cupyx.scipy.sparse import csr_matrix as cp_csr_matrix

def generate_intermediate_cells(adata, n_points=10000, GPU=True):
    cell_types = adata.obs['CellTypes'].unique()
    intermediate_expressions = []
    intermediate_cell_types = []
    
    for i in range(len(cell_types)):
        for j in range(i+1, len(cell_types)):
            cells_i = adata[adata.obs['CellTypes'] == cell_types[i]]
            cells_j = adata[adata.obs['CellTypes'] == cell_types[j]]
            
            points_per_pair = n_points // (len(cell_types) * (len(cell_types) - 1) // 2)
            
            # Move data to GPU or keep on CPU, using raw data from layers
            if GPU:
                if sparse.issparse(cells_i.layers['raw']):
                    X_i = cp_csr_matrix(cells_i.layers['raw'])
                    X_j = cp_csr_matrix(cells_j.layers['raw'])
                else:
                    X_i = cp.array(cells_i.layers['raw'].toarray() if sparse.issparse(cells_i.layers['raw']) else cells_i.layers['raw'])
                    X_j = cp.array(cells_j.layers['raw'].toarray() if sparse.issparse(cells_j.layers['raw']) else cells_j.layers['raw'])
                xp = cp
            else:
                X_i = cells_i.layers['raw']
                X_j = cells_j.layers['raw']
                xp = np
            
            # Generate all random selections and interpolation factors at once
            i_indices = xp.random.choice(X_i.shape[0], points_per_pair)
            j_indices = xp.random.choice(X_j.shape[0], points_per_pair)
            t_values = xp.random.random(points_per_pair)
            
            # Interpolate all points at once
            exp_i = X_i[i_indices].toarray() if sparse.issparse(X_i) else X_i[i_indices]
            exp_j = X_j[j_indices].toarray() if sparse.issparse(X_j) else X_j[j_indices]
            
            # Linear interpolation for raw counts
            intermediate_exp = (1-t_values[:, None]) * exp_i + t_values[:, None] * exp_j
            
            # Add Poisson noise
            intermediate_exp = xp.random.poisson(intermediate_exp)
            
            # Round to integers and ensure non-negative values
            intermediate_exp = xp.round(intermediate_exp).clip(0)
            
            # Move results back to CPU if on GPU
            if GPU:
                intermediate_expressions.append(cp.asnumpy(intermediate_exp))
            else:
                intermediate_expressions.append(intermediate_exp)
            intermediate_cell_types.extend([f"{cell_types[i]}_to_{cell_types[j]}" for _ in range(points_per_pair)])
    
    return np.vstack(intermediate_expressions).astype(int), intermediate_cell_types
```

## Structure
1. Function Overview:
The `generate_intermediate_cells` function creates synthetic cells that represent intermediates between different cell types in a single-cell dataset. It uses **linear interpolation and adds Poisson noise** to generate these intermediate cells.
2. Data Preparation:
	- The function first extracts unique cell types from the dataset.
	- It then calculates how many points to generate for each pair of cell types.
3. Interpolation Process:
	- For each pair of cell types (`i` and `j`):
		- Randomly choose cells from each type to interpolate between.
		- Generate random **interpolation factors** (t_values) between 0 and 1.
		- Perform linear interpolation:
	- Formula: $intermediate_{exp} = (1-t) * exp_i + t * exp_j$
   Where:
   - $exp_i$ is the expression of a randomly chosen cell from type i
   - $exp_j$ is the expression of a randomly chosen cell from type j
   - $t$ is a random value between 0 and 1
4. Adding Noise:
	- [[Poisson noise]] is added to the interpolated values to simulate biological variability.
	- The **values are then rounded to integers and clipped to non-negative values** to represent realistic gene counts.
5. Return:
	The function returns:
		- A numpy array of interpolated gene expressions
		- A list of labels for the interpolated cells

Key Points on Data Interpolation:
1. Linear Interpolation: The core of the interpolation is linear, creating a "straight line" between the expression values of two cells.
2. Random Selection: Cells and interpolation factors are randomly chosen, ensuring diversity in the generated data.
3. Biological Realism: Poisson noise is added to simulate the stochastic nature of gene expression.
4. Scaling: The interpolation can create any number of points between existing cell types, allowing for fine-grained exploration of the gene expression space.
5. Efficiency: The code is optimized for batch operations, interpolating many points at once rather than one at a time.

This approach allows for the creation of a continuous spectrum of cells between discrete cell types, which can be useful for understanding developmental trajectories or transition states between cell types.

## Interpolation method
1. Imports:
   The code imports necessary libraries:
   - `cupy` and `numpy` for array operations
   - `anndata` for handling annotated data matrices
   - `scipy.sparse` for [[sparse matrix operations]]
   - `cupyx.scipy.sparse` for [[GPU-accelerated sparse matrix operations]]
2. Main Loop:
   The function iterates over pairs of cell types using nested loops:
   ```python
   for i in range(len(cell_types)):
       for j in range(i+1, len(cell_types)):
   ```
3. Random Sampling:
   Generates random indices and interpolation factors:
   ```python
   i_indices = xp.random.choice(X_i.shape[0], points_per_pair)
   j_indices = xp.random.choice(X_j.shape[0], points_per_pair)
   t_values = xp.random.random(points_per_pair)
   ```
4. Return Statement:
   ```python
   return np.vstack(intermediate_expressions).astype(int), intermediate_cell_types
   ```
   Returns a numpy array of generated expressions and a list of corresponding cell type labels.

Key Features:
1. [[GPU Acceleration]]: The function can use GPU for faster computation when available.
2. [[Sparse Matrix Support]]: Handles both dense and sparse input matrices.
3. Vectorized Operations: Uses [[batch operations]] for efficiency.
4. Flexible Input: Works with AnnData objects, which are common in single-cell analysis.
5. Randomization: Incorporates random sampling and noise addition.