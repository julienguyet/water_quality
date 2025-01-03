# TSA on GECCO Industrial Challenge Dataset


## Dataset Description

The GECCO 2018 Industrial Challenge, presented by the Genetic and Evolutionary Computation Conference, centered around a critical and increasingly relevant issue—monitoring drinking-water quality. This dataset was provided as part of a competition aimed at fostering innovative approaches in evolutionary computation and machine learning to detect anomalies in water quality, which are indicative of potential health hazards or system failures.

### Dataset Overview

The challenge dataset comprises multivariate time series data collected from a real-world water management system. The data includes several physicochemical parameters measured over time, reflecting the complexity and dynamics of water quality management. Key features include:

- `Time`: Timestamps for each measurement, providing the temporal context for the data.
- `Tp` (Temperature): The water temperature, measured in degrees Celsius, which can influence chemical reactions and microbial growth in water.
- `Cl` (Chlorine Dioxide - MS1) and Cl_2 (Chlorine Dioxide - MS2): Concentrations of chlorine dioxide from two different measurement stations, indicating the level of disinfectant present, which is crucial for maintaining water safety.
- `pH`: A measure of how acidic/basic water is, pivotal for both water quality and equipment maintenance.
- `Redox` (Reduction-Oxidation Potential): An indicator of the water’s ability to break down contaminants.
- `Leit` (Electrical Conductivity): Reflects the water's ionic activity and total dissolved solids.
- `Trueb` (Turbidity): Measures the clarity of water, where higher values may indicate pollution.
- `Fm` and `Fm_2` (Flow rates): Recorded at two different water lines, these indicate the volume of water flow, essential for detecting leaks or changes in water demand.
- `EVENT`: A boolean label identifying whether a measurement is considered an anomaly, crucial for training models to recognize signs of potential issues.

<img width="685" alt="Screenshot 2024-06-07 at 16 52 46" src="https://github.com/user-attachments/assets/00266ffe-6964-466c-8753-27cc5c8e037f">

---

## Applications and Importance

The dataset not only serves as a valuable resource for developing and benchmarking anomaly detection algorithms but also plays a vital role in enhancing the safety and efficiency of water treatment facilities. By accurately identifying anomalies, predictive models can help prevent serious issues such as contamination events or system malfunctions before they affect water quality and public health.

---

## Link to the dataset

- [GECCO Challenge 2018](https://www.spotseven.de/gecco/gecco-challenge/gecco-challenge-2018/)
- [Resource Package (contains the dataset)](https://www.spotseven.de/wp-content/uploads/2018/03/ResourcePackage2018.zip)

---

## Authors

Group: Duy Tan LE and Julien Guyet

---

## EDA Summary

It is important to note that per the GECCO Organization, the **<font color=#ff7400>flow rate and the temperature</font>** of the water is considered as operational data: **<font color=#ff7400>changes</font>** in these values may indicate variations in the related quality values but **<font color=#ff7400>are not considered as events themselves</font>**.

Missing values count for 0.7% only of the data. As we can see on picture below, after investigation we notice values for every columns are always missing for the same rows, so we will simply drop them.

<img width="673" alt="Screenshot 2024-08-24 at 12 39 52" src="https://github.com/user-attachments/assets/6d54d9f3-43b0-48af-822f-8b0285ff031d">

To understand better our anomalies, we did a plot per feature. If some data points look like obvious outliers, others are mixed in the crowd and it might be harder to properly detect them. The plot below for the pH value of the water over time is  a good example of this:

<img width="1156" alt="Screenshot 2024-08-24 at 12 40 33" src="https://github.com/user-attachments/assets/be7edb70-7655-4073-9fef-4ec7ef8bfef5">

Finally, we looked at an hourly level and it seems anomalies are:
- not appearing at 2am and midnight
- very low at 6pm
- peaks at 9 and 10am

Appart from that, anomalies are quite evenly distributed.

<img width="864" alt="Screenshot 2024-08-24 at 12 40 50" src="https://github.com/user-attachments/assets/e47a8251-bcb1-4cee-a550-0a57faaf83f5">

---

## Anomaly Detection

Now that we explored the data, we can move to the modeling part. Our goal is to build a model capable of perfectly detecting anomalies to ensure tap water is safe to drink for citizens. In this section, we will explore algorithms of increasing complexity.

### Heuristic Approach

First, let's define a baseline by applying basic statistical concepts and see if it can solve our problem. After all, why building complex ML model if mean and [z-score](https://en.wikipedia.org/wiki/Standard_score) can help us here?

The idea is pretty simple: we create a sliding window selecting N data points. Then we compute in this window: 
- the mean
- the standard deviation
- the deviation to the mean for each points

If the deviation is greater than a define threshold, the value is labelled as an anomaly. Below are some graphs with formulas to help us visualize what we are doing:

<img width="645" alt="Screenshot 2024-08-24 at 13 12 03" src="https://github.com/user-attachments/assets/0b4723e3-f6dd-4922-abcb-325548879f06">

<img width="642" alt="Screenshot 2024-08-24 at 13 12 20" src="https://github.com/user-attachments/assets/b93a88b1-d988-419c-9e43-534fa5128af7">

As you can see on formula (3) we have included an alpha hyper-parameter when building the model. It is set to 2 by default (following empirical rule of Normal Distribution where 95% of the data will fall within +/- two standard deviations from the mean).

Code is available in the dedicated [notebook](https://github.com/julienguyet/water_quality/blob/main/notebooks/heuristic.ipynb) where we applied different values for alpha and the window sizes (how many points to include when computing). Below is a table of our scores for Precision and Recall: 

<img width="341" alt="Screenshot 2024-08-24 at 13 24 06" src="https://github.com/user-attachments/assets/544aa1d7-36f3-4ff0-8c42-17196a623a9b">

Obviously, we cannot be satisfied with such results. It is clear that our algorithms is failing to both detect anomalies and not confuse "normal" data points with anomalies. The below graph is self-explanatory:

<img width="1004" alt="Screenshot 2024-08-24 at 13 25 19" src="https://github.com/user-attachments/assets/bfe3dd3f-5d35-4eb2-a734-c5963e76bf3a">

### Fourier Transform

To solve our problem we need more robust and complex tools. As we know, the Fourier Transform allows us to represent a function from the Time domain into the Frequency domain. This means the component frequencies of the original signal can then be used to analyze patterns, extract them and remove noise. If you are familiar with audio processing and how Fourier Transform can help, logic here is mainly the same. In case you are not familiar with the Fourier Transform, this [video](https://www.youtube.com/watch?v=spUNpyF58BY&t=1003s) by 3Blue1Brown is an excellent starting point. 

We built our algorithm leveraging Numpy and its integrated Fourier functions. Code is available in the [Fourier notebook](https://github.com/julienguyet/water_quality/blob/main/notebooks/fourier_transform.ipynb). In short, the following steps are:
1. Compute the Fourier Transform
2. Order values and keep only top k samples and cast rest as 0
3. Compute restructured signal X'
4. Check the deviation between X and X' and compare it to a threshold to detect anomalies

Below is the results when keeping the top 10 samples of our "signal":

<img width="997" alt="Screenshot 2024-08-24 at 13 47 28" src="https://github.com/user-attachments/assets/d6d8ff7f-ae7e-4d9d-9847-aed0b49ee2ca">

Again, we designed our model with hyper-parameters such as the number of top k samples to include or the alpha value to set the threshold. 

<img width="312" alt="Screenshot 2024-08-24 at 13 50 36" src="https://github.com/user-attachments/assets/c573e60e-d696-4a10-9b5e-4743992a7586">

A choice must be made between the number of samples k and the alpha value to balance the amount of False Negatives:

<img width="333" alt="Screenshot 2024-08-24 at 13 50 49" src="https://github.com/user-attachments/assets/b07f4a66-9e9f-4ac4-86e5-e43f3b390e7f">


To date we haven’t find any optimal solution with the Fourier Transform. Some ideas to explore: (i) change threshold function (median, quantiles, …) or (ii) create a sliding window Fourier function.

### Long-Short Term Memory (LSTM)

Despite promising results, the Fourier Transform cannot be used as final answer to our problem. Indeed, we are talking about public health issues here, and we must find a solution leaving no doubt to the question: "can I safely drink this water?".

As for the Fourier function, we decided to apply a concept first designed for other kind of problems. In NLP, it is (or was?) common to use Recurrent Neural Network and more especially LSTM as these ML models are very good at understanding sequences. 

To apply LSTM to our use case, we first created sequences of length 10 with associated label for each datapoint included in the sequence. Then, we trained the model to learn the pattern of each sequence and predict the label of the next point in the given sequence. 

<img width="492" alt="Screenshot 2024-08-24 at 14 02 53" src="https://github.com/user-attachments/assets/38c62e5b-78c0-4b66-a96c-4eead27d8f7d">

After training, we obtained a Precision score of 0.75 and a Recall of 0.47. Here is the associated confusion matrix:

<img width="705" alt="Screenshot 2024-08-24 at 14 03 21" src="https://github.com/user-attachments/assets/f7769bda-c693-4407-bb76-030488f47617">

From the matrix and below graph we can deduct our model is facing difficulties to detect anomalies, but the good news is it almost never confuse anomalies with normal data.

<img width="1008" alt="Screenshot 2024-08-24 at 14 06 14" src="https://github.com/user-attachments/assets/9fd022d7-fec5-4f4f-a63b-e7e2bd2369e3">

To improve the performance of our model we leveraged the imbalance-learn library and combined LSTM with SMOTE.

SMOTE stands for Synthetic Minority Oversampling Technique. It works by selecting examples that are close in the feature space. Then it draws a line and “create” a new sample in this feature space. This way, it artificially creates new data points and provide you with a new dataset. Code from imbalance-learn.org (sklearn) is available [here](https://imbalanced-learn.org/stable/references/generated/imblearn.over_sampling.SMOTE.html).

We used SMOTE to generate new anomalies in our dataset and therefore help the model to learn better what is an anomaly. Finally, we obtained the below results:

<img width="139" alt="Screenshot 2024-08-24 at 14 19 46" src="https://github.com/user-attachments/assets/8535614f-b105-47ac-a6fc-3b6cd1f04a3f">

<img width="684" alt="Screenshot 2024-08-24 at 14 19 56" src="https://github.com/user-attachments/assets/64625b38-3429-4984-ab80-bdeac7d1735d">

As we can see, we are obtaining a model capable of classifying almost perfectly all data points.

<img width="1012" alt="Screenshot 2024-08-24 at 14 23 23" src="https://github.com/user-attachments/assets/1ee9746c-a87b-4f94-afa4-27ca941ac64e">