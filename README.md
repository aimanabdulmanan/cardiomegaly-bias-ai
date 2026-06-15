# cardiomegaly-bias-ai

## Overview

Cardiomegaly is the medical term for an enlarged heart,
which could be the entire heart, one side, or specific chambers. It is an indication of an underlying heart
condition, and can cause high blood pressure, coronary artery disease, or heart attacks. 

Public datasets such as MIMIC-CXR and CheXpert have allowed for the development of models
capable of detecting abnormalities including pneumonia, pleural effusion, and cardiomegaly. However, the
accuracy and performance of these algorithms is dependent on the quality and generalisability of the datasets.

**Research Questions:**
| # | Research Question |
|---|---|
| RQ1 | Is there a statistically significant disparity in False Negative Rate (FNR) and AUROC across racial and gender subgroups in a baseline CNN? |
| RQ2 | To what extent does reweighing as a bias mitigation technique reduce FNR and AUROC disparities? |
| RQ3 | In what ways do biased diagnostic tools lead to tangible delays in life-saving treatment, and how does this reinforce historical medical mistrust? |

## Key Findings

**Baseline Model:**
| Subgroup | n | AUROC | FNR |
|---|---|---|---|
| **Race** | | | |
| Black | 55 | 0.7740 | **0.3529** |
| Hispanic | 26 | 0.5903 | 0.2500 |
| White | 658 | 0.7957 | 0.1923 |
| **Sex** | | | |
| Female | 358 | 0.7630 | **0.2385** |
| Male | 395 | 0.8039 | 0.1899 |

**Bias mitigated model:**
| Subgroup | FNR before | FNR after | Change |
|---|---|---|---|
| Black | 0.3529 | 0.2353 | −0.1176 |
| Hispanic | 0.2500 | 0.1250 | −0.1250 |
| White | 0.1923 | 0.0962 | −0.0961 |
| Female | 0.2385 | 0.1009 | −0.1376 |
| Male | 0.1899 | 0.1139 | −0.0760 |

## Model Architecture

The baseline model uses **DenseNet121** pre-trained on ImageNet, chosen for its established benchmark
performance in chest X-ray pathology detection (Rajpurkar et al., 2017 — CheXNet).

**Key Modifications:**
1. Input layer changed from 3-channel RGB to **1-channel grayscale** to reflect CXR image format
2. Final classification layer replaced with a **single output neuron** for binary cardiomegaly classification
3. Images resized to **224×224 pixels** and normalised with dataset-specific mean (0.472) and std (0.301)
4. Class imbalance addressed via **BCEWithLogitsLoss** with positive class weight of 4.6

**Training Configuration:**
| Parameter | Value |
|---|---|
| Optimiser | Adam |
| Learning rate | 1 × 10⁻³ |
| Batch size | 64 |
| Epochs | 15 |
| Framework | PyTorch + torchvision |
| Hardware | Kaggle GPU (P100) |

**Data Augmentation:**
Small affine transformations, scaling, and random resized cropping to simulate minor clinical imaging variation without distorting medically relevant structures.

**Bias Mitigation:**
Used AIF360's reweighing algorithm. No data was added, removed, or duplicated.

## Data Access

1. The MIMIC-CXR dataset contains de-identified patient data and cannot be included in this repository.
2. Access requires credentialing through PhysioNet.

## Limitations

1. Dataset restricted to MIMIC-CXR folders `p10`–`p11`, limiting generalisability (75,216 of 377,110 records).
2. Hispanic and Asian subgroups are substantially underrepresented; their metrics carry wide confidence intervals.
3. Reweighing reduced FNR disparities but introduced increased false positive rates, reducing precision
4. The model was trained and evaluated on a single US hospital system dataset (Beth Israel Deaconess Medical Center), which limits geographic generalisability

## Acknowledgements

This project was completed as part of **IJC202: Responsible Data Science** at the University of Sheffield (May 2026). We thank PhysioNet and the MIMIC team for maintaining open access to de-identified clinical data under appropriate ethical oversight.

## Key references:

- Rajpurkar et al. (2017) — CheXNet: Radiologist-level pneumonia detection
- Seyyed-Kalantari et al. (2021) — Underdiagnosis bias in chest radiograph AI
- Hardt et al. (2016) — Equality of opportunity in supervised learning
- Gichoya et al. (2022) — AI recognition of patient race in medical imaging












