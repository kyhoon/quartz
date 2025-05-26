---
url: https://arxiv.org/abs/2305.02575
tags:
  - recsys
---
## Abstract

Conversational recommendation systems (CRS) aim to timely and proactively acquire user dynamic preferred attributes through conversations for item recommendation. In each turn of CRS, there naturally have two decision-making processes with different roles that influence each other: 1) director, which is to select the follow-up option (i.e., ask or recommend) that is more effective for reducing the action space and acquiring user preferences; and 2) actor, which is to accordingly choose primitive actions (i.e., asked attribute or recommended item) that satisfy user preferences and give feedback to estimate the effectiveness of the director's option. However, existing methods heavily rely on a unified decision-making module or heuristic rules, while neglecting to distinguish the roles of different decision procedures, as well as the mutual influences between them. To address this, we propose a novel Director-Actor Hierarchical Conversational Recommender (DAHCR), where the director selects the most effective option, followed by the actor accordingly choosing primitive actions that satisfy user preferences. Specifically, we develop a dynamic hypergraph to model user preferences and introduce an intrinsic motivation to train from weak supervision over the director. Finally, to alleviate the bad effect of model bias on the mutual influence between the director and actor, we model the director's option by sampling from a categorical distribution. Extensive experiments demonstrate that DAHCR outperforms state-of-the-art methods.

## Introduction

![[Pasted image 20250525170032.png]]

Conversational recommendation systems (CRS) aim to dynamically learn user preferences by iteratively interacting with the user.

For each turn in CRS, the system naturally includes two essential decision-make procedures, **when to recommend** (i.e., ask or recommend), and **what to talk about** (i.e., the specific attribute/items). ... Early works [Lei et al., 2020a; Sun and Zhang, 2018] develop policy learning for a subset of decision procedures and outsource the other procedures to heuristic rules (SCPR as illustrated in Figure 1 (a)). These works isolate strategies for different decisions and make policy learning hard to converge due to their lack of mutual influence during training. To solve this problem, Deng et al. [2021] and Zhang et al. [2022] develop unified policy learning frameworks (Unicorn as illustrated in Figure 1 (b))

... Despite effectiveness, the unified strategy brings out issues to be solved: (i) The unified strategy complicates the action selection of the CRS strategy by enlarging the action space and introducing data bias into the action space due to the imbalance in the number of items and attributes. (ii) The unified strategy ignores the different roles of the two decision procedures, leading to the sub-optimal CRS strategy.

There remain three challenges in modeling these two roles and their mutual influence. The first challenge is **weak supervision.** ... The second challenge is **user preference modeling**. In the scenario of CRS, the user likes/dislikes items since they satisfy some attributes, which is a three-order relation (i.e., user-attribute-item). To specify the attributes that motivate the user to like/dislike the item, we should model user preferences with such high-order relations. The third challenge is the bad effect of **model bias** [Battaglia et al., 2018; Tarvainen and Valpola, 2017] on the mutual influence between director and actor.

* We emphasize the different roles in two decision procedures for CRS, and the mutual influence between them.
* We propose a novel Director-Actor Hierarchical conversational recommender with intrinsic motivation to train from weak supervision and a dynamic hypergraph to learn user preferences from high-order relations. To alleviate the bad effect of model bias on the mutual influence between director and actor, DAHCR models the director’s options by sampling from a categorical distribution with Gumbel-softmax.
* We conduct extensive experiments on two benchmark datasets, and DAHCR effectively improves the performance of conversational recommendation.

## The Proposed Model

Then at each turn t, MCR can either **ask the user an attribute** pt ∈ Pcand or **recommend a certain number of items** (e.g., the top ten items) Vt ⊆ Vcand to the user. According to the target item v ∗ and its associated attributes Pv ∗ , the user will choose to accept or reject the proposal of MCR. Based on the user’s feedback, MCR will update the candidate attribute set Pcand and the candidate item set Vcand.

**DAHCR Framework**

![[Pasted image 20250525170509.png]]

![[Pasted image 20250525170553.png]]

![[Pasted image 20250525170603.png]]

![[Pasted image 20250525170716.png]]

**DAHCR Policy Learning**

![[Pasted image 20250525171133.png]]

![[Pasted image 20250525171156.png]]

## Experiments

![[Pasted image 20250525171244.png]]

![[Pasted image 20250525171303.png]]

