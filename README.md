# Multitask and Multimodal Prediction of Preclinical Drug Toxicity

A deep learning project for predicting preclinical toxicity using the **Tox21 dataset**, combining molecular graph representations from a Graph Isomorphism Network (GIN) with Avalon molecular fingerprints.

## Overview

Early prediction of compound toxicity is an important step in drug discovery. This project explores whether combining complementary molecular representations can help predict biological activity across multiple toxicity assays.

A hybrid GIN–Avalon architecture is implemented in two settings:
- **Multitask learning:** A single model predicts activity across 12 Tox21 toxicity endpoints.
- **Single-task learning:** Separate models are trained for each endpoint to investigate task-specific predictive performance.

The project compares these approaches to examine how model performance varies across toxicity assays.

## Objectives

- Develop a multimodal deep learning model for preclinical toxicity prediction.
- Integrate molecular graph embeddings with Avalon fingerprints.
- Predict activity across 12 Tox21 toxicity endpoints.
- Compare multitask and endpoint-specific single-task learning.
- Evaluate model performance using classification metrics and ROC-AUC.

## Dataset

The project uses the **Tox21 dataset**, which contains chemical compounds labelled for 12 toxicity-related biological assays. Not all compounds have experimental labels for every endpoint, and the dataset exhibits class imbalance.

The 12 endpoints are:

| Nuclear receptor assays | Stress response assays |
|---|---|
| NR-AR | SR-ARE |
| NR-AR-LBD | SR-ATAD5 |
| NR-AhR | SR-HSE |
| NR-Aromatase | SR-MMP |
| NR-ER | SR-p53 |
| NR-ER-LBD | |
| NR-PPAR-gamma | |

### Data preprocessing

The workflow includes:
- Cleaning and validating molecular SMILES strings.
- Removing invalid molecular representations and applying the notebook's preprocessing rules.
- Handling missing endpoint labels: the multitask model uses masked loss, while single-task models exclude samples without labels for the selected endpoint.
- Generating molecular graph representations and Avalon fingerprints.

**Dataset access:** The dataset file is not included in this repository by default. Place the appropriately sourced dataset at the path expected by the notebooks, or update the data-loading path as needed. Refer to the original dataset source and its applicable terms before redistributing it.

## Methodology

### 1. Molecular representation

Each compound is represented in two complementary ways.

**Molecular graphs and GIN**

Molecules are converted into graph representations, where atoms are nodes and chemical bonds define edges. A Graph Isomorphism Network (GIN) learns graph-level embeddings from these molecular structures.

**Avalon fingerprints**

Each molecule is also represented by a 1,024-bit Avalon fingerprint encoding structural and substructural features.

### 2. GIN–Avalon hybrid architecture

The graph encoder produces a molecular graph embedding. This embedding is concatenated with the Avalon fingerprint and passed to a multilayer perceptron (MLP) for toxicity prediction.

The architecture combines learned graph representations with fingerprint-based molecular features.

### 3. Multitask learning

The multitask model predicts all 12 toxicity endpoints within a shared model. A masked binary cross-entropy loss handles missing labels so that unobserved endpoint labels do not contribute to the loss.

### 4. Single-task learning

Twelve separate models are trained, one for each toxicity endpoint. Samples with missing labels for the selected endpoint are removed before training.

This setup enables comparison between shared multitask learning and endpoint-specific models.

### 5. Hyperparameter optimization

Optuna is used to explore hyperparameter configurations, with validation ROC-AUC as the optimization objective.

### 6. Model evaluation

Performance is assessed using:
- **Accuracy:** Overall proportion of correct predictions.
- **Precision:** Proportion of predicted positives that are actual positives.
- **Recall:** Proportion of actual positives correctly identified.
- **F1 score:** Harmonic mean of precision and recall.
- **ROC-AUC:** Ability to rank positive samples above negative samples across classification thresholds.


## Results

The following values are the performance metrics. They are included to summarize the current experiments.

| Model / endpoint | Accuracy | Precision | Recall | F1 score | ROC-AUC |
|---|---:|---:|---:|---:|---:|
| Multitask | 0.9187 | 0.5188 | 0.4877 | 0.5028 | 0.8559 |
| NR-AR | 0.9696 | 0.9143 | 0.4384 | 0.5926 | 0.7412 |
| NR-AR-LBD | 0.9777 | 0.7273 | 0.6400 | 0.6809 | 0.8207 |
| NR-AhR | 0.8965 | 0.5934 | 0.6391 | 0.6154 | 0.8772 |
| NR-Aromatase | 0.9336 | 0.5319 | 0.3125 | 0.3937 | 0.6919 |
| NR-ER | 0.8800 | 0.5146 | 0.3510 | 0.4173 | 0.7169 |
| NR-ER-LBD | 0.9509 | 0.6200 | 0.3875 | 0.4769 | 0.7675 |
| NR-PPAR-gamma | 0.9782 | 0.6111 | 0.3438 | 0.4400 | 0.8026 |
| SR-ARE | 0.8303 | 0.4131 | 0.5500 | 0.4718 | 0.7920 |
| SR-ATAD5 | 0.9631 | 0.4865 | 0.3529 | 0.4091 | 0.8272 |
| SR-HSE | 0.9356 | 0.4583 | 0.2785 | 0.3465 | 0.7213 |
| SR-MMP | 0.9041 | 0.6848 | 0.6570 | 0.6706 | 0.8773 |
| SR-p53 | 0.9340 | 0.4474 | 0.4198 | 0.4331 | 0.8387 |

### Results interpretation

Performance varies across endpoints. SR-MMP and NR-AR-LBD show comparatively strong F1 scores among the single-task models, while NR-Aromatase and SR-HSE have lower F1 scores. The multitask model has a reported ROC-AUC of 0.8559, although performance comparisons depend on the evaluation protocol and endpoint-specific data distributions.

**Evaluation note:** In the current notebook workflow, classification thresholds were selected using test labels. Therefore, the reported threshold-dependent metrics (accuracy, precision, recall, and F1) may be optimistic and should not be interpreted as unbiased estimates of generalization performance. The results are presented as the current experimental outputs, not as independently validated benchmark results.

## Installation and usage

The notebooks were developed in Python using deep learning, cheminformatics, and machine learning libraries.

Key dependencies include:
- Python
- PyTorch
- PyTorch Geometric
- RDKit
- NumPy
- pandas
- scikit-learn
- Optuna
- The library providing Avalon fingerprint functionality

Exact package versions and installation instructions should match the environment used to develop the notebooks. See `requirements.txt` once it has been prepared.

### Running the notebooks

1. Clone or download this repository.
2. Set up the required Python environment and install the dependencies.
3. Obtain the Tox21 dataset and place it at the expected location.
4. Open the multitask notebook to run the shared 12-endpoint experiment.
5. Open an individual endpoint notebook to inspect its single-task experiment.
6. Run notebook cells in order, ensuring that the dataset and required packages are available.

## Limitations and future work

Potential improvements include:
- Selecting classification thresholds using validation data rather than test labels.
- Evaluating models with scaffold-based splits to assess generalization to structurally distinct compounds.
- Investigating class imbalance and endpoint-specific predictive challenges.
- Comparing the hybrid model with fingerprint-only, graph-only, and other baseline models.
- Reporting repeated-run variability and additional evaluation metrics.

## Acknowledgements

This project was developed as an academic project on **multitask and multimodal prediction of preclinical drug toxicity**, using the Tox21 dataset and a hybrid GIN–Avalon modeling approach.
