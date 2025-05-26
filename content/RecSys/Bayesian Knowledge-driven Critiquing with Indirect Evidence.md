---
url: https://arxiv.org/abs/2306.05636
github: https://github.com/atoroghi/BCIE
tags:
  - recsys
---
## Abstract

Conversational recommender systems (CRS) enhance the expressivity and personalization of recommendations through multiple turns of user-system interaction. Critiquing is a well-known paradigm for CRS that allows users to iteratively refine recommendations by providing feedback about attributes of recommended items. While existing critiquing methodologies utilize direct attributes of items to address user requests such as 'I prefer Western movies', the opportunity of incorporating richer contextual and side information about items stored in Knowledge Graphs (KG) into the critiquing paradigm has been overlooked. Employing this substantial knowledge together with a well-established reasoning methodology paves the way for critique-based recommenders to allow for complex knowledge-based feedback (e.g., 'I like movies featuring war side effects on veterans') which may arise in natural user-system conversations. In this work, we aim to increase the flexibility of critique-based recommendation by integrating KGs and propose a novel Bayesian inference framework that enables reasoning with relational knowledge-based feedback. We study and formulate the framework considering a Gaussian likelihood and evaluate it on two well-known recommendation datasets with KGs. Our evaluations demonstrate the effectiveness of our framework in leveraging indirect KG-based feedback (i.e., preferred relational properties of items rather than preferred items themselves), often improving personalized recommendations over a one-shot recommender by more than 15%. This work enables a new paradigm for using rich knowledge content and reasoning over indirect evidence as a mechanism for critiquing interactions with CRS.

## Introduction

![[Pasted image 20250525160041.png]]

In this work, we make the following contributions: (i) We introduce the Gaussian variant of a popular **tensor factorization** approach for KG-enhanced recommendation. (ii) We propose **Bayesian Critiquing with Indirect Evidence** (BCIE), a knowledge-driven critiquing framework, and formulate a Bayesian closed-form user belief updating methodology to enable critiquing CRSs to address indirect feedback. (iii) We empirically show that BCIE results in considerable improvement of personalized recommendation over one-shot recommendation by evaluation on two datasets and a KG.

![[Pasted image 20250525160152.png]]

## Bayesian Critiquing with Indirect Evidence

In the **conversational critiquing problem** setting that we investigate, ...

The recommender’s duty in the next step is to update its belief in the user’s interests and refine 𝑅𝑢 given 𝑑𝑛, the evidence of the user’s taste observed from the critique at iteration 𝑛. Hence, the recommender needs a critique-modified recommendation function 𝑓𝑚, such that 𝑅ˆ 𝑢 = 𝑓𝑚 (𝑅𝑢, 𝑑𝑛). This process continues either for a fixed number of iterations or **until the user accepts** the recommendation or leaves the conversation.

**Pre-critiquing phase**

We build our recommender upon **SimplE**, a well-known tensor factorization-based KG embedding model, because of its efficient computations and full-expressiveness [8]. This model assigns two embedding vectors ℎ𝑒 and 𝑡𝑒 to each entity 𝑒 and two vectors 𝑣𝑟 and 𝑣𝑟 −1 to each relation 𝑟, and defines its scoring function for a triple (𝑒𝑖 , 𝑟, 𝑒𝑗) as Φ(𝑒𝑖 , 𝑟, 𝑒𝑗) = 1 2 (⟨ℎ𝑒,𝑖, 𝑣𝑟, 𝑡𝑒,𝑗⟩+⟨ℎ𝑒,𝑗, 𝑣−1 𝑟 , 𝑡𝑒,𝑖⟩), in which ⟨𝑣,𝑤, 𝑥⟩ = (𝑣 ⊙ 𝑤) · 𝑥 where ⊙ is element-wise and · represents dot product.

* [[SimplE Embedding for Link Prediction in Knowledge Graphs]]

... Using the learned embeddings of entities and relations, the set of items yielding the highest plausibility scores for (𝑢𝑠𝑒𝑟,𝑙𝑖𝑘𝑒𝑠,𝑖𝑡𝑒𝑚) triples are picked for recommendation.

**Critiquing phase: Bayesian User Belief Updating with Indirect Evidence**

In each critiquing session, the user provides knowledge-based feedback containing *indirect* evidence of her preference. ... Hence, in the BCIE framework, we need to consider a distribution over representations of items that cover the user’s interest, which is denoted by 𝒛𝒎. To this end, we require to maintain a belief state over the user preferences, hereafter called *user belief*, which is initially centered at the learned embedding of the user entity and update it conditioned on the user critiques.

![[Pasted image 20250525161011.png]]

The next challenge is obtaining 𝑱𝒖,𝒛 . Note that by adopting the Gaussian variant of SimplE, the likelihood factor between the user belief distribution 𝒛𝑢 and item distribution 𝒛𝑚 becomes exp{−⟨𝒛𝑢,𝒓, 𝒛𝑚⟩} where 𝒓 is the embedding vector of the likes relation — this is log-bilinear in 𝒛𝑢 and 𝒛𝑚 and would appear to stymie closed-form Gaussian belief propagation. Serendipitously, we can rewrite ⟨𝒛𝒖,𝒓, 𝒛𝒎⟩ as 𝒖 𝑇 𝑫𝒓 𝒛, where 𝑫𝒓 is derived by reshaping 𝑟 as a diagonal matrix. Hence, we have 𝑱𝒖,𝒛 = 𝑫𝑟.

![[Pasted image 20250525161207.png]]

To summarize, while the use of a tensor-based likelihood introduced an unusual log-bilinear form and the need to marginalize over the latent item distribution induced by the KG critiques, we have shown that we can manipulate all necessary quantities in Gaussian form. In this way, we can perform a closed-form Gaussian user belief update w.r.t. an item distribution inferred by indirect KG properties.

## Experiments and Evaluation

We evaluate BCIE1 on two of the most popular recommendation datasets, MovieLens 20M 2 and Amazon-Book 3 , and acquire facts about their items from Freebase KG [2]. We consider ratings greater than 3.5 to indicate that the user likes an item and extract facts about items from Freebase using entity matching data from [20]. Also, since we conduct 5 steps of critiquing, we only keep items with at least 5 KG facts to enable selection of non-repetitive critiques. Table 1 shows dataset statistics.

As prior KG-enhanced recommendation studies have not considered the conversational setting and previous critiquing works do not handle knowledge-based indirect evidence, we propose two comparison baselines, namely ’Mapped items’ and ’Direct’. Mapped items is a heuristic method that maps each critique to a maximum of 10 relevant items from the KG and uses them for user belief updating. For example, for the critique "I prefer movies like Nolan’s works", movies that are directed by, Nolan are mined from the KG and used as examples of the user’s interests.

![[Pasted image 20250525161758.png]]


---

![[Pasted image 20250525161435.png]]
(from https://cedar.buffalo.edu/~srihari/CSE674/Chap7/7.1-MultiGauss.pdf)