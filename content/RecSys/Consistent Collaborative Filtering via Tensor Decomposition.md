---
url: https://arxiv.org/abs/2201.11936
github: https://github.com/apple/ml-sad
tags:
  - paper
  - recsys
  - todo
---
## Abstract

Collaborative filtering is the de facto standard for analyzing users' activities and building recommendation systems for items. In this work we develop Sliced Anti-symmetric Decomposition (SAD), a new model for collaborative filtering based on implicit feedback. In contrast to traditional techniques where a latent representation of users (user vectors) and items (item vectors) are estimated, SAD introduces one additional latent vector to each item, using a novel three-way tensor view of user-item interactions. This new vector extends user-item preferences calculated by standard dot products to general inner products, producing interactions between items when evaluating their relative preferences. SAD reduces to state-of-the-art (SOTA) collaborative filtering models when the vector collapses to 1, while in this paper we allow its value to be estimated from data. Allowing the values of the new item vector to be different from 1 has profound implications. It suggests users may have nonlinear mental models when evaluating items, allowing the existence of cycles in pairwise comparisons. We demonstrate the efficiency of SAD in both simulated and real world datasets containing over 1M user-item interactions. By comparing with seven SOTA collaborative filtering models with implicit feedbacks, SAD produces the most consistent personalized preferences, in the meanwhile maintaining top-level of accuracy in personalized recommendations. We release the model and inference algorithms in a Python library [this https URL](https://github.com/apple/ml-sad).

