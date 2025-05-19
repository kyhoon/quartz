---
url: https://arxiv.org/abs/2104.07368
tags:
  - paper
  - recsys
---
## Abstract

Modeling user preference from his historical sequences is one of the core problems of sequential recommendation. Existing methods in this field are widely distributed from conventional methods to deep learning methods. However, most of them only model users’ interests within their own sequences and ignore the dynamic collaborative signals among different user sequences, making it insufficient to explore users’ preferences. We take inspiration from dynamic graph neural networks to cope with this challenge, modeling the user sequence and dynamic collaborative signals into one framework. We propose a new method named Dynamic Graph Neural Network for Sequential Recommendation (DGSR), which connects different user sequences through a dynamic graph structure, exploring the interactive behavior of users and items with time and order information. Furthermore, we design a Dynamic Graph Recommendation Network to extract user’s preferences from the dynamic graph. Consequently, the next-item prediction task in sequential recommendation is converted into a link prediction between the user node and the item node in a dynamic graph. Extensive experiments on three public benchmarks show that DGSR outperforms several state-of-the-art methods. Further studies demonstrate the rationality and effectiveness of modeling user sequences through a dynamic graph.

## Introduction

Although these methods have achieved compelling results, we argue that these methods lack explicit modeling of the **dynamic collaborative signals** among different user sequences

...(1) These models do not explicitly leverage the collaborative information among **different user sequences**, in other words, most of them focus on encoding each user’s own sequence, while ignoring the high-order connectivity between different user sequences,

(2) These models ignore the **dynamic influence of the high-order collaboration** information at different times.

![[Pasted image 20250518190938.png]]

firstly, we convert all user sequences into a dynamic graph annotated with time and order information on edges (Section 4.1). Consequently, the user sequences having common items are associated with each other via user → item and item → user connections.

Secondly, we devise a sub-graph sampling strategy (Section 4.2) to dynamically extract sub-graphs containing user’s sequence and associated sequences.

Thirdly, to encode user’s preference from the sub-graph, we design a Dynamic Graph Recommendation Network (DGRN) (Section 4.3), in which a dynamic attention module is constructed to capture the long-term preference of users and long-term character of items, and a recurrent neural module or attention module is further utilized to learn short-term preference and character of users and items, respectively.

## Methodology

**Dynamic Graph Construction**

When the user u acts on the item i at time t, an edge e is established between u and i, and e can be represented by the quintuple $(u, i, t, o^i_u, o^u_i)$

$o^i_u$ is the order of u−i interaction, that is, the position of item i in all items that the u has interacted with. $o^u_i$ refers to the order of u in all user nodes that have interacted with item i.

![[Pasted image 20250518191206.png]]

**Sub-Graph Sampling**

Specifically, we first take user node u as the anchor node and select its most recent n first-order neighbors from graph G tk , that is, the historical items that u has interacted with, written as Nu, where n is the maximum length of user sequence (Line 5, 6, and 8 in Algorithm 1).

Next, for each item i ∈ Nu, we use each of them as an anchor node to sample the set of users who have interacted with them, written as Ni (Line 11, 12, and 14 in Algorithm 1).

Followed by analogy, we can obtain the multi-hop neighbors of node u, which could forms u’s m-order sub-graph G m u (tk) of S u (m is hyper-parameter used to control the size of sub-graph).

**Dynamic Graph Recommendation Networks**

Similar to most GNNs, The DGRN component consists of message propagation and node updating components.

The message propagation mechanism aims to learn the message propagation information from user to item and item to user in G m u (tk), respectively. The challenge is how to encode the sequential information of neighbors from user and item perspectives, respectively.

**From item to user.** ... we need to extract two types of information from the neighbors of each user node, which are **long-term** preference and **short-term** preference respectively. The long-term preference [46] of user reflects his or her inherent characteristics and general preference, which can be induced from the user’s all historical items. The shortterm preference of the user reflects his or her latest interest.

**From user to item.** ... On the one hand, the long-term character can reflect the general characters of the item. For example, the wealthy people usually buy high-end cosmetics. On the other hand, short-term character reflects the newest property of item.

**Long-term Information.**

![[Pasted image 20250518191553.png]]

**Dynamic Graph Attention Mechanism.**

![[Pasted image 20250518191701.png]]

**Short-term Information.**

![[Pasted image 20250518191741.png]]

**Node updating**

![[Pasted image 20250518191838.png]]

**Recommendation and Optimization**

![[Pasted image 20250518191917.png]]