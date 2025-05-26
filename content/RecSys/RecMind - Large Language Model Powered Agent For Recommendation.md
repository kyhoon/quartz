---
url: https://arxiv.org/abs/2308.14296
tags:
  - recsys
---
## Abstract

While the recommendation system (RS) has advanced significantly through deep learning, current RS approaches usually train and fine-tune models on task-specific datasets, limiting their generalizability to new recommendation tasks and their ability to leverage external knowledge due to model scale and data size constraints. Thus, we designed an LLM-powered autonomous recommender agent, RecMind, which is capable of leveraging external knowledge, utilizing tools with careful planning to provide zero-shot personalized recommendations. We propose a Self-Inspiring algorithm to improve the planning ability. At each intermediate step, the LLM self-inspires to consider all previously explored states to plan for the next step. This mechanism greatly improves the model's ability to comprehend and utilize historical information in planning for recommendation. We evaluate RecMind's performance in various recommendation scenarios. Our experiment shows that RecMind outperforms existing zero/few-shot LLM-based recommendation baseline methods in various tasks and achieves comparable performance to a fully trained recommendation model P5.

## Introduction

... most existing RS methods have been designed for specific tasks and are inadequate in generalizing to unseen recommendation tasks (Fan et al., 2023).

The first key component is **Planning** which enables the agent to break complex recommendation tasks into manageable steps for efficient handling of complex situations. Each step of planning involves *thought*, *action* and *observation* (see Figure 1 for examples and Section 3 for details). The agent is also equipped with **Memory** consisting of *Personalized Memory* and *World Knowledge*, each accessible through specific tools. The **Tools** enhance the agent’s functionality on top of the LLM, such as retrieving relevant knowledge, or assisting with the reasoning process.

... we propose a new planning algorithm *Self Inspiring* (SI). At each intermediate planning step, the agent “self-inspires” to consider all previously explored paths for the next planning. Unlike existing Chain-of-Thoughts (CoT) (Wei et al., 2022) and Tree-of-Thoughts (ToT) (Yao et al., 2023) which **discards** states (thoughts) in previously explored paths when generating a new state, SI **retains** all previous states from all history paths when generating new state.

* We introduce RecMind, the first LLM-powered agent designed for general recommendation purposes, which operates without the need for finetuning for domain adaptation across datasets or tasks.
* We incorporate a novel self-inspiring (SI) planning technique in RecMind. This technique integrates multiple reasoning paths and offers an empirical improvement over currently popular methods, such as CoT and ToT.
* We evaluate the effectiveness and generalizability of RecMind across five recommendation tasks and two datasets. Extensive experiments and analyses demonstrate that RecMind outperforms state-of-the-art (SOTA) LLM-based baselines that do not involve any fine-tuning and achieves competitive performance with a fully pre-trained expert recommendation model such as **P5** (Geng et al., 2022). In addition, SI outperforms CoT and ToT on general reasoning tasks, showing that the proposed the impact of SI is beyond recommendation tasks.

## Architecture

![[Pasted image 20250525182301.png]]

![[Pasted image 20250525182335.png]]

At m-th path and step t, SI generates the next step of planning by considering all previous paths, i.e., s (m) t+1 ∼ pθ(st+1|z (1), ..., z(m) ). After exploring n paths, the RecMind obtains the final result y ∼ Pθ(x, z(1), ..., z(n) ).

![[Pasted image 20250525190836.png]]

**Memory** Information stored in memory, including Personalized Memory and World Knowledge, enables the model to access knowledge beyond what is inherently present in the LLM’s parameters. Using the Amazon Reviews dataset as an illustrative example, Personalized Memory includes individualized user information, such as **their reviews or ratings** for a particular item. World Knowledge consists of two components: the first component is **item metadata** information, which also falls under the domain-specific knowledge category; the second component involves real-time information that can be accessed through **Web search** tool.

**Tool Use**
* **Database Tool**: This tool translates natural language questions into SQL queries.
* **Search Tool**: This tool employs a search engine (e.g., Google) to access real-time information.
* **Text Summarization Tool**: This tool helps summarize lengthy texts by invoking a text summarization model from the Hugging Face Hub.

## Experiments

We evaluate the performance of the RecMind agent in various recommendation scenarios, i.e., rating prediction, sequential recommendation, direct recommendation, explanation generation, review summarization.

![[Pasted image 20250525191139.png]]

Rating prediction is an essential task in recommendation systems that aims to predict the rating that a user would give to a particular item.

![[Pasted image 20250525191151.png]]

In the scenario of the direct recommendation, RecMind predicts the recommended items from a candidate set of 100 items, where only one candidate is positive.

For sequential recommendation, the agent takes the names of the user’s historically interacted items in order as input. Next, the agent is prompted to predict the title of the next item that the user might interact with.

![[Pasted image 20250525191259.png]]

In explanation generation, we assess the performance of RecMind in crafting textual explanations that justify a user’s interaction with a specific item.

![[Pasted image 20250525191332.png]]

![[Pasted image 20250525191407.png]]