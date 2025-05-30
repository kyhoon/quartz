---
url: https://dl.acm.org/doi/10.1145/3404835.3462948
tags:
  - recsys
  - todo
---
## Abstract

This paper investigates recommendation fairness among new items. While previous efforts have studied fairness in recommender systems and shown success in improving fairness, they mainly focus on scenarios where unfairness arises due to biased prior user-feedback history (like clicks or views). Yet, it is unknown whether new items without any feedback history can be recommended fairly, and if unfairness does exist, how can we provide fair recommendations among these new items in such a cold-start scenario. In detail, we first formalize fairness among new items with the well-known concepts of equal opportunity and Rawlsian Max-Min fairness. We empirically show the prevalence of unfairness in cold start recommender systems. Then we propose a novel learnable post-processing framework as a model blueprint for enhancing fairness, with which we propose two concrete models: a joint-learning generative model, and a score scaling model. Extensive experiments over four public datasets show the effectiveness of the proposed models for enhancing fairness while also preserving recommendation utility.