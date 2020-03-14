# PyTorch implementation of SimCLR: A Simple Framework for Contrastive Learning of Visual Representations

### Blog post with full documentation: [Exploring SimCLR: A Simple Framework for Contrastive Learning of Visual Representations](https://sthalles.github.io/simple-self-supervised-learning/)

#### For a Tensorflow 2.0 Implementation: [Tensorflow SimCLR](https://github.com/sthalles/SimCLR-tensorflow)

![Image of SimCLR Arch](https://sthalles.github.io/assets/contrastive-self-supervised/cover.png)


## Installation

```
$ conda create --name simclr python=3.7 --file requirements.txt
$ conda activate simclr
$ python run.py
```

## Config file

Before running SimCLR, make sure you choose the correct running configurations on the ```config.yaml``` file.

```yaml

# A batch size of N, produces 2 * (N-1) negative samples. Original implementation uses a batch size of 8192
batch_size: 512 

# Number of epochs to train
epochs: 40

# Frequency to eval the similarity score using the validation set
eval_every_n_epochs: 1

# Specify a folder containing a pre-trained model to fine-tune
fine_tune_from: 'Mar13_20-12-09_thallessilva'

# Frequency to which tensorboard is updated
log_every_n_steps: 50

# Model related parameters
model:
  # Output dimensionality of the embedding vector z. Original implementation uses 2048
  out_dim: 256 
  
  # The ConvNet base model. Choose one of: "resnet18" or "resnet50". Original implementation uses resnet50
  base_model: "resnet18"

# Dataset related parameters
dataset:
  s: 1
  
  # dataset input shape. For datasets containing images of different size, this defines the final 
  input_shape: (96,96,3) 
  
  # Number of workers for the data loader
  num_workers: 0
  
  # Size of the validation set in percentage
  valid_size: 0.05

# NTXent loss related parameters
loss:
  # Temperature parameter for the contrastive objective
  temperature: 0.5 
  
  # Distance metric for contrastive loss. If False, uses dot product. Original implementation uses cosine similarity.
  use_cosine_similarity: True
```

## Feature Evaluation

Feature evaluation is done using a linear model protocol. 

Features are learned using the ```STL10 train+unsupervised``` set and evaluated in the ```test``` set;

Check the [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/sthalles/SimCLR/blob/9d071bb3dd93e921217c415cf0924aad2d0b13eb/feature_eval/linear_feature_eval.ipynb) notebook for reproducibility.


|      Linear Classifier      | Feature Extractor | Architecture | Feature dimensionality | Projection Head  dimensionality | Epochs | STL10 Top 1 |
|:---------------------------:|:-----------------:|:------------:|:----------------------:|:-------------------------------:|:------:|:-----------:|
|     Logistic Regression     |    PCA Features   |       -      |           256          |                -                |        |    36.0%    |
|             KNN             |    PCA Features   |       -      |           256          |                -                |        |    31.8%    |
| Logistic Regression (LBFGS) |       SimCLR      |   [ResNet-18](https://drive.google.com/file/d/12kKgvo4h41G9qnDdhDnZXFlR5_aqvaVR/view?usp=sharing)  |           512          |               256               |   40   |    70.3%    |
|             KNN             |       SimCLR      |   ResNet-18  |           512          |               256               |   40   |    66.2%    |
| Logistic Regression (LBFGS) |       SimCLR      |   [ResNet-18](https://drive.google.com/open?id=1LjuZ1RmhotrnugprRQc2Exk0EbQHMJhL)  |           512          |               256               |   80   |    72.9%    |
|             KNN             |       SimCLR      |   ResNet-18  |           512          |               256               |   80   |    69.8%    |
| Logistic Regression (LBFGS) |       SimCLR      |   ResNet-50  |          2048          |                -                |   40   |      -      |


