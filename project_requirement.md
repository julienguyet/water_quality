# Project description

In this project, you will be exploring the un-supervised learning approach for detecting anomalies on a time series
dataset. The goal is not only to detect the different anomalies in the dataset but most importantly to analyse the model
errors and why some anomalies are not detected by a given model and detected by another one.

## Dataset

You will be working with a labeled uni-variate or multi-variate time series dataset where the labels represent the
anomalies

- The labels will not be used to train the model as you will be using a non-supervised approaches to detect anomalies.
  But they will be used to identify the model errors and analyse them.
- Uni-variate: single feature like temperature
- Multi-variate: multiple features like temperature and pressure

In your 1st assignment, you'll be proposing a dataset that respect the previous requirements.

## Algorithms specification

To detect the anomalies, you'll be using 3 different algorithms: one based on heuristics and two others based on Machine
Learning techniques

The chosen algorithms need to respect the following criteria:

- **Can not be used**: Isolation forest, One-class SVM
- **Can be used (only one of this list)**:
  - Naive approach with global or Local mean/std (for heuristics based),
  - STL decomposition, Forecasting with Automatic decomposition
- **Others that can be used**: Mean Absolute Deviation (for heuristics based), Fourier transform, Vector
  auto-regression, ...

## Model error analysis

After each modeling approach, you need to:

1. Visualize anomalies: correct anomalies (labels) in green and predicted anomalies in red
2. Analyze the model errors:
    - Why some data points were classified as anomalies while they are not (False positives)?
    - Why some data points were not classified as anomalies while they are (False negatives)?

**You can find more information on the grading criteria and what is expected from you in the defense in the *defense.md* file**

# Dataset and group formation

For this assignment, you need to submit a link to the project github repository with the followings:

1. README.md file with

- Group members
- A link to the dataset you will be used for the project (make sure it respects the requirements
  defined *1. project description.md* file)
- A small description of the use case (energy consumption for London habitants for example)
- A description of the dataset
  - What it represents,
  - what an anomaly represents,
  - different features,
  - etc
- Some statistics about the dataset:
  - percentage of anomalies,
  - ...

✅ 2. A notebook with EDA on the dataset (make sure to have plots about all dataset anomalies)

# Defense - Anomaly detection on a time series dataset

For the TSA project defense, you will have 35 min (presentation (25 min) + Q&A (10 min)) to present the followings:

1. Use case, dataset, dataset anomalies (what they are, what they present, when do they occur, percentage, etc)
2. The results of the different detection algorithms (detected and non-detected anomalies)
3. Analysis of the model errors: why some anomalies were not detected (False negative) and why some were considered
as anomalies while they are not (False positive)

You can use whatever support you see fit (PPT, jupyter notebook Slideshow, ...)

Make sure to:

- Focus on the Model error analysis part as it is the most important in this project
- Use visuals in the presentation
- References of the different resources used for the project (research papers, blogs, etc)


**You need to submit the link to your github repository (don't forget to add me as a collaborator)**

## Grading criteria

- Model error analysis: 40%
- Relevance of the anomaly detection algorithms: 30%
- Notebook quality (graphs, comments, ect): 15%
- Presentation quality: 10%
- Code versioning in git and use of commits, branches, etc: 5%