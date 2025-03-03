# Kaggle-Backpack
**Author**: jjadey

## Overview
**Placement**: Top 51%\
**Competition**: [Backpack Prediction Challenge](https://www.kaggle.com/competitions/playground-series-s5e2)

This is a regression task where a range of backpack features, such as colour, brand, and material, are used to predict its price. This competition presents a unique challenge due to its synthetically generated dataset, characterized by high noise and low signal. To quote Kaggle Grandmasters who achieved Rank 1 and Rank 2 in this competition:

- cdeotte: "Kaggle's Backpack competition is a tough competition! It is hard to build a model that performs better than simply predicting the constant value of train mean for all test samples."
- ravi20076: "This is a very unique Playground episode in my opinion. I have been around here for 3 years, but have never seen such a noisy dataset to start with."

## My Approach
Each step of my approach is marked by a separate git commit, and the score obtained on the Kaggle leaderboard (For this competition, score is Root Mean Squared Error, so lower is better):

1. **Baseline - Error 39.14811**: Getting an initial model up and working.
    - Performed EDA covering missing values, feature shape, summary statistics, and relationships (correlation matrix, scatterplots, boxplots).
    - Built pipelines for imputation (mean for numerical, mode for categorical) and encoding (ordinal where it made sense contextually, otherwise one-hot).
    - Trained simple models (linear regression, k-nearest neighbours, decision tree), and evaluated them based on Root Mean Squared Error (competition metric). Linear regression was the most accurate.

2. **Further Feature Engineering - Error 39.14791 (-0.0002)**: Improving my pipeline to create more informative features.
    - Reduced one-hot encoding to Boolean features only and dropped the first column. This reduces sparsity and prevents multicollinearity.
    - Target encoded the leftover features. This turns them into a numerical feature which is easier to work with and reduces dimensionality.
    - Min-max scaled all the features (now numerical) to a range of [0,1]. This is important for models such as KNN which is sensitive to the scale of data.
    - Unfortunately, the results did not improve much from the baseline, and I suspect this is due to low underlying signal.

3. **Advanced Models - Error 39.15073 (+0.00282)**: Trying a range of more complex models, including elastic net, support vector machine, multi-layer perceptron, random forest, and xgboost.
    - EN - Regularisation made performance worse, likely due to low underlying signal.
    - SVM - Took a long time to train (around an hour for 240,000 instances, due to O(n<sup>2</sup>) to O(n<sup>3</sup>) time complexity), and results were not impressive otherwise.
    - MLP - Best results, beating linear regression.
    - RF and XGB - Bad performance, but fast to train and worth exploring in the next section.

4. **Hyperparameter Optimisation - Error 39.13796 (-0.01277)**: Exploring promising models by identifying optimal hyperparameters.
    - Used Optuna to perform Bayesian optimisation on MLP, RF and XGB models. Chose 3, 5, and 7 hyperparameters respectively to tune from each model.
    - MLP showed little improvement. RF and XGB both improved significantly, with XGB becoming the best model overall.

For reference, the naive average (predicting the train set average price for all test set instances) gives an error of 39.16456, and using AutoML (AutoGluon) on the raw data gives an error of 39.12921.