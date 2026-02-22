# Jaguar_Re-Identification
The Jaguar Identification Project aims to automate this process. The goal of this competition is to develop a computer vision model capable of identifying individual jaguars (such as "Medrosa," "Patricia," or "Ousado") from wildlife photographs.

The Challenge

Identifying individuals in the wild is harder than standard image classification. Challenges include:

Intra-class Variation: The same jaguar can look different depending on lighting, angle, and posture.
Inter-class Similarity: Different jaguars can have similar spot patterns.
Data Imbalance: Some jaguars have dozens of high-quality photos, while others have only a few.
Spurious Correlations: Models can "cheat" by learning the background or the riverbank where a jaguar is seen, rather than the animal's unique patterns.

Dataset Description

The dataset consists of thousands of images curated from the Jaguar Field Guide 2025 and the Pantanal Jaguar ID Project.

Train Set: Images labeled by individual ID.
Test Set: A hidden set of images for which participants must predict the correct jaguar ID. Note that these images include the original background information on their RGB channels. The alpha mask to separate the jaguar from its background is present, but you are not obliged to use it.

Suggested Baseline

Participants are encouraged to explore foundation models and self-supervised learning approaches. A starting point includes:

Backbone: MegaDescriptor-Large for dense image embeddings.
Fine-tuning: Implementing ArcFace Loss to cluster images of the same jaguar in the embedding space.
* Handling Imbalance: Using Focal Loss or weighted cross-entropy to address the long-tail distribution of the data.

Dataset Characteristics

The test set contains 371 images representing 31 individual jaguars. The dataset exhibits significant class imbalance, which reflects real-world monitoring scenarios where some jaguars are photographed more frequently than others.

This imbalance presents a substantial challenge. Models that perform well on common identities but fail on rare ones will not achieve strong scores under the evaluation metric used in this competition.

Image preprocessing All images have been processed using the SAM3 (Segment Anything Model 3) semantic segmentation model with the prompt "jaguar" to remove backgrounds. This preprocessing focuses attention on the jaguar itself while introducing variability in segmentation quality across images.

Image quality variability The images come from citizen scientists, which introduces natural variations:

Lighting conditions (daylight, dusk)
Image quality and resolution
Distance from the camera to the subject
Viewing angles (frontal, lateral, rear views)
Partial occlusion by vegetation and the segmentation artifacts produced while removing it
Training set considerations The training set contains near-duplicate images where the same jaguar was captured multiple times in quick succession by the same photographer. These near-duplicates can help models learn robust features but may also lead to overfitting if not handled carefully. Note that near-duplicates between the training and test sets have been removed to ensure fair evaluation.

The Challenge

Identifying individuals in the wild is harder than standard image classification. Challenges include:

Intra-class Variation: The same jaguar can look different depending on lighting, angle, and posture.
Inter-class Similarity: Different jaguars can have similar spot patterns.
Data Imbalance: Some jaguars have dozens of high-quality photos, while others have only a few.
Spurious Correlations: Models can "cheat" by learning the background or the riverbank where a jaguar is seen, rather than the animal's unique patterns.

Evaluation

Identity-balanced Mean Average Precision (mAP) Identity-balanced mean Average Precision (class-balanced mAP) is a retrieval metric in which mean Average Precision is computed per identity/class and then averaged to compensate for class imbalance, instead of allowing frequent identities to dominate the score. It is used in person and object re-identification because the number of images per identity is uneven, and raw mAP can be biased toward identities with many examples.



Evaluation Metric

This competition uses Identity-Balanced Mean Average Precision (mAP), a macro-averaged metric that ensures each jaguar contributes equally to the final score regardless of how many images they have in the test set.

Why identity-balanced evaluation matters

Standard mAP would be dominated by frequently photographed individuals. An identity with 36 images generates 36 query results, while an identity with only 3 images generates just 3 query results. Without balancing, the model could achieve a high score by focusing on common identities while ignoring rare ones.

Identity-balanced mAP addresses this by:

Computing the average precision for each query image
Averaging the precision scores for all queries of the same identity
Computing the final mAP as the mean across all identities (macro-average)
This approach aligns with conservation priorities: every individual jaguar has equal importance, regardless of how often it appears in camera trap footage.

Mathematical formulation

For each query image $q$, the Average Precision $AP(q)$ is:



Where $P_q$ is the set of relevant (matching) images for query $q$
The term $P(k)$ is the precision at rank $k$
Finally, $rel(k)$ is an indicator that equals 1 if the image at rank $k$ matches the query identity, else 0
The identity-balanced mAP is then:



where:

$I$ is the set of all identities
$Q_i$ is the set of queries for identity $i$
Recommended Approaches

Successful solutions to re-identification problems typically involve:

Feature extraction Use pre-trained vision models (e.g. MegaDescriptor, DINOv3, ResNet, EfficientNet, Vision Transformers) or models designed for fine-grained recognition.

Metric learning Fine-tune embeddings using losses like ArcFace, CosFace, or Triplet Loss to optimize similarity-based matching.

Data augmentation Apply geometric and photometric augmentations to handle pose and lighting variations.

Handling class imbalance Use sampling strategies or loss functions that prevent the model from ignoring rare identities.