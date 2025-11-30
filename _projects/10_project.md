---
layout: page
title: Convolutional Surrogate for 3D Discrete Fracture-Matrix Tensor Upscaling
description:
#img: assets/img/1.jpg
importance: 1
category: work
---

[Paper](https://martinspetlik.github.io/assets/pdf/convolutional_surrogate_3D_dfm_tensor_upscaling_preprint.pdf) &#124; [Code Repository](https://github.com/martinspetlik/MLMC-DFM/)


### Summary

Simulating water flow in fractured crystalline rock involves addressing its stochastic nature. We aim to use the multilevel Monte Carlo method to estimate simulation statistics cost-effectively. This multiscale approach includes upscaling fracture hydraulic conductivity through homogenization.
To expedite computations, we replace 3D numerical homogenization based on the discrete fracture-matrix (DFM) approach with a deep-learning-based surrogate model. This model predicts the equivalent hydraulic conductivity tensor, $\boldsymbol{K}^{eq}$, using input tensorial spatial random fields of hydraulic conductivities and the hydraulic conductivity of fractures.
We train three independent surrogates, all with the same architecture, each corresponding to a distinct fracture-to-matrix hydraulic conductivity ratio ($K_f/K_m$). As the $K_f/K_m$ ratio increases, the distribution of $\boldsymbol{K}^{eq}$ becomes more complex, which slightly reduces the prediction accuracy of surrogates.
The surrogates provide significant speedup, with acceleration factors ranging from $16\times$ to $100\times$, depending on the number of homogenization blocks and whether CPU or GPU inference is used.


### DFM model upscaling

* The **discrete fracture-matrix models** integrate **discrete fracture networks** and the **rock matrix** through a mixed mesh composed of 3D and 2D finite elements. 
* **Numerical homogenization** in a block-average sense is adopted to upscale DFM models.
* For each homogenization block, I calculate the components of an **equivalent hydraulic conductivity tensor** $\mathbf{\boldsymbol{K}}^{eq}$:
    $$\langle \mathbf{\boldsymbol{u}} \rangle = - \mathbf{\boldsymbol{K}}^{eq} \cdot \langle \nabla \mathbf{\boldsymbol{p}} \rangle,$$
    where $\langle \mathbf{\boldsymbol{u}} \rangle$ and $\langle \nabla \mathbf{\boldsymbol{p}} \rangle$ are the **volume-averaged fluid velocities** and **pressure gradients**.
* For an upscaled model, fractures that can be represented in a coarser mesh are employed, and **matrix conductivity** is constructed from a given set of $\mathbf{\boldsymbol{K}}^{eq}$.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/project_cnn_surrogate/hom_sample_mesh_field_voxelized.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Illustration of a homogenization block sample, depicting the $K_{xx}$ component of the input hydraulic conductivity tensor. The values for an unstructured mesh and its corresponding voxelized representation are shown.
</div>


### Surrogate

* The **surrogate** maps input random **DFM parameters** (hydraulic conductivity tensors on matrix and fractures) to output **equivalent hydraulic conductivity tensors** using generated sample pairs from a DFM model run.
* It comprises a **convolutional neural network** (CNN) as a feature extractor and a **feed-forward neural network** (FFNN) for the regression task.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/project_cnn_surrogate/3D_cnn_scheme.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The architecture of the surrogate.
</div>

### Results

* Three different **fracture-to-matrix hydraulic conductivity ratios** ${K_f/K_m \in {10^3, 10^5, 10^7}}$ were investigated.
* For each ratio, a separate surrogate of the presented architecture was trained and evaluated on test datasets.
* The best result ($R^2 = 0.9985$) was achieved for **$K_f/K_m = 10^3$**, where the impact of fractures is the lowest.
* Prediction accuracy slightly deteriorated with increasing $K_f/K_m$ due to the **longer-tailed distribution** of $\boldsymbol{K}^{eq}$ components.
* The observed **speedup** gained by surrogates ranges from **$16\times$** for CPU run to **$100\times$** for GPU used for the inference. Training time is not included.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/project_cnn_surrogate/k_xx_pred_accuracy.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Prediction accuracy of $\boldsymbol{K}^{eq}_{xx}$ component for different $K_f/K_m$ ratios.
</div>


