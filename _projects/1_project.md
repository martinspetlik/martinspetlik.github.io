---
layout: page
title: MLMC Python Package
description: 
#Python framework for Multilevel Monte Carlo methods
#img: assets/img/12.jpg
importance: 4
category: work
related_publications: false
---

[Code repository](https://github.com/GeoMop/MLMC)

Monte Carlo methods ([Giles, 2015](https://people.maths.ox.ac.uk/gilesm/files/acta15.pdf)) are statistical techniques used to estimate expectations arising from stochastic models. They are widely applied across computational finance, uncertainty quantification, physical process modeling, environmental engineering, and other fields.

This project outlines the the structure of the Python [**mlmc**](https://pypi.org/project/mlmc/) software library as a tool to run **standard Monte Carlo method** and **multilevel Monte Carlo (MLMC) method**. With a detailed documentation available at [https://mlmc.readthedocs.io](https://mlmc.readthedocs.io)

---

## MLMC Python Package

The most recently released version, **v1.0.3 (Oct 21, 2025)**, provides:

- **User-defined simulations** with flexible **Quantities of Interest (QoI)**, including derived QoIs).
- Iterative MLMC sample scheduling using dynamically estimated variance \(V_l\) and cost \(C_l\).
- Support for **parallel execution** locally or on clusters using **PBS**.
- Efficient **HDF5** storage for large datasets and chunk-wise post-processing.
- Methods for estimating expectations, higher moments, covariance matrices, and **PDF reconstruction** via maximum entropy method.

---

# MLMC Workflow

The MLMC library provides a streamlined workflow for implementing multilevel Monte Carlo simulations, enabling users to balance **accuracy** and **computational cost** across multiple refinement levels.

---

## 1. Simulation Selection

The library provides three built-in simulations:

- **Artificial Simulation**  
  Generates synthetic QoIs from distributions such as Normal, Lognormal, Weibull, Chi-square, etc.

- **2D Projectile Motion**  
  A simple physical model used for demonstration and testing.

- **Porous Medium Flow**  
  A Flow123d-based fluid flow simulation (currently in 2D).

Users can also implement **custom simulations** by extending the abstract `Simulation` class.

---

## 2. Levels and Computational Work

The user defines the number of levels $L$:

- $L = 1$ ⇒ classical Monte Carlo  
- $L > 1$ ⇒ MLMC with increasing resolution

The user also defines the computational range $(n_0, n_L)$:

- $n_0$: coarsest level (highest variance)  
- $n_L$: finest level (lowest variance)

Intermediate levels are interpolated:

$$
n_l = n_0^{(1 - \frac{l}{L-1})} n_L^{\frac{l}{L-1}}, 
\quad l = 0,\dots,L-1.
$$

If $L=1$, only $n_L$ is used.

For mesh-based models, users may specify min/max **grid step sizes** instead, and the program derives the work estimate.

---

## 3. MLMC Core Principle

At each level $l>0$, MLMC uses **paired (correlated) simulations**:

1. Coarse simulation: resolution $n_{l-1}$  
2. Fine simulation: resolution $n_l$

The correction term is:

$$
Y_l = \hat{P}_l - \hat{P}_{l-1}.
$$

At level $l=0$, only the fine simulation exists.

This hierarchical estimator greatly reduces variance for a given computational cost compared to single level Monte Carlo method.

---

# Execution Options

The library supports two execution modes:

### **1. User-defined sample counts**
The user manually sets $N_l$.  
Useful for controlled experiments and bootstrapping.

### **2. Execution based on target variance or target time**
The user specifies either:

- **Target variance** $\varepsilon^2$, or  
- **Target total runtime**

The library automatically computes the **optimal number of samples per level**, balancing variance and computational cost.

All simulations can run **in parallel**, including on batch systems.

---

# Batch Job Submission (PBS Support)

For large-scale simulations, the library supports **PBS job scheduling**.

The library generates PBS scripts, embeds simulation batches, and archives outputs for debugging and reproducibility.

Computations can also run without PBS.

---

##  Library Structure

The fundamental relationships between the MLMC library's classes are outlined in the UML diagram below.
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/project_mlmc_library/mlmc.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    UML diagram of MLMC library.
</div>

The entirety of the MLMC calculation configuration is managed within the **`Process`** class, where the specific simulation is chosen. Users can also create their own simulations, provided they implement the methods defined in the abstract **`Simulation`** class. Other settings—such as the number of levels, the type of random field, and the use of the PBS system—are also configured here.

The computation is initiated by calling methods of the **`MLMC`** class. This class is responsible for creating the individual **`Level`** instances. The actual simulations are then launched from these `Level` objects. The simulations independently generate **random fields** within their code; currently, fields based on **Normal** and **Exponential** distributions are supported.

The **`Estimate`** class is used for necessary statistical estimates. It contains methods utilized during the MLMC realization and for subsequent data processing and interpretation. Most notably, it offers methods for estimating the **cumulative distribution function (CDF)** and the **probability density function (PDF)** of the resulting random variable.

The results of individual simulations are represented by instances of the **`Sample`** class. These results are progressively saved during the computation to an **HDF5** file using methods of the **`Hdf`** class.

### Main Components:

#### **`Process`**
Defines the MLMC run configuration and selected simulation.

#### **`Simulation`**
Base class for all user-defined or built-in models.

#### **`MLMC`**
Constructs `Level` instances and orchestrates the full computation.

#### **`Level`**
Executes paired coarse/fine simulations at each level.

#### **`Estimate`**
Provides statistical estimators—expectation, moments, covariance, **CDF** and **PDF** reconstruction.

#### **`Sample`** and **`Hdf`**
Handle sampling and storage (including HDF5-based).

---

# Results Storage and Resumption

Intermediate results are gradually stored in HDF5 files. This enables:

- Independent post-analysis  
- Restoring interrupted computations  
- Adding extra samples to existing MLMC runs  
- Running extremely large simulations on HPC clusters without memory overload

---

# MLMC Sampling Algorithm

The optimal sample vector  
$$
\mathbf{N}^{\mathrm{opt}} = (N_1,\dots,N_L)
$$  
depends on:

- Level variances $\mathbf{V} = (\hat V_1,\dots,\hat V_L)$  
- Level costs $\mathbf{C} = (\hat C_1,\dots,\hat C_L)$

The library uses an **iterative procedure** to approach optimality.

---

## Algorithm: Iterative MLMC Sample Estimation

1. Initialize scheduled samples:  
   $\mathbf{N}^s_0 \leftarrow (100, \dots, 10)$

2. Set initial target samples:  
   $\mathbf{N}^t_0 \leftarrow 2\mathbf{N}^s_0$

3. Set iteration counter $i = 0$

4. **Repeat until convergence**:  
   - Schedule up to $\mathbf{N}^s_i$ samples  
   - Wait until 50% are completed  
   - Estimate $\mathbf{V}_i$, $\mathbf{C}_i$ 
   - Compute new target  
     $$
     \mathbf{N}^t_{i+1} = N^{\mathrm{opt}}(\mathbf{V}_i, \mathbf{C}_i)
     $$
   - Update scheduled samples  
     $$
     \mathbf{N}^s_{i+1} = \mathbf{N}^s_i + \alpha(\mathbf{N}^t_{i+1} - \mathbf{N}^s_i)
     $$
   - Increment $i$

5. Stop when the relative difference is below tolerance $\epsilon$ on all levels.

