---
url: https://arxiv.org/abs/2412.18170
github: https://github.com/Asa9aoTK/PNN-RecBole
tags:
  - recsys
  - todo
---
## Abstract

Collaborative filtering (CF) stands as a cornerstone in recommender systems, yet effectively leveraging the massive unlabeled data presents a significant challenge. Current research focuses on addressing the challenge of unlabeled data by extracting a subset that closely approximates negative samples. Regrettably, the remaining data are overlooked, failing to fully integrate this valuable information into the construction of user preferences. To address this gap, we introduce a novel positive-neutral-negative (PNN) learning paradigm. PNN introduces a neutral class, encompassing intricate items that are challenging to categorize directly as positive or negative samples. By training a model based on this triple-wise partial ranking, PNN offers a promising solution to learning complex user preferences. Through theoretical analysis, we connect PNN to one-way partial AUC (OPAUC) to validate its efficacy. Implementing the PNN paradigm is, however, technically challenging because: (1) it is difficult to classify unlabeled data into neutral or negative in the absence of supervised signals; (2) there does not exist any loss function that can handle set-level triple-wise ranking relationships. To address these challenges, we propose a semi-supervised learning method coupled with a user-aware attention model for knowledge acquisition and classification refinement. Additionally, a novel loss function with a two-step centroid ranking approach enables handling set-level rankings. Extensive experiments on four real-world datasets demonstrate that, when combined with PNN, a wide range of representative CF models can consistently and significantly boost their performance. Even with a simple matrix factorization, PNN can achieve comparable performance to sophisticated graph neutral networks. Our code is publicly available at https://github.com/Asa9aoTK/PNN-RecBole.

## Introduction

Most existing works [2, 3, 5, 18, 36] make an intuitive assumption that unlabeled data can directly provide negative signals. Yet, recent works reveal a nuanced reality: *unlabeled data and negative samples harbor an inevitable disparity.*

In this paper, we pose a fundamental question: *Is it feasible to fully unearth and leverage the information within massive unlabeled data?* An intuitive approach might be to initially follow the existing methods by filtering out some negative samples and treating the remaining items as positive instances. ... We cannot guarantee that the remaining items exclusively belong to the positive class.

Our answer is a novel generic positive-**neutral**-negative (PNN) learning paradigm. ... With this new class, our goal is to train a model based on partial rankings Σ 𝑢 𝑝𝑜𝑠 >𝑢 Σ 𝑢 𝑛𝑒𝑢 >𝑢 Σ 𝑢 𝑛𝑒𝑔 (i.e., 𝑢 prefers items Σ 𝑢 𝑝𝑜𝑠 over Σ 𝑢 𝑛𝑒𝑢 over Σ 𝑢 𝑛𝑒𝑔).

While the conceptual framework of the PNN paradigm effectively addresses the aforementioned issue, providing a concrete implementation is technically challenging for at least two reasons:
* **Sparse supervised signals**. Reliable signals primarily stem from user interactions, which tend to be sparse compared to the pool of unlabeled data. This scarcity impedes the effective identification of neutral and negative items within massive unlabeled data.
* **Constrained positive-negative loss functions**. Traditional loss functions categorize all items into either positive or negative classes, failing to accommodate the nuanced nature of neutral items. Even if these latent neutral items are unearthed, they are inevitably pigeonholed into positive or negative classifications.

We summarize our main contributions as follows:
* We introduce the novel PNN learning paradigm, a pioneering approach to fully exploit the wealth of information within massive unlabeled data in CF. By incorporating a **third neutral class** and leveraging **set-level ranking** relationships, PNN addresses the complexity of unlabeled data. Furthermore, through mathematical analysis, we establish the relationship between PNN and OPAUC, demonstrating PNN’s ability to optimize various indicators of the recommendation system.
* We propose a concrete implementation of the PNN learning paradigm which can be seamlessly integrated with multiple mainstream CF models. It features a semi-supervised learning method with a user-aware attention model to reliably classify unlabeled data and a two-step centroid ranking approach to accommodate set-level rankings.
* We perform extensive experiments on four public datasets to demonstrate that, when combined with PNN, a wide variety of mainstream CF models can consistently and substantially boost their performance, confirming the value of PNN.

## PNN Meets OPAUC

OPAUC = one-way partial AUC (OPAUC)
We are integrating the ROC but just between $[\gamma, \delta]$

![[Pasted image 20250601195143.png]]

. It becomes evident that neutral samples and the set-level rankings introduced by PNN can **directly optimize the OPAUC** metric, thereby enhancing various evaluation metrics of recommender systems.

## Methodology

... Given user 𝑢 ∈ 𝑈 , we formulate the loss function as follows:

L𝑢 = (1 − 𝜆)LBPR + 𝜆L

... Similarly, how to design 𝜆 is also a challenging task due to a lack of supervised signals. Inspired by previous works [6, 15], we propose a user-aware attention model to indirectly assess the classification performance based on the user’s positive items sets Σ 𝑢 𝑝𝑜𝑠 . Intuitively, if the model can correctly identify these items as positive samples, it suggests that the model has adequately learned user preferences from the BPR loss. Following this intuition, we devise 𝜆 as follows:

![[Pasted image 20250601195722.png]]

![[Pasted image 20250601195731.png]]

When our model possesses sufficient knowledge, the positive items should exhibit a high degree of similarity to the user’s preferences. As a result, 𝛼 𝑎𝑡𝑡𝑟 𝑖 will receive a higher value. Consequently, the weight 𝜆 will also increase, prioritizing the significance of LPNN in the overall loss function.

![[Pasted image 20250601195742.png]]

**Uniform Loss** ... Since the BPR loss is designed to set positive items apart from unlabeled data , it may lead to clusters of unlabeled data with similar scores, which is detrimental to our classification goal.

![[Pasted image 20250601195911.png]]

**Clamp Embeddings** ... Inspired by previous works [7, 9, 33, 35], in the second step, we propose a novel clamp mechanism to adaptively generate the margin. Our insight is to utilize two clamp embeddings to tightly constrain the neutral classes in the embedding space.

![[Pasted image 20250601200011.png]]

![[Pasted image 20250601200034.png]]

## Experiments

![[Pasted image 20250601200100.png]]

![[Pasted image 20250601200118.png]]