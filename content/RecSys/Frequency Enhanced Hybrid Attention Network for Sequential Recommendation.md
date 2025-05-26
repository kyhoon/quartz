---
url: https://arxiv.org/abs/2304.09184
github: https://github.com/sudaada/FEARec
tags:
  - recsys
---
## Abstract

The self-attention mechanism, which equips with a strong capability of modeling long-range dependencies, is one of the extensively used techniques in the sequential recommendation field. However, many recent studies represent that current self-attention based models are low-pass filters and are inadequate to capture high-frequency information. Furthermore, since the items in the user behaviors are intertwined with each other, these models are incomplete to distinguish the inherent periodicity obscured in the time domain. In this work, we shift the perspective to the frequency domain, and propose a novel Frequency Enhanced Hybrid Attention Network for Sequential Recommendation, namely FEARec. In this model, we firstly improve the original time domain self-attention in the frequency domain with a ramp structure to make both low-frequency and high-frequency information could be explicitly learned in our approach. Moreover, we additionally design a similar attention mechanism via auto-correlation in the frequency domain to capture the periodic characteristics and fuse the time and frequency level attention in a union model. Finally, both contrastive learning and frequency regularization are utilized to ensure that multiple views are aligned in both the time domain and frequency domain. Extensive experiments conducted on four widely used benchmark datasets demonstrate that the proposed model performs significantly better than the state-of-the-art approaches.

## Introduction

Despite their effectiveness, self-attention used in current Transformer based models is constantly a **low-pass filter**, which continuously erases high-frequency information according to the theoretical justification in [13, 14].

To alleviate these issues, existing methods import local constraints in different ways to complement Transformer-based models. Such as LSAN [17] adopts a novel twin-attention paradigm to capture the global and local preference signals via a self-attention branch and a convolution branch module, respectively.

Moreover, users’ behaviors on the Internet tend to show certain **periodic trends** [20–22]. ... However, it is difficult to find the periodic behavior patterns hidden in the sequence by directly calculating the overall attention scores of items in the time domain. But in the frequency domain, there emerges some methods [23] constructing models to recognize the periodic characterize with the help of the **Fourier transform**, which inspires us to tackle this challenge from a new perspective for recommendation.

* We shift the perspective to the frequency domain and design a frequency ramp structure to improve existing time domain self-attention.
* We propose a novel frequency domain attention based on an autocorrelation mechanism, which discovers similar period-based dependencies by aggregating most relative time delay sequences.
* We unify the frequency ramp structure with vanilla self-attention and frequency domain attention in one framework and design a frequency domain loss to regularize the model training.
* We conduct extensive experiments on four public datasets, and the experimental results imply the superiority of the FEARec compared to state-of-the-art baselines.

![[Pasted image 20250525151326.png]]

## Proposed Method

**Frequency Enhanced Hybrid Attention Encoder**

Based on the embedding layer, we develop the item encoder by stacking 𝐿 Frequency Enhanced hybrid Attention (FEA) blocks, which generally consists of three modules, 𝑖.𝑒., frequency ramp structure, hybrid attention layer, and the point-wise Feed Forward Network (FFN).

*Frequency Ramp Structure.* In FEARec, instead of preserving all frequency components, we only extract a subset of frequencies for each layer to guarantee that different attention blocks focus on different spectrums. This strategy is used in both time domain attention and frequency domain attention as shown in Figure 2.

![[Pasted image 20250525151835.png]]

![[Pasted image 20250525151857.png]]

*Frequency Domain Attention Layer.* ... As discussed in Section 1, by calculating the auto-correlation, we can find the most related time-delay sequences in the frequency domain and thus discover the periodicity hidden in the behaviors.

![[Pasted image 20250525152119.png]]

![[Pasted image 20250525152236.png]]

**Multi-Task Learning**

*Contrastive Learning.* Contrastive learning aims to minimize the difference between differently augmented views of the same user and maximize the difference between the augmented sequences derived from different users.

Although previous augmentations methods [8] including item cropping, masking, and reordering help to enhance the performance of SR models, the data-level augmentations cannot guarantee a high level of semantic similarity [7]. Instead of using typical data augmentations, we use a dropout-based augmentations methods as shown in the right part of Figure 2, which is proposed in [7, 49]. We let E𝑢 and E ′ 𝑢 pass through the FEA encoder twice for two output views H 𝐿 𝑢 and (H 𝐿 𝑢 ) ′ respectively and model the frequency components to construct harder positive samples by mixing the frequency feature extract from time domain self-attention and frequency autocorrelation attention.

*Frequency Domain Regularization.* ... Since time-domain and frequency-domain features represent the same semantics, but only in different domains, we assume that the frequency spectrum of similar time-domain features should also be similar. To ensure the alignment of the representation of different augmented views in the frequency domain, we suggest an L1 regularization in the frequency domain as a complement to FEARec, which contributes to enriching the regularization of the spectrum of the augmented views.

### Experiment

![[Pasted image 20250525152704.png]]

![[Pasted image 20250525152912.png]]