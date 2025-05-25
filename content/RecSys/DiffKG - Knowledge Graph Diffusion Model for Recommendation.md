---
url: https://arxiv.org/abs/2312.16890
github: https://github.com/HKUDS/DiffKG
tags:
  - paper
  - recsys
  - todo
---
## Abstract

Knowledge Graphs (KGs) have emerged as invaluable resources for enriching recommendation systems by providing a wealth of factual information and capturing semantic relationships among items. Leveraging KGs can significantly enhance recommendation performance. However, not all relations within a KG are equally relevant or beneficial for the target recommendation task. In fact, certain item-entity connections may introduce noise or lack informative value, thus potentially misleading our understanding of user preferences. To bridge this research gap, we propose a novel knowledge graph diffusion model for recommendation, referred to as DiffKG. Our framework integrates a generative diffusion model with a data augmentation paradigm, enabling robust knowledge graph representation learning. This integration facilitates a better alignment between knowledge-aware item semantics and collaborative relation modeling. Moreover, we introduce a collaborative knowledge graph convolution mechanism that incorporates collaborative signals reflecting user-item interaction patterns, guiding the knowledge graph diffusion process. We conduct extensive experiments on three publicly available datasets, consistently demonstrating the superiority of our DiffKG compared to various competitive baselines. We provide the source code repository of our proposed DiffKG model at the following link: [this https URL](https://github.com/HKUDS/DiffKG).