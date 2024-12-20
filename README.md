# Rain Prediction in Perth

### Abstract: This project aims to develop a model to predict whether it will rain in Perth Australia tomorrow based on today’s weather data. We tested for both parametric and non-parametric models, including logistics regression, KNN, random forest, gradient boosting, etc. Predictors in these models include temperature, sunshine, and humidity, etc.

## Introduction

Weather prediction is a complex challenge due to the non-linear interactions between atmospheric conditions. This project focuses on developing a classification model to predict whether it will rain in Perth, Australia, tomorrow using today’s weather data. Accurate rainfall predictions have significant implications in agriculture, transportation, and emergency preparedness, making this problem both practical and meaningful. Beyond immediate applications, rainfall prediction provides insights into seasonal trends and supports the understanding of climate change.

<img src="https://raw.githubusercontent.com/Kev1nTang/QTM-347-Project/main/Figure/Figure%201.png" style="width: 100%; height: auto;">

To address this problem, we propose building and evaluating various classification models, including Logistic Regression, Decision Trees, and ensemble methods like Random Forests and Gradient Boosting. These approaches are well-suited to handling the multi-dimensional and non-linear nature of weather data, with ensemble methods particularly adept at improving prediction accuracy.

## Data Exploration

### Feature Overview

| **Feature Name**       | **Description**                                      | **Type**    |
|-------------------------|------------------------------------------------------|-------------|
| `MinTemp`              | Minimum temperature of the day (°C)                  | Numeric     |
| `MaxTemp`              | Maximum temperature of the day (°C)                  | Numeric     |
| `Rainfall`             | Total rainfall for the day (mm)                      | Numeric     |
| `Evaporation`          | Evaporation for the day (mm)                         | Numeric     |
| `Sunshine`             | Total hours of sunshine                              | Numeric     |
| `WindGustSpeed`        | Maximum wind gust speed (km/h)                       | Numeric     |
| `Month`                | Month of the observation (1-12)                      | Categorical |
| `Humidity`             | Average humidity (%)                                 | Numeric     |
| `Pressure`             | Average atmospheric pressure (hPa)                   | Numeric     |
| `Cloud`                | Average cloud coverage (0-8 scale)                   | Numeric     |
| `RainToday_Yes`        | Indicator if it rained today (1 = Yes, 0 = No)       | Binary      |
| `RainTomorrow_Yes`     | Target variable: Will it rain tomorrow? (1 = Yes, 0 = No) | Binary      |

### Visualizations
To start with, we can explore our data with several visualizations:

<details>
  <summary>Click to expand</summary>

#### Pie charts on distribution of categorical features

<img src="https://raw.githubusercontent.com/Kev1nTang/QTM-347-Project/main/Figure/Figure%203.png" style="width: 100%; height: auto;">

For **RainToday** and **RainTomorrow**, the figures demonstrate that approximately 20% of the observations indicate rain, while the majority, about 80%, do not. 

#### Bar graphs on distribution of continuous features

<img src="https://raw.githubusercontent.com/Kev1nTang/QTM-347-Project/main/Figure/Figure%202.png" style="width: 100%; height: auto;">

From these figures, we observe that **Rainfall** has a highly skewed distribution with most days experiencing little to no rain, while variables like **MinTemp** and **MaxTemp** follow a more normal distribution.

#### Correlation heatmap of the variables

<img src="https://raw.githubusercontent.com/Kev1nTang/QTM-347-Project/main/Figure/Figure%204.png" style="width: 100%; height: auto;">

From the map, we can observe that:

1. There is a strong positive correlation between **Evaporation** and **MaxTemp** (0.80). This suggests that higher maximum temperatures may lead to increased evaporation.
2. **RainToday_Yes** is positively correlated with **Rainfall** (0.64) and **Humidity** (0.49), which are strong indicators of the likelihood of rain on the same day or the next day.
3. **Pressure** shows a negative correlation with **Cloud** (-0.49) and **Humidity** (-0.66). This indicates that lower atmospheric pressure is associated with increased cloudiness and humidity, likely contributing to precipitation events.

However, these correlations are not protected from multicollinearity that exists in these weather data.

#### Boxplots on continous variables vs. target variable

<img src="https://raw.githubusercontent.com/Kev1nTang/QTM-347-Project/main/Figure/Figure%205.png" style="width: 100%; height: auto;">

These boxplots demonstrate patterns like higher **Rainfall** and lower **Sunshine** increasing the likelihood of **rain**.

#### Bar graph on categorical variables vs. target variable

<img src="https://raw.githubusercontent.com/Kev1nTang/QTM-347-Project/main/Figure/Figure%206.png" style="width: 100%; height: auto;">

The bar graph for month indicates that from May to September, the possibility of rain is higher. The bar graph comparing RainToday and RainTomorrow highlights a strong correlation: if it rains today, there is a much higher likelihood of rain tomorrow.

</details>


## Experimental Setup

### Computing Environment:
- **Programming Language:** Python 3.8+
- **Platform:** Jupyter Notebook
- **Libraries:** NumPy, pandas, scikit-learn, XGBoost, and Matplotlib/Seaborn for visualization

### Problem Setup:
- **Objective:** Binary classification to predict whether it will rain tomorrow (`RainTomorrow` = `1` for rain, `0` otherwise)
- **Evaluation Metric:** Misclassification Error Rate


## Model Implementation:
### 1. **Parametric Models:**
Parametric models rely on assumptions about the data distribution and aim to estimate parameters that best explain the relationship between predictors and the target variable. For our rain prediction project, we implemented logistic regression as the primary parametric method. Additionally, we used forward selection and regularization techniques to improve model performance and interpretability.

<details>
  <summary>Click to expand</summary>

#### 1.1 **Logistic Regression:** 
Logistic regression is a statistical model used for binary classification problems. It estimates the probability of a binary outcome using a logistic function, making it ideal for predicting categorical variables like "rain" or "no rain."

<img src="https://raw.githubusercontent.com/Kev1nTang/QTM-347-Project/main/Figure/Figure%207.png" style="width: 100%; height: auto;">

The misclassification error rate of 12.26% indicates that approximately 12 out of 100 predictions were incorrect. This is a good starting point, showing that the weather variables used are informative but could benefit from refinement.

#### 1.2 **Forward Selection:** 
Forward selection is a stepwise feature selection method that iteratively adds the most significant predictors to the model. It helps identify the best subset of features that contribute most to predicting the target variable.

<img src="https://raw.githubusercontent.com/Kev1nTang/QTM-347-Project/main/Figure/Figure%208.png" style="width: 100%; height: auto;">

Forward selection selects the 7 most influential predictors based on their contribution to improving the model's log-likelihood. This refinement reduced the misclassification error rate to 11.64%, the best performance among parametric models. The chosen predictors provide the most information about rain likelihood. For instance, variables like humidity or pressure may dominate due to their direct physical connection to precipitation. A reduction of error rate from 12.26% to 11.64% indicates that including fewer but more relevant predictors improves model efficiency and accuracy. The model selected is demonstrated in the following figure.

<img src="https://raw.githubusercontent.com/Kev1nTang/QTM-347-Project/main/Figure/Figure%209.png" style="width: 100%; height: auto;">

#### 1.3 **Principal Component Analysis (PCA) / Partial Least Squares (PLS):** 
PCA is a dimensionality reduction technique that transforms the data into principal components, capturing the most variance in fewer dimensions. PLS, on the other hand, maximizes the covariance between predictors and the target variable, making it suitable for highly correlated features.

**PCA:**

<img src="https://raw.githubusercontent.com/Kev1nTang/QTM-347-Project/main/Figure/Figure%2010.png" style="width: 100%; height: auto;">

PCA transformed the predictors into 6 uncorrelated components while retaining as much variance as possible. The misclassification error rate remained at 11.95%, slightly worse than forward selection. PCA effectively addressed multicollinearity among predictors by transforming correlated variables into orthogonal components. However, due to the small number of predictors in our dataset, PCA was less impactful.

**PLS:**

<img src="https://raw.githubusercontent.com/Kev1nTang/QTM-347-Project/main/Figure/Figure%2011.png" style="width: 100%; height: auto;">

PLS selected 2 components by maximizing the covariance between predictors and the target variable. However, the misclassification error rate of 12.26% was identical to the full logistic regression model. Like PCA, PLS is more suitable for datasets with a higher number of predictors.

#### 1.4 **Ridge and Lasso Regression:** 
Ridge and Lasso are regularization techniques that add penalties to the regression model to reduce overfitting. Ridge minimizes the sum of squared coefficients, while Lasso encourages sparsity by shrinking coefficients of less important features to zero.

**Ridge Regression:**

<img src="https://raw.githubusercontent.com/Kev1nTang/QTM-347-Project/main/Figure/Figure%2012.png" style="width: 100%; height: auto;">

The misclassification error rate of 11.95% indicates that this approach effectively stabilized the model but did not outperform forward selection. Ridge regression provides better generalization by shrinking large coefficients and retaining all predictors. While it controls overfitting, the inclusion of less relevant predictors can dilute the model’s predictive power.

**Lasso Regression:**

<img src="https://raw.githubusercontent.com/Kev1nTang/QTM-347-Project/main/Figure/Figure%2013.png" style="width: 100%; height: auto;">

Lasso regression model has a misclassification error rate of 12.42%, the highest among regularization techniques. Lasso’s feature selection is valuable for simplifying models in high-dimensional datasets. In this case, Lasso likely eliminated some predictors that contribute small but meaningful information, leading to a loss in predictive accuracy.

The following figure demonstrates a summary of the misclassification errors derived using different parametric models.

<img src="https://raw.githubusercontent.com/Kev1nTang/QTM-347-Project/main/Figure/Figure%2014.png" style="width: 100%; height: auto;">

From the figure above, forward selection yields the best outcome among the parametric models, this is due to its ability to iteratively select the most significant predictors. By adding features one at a time based on their contribution to improving the model's performance, forward selection minimizes overfitting while retaining the most relevant variables.

</details>

### 2.  **Non-parametric Models:**
Non-parametric models do not assume a specific data distribution and instead rely on the data itself to reveal patterns and relationships. By not making strong assumptions about the data, non-parametric models can capture intricate patterns that parametric models might miss. For our rain prediction project, we implemented models including K-Nearest Neighbors (KNN), Classification Trees, Random Forests, and Gradient Boosting.

<details>
  <summary>Click to expand</summary>

#### 2.1 **K-Nearest Neighbors (KNN):**  
An instance-based learning algorithm that classifies a data point based on the majority class of its k-nearest neighbors. It is simple and effective for datasets with distinct clusters but may struggle with high-dimensional data.

<img src="https://raw.githubusercontent.com/Kev1nTang/QTM-347-Project/main/Figure/Figure%2015.png" style="width: 100%; height: auto;">

The optimal value for k was determined to be 15, which balances bias and variance to achieve the best model performance. At this optimal value, the misclassification error rate is 11.64%, indicating the proportion of incorrectly classified data points.


#### 2.2 **Classification Tree:**  
Partition data into subsets based on feature values, forming a tree-like structure. They are easy to interpret and effective for capturing non-linear relationships in the data.

<img src="https://raw.githubusercontent.com/Kev1nTang/QTM-347-Project/main/Figure/Figure%2017.png" style="width: 100%; height: auto;">

The tree achieves a misclassification error rate of 12.11%, effectively capturing the relationships between predictors and target classes.


#### 2.3 **Random Forest:**  
An ensemble learning method that builds multiple decision trees and combines their predictions for improved accuracy. It reduces overfitting and works well with complex datasets with many features.

<img src="https://raw.githubusercontent.com/Kev1nTang/QTM-347-Project/main/Figure/Figure%2018.png" style="width: 100%; height: auto;">

This model achieves a misclassification error rate of 11.32%. Feature importance rankings highlight that "Sunshine" is the most influential variable, followed by "Pressure," "Humidity," and "WindGustSpeed." Other factors such as "MaxTemp," "MinTemp," and "Cloud" also contribute to the model's predictions, albeit to a lesser extent.

#### 2.4 **Gradient Boosting:**  
A powerful ensemble method that builds trees sequentially, optimizing for errors made by previous trees. XGBoost, an implementation of Gradient Boosting, is known for its speed and high predictive performance, especially in structured data problems.

<img src="https://raw.githubusercontent.com/Kev1nTang/QTM-347-Project/main/Figure/Figure%2020.png" style="width: 100%; height: auto;">

The model achieves a misclassification error rate of 10.85%, making it one of the best-performing non-parametric models. The visualization highlights how decision trees are split based on various features, such as wind gust speed, sunshine, and humidity, to refine predictions and reduce overall error.

The following figure demonstrates a summary of the misclassification errors derived using different non-parametric models.

<img src="https://raw.githubusercontent.com/Kev1nTang/QTM-347-Project/main/Figure/Figure%2021.png" style="width: 100%; height: auto;">

From the figure above, Gradient Boosting yields the best outcome among the non-parametric models, this is due to its ability to sequentially build decision trees that correct the errors made by previous trees, allowing it to capture complex, non-linear relationships in the data. Note that the misclassification error rate of Gradient Boosting is smaller than that of all the parametric models, highlighting its superior performance in this task. 

</details>

## **Regularization of Gradient Boosting:**
  
The purpose of building a predictive model is to ensure that it generalizes well to new datasets. In this case, while Gradient Boosting proved to be the most suitable for weather prediction in Perth, it can sometimes overfit the data, especially when applied to noisy datasets or small sample sizes. In this section, we will introduce different regularization methods to reduce the risk of overfitting.

### i) **Subsampling:**  
Subsampling is an effective regularization technique in Gradient Boosting. The use of subsampling refers to randomly selecting a fraction of the training data in each iteration to train each base learner. By introducing randomness into the learning process, subsampling helps prevent the model from fitting too closely to the training data, which can lead to overfitting and poor performance. 

One key advantage of subsampling is that it naturally reduces computational complexity. By training smaller subsamples of the data in each iteration, the implementation of Gradient Boosting requires less time and memory storage and is, therefore, well-suited for large datasets. However, the fraction of data selected at each iteration needs to be determined manually. While a smaller subsample results in greater randomness and better model generalization, it comes at the potential cost of increasing bias, which means that the model may underfit the data if too small a fraction of the data is selected in each iteration. In this case, the model may fail to capture important patterns in the data, leading to decreased accuracy.

### ii) **Shrinkage:**  
Shrinkage, also can be referred to as the learning rate, is another important regularization technique in Gradient Boosting. It works by shrinking the contribution of each weak learner in the overall model. In the context of regularization, one can reduce the learning rate. By slowing down the learning process, shrinkage ensures that the model improves gradually, reducing the risk of overfitting.

One of the key advantages of shrinkage is that it strikes a balance between model generalization and model complexity. When the value of the learning rate decreases, the algorithm will use a larger number of iterations to minimize the loss function, allowing the model to be built by small incremental improvements. These gradual improvements help the model to focus more on meaningful patterns in the data rather than noise, which reduces the risk of overfitting. However, shrinkage also introduces computational trade-offs. A smaller value of $\lambda$ requires a larger number of iterations to reach convergence, which will take more time and memory storage in the learning process.

### iii) **Early Stopping:**  
In the previous section, a disadvantage of shrinkage mentioned is that it significantly raises the computational cost due to the increased number of iterations. One regularization technique in Gradient Boosting that can help mitigate this issue is early stopping. Early stopping works by monitoring the performance of the model during the learning process and stopping the iterations once the model stops improving. By terminating the learning process early, early stopping prevents the model from becoming too complex and fitting noises in the training data, which can lead to overfitting.

One key advantage of early stopping is its ability to dynamically adjust the number of iterations, eliminating the need to manually predetermine the number of iterations. In addition, the early stopping approach complements other regularization methods, such as shrinkage and subsampling, as it ensures that the model does not continue to improve itself with negligible improvement, ultimately leading to overfitting.



## Results:

In our experiment, we compared both parametric models (such as Logistic Regression) and non-parametric models (including KNN, Random Forest, and Gradient Boosting). Among the parametric models, Logistic Regression achieved a misclassification error rate of 12.26%, while forward selection improved this to 11.64%. In contrast, the non-parametric models performed better overall, with Gradient Boosting emerging as the best-performing model with a misclassification error rate of 10.85%. The Random Forest model followed closely with an error rate of 11.32%, and KNN achieved 11.64%. These results suggest that non-parametric models are better suited to this problem, particularly for capturing the complex, non-linear relationships inherent in weather data.

## Discussion: 

The comparison between parametric and non-parametric models clearly shows that the latter outperforms the former in this task. While Logistic Regression is effective in capturing some trends in the data, it is constrained by the assumption of linearity, which limits its ability to model complex interactions between features. On the other hand, non-parametric models like Gradient Boosting and Random Forest are more flexible and capable of capturing non-linear relationships, leading to better performance. Despite the strength of Gradient Boosting, the model could still benefit from regularization methods such as subsampling, shrinkage, and early stopping to reduce overfitting.

## Conclusion:

In this project, we developed and evaluated both parametric and non-parametric models to predict whether it will rain tomorrow in Perth using today's weather data. The non-parametric models, particularly Gradient Boosting, outperformed parametric models like Logistic Regression, achieving a lower misclassification error rate of 10.85%. This suggests that non-parametric models, with their ability to capture complex, non-linear relationships, are better suited for weather prediction tasks. Regularization techniques for Gradient Boosting could further improve performance by preventing overfitting, offering a promising direction for future work.

## References: 

1. A. Natekin and A. Knoll. (2013). *Gradient boosting machines, a tutorial*. *Frontiers in Neurorobotics*, 7:21.  
2. Young, J. (2020, December 11). *Rain in Australia*. Kaggle. [Link to dataset](https://www.kaggle.com/datasets/jsphyg/weather-dataset-rattle-package).
