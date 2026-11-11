# cloud-hysteresis-queueing-datasets

## Overview

This repository contains datasets generated numerically from a continuous-time Markov model of a multi-server queueing system with hysteresis-based control and non-instantaneous server activation and deactivation.

Each observation contains an admissible hysteresis threshold configuration and the corresponding mean number of customers in the system and mean number of active servers.

The datasets are used to train, validate, and test a multilayer perceptron surrogate that predicts these two stationary performance measures from the threshold configurations.

## Data Description

The datasets were generated with $K = 5$ servers (and fixed activation/deactivation rates $\alpha = 0.01$, $\beta = 0.1$). Within each dataset, the threshold configurations vary while the remaining system parameters are held fixed.

- `mlp_dataset_EN_ESa11.csv`: Contains 100,000 observations for $\lambda = 1$ and $\mu = 1$.
- `mlp_dataset_EN_ESa12.csv`: Contains 100,000 observations for $\lambda = 1$ and $\mu = 2$.
- `mlp_dataset_EN_ESa21.csv`: Contains 79,201 observations for $\lambda = 2$ and $\mu = 1$, data generation stopped after 24 hours, before reaching the intended 100,000 observations.

## Threshold Admissibility

Each threshold configuration consists of two vectors of positive integers:

```math
F = (F_1, F_2, \dots, F_{K-1}), \qquad)
R = (R_2, R_3, \dots, R_K),
```

where $F$ contains the activation thresholds and $R$ contains the deactivation thresholds.


An admissible configuration satisfies the following constraints:

```math
F_1 < F_2 < \dots < F_{K-1},
```

```math
R_2 < R_3 < \dots < R_K,
```

and

```math
0 < R_{i+1} < F_i, \qquad i = 1, \dots, K-1.
```

## Data Generation Methodology

For each sample, indexed from $i = 1$ to $N_s$, the data generation process follows three main steps:

1. **Threshold sampling:** An admissible hysteresis threshold vector is generated within the admissible threshold domain:

   ```math
   \mathbf{x}_i = (F_1, F_2, F_3, F_4, R_2, R_3, R_4, R_5).    ```

2. **Stationary analysis:** The stationary distribution of the continuous-time Markov chain associated with this threshold configuration is computed numerically using a truncation test.

3. **Performance evaluation:** The corresponding output vector is computed from the stationary distribution:

   ```math
   \mathbf{y}_i = \left(\mathbb{E}[N], \mathbb{E}[S_a]\right),    ```

   where:
   - $\mathbb{E}[N]$ is the mean number of customers in the system.
   - $\mathbb{E}[S_a]$ is the mean number of active servers.

Each input-output pair is added to the dataset:

```math
\mathcal{D} = \left\{(\mathbf{x}_i, \mathbf{y}_i)\right\}_\){i=1}^{N_s}.
```

## Data Format

The datasets are provided as CSV files with a header row. Each row represents an admissible hysteresis threshold configuration and contains the corresponding mean number of customers and mean number of active servers.

The columns are organized as follows:

| Columns | Description |
| :--- | :--- |
| `F1`, `F2`, `F3`, `F4` | Activation thresholds. |
| `R2`, `R3`, `R4`, `R5` | Deactivation thresholds, using the CSV indexing convention. |
| `EN` | Mean number of customers in the system ($\mathbb{E}[N]$). |
| `ESa` | Mean number of active servers ($\mathbb{E}[S_a]$). |

## Note on indexing
The CSV files use shifted indexing for the deactivation thresholds. The threshold denoted by $R_i$ in the article is named `R_(i+1)` in the CSV files.

