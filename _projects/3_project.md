---
layout: page
title: Generative AI for 3D CT Scans of Pelvic Bones
description: 
#img: assets/img/7.jpg
importance: 2
category: work
---

[Code repository](https://github.com/martinspetlik/OsteoDiffusion/)

### Objective
Osteoporotic fractures are life-altering events that can potentially lead to death. Currently, one in three women over the age of 50 experience such fractures. Developing new treatments requires a thorough understanding of bone adaptation and dysfunction.
Our goal is to elucidate how population variation in bone morphology, a poorly understood area, relates to bone remodeling, a crucial process for optimal bone function. We hypothesize that external mechanical forces influence bone morphology and mineral density variations, which can be detected through clinical CT scans.
We aim to identify patients' physiological loading states from CT scans. This innovative approach could revolutionize osteoporosis treatment by providing crucial information for early and precise individualized treatment and enhancing modern in-silico drug prediction models.
However, the **insufficient number of CT data limits** the validation of the hypothesis and the model. To address this, we augment dataset using **generative machine-learning models**, specifically a vector quantized GANs and denoissing diffusion models.


### Dataset
* **Dataset Size and Demographics:** The study utilizes **371 abdominal CT scans** of patients, ranging in age from **16 to 91 years**. Exclusions include patients with bone trauma, large osteophytes, or fractures.
* **Scan Voxel Size:** The voxel size of the scans varied, falling in the range from $0.5\times0.5\times0.6\ \mathrm{mm}^3$ to $0.85\times0.85\times1\ \mathrm{mm}^3$.
* **Bones of Interest:** The analysis focuses on the **bones from the pelvic complex**, as they are highly susceptible to the effects of aging and the risks associated with osteoporosis.
* **Preprocessing and Representation:**
    * Each of the 371 samples is preprocessed to be represented by the same number of voxels: (320, 192, 320)
    * At each voxel a scalar value of mineral density is prescribed

---

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/project_gen_bones/orig_sample.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    An example of original CT scan sample
</div>


---

### Generative AI Architecture

Standard 3D diffusion models are computationally infeasible for volumes of this size. Inspired by recent work in 3D latent diffusion, including:

- *Kim et al., 2024*:  
  **Adaptive Latent Diffusion Model for 3D Medical Image-to-Image Translation**  
  https://openaccess.thecvf.com/content/WACV2024/papers/Kim_Adaptive_Latent_Diffusion_Model_for_3D_Medical_Image_to_Image_WACV_2024_paper.pdf

- *Scientific Reports, 2023*:  
  **VQ-GAN-based Latent Diffusion for 3D Medical Imaging**  
  https://www.nature.com/articles/s41598-023-34341-2

I designed a **two-stage generative pipeline** as follows.

## 1. VQ-GAN for latent representation
A 3D VQ-GAN is trained to compress CT volumes into a compact latent representation (**40 × 24 × 40**), mitigating the blurriness typically observed in VAEs. The encoder output is discretized using a **learned codebook**, which improves reconstruction fidelity and facilitates latent-space modeling.

## 2. Latent diffusion model
A denoising diffusion probabilistic model (DDPM) operates on the quantized latent space:
- Significantly reduces computational and memory requirements  
- Improves training stability, especially with limited datasets

## 3. Reconstruction to voxel space
The generated latents are decoded by the VQ-GAN decoder to produce full-resolution volumes (**320 × 192 × 320**), yielding anatomically realistic CT reconstructions.

Memory constraints were additionally mitigated through mixed-precision training and gradient accumulation.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/project_gen_bones/sample_25.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Example of a generated CT scan sample
</div>

---

### Current Limitations and Next Steps

The primary limitation is the **restricted dataset size**. Ongoing and future work includes:
 
- Quantitative evaluation using SSIM, LPIPS-3D, and morphological plausibility metrics 
- Expert validation by biomedical imaging specialists  


A detailed methodological description and full results will be published in an upcoming manuscript.

