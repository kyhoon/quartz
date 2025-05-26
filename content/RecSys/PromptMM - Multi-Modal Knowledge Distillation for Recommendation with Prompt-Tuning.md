---
url: https://arxiv.org/abs/2402.17188
github: https://github.com/HKUDS/PromptMM
tags:
  - recsys
---
## Abstract

Multimedia online platforms (e.g., Amazon, TikTok) have greatly benefited from the incorporation of multimedia (e.g., visual, textual, and acoustic) content into their personal recommender systems. These modalities provide intuitive semantics that facilitate modality-aware user preference modeling. However, two key challenges in multi-modal recommenders remain unresolved: i) The introduction of multi-modal encoders with a large number of additional parameters causes overfitting, given high-dimensional multi-modal features provided by extractors (e.g., ViT, BERT). ii) Side information inevitably introduces inaccuracies and redundancies, which skew the modality-interaction dependency from reflecting true user preference. To tackle these problems, we propose to simplify and empower recommenders through Multi-modal Knowledge Distillation (PromptMM) with the prompt-tuning that enables adaptive quality distillation. Specifically, PromptMM conducts model compression through distilling u-i edge relationship and multi-modal node content from cumbersome teachers to relieve students from the additional feature reduction parameters. To bridge the semantic gap between multi-modal context and collaborative signals for empowering the overfitting teacher, soft prompt-tuning is introduced to perform student task-adaptive. Additionally, to adjust the impact of inaccuracies in multimedia data, a disentangled multi-modal list-wise distillation is developed with modality-aware re-weighting mechanism. Experiments on real-world data demonstrate PromptMM's superiority over existing techniques. Ablation tests confirm the effectiveness of key components. Additional tests show the efficiency and effectiveness.

## Introduction

**I1: Overfitting & Sparsity.** Current multimedia recommenders excel by employing advanced encoders to handle high-dimensional features from pre-trained extractors (CLIP-ViT[36], BERT[5]). The auxiliary modalities alleviate data sparsity, but inevitably lead to increased consumption [52].

**I2: Noise & Semantic Gap**. As side information, multimedia content has inherent inaccuracies and redundancies when modeling user preference with collaborative relations.

To cope with the above issues, we propose the following solutions: **I1**: Developing a multi-modal KD (PromptMM) recommendation framework to free the inference recommender from the additional feature reduction parameters, by using KD for model compression. ... The three types of KD respectively convey i) Pure knowledge through a modified KL divergence[24] based on BPR loss[40]; ii) Fine-grained modality-aware list-wise ranking knowledge; iii) Modality-aware embedding KD through SCE loss [18], an enhanced version of MSE.

**I2:** Developing two modules to tackle issues ’Noise & Semantic Gap’ based on the KD framework: i) Semantic bridging soft prompt-tuning is meant to reduce the impact of redundancy by prompting teacher to deliver student-task adaptive knowledge.  ... ii) Modality-aware disentangled denoising list-wise ranking KD is to adjust the influence of inaccuracies in modality-aware user preference.

* In this work, we propose a novel multi-modal KD framework PromptMM for multimedia recommendation, which can produce a **lightweight** yet effective student inference recommender with minimal online inference time and resource consumption.
* We integrate prompt-tuning with multi-modal KD to bridge the **semantic gap** between modality content and collaborative signals. Additionally, by disentangling the modality-aware ranking logits, the impact of noise in multimedia data is adjusted.
* We conduct experiments to evaluate our model performance on real-world datasets. The results demonstrate our PromptMM outperforms state-of-the-art baselines. The ablation studies and further analysis show the effectiveness of sub-modules.

## Methodology

The goal of multi-modal recommender systems is to learn a function that predicts the likelihood of a user adopting an item, given an interaction graph G with multi-modal context X.

**Teacher-Student in CF.** For optimization, we employ offline distillation [11] which is a two-stage process, for flexibility concerns. In the first stage, only the teacher is trained, and in the second stage, the teacher remains fixed while only the student is trained.

![[Pasted image 20250525194413.png]]

![[Pasted image 20250525194448.png]]

**Soft Prompt-Tuning as Semantic Bridge.** Drawing inspiration from parameter efficient finetuning (PEFT) [25, 26], we employ soft prompt-tuning[25] as the solution.

* [[The Power of Scale for Parameter-Efficient Prompt Tuning]]

![[Pasted image 20250525194827.png]]

𝜂(·) denotes the dimensionality reduction function (e.g., PCA) for multi-modal features.

Having obtained prompt p, we apply it to the feature reduction layer R (·) in teacher T (·) for enhancing the overfitting teacher, while simultaneously conducting student-task adaptive knowledge distillation through the frozen teacher. To be specific, we transform our prompt p into the modality-specific module, i.e., p → p 𝑚, which allows the prompt to capture modality-specific information.

![[Pasted image 20250525194949.png]]

During teacher training, the prompt module P (·) undergoes gradient descent with teacher T (·), affecting the teacher’s inference process. During student training, we employ offline knowledge distillation[11], freezing the teacher’s parameters 𝜃T and updating the prompt module P (·) again according to the student’s recommended loss, which allows the prompt p to provide additional guidance to the feature reduction process and distill task-relevant knowledge from teacher T (·).

To comprehensively obtain the quality collaborative signal and modality-aware user preference from teacher T (·), we have designed three types of KD paradigms to convey knowledge from different perspectives: i) Ranking KD; ii) Denoised Modality-aware Ranking KD; and iii) Modality-aware Embedding KD.

**Pure Ranking KD.** 

![[Pasted image 20250525195140.png]]

**Denoised Modality-aware Ranking Disentangled KD.** Previously encoded multi-modal content f 𝑚 𝑢 ,f 𝑚 𝑖 in teacher T (·) contains noise and can affect the modality-aware user preferences modeling.

For a 𝐾 samples ranking list, the predicted logits can be denoted as ylist = [ 𝑦 + 1 ;𝑦 − 2 , 𝑦− 3 , ..., 𝑦− 𝑘 , ..., 𝑦− 𝐾 ], where 𝑦 + and 𝑦 − are the scores of the observed edge A+ and **unobserved** edge A−, respectively.

![[Pasted image 20250525195419.png]]

**Modality-aware Embedding Distillation.** In addition to the logit-based KD, we propose to enhance our PromptMM framework with embedding-level distillation.

![[Pasted image 20250525195509.png]]

**Model Joint Training of PromptMM.** We train our recommender using a multi-task learning scheme to jointly optimize PromptMM with the following tasks: i) the main user-item interaction prediction task, represented by LBPR; ii) the pair-wise robust ranking KD L𝑃𝑎𝑖𝑟𝐾𝐷 ; iii) the modality-aware list-wise disentangled KD L𝐿𝑖𝑠𝑡𝐾𝐷 ; iv) modality-aware embedding KD L𝐸𝑚𝑏𝐾𝐷 . The overall loss function L is given as follows:

![[Pasted image 20250525195534.png]]

## Evaluation

![[Pasted image 20250525195609.png]]
* Tiktok: https://www.biendata.xyz/competition/icmechallenge2019/
* Electronics: https://www.biendata.xyz/competition/icmechallenge2019/

![[Pasted image 20250525195721.png]]