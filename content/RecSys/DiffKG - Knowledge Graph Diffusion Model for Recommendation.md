---
url: https://arxiv.org/abs/2312.16890
github: https://github.com/HKUDS/DiffKG
tags:
  - recsys
---
## Abstract

Knowledge Graphs (KGs) have emerged as invaluable resources for enriching recommendation systems by providing a wealth of factual information and capturing semantic relationships among items. Leveraging KGs can significantly enhance recommendation performance. However, not all relations within a KG are equally relevant or beneficial for the target recommendation task. In fact, certain item-entity connections may introduce noise or lack informative value, thus potentially misleading our understanding of user preferences. To bridge this research gap, we propose a novel knowledge graph diffusion model for recommendation, referred to as DiffKG. Our framework integrates a generative diffusion model with a data augmentation paradigm, enabling robust knowledge graph representation learning. This integration facilitates a better alignment between knowledge-aware item semantics and collaborative relation modeling. Moreover, we introduce a collaborative knowledge graph convolution mechanism that incorporates collaborative signals reflecting user-item interaction patterns, guiding the knowledge graph diffusion process. We conduct extensive experiments on three publicly available datasets, consistently demonstrating the superiority of our DiffKG compared to various competitive baselines. We provide the source code repository of our proposed DiffKG model at the following link: : https://github.com/HKUDS/DiffKG.

## Introduction

The recommendation performance in practical scenarios is significantly hindered by the inherent **sparsity** of user-item interactions [36, 40]. To mitigate this issue, the integration of a knowledge graph (KG) as a comprehensive information network for items has emerged as a new trend in collaborative filtering, known as knowledge-aware recommendation. Researchers have explored knowledge-aware recommendation through two primary approaches: embedding-based methods and path-based methods. ... To combine the strengths of embedding-based and path-based methods, recent research has turned to GNNs as a powerful tool.

Despite the demonstrated effectiveness of existing knowledge graph (KG)-aware recommendation methods, their performance heavily relies on high-quality input knowledge graphs and can be adversely affected by the **presence of noise**. ... To address these challenges, recent research has proposed the utilization of contrastive learning (CL) techniques to enhance knowledge-aware recommendation.

... , we propose a unique knowledge graph diffusion paradigm that effectively balances corruption and reconstruction.

* We present a novel recommendation model called DiffKG, which leverages task-relevant item knowledge to enhance the collaborative filtering paradigm. Our approach introduces a new framework that allows for the distillation of high-quality signals from the aggregated representation of **noisy** knowledge graphs. 
* We propose an integration of the generative diffusion model with the knowledge graph learning framework, designed for knowledge-aware recommendation. This integration allows us to effectively align the semantics of knowledge-aware items with collaborative relation modeling for recommendation purposes.
* Our extensive experimental evaluations substantiate the substantial performance gains achieved by our DiffKG framework when compared to various baseline models across diverse benchmark datasets. Notably, our approach effectively tackles the challenges stemming from data noise and data scarcity, which are known to exert a negative impact on the accuracy of recommendation.

## The Proposed DiffKG Framework

![[Pasted image 20250525164031.png]]

**Contrastive learning** has recently gained remarkable success in the realm of recommendation systems. ... Unfortunately, the random augmentation can introduce unwanted noise, and the supplementary knowledge graph view may contain irrelevant information. 

To tackle these challenges, we propose the use of a generative model to reconstruct a subgraph G ′ 𝑘 of the knowledge graph G𝑘 that specifically contains the relationships relevant to the downstream recommendation task.

**Diffusion with Knowledge Graph**

![[Pasted image 20250525164322.png]]

*Noise Diffusion Process.* In Fig. 2, we can observe that our knowledge graph (KG) diffusion, similar to other diffusion models, consists of two essential processes: the forward process and the reverse process. In order to apply these processes to the KG, we represent the KG using an adjacency matrix.

... We initialize the initial state 𝝌0 as the original **adjacency matrix** z𝑖 of the item.

*Knowledge Graph Generation with Diffusion Model.* In contrast to other diffusion models that randomly draw Gaussian noises for reverse generation, we have designed a simple inference strategy that aligns with the training of DiffKG for relation prediction in knowledge graphs (KGs).

In our inference strategy, we begin by corrupting the original KG relations 𝝌0 in a step-by-step manner during the forward process, resulting in 𝝌T ′ . We then set 𝝌ˆT = 𝝌T ′ and perform reverse denoising, where we **ignore the variance** and use 𝝌ˆ𝑡−1 = 𝜇𝜃 (𝝌ˆ𝑡 , 𝑡) for deterministic inference. ... For each item 𝑖, we **select the top 𝑘** zˆ 𝑗 𝑖 (𝑗 ∈ [0, |E | − 1], 𝑗 ∈ J, and |J | = 𝑘) and add 𝑘 relations between item 𝑖 and entities 𝑗 ∈ J.

*Collaborative Knowledge Graph Convolution.* To mitigate the potential limitations of the diffusion model in generating a denoised knowledge graph that encompasses pertinent relationships for downstream recommendation tasks, we propose a collaborative knowledge graph convolution (CKGC) mechanism.

The loss of collaborative knowledge graph convolution, denoted as L𝑐𝑘𝑔𝑐 , is computed by incorporating user-item interaction information and knowledge graph predictions into the item embedding generation process. Specifically, we begin by aggregating the **user-item interaction information** A with the predicted relation probabilities from the knowledge graph, represented as 𝝌ˆ0. This aggregation updates the user-item interaction matrix, effectively integrating the knowledge graph information. Next, we combine this updated user-item matrix with the user embeddings E𝑢 to obtain an item embedding E ′ 𝑖 that jointly incorporates both the knowledge graph and user information. Finally, we calculate the mean squared error (MSE) loss between the aggregated item embedding E ′ 𝑖 and the original item embedding E𝑖 , and optimize it alongside the ELBO loss (L𝑒𝑙𝑏𝑜 ).

![[Pasted image 20250525164741.png]]

## Experiments

![[Pasted image 20250525164822.png]]

![[Pasted image 20250525164844.png]]

![[Pasted image 20250525164958.png]]