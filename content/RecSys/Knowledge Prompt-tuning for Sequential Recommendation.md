---
url: https://arxiv.org/abs/2308.08459
github: https://github.com/zhaijianyang/KP4SR
tags:
  - paper
  - recsys
---
## Abstract

Pre-trained language models (PLMs) have demonstrated strong performance in sequential recommendation (SR), which are utilized to extract general knowledge. However, existing methods still lack domain knowledge and struggle to capture users’ fine-grained preferences. Meanwhile, many traditional SR methods improve this issue by integrating side information while suffering from information loss. To summarize, we believe that a good recommendation system should utilize both general and domain knowledge simultaneously. Therefore, we introduce an external knowledge base and propose Knowledge Prompt-tuning for Sequential Recommendation (KP4SR). Specifically, we construct a set of relationship templates and transform a structured knowledge graph (KG) into knowledge prompts to solve the problem of the semantic gap. However, knowledge prompts disrupt the original data structure and introduce a significant amount of noise. We further construct a knowledge tree and propose a knowledge tree mask, which restores the data structure in a mask matrix form, thus mitigating the noise problem. We evaluate KP4SR on three real-world datasets, and experimental results show that our approach outperforms state-of-the-art methods on multiple evaluation metrics. Specifically, compared with PLM-based methods, our method improves NDCG@5 and HR@5 by 40.65% and 36.42% on the books dataset, 11.17% and 11.47% on the music dataset, and 22.17% and 19.14% on the movies dataset, respectively. Our code is publicly available at the link: https://github.com/zhaijianyang/KP4SR.

## Introduction

... However, most of these methods only model the IDs of users and items, considering only the user’s sequential preferences, and cannot capture the user’s fine-grained preferences.

![[Pasted image 20250525153838.png]]

A straightforward and simple approach is to describe domain knowledge using natural language text and then use the powerful reasoning ability of PLMs to improve recommendation performance, as shown in Figure 1. However, there are two challenges with this approach: 1) How to convert structured knowledge graphs into text sequences. 2) Converting into text sequences may destroy the original data structure and how to deal with the noise caused by irrelevant entities and relationships.

* We propose KP4SR, which, to the best of our knowledge, is the first work that transforms knowledge graphs into **knowledge prompts** (KP) to improve SR performance.
* We construct KP, which addresses the problems of semantic difference between structured knowledge data contained in the KG and the sequential text data used by PLMs and allows for easy utilization of high-order information from the KG.
* We propose **prompt denoising** (PD), which mitigates knowledge noise by restoring the KG data structure in the form of a mask matrix.
* We conduct extensive experiments on three datasets, and the results demonstrate the effectiveness of our method. In addition, ablation experiments show that transforming the SR task into an NLP task still follows the general pattern of NLP, which indicates the great research prospects and research value of PLMs in improving the performance of recommendation systems.

![[Pasted image 20250525154055.png]]

## Problem Definition

By sorting the interactions between users and items by timestamps, we can obtain the interaction sequence 𝑆𝑢 of user 𝑢, which can be represented as 𝑆𝑢 = {𝑣 𝑢 1 , 𝑣𝑢 2 , ..., 𝑣𝑢 |𝑢| }, where |𝑢| denotes the length of the sequence, 𝑢 ∈ U and 𝑣 ∈ V. Our goal is to predict the next item 𝑣 𝑢 |𝑢|+1 that the user is likely to interact with.

Our goal is to incorporate domain knowledge from KG into PLMs to mine users’ complex preferences. For instance, given a basic input sample: "Tom has watched Cast Away, Back to the Future, and is going to watch [mask].", we need to input the relevant KG information, such as (Cast Away, film.genre, Adventure).

## Methodology

**Prompts Construction**

*Masked personalized prompts.* ... Specifically, MPP can transform the recommendation task into a pre-training task, namely a cloze task, as shown in Figure 3. For a user 𝑢 and his/her interaction sequence {𝐴, 𝐵,𝐶, 𝐷, 𝐸, 𝐹 }, we can fill in the corresponding fields in the template to obtain: User u has previously watched {A, B, C, D, E}, and is going to watch [mask] next. Here, [mask] is the next item to be predicted, i.e., the target item 𝐹 .

![[Pasted image 20250525154438.png]]

MPP can transform the recommendation task into a **pre-training task**, improving task performance when downstream task data is sparse.

*Knowledge prompts.* Using KG as side information in recommendation systems can significantly improve their performance.

For a triple (ℎ, 𝑟, 𝑡), where ℎ represents the head entity, 𝑟 represents the relation, and 𝑡 represents the tail entity, we manually design a relation template for each relation 𝑟 ∈ R to express the semantics of the corresponding triple. For example, in Figure 3, we design a template for the relation film.genre: The genre of [X] is [Y]. Then for the triple (Cast Away, film.genre, Adventure), we replace [X] and [Y] with the head and tail entities, respectively, to obtain a basic triple prompt: "The genre of Cast Away is Adventure.".

*Fused Prompt.* After obtaining MPP and KP, we directly concatenate them as the input of PLM. Specifically, MPP can be represented as: 𝑋𝑑 = {𝑥1, 𝑥2, ..., [𝑚𝑎𝑠𝑘], ..., 𝑥𝑚}, where 𝑥𝑖 is the 𝑖-th token of the text sequence, 𝑚 represents the length of the text in tokens, and [𝑚𝑎𝑠𝑘] represents the next item to be predicted.

**Prompt Denoising**

Converting KG to knowledge prompts can disrupt the original data structure and introduce a large amount of irrelevant and noisy knowledge.

*Knowledge tree construction.* ... The root node of the knowledge tree is the MPP, which contains multiple items that the user has interacted with. Therefore, the knowledge tree has multiple knowledge subtrees.

... For example, in Figure1, the movie "Cast Away" can be represented by two triplets: (Cast Away, genre, Adventure) and (Cast Away, starred, Tom Hanks). We use 𝐴 to represent "Cast Away", 𝐴1 to represent "Adventure", and 𝐴2 to represent "Tom Hanks." By introducing the relationship templates "The genre of [X] is [Y]." and "[X] starring [Y].", we obtain two triplet prompts for 𝐴: "𝐴𝐴1: The genre of Cast Away is Adventure." and "𝐴𝐴2: Cast Away starring Tom Hanks.". 𝐴𝐴1 and 𝐴𝐴2 are 1-hop triplet prompts for 𝐴.

*Knowledge tree mask.* ... Triple prompts without logical and semantic relationships will generate much noise, and we should limit their mutual influence.

![[Pasted image 20250525155001.png]]

![[Pasted image 20250525155021.png]]

**Training and Recommendation**

We employ the T5 model architecture [28],which is an encoder-decoder-based pre-trained language model using mask prediction as the pre-training task. We construct personalized prompts with masks, transforming the recommendation task into a mask prediction task similar to the pre-training task of PLMs.

## Experiments

We conduct experiments on three public datasets: Amazon books [12], LFM-1b [11], and Movielens-10M [30]. These datasets record the interaction information between users and books, music, and movies.

The KG we used is from KB4Rec [45], which links the above three widely used datasets with the widespread knowledge base Freebase[1] to provide side information for recommendation systems.

![[Pasted image 20250525155132.png]]