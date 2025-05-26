---
url: https://arxiv.org/abs/2308.15980
github: https://github.com/HoldenHu/MMSR
tags:
  - recsys
---
## Abstract

In sequential recommendation, multi-modal information (e.g., text or image) can provide a more comprehensive view of an item's profile. The optimal stage (early or late) to fuse modality features into item representations is still debated. We propose a graph-based approach (named MMSR) to fuse modality features in an adaptive order, enabling each modality to prioritize either its inherent sequential nature or its interplay with other modalities. MMSR represents each user's history as a graph, where the modality features of each item in a user's history sequence are denoted by cross-linked nodes. The edges between homogeneous nodes represent intra-modality sequential relationships, and the ones between heterogeneous nodes represent inter-modality interdependence relationships. During graph propagation, MMSR incorporates dual attention, differentiating homogeneous and heterogeneous neighbors. To adaptively assign nodes with distinct fusion orders, MMSR allows each node's representation to be asynchronously updated through an update gate. In scenarios where modalities exhibit stronger sequential relationships, the update gate prioritizes updates among homogeneous nodes. Conversely, when the interdependent relationships between modalities are more pronounced, the update gate prioritizes updates among heterogeneous nodes. Consequently, MMSR establishes a fusion order that spans a spectrum from early to late modality fusion. In experiments across six datasets, MMSR consistently outperforms state-of-the-art models, and our graph propagation methods surpass other graph neural networks. Additionally, MMSR naturally manages missing modalities.

## Introduction

Modality information, such as images or text, has been extensively studied in collaborative recommendation [3, 43, 46, 57], but its potential in sequential recommendation (SR) remains largely unexplored. In collaborative recommendation, modalities are represented as high-dimensional feature vectors, which are captured through pretrained models like BERT [9] for texts and ResNet [13] for images.

However, incorporating multiple modalities into SR poses two key challenges: (1) Identifying **sequential patterns within each modality**, as they may exhibit distinct patterns; (2) Capturing the complex **interplay between modalities** that can influence users’ sequential behavior.

In Sequential Recommendation, existing approaches for merging different channels of features include **early** [19, 25, 39] and **late** fusion [54], which determine whether merging occurs before or after sequential modeling. ... early fusion is less sensitive to the interactions between intra-channel features, while late fusion is less sensitive to the interactions among different channels of features.

![[Pasted image 20250525192047.png]]

... We found late fusion models are more sensitive to the disordered version (resulting in a significant performance drop). In contrast, early fusion is less sensitive to sequential patterns within each channel. Under mismatched conditions, this reversed, with early fusion experiencing a larger performance drop.

These findings reveal that **fusion order is crucial**. While holistic fusion methods like Trans2D [40] suggest features can be fused without a strict order, they do not address the heterogeneity of feature channels or consider fusion order impact. ... Our MMSR framework comprises three stages: ***representation***, where item features in each channel are represented as nodes; ***fusion***, which aggregates features from different channels using graph techniques; and ***prediction***, which generates the final representations.

We represent each user’s behavior history with a graph, where the modality features of items are nodes. We consider three feature channels: item identifier, visual, and textual modalities.

Firstly, in graph construction, treating each modality (such as images) as an individual node will overlook their semantic relatedness.  
-> to construct graphs, we adopt a similar approach [51] to create compositional embeddings that represent nodes as compositions of smaller groups. Specifically, we cluster modality features and select the identifiers of the cluster centers as modality codes, which are then treated as new nodes in the graph.

Secondly, graph nodes and relations are typed.  
-> for graph aggregation, we employ a dual attention function that distinguishes between homogeneous and heterogeneous nodes’ correlations.

Thirdly, naïve graph updating is synchronous for all nodes, unable to support fusion order.  
-> for graph updating, in MMSR, each node adaptively chooses the order of fusion through an update gate.

We summarise our contributions as follows: (i) We spotlight challenges in modality fusion for sequential recommendation, and propose a versatile solution — our MMSR framework. It accommodates both early and late fusion across modalities. (ii) We offer a **graph-centric holistic fusion** method as the engine in MMSR, enabling the adaptive selection of fusion order for each feature node. (iii) We conduct comprehensive experiments on six datasets, which show significant gains in both accuracy and robustness.

## Preliminaries

In our problem, the core task is sequential recommendation: Given a user 𝑢’s historical interaction data H𝑢, the aim is to find a function 𝑓 : H𝑢 → 𝑣 that predicts the next item 𝑣 that the user is most likely to consume.

![[Pasted image 20250525192713.png]]

## Approach

For each user𝑢, we represent his/her history as a graph — a Modality-enriched Sequence Graph (MSGraph), G𝑢 = (N𝑢, R, E𝑢). Note that each user’s graph N𝑢 and E𝑢 can differ.

*Nodes and their initialization.* Each MSGraph should consist of 𝑚 × 3 nodes (where 𝑚 is the sequence length), forming the node set N. N encompasses the three types of nodes, representing three distinct features of channels: {𝑣1, ..., 𝑣𝑚}, {𝑎1, ..., 𝑎𝑚}, and {𝑏1, ..., 𝑏𝑚}. Their representations are associated with the first row (item ID feature), second row (image feature), and third row (text feature) of matrix representation tensor E, respectively.

*Node transformation and compositions.* According to Hou et al. [20], closely binding text encodings with item representations can be detrimental. Thus, instead of using each modality as an individual node, we introduce “modality codes” [20, 36] as alternative nodes.

* [[Learning Vector-Quantized Item Representation for Transferable Sequential Recommenders]]

*Edges and Relation Types.* . In the MSGraphs, we specify the edges as relations E between nodes, including **homogeneous** relations Eℎ𝑜𝑚𝑜 and **heterogeneous** relations Eℎ𝑒𝑡𝑒. ... Additionally, in both types of relations, we introduce **self-loop** relations for each node to preserve its original information.

**Heterogeneity-aware.** To aggregate homogeneous and heterogeneous neighbor nodes, we employ a divide-and-conquer strategy. ... they represent neighbors that differ in type from the central node.

For attention regarding homogeneous nodes, their shared space allows direct comparison. We employ content-based attention for this.

![[Pasted image 20250525193341.png]]

For the attention calculation with respect to heterogeneous nodes, as they are located in different spaces, so we employ type-specific transformation matrices (𝑡𝑗 ≠ 𝑡𝑖 ) to bring them into a common space for comparison.

![[Pasted image 20250525193406.png]]

**Asynchronous updating.** Synchronous updating overlooks the effect of the fusion order. Therefore, we propose an asynchronous updating strategy with two defined updating orders.

![[Pasted image 20250525193438.png]]

**Non-invasive fusion.** Drawing inspiration from NOVA [28], we employed a non-invasive technique to limit interference among different node types during feature updates. For example, although the image features are fused with the item node in Phase 1, they **do not actually update it** but only use the updated representation for calculating the **attention scores** in Phase 2.

## Experiment

In line with previous studies [15, 53], we utilized the Amazon review dataset [14] for evaluation. This dataset provides both product descriptions and images, with varying sizes across product categories. To showcase our approach’s versatility, we selected six datasets from diverse categories: Beauty, Clothing, Sport, Toys, Kitchen, and Phone.

![[Pasted image 20250525193618.png]]

![[Pasted image 20250525193645.png]]

![[Pasted image 20250525193659.png]]





