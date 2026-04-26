# Data-Driven Microclimate Modeling (SINDy vs LSTM)

## Overview

This project investigates **data-driven modeling of near-surface temperature dynamics** using ERA5 reanalysis data.

The main objective is to understand and compare two fundamentally different approaches:

- **SINDy (Sparse Identification of Nonlinear Dynamics)**  
  → interpretable, physics-inspired equation discovery

- **LSTM (Long Short-Term Memory networks)**  
  → data-driven, nonlinear sequence prediction

The project is structured as a sequence of iterations, progressively improving both **data quality** and **modeling strategy**, ultimately highlighting the trade-off between **interpretability and predictive performance**.

---

## Scientific Motivation

Urban and regional microclimate dynamics are governed by complex interactions between:

- solar radiation (energy input)
- wind (transport and mixing)
- intrinsic thermal behavior

The key question addressed in this work is:

> Can temperature dynamics be modeled in a way that is both **physically interpretable** and **predictively accurate**?

---

## Methodology

### 1. Problem Formulation

We model temperature evolution as a **forced dynamical system**:

dT/dt = f(T, R, W)

Where:
- `T` = temperature
- `R` = solar radiation
- `W` = wind speed

---

### 2. Models

#### SINDy (Sparse Regression)

- Learns a **compact governing equation**
- Uses:
  - polynomial libraries
  - sparse regression (STLSQ)
  - Savitzky–Golay derivative estimation

Example discovered structure:
dT/dt ≈ a₁T + a₂R + a₃W + a₄(T × R)


✔ Interpretable  
✘ Sensitive to preprocessing  

---

#### LSTM (Deep Learning)

- Learns temporal dependencies from sequences
- Inputs: past `(T, R, W)`
- Output: future `T`
- Evaluated using:
  - one-step prediction
  - multi-step forward rollout

---

### 3. Validation Strategy

To ensure fair comparison:

- Training: **June–August**
- Testing: **September (out-of-sample)**

Both models:
- use identical input variables
- are evaluated on the same time period
- use real forcing variables (R, W) during rollout

---
#### Clustered SINDy (CSINDy)

To account for spatial heterogeneity in the ERA5 grid, a clustered SINDy approach was also tested.

Instead of forcing one global equation across all locations, grid points were grouped into microclimate regimes such as:

- coastal regime
- inland regime
- mixed/transition regime

A separate SINDy model was then fitted for each regime.

Key finding:

- Inland dynamics were captured well.
- Coastal dynamics remained weaker.
- Reducing from 3 clusters to 2 clusters did not improve the issue, because the transition dynamics became mixed into the coastal cluster.

This showed that regime-aware modeling is useful, but clustering quality strongly affects equation discovery. The inland regime showed more stable and physically consistent dynamics, while the coastal regime was harder to capture. A 3-cluster setup separated coastal, inland, and transition behavior, but the transition cluster had weak/mixed coefficients. When reduced to 2 clusters, the transition regime was absorbed into the coastal cluster, so the coastal simulation remained weak.
This suggests that the main difficulty is not simply the number of clusters, but the presence of mixed transition dynamics between coastal and inland regimes.


## Results
<img width="790" height="390" alt="image" src="https://github.com/user-attachments/assets/70d9d393-fb28-4409-ae11-cd871ea76916" />

Key observations:

* LSTM achieves significantly better predictive accuracy
* SINDy captures physically meaningful structure
* SINDy produces smoother, lower-variance predictions
* LSTM captures nonlinear temporal dynamics
* CSINDy revealed that the ERA5 domain does not behave as one uniform microclimate system over the selected grids in this project.

## Limitations
SINDy model is sensitive to noise and preprocessing
LSTM lacks physical interpretability
Spatial interactions between grid points are not explicitly modeled
Model out put from forward simulation and extrapolation requires significant imporvement for resilience model

## Future Work
Try CONVLSTM in the Comparative Study
Incorporate spatial coupling (multi-grid dynamics)
Hybrid models (physics + neural networks)
Compare with PDE-based approaches
## Installation

Clone the repository:

```bash
git clone https://github.com/twinnerhenock/Data-Driven-Discovery-of-micro-climate-dynamics.git
cd Data-Driven-Discovery-of-micro-climate-dynamics

pip install -r requirements.txt

```

Author

Henock Yared
BSc Mechanical Engineering
MSc Data Science
