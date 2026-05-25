---
layout: about
title: About
permalink: /
#subtitle: <a href='#'>Affiliations</a>. Address. Contacts. Motto. Etc.

profile:
  align: right

selected_papers: false # includes a list of papers marked as "selected={true}"
social: true # includes social icons at the bottom of the page

announcements:
  enabled: true # includes a list of news items
  scrollable: true # adds a vertical scroll bar if there are more than 3 news items
  limit: 5 # leave blank to include all the news in the `_news` folder

latest_posts:
  enabled: true
  scrollable: true # adds a vertical scroll bar if there are more than 3 new posts items
  limit: 3 # leave blank to include all the blog posts
---

I am an applied machine learning researcher and software engineer with a PhD in Applied Sciences in Engineering from the Technical University of Liberec. My work focuses on **deep learning, uncertainty quantification, high-performance computing (HPC), and scientific simulation**.

My doctoral research addressed the computational challenges of large-scale uncertainty quantification in complex physical models. I developed **3D CNN-based surrogate models** to replace expensive numerical homogenisation in discrete fracture–matrix (DFM) simulations, achieving confirmed **100× GPU speedups** and reducing Multilevel Monte Carlo (MLMC) computational cost by over 90% compared to standard Monte Carlo methods. To the best of my knowledge, this represents the first application of MLMC to 3D DFM models. I also explored **graph neural networks (GNNs)** as surrogate models for groundwater processes in earlier stages of this work.

In parallel, I worked on **generative models for 3D medical imaging**, designing VQ-GAN and diffusion-based approaches to synthesise realistic pelvic bone CT scans under low-sample, high-dimensional conditions.

I am the **lead developer of [mlmc](https://pypi.org/project/mlmc/)**, an open-source Python package for Multilevel Monte Carlo simulations, supporting parallel execution on HPC clusters (PBS), efficient sample scheduling, and HDF5-based data management.

I have also worked on **data assimilation for the Richards equation** using the **Unscented Kalman Filter (UKF)**, integrating laboratory and field soil-moisture measurements with meteorological data to improve predictive modeling of unsaturated flow.

Most of my work is carried out on large-scale HPC systems.

## Research Interests

- Scientific Machine Learning (SciML)
- Generative Models for 3D Volumetric Data
- Multilevel Monte Carlo & Uncertainty Quantification
- High-Performance Computing for Machine Learning
- Geometric Deep Learning (Graph Neural Networks)
- Data Assimilation & Kalman Filtering


