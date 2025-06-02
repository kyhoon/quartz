---
url: https://dl.acm.org/doi/10.1145/3640457.3688133
tags:
  - recsys
---
## Abstract

Conversational recommender systems (CRSs) are able to elicit user preferences through multi-turn dialogues. They typically incorporate external knowledge and pre-trained language models to capture the dialogue context. Most CRS approaches, trained on benchmark datasets, assume that the standard items and responses in these benchmarks are optimal. However, they overlook that users may express negative emotions with the standard items and may not feel emotionally engaged by the standard responses. This issue leads to a tendency to replicate the logic of recommenders in the dataset instead of aligning with user needs. To remedy this misalignment, we introduce empathy within a CRS. With empathy we refer to a system’s ability to capture and express emotions. We propose an empathetic conversational recommender (ECR) framework.

ECR contains two main modules: emotion-aware item recommendation and emotion-aligned response generation. Specifically, we employ user emotions to refine user preference modeling for accurate recommendations. To generate human-like emotional responses, ECR applies retrieval-augmented prompts to fine-tune a pre-trained language model aligning with emotions and mitigating hallucination. To address the challenge of insufficient supervision labels, we enlarge our empathetic data using emotion labels annotated by large language models and emotional reviews collected from external resources. We propose novel evaluation metrics to capture user satisfaction in real-world CRS scenarios. Our experiments on the ReDial dataset validate the efficacy of our framework in enhancing recommendation accuracy and improving user satisfaction.

## Introduction

A crucial aspect of CRSs is to elicit user preferences through multi-turn dialogues, with two main subtasks: item recommendation and response generation [9]. A prominent challenge is the lack of sufficient contextual information for accurately modeling user preferences.

... leads to a tendency of CRS to replicate the logic of recommenders in the dataset instead of addressing user needs.

![[Pasted image 20250601191836.png]]

Their work suggests that capturing **emotions** expressed in user utterances within dialogues is prominent for achieving accurate user preference modeling for item recommendation

To construct empathetic CRSs, we face two major challenges: (i) how to accurately model user preferences using emotions; and (ii) how to generate emotional responses contributing to user satisfaction. To address these challenges, we propose an empathetic conversational recommender (ECR) framework comprising two key modules: *emotion-aware item recommendation* and *emotion-aligned response generation*.

The contributions of this paper are as follows:
* To bridge the gap between system outputs and user needs, we define **empathy** within a CRS and propose a novel framework ECR.
* We augment user preference modeling by integrating their emotions, with a new training strategy to minimize the impact of incorrect labels.
* We **fine-tune a PLM** to express emotions and apply **retrieval-augmented** prompts to mitigate hallucination.
* We use LLMs to **annotate** **user emotions** and collect emotional reviews from external resources as empathetic CRS training data, which facilitates future research in this area.
* We propose new evaluation metrics tailored to user satisfaction in real-world CRS scenarios, and our experimental results demonstrate that ECR significantly outperforms baselines on the ReDial dataset.

## Method

![[Pasted image 20250601192344.png]]

**User Emotion Extraction.** ... we employ GPT-3.5-turbo [45] to initially annotate user emotions in 5,082 utterances from the ReDial dataset. We limit the number of annotated emotions per utterance to a maximum of two labels. In annotating with GPT-3.5-turbo [45] for utterance-level user emotions, we adopted nine emotion labels: “like,” “curious,” “happy,” “grateful,” “negative,” “neutral,” “nostalgia,” “agreement,” and “surprise.” The “negative” label, denoting adverse emotions, accounted for 8.0%.

... Based on the annotations, we fine-tune a GPT-2 model, which achieves 87.75% in terms of Recall@2 in categorizing emotions.

**Emotional Response Construction.** ... To construct a emotional review database 𝑅, we collect movie reviews from IMDb. ... Each emotional review sentence 𝑟 = {𝑤𝑗 } |𝑟| 𝑗=1 consists of a sequence of words. From each emotional review sentence 𝑟, we extract a list of entities 𝐸𝑟 = {𝑒𝑗 } |𝐸𝑟 | 𝑗=1 , 𝑒𝑗 ∈ E. We then retrieve a set of knowledge triples T𝑟 = {⟨𝑖𝑟,𝑙, 𝑒𝑗⟩, 𝑒𝑗 ∈ 𝐸𝑟 } | T𝑟 | 𝑗=1 from the knowledge graph G, where the item 𝑖𝑟 is the head entity, the entity 𝑒𝑗 ∈ 𝐸𝑟 is the tail entity.

**Local emotion-aware entity representing.** we characterize utterance-level user emotions F𝑢 𝑢 𝑘 = {𝑓𝑗 } | F𝑢 𝑢 𝑘 | 𝑗=1 as reflecting emotions towards entities mentioned by the user in the current utterance ...

![[Pasted image 20250601192740.png]]

**Global emotion-aware entity representing.** We first use utterance-level user emotions to filter global entities and then aggregate their representations. Concretely, we assume that if a user exhibits similar emotions towards both 𝑒𝑗 and 𝑒𝑖 in a conversation, then 𝑒𝑖 is globally emotion-related to 𝑒𝑗 .

![[Pasted image 20250601192908.png]]

**Feedback-aware Item Reweighting.** ... we introduce a mapping function 𝑚(𝑓𝑖𝑘 ) that converts each user feedback 𝑓𝑖𝑘 as a weight scalar. The mapping function converts negative or unclear feedback into a lower weight.

![[Pasted image 20250601193050.png]]

**Emotion-aligned Generator.** ... Specifically, during the training stage, given the extracted knowledge entities 𝐸𝑟 and the retrieved knowledge triples T𝑟, we transform the entities and triples into word sequences, represented as 𝑆T𝑟 and 𝑆𝐸𝑟 . The prompt for generating emotional review 𝑟 consists of the word sequence of the knowledge entities 𝑆𝐸𝑟 , knowledge triples 𝑆T𝑟 , and the item name 𝑆𝑖𝑟 . Then, we incorporate the recommendation response 𝑢 𝑟 𝑡 into the prompt, guiding the model to generate contextually relevant responses.

![[Pasted image 20250601193136.png]]

## Experiments

**Objective evaluation metrics.** ... To validate the model’s effectiveness in estimating user preferences while negating the logged errors in the dataset, we calculate Recall_True@𝑛 (RT@𝑛, where 𝑛 = 1, 10, 50). This metric refines Recall@𝑛 but only considers the items that get the user feedback of “like” as the standard answers.

**Subjective evaluation metrics.** ... Following Wang et al. [33], we employ an LLM-based scorer capable of automatically assigning scores based on specific prompts to alleviate the evaluation reliance on human annotations and randomly sampling 1,000 examples for evaluation. In this context, GPT4-turbo from the OpenAI serves as the scoring tool. Given the inherent instability in LLMs, we invite three human annotators to assess the reliability of our LLM-based scorer’s evaluation results. The annotators are enlisted to rate 200 examples.

![[Pasted image 20250601193207.png]]

![[Pasted image 20250601193357.png]]




