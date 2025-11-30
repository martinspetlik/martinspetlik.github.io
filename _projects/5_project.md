---
layout: page
title: Graph Surrogate for MLMC-Based Groundwater Contaminant Transport UQ
description: 
#img: assets/img/1.jpg
importance: 3
category: work
---

[Paper](https://www.mdpi.com/2076-3417/12/15/7382) &#124; [Code repository](https://github.com/martinspetlik/MLMC-graph-metamodel)



### Summary

Accurate prediction of **groundwater contaminant transport** is crucial for environmental protection, risk assessment, and public health. Subsurface conditions are highly uncertain and heterogeneous, making fully resolved simulations extremely expensive. Traditional **Monte Carlo (MC)** approaches require thousands of high-fidelity model evaluations, which becomes infeasible for realistic hydrogeological domains.

This research combines two ideas:

1. **Surrogate modeling**  
   A deep learning model approximates the original numerical solver on unstructured meshes. The surrogate consists of:
   - a **Graph Convolutional Neural Network (GCNN)** (ChebNet),
   - followed by a **global pooling layer**,
   - and a **feed-forward neural network** for mapping latent features to output quantities of interest.

2. **Multilevel Monte Carlo (MLMC)**  
   MLMC reduces cost by distributing model evaluations across multiple fidelity levels.  
   Introducing a surrogate as the **new cheapest level** reduces cost even further, without requiring the surrogate to be perfectly accurate.

This combination yields **up to ~25% reduction** in total computational cost for estimating groundwater transport statistics, depending on the MLMC hierarchy and the number of statistical moments required.

The approach is especially beneficial when the **dominant computational cost lies on the lowest MLMC level**—the level replaced by the surrogate.

---

### A Graph-based Machine Learning Surrogate

Modeling groundwater contaminant behavior under uncertainty traditionally requires solving:

- a **PDE-based groundwater flow equation**, and  
- an **advection–dispersion contaminant transport equation**,  
- across many realizations of random hydraulic conductivity fields.

This project deploys a **graph-based deep learning surrogate** to accelerate uncertainty quantification:

- It handles **unstructured meshes**, common in hydrogeology.
- It processes spatially correlated random fields.
- It approximates PDE solutions in seconds rather than minutes.
- Integrated into MLMC, it reduces computational demands even if the surrogate is not perfectly accurate.

This makes previously intractable UQ problems feasible on standard hardware.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/project_graph_mlmc/mesh_srf_graph.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
A random hydraulic conductivity field defined on an unstructured mesh, and its corresponding graph representation for GCNN processing.
</div>

The surrogate uses a **Chebyshev polynomial-based graph convolution (ChebNet)** to operate on irregular node connectivity patterns in the mesh.


<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/project_graph_mlmc/GCNN_architecture.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
Architecture of the surrogate model: a ChebNet GCNN (with convolution kernel of $Ch$ channels), followed by global summation pooling and two feed-forward layers (64 and 32 neurons).
</div>

### Inputs and Outputs

- **Input:** mesh-domain hydraulic conductivity field represented as node features  
- **Graph structure:** adjacency matrix derived from unstructured mesh connectivity  
- **Output:** scalar or vector quantity of interest (pressure, head, flux, or derived quantity)

The network is trained simulation data generated from a numerical groundwater solver.

---

# The MLMC–Surrogate Hybrid

MLMC reduces computational cost through a hierarchy of simulations:

- Level 0: **surrogate model** (cheapest)  
- Higher levels: **coarser numerical PDE solves**  
- Top level: **finest, most expensive PDE solve**

In classical MLMC, the lowest level dominates total cost. Replacing it with the surrogate yields substantial savings.

---

# Results

The hybrid MLMC–GCNN method was tested on 2D groundwater flow problems with log-normal hydraulic conductivity fields.

### Performance highlights

- **Up to ~25% reduction in total cost** for estimating multiple statistical moments  
- Accuracy comparable to full MLMC using only numerical solvers  
- Significant robustness even with moderate surrogate inaccuracies  
- Surrogate predictions are nearly instantaneous after training  
- Effective even when the surrogate replaces the dominant-cost MLMC level

### Computational insight

Cost reductions depend on:

- cost distribution across levels,  
- surrogate evaluation cost,  
- required estimation accuracy,  
- number of moments measured.

When lower MLMC levels dominate cost and are replaced by the surrogate, savings are maximized.


The project bridges scientific machine learning, environmental engineering, and high-performance uncertainty quantification and demonstrates the usage of **graph neural networks can serve as efficient surrogates** for PDE problems defined on **irregular meshes**.





