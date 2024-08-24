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


