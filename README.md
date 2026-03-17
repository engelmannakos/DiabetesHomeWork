# Diabetes HomeWork
>[!NOTE]
>Since the assignment was in English, I wrote this README and the comments in English as well.

## Exploratory Data Analysis
### Data Collection
In this section the original dataset was loaded and the datasets for the Scenarios were created.

### Basic Information
In this section we can see that all features are numeric (float64), there are no missing values, and that the features have different ranges. 

### Univariate Analysis
Here we can check what distribution each feature has (usually normal with a left/right skew) and if they have outliers.
![Distribution example](https://github.com/engelmannakos/DiabetesHomeWork/blob/main/s6_box_hist.png)

At the end we can see how the threshold affects the _endangered_ feature distribution. In Scenario 1 with a lower threshold the classes are somewhat balanced (_not endangered_: 242 and _endangered_: 200, 1.2x ratio), however in Scenario 2 where the threshold is higher, the dataset is more imbalanced (_not endangered_: 377 and _endangered_: 65, 5.8x ratio).

### Multivariate Analysis
The heatmap in this section shows the correlation between the features. We can see that usually they are not correlated, however in some cases (_s1 & s2_) a higher correlation can be seen.

The pairplot shows that the _endangered_ samples are usually not distinct from the others, there are no separate groups in any feature combination. On the diagonal we can see that the _endangered_ samples sometimes have a similar distribution to the _not endangered_ ones, seemingly with similar mean/median/mode. For example, feature _age_ shows that both groups have only a few samples under 20 and over 80, but a lot more around 50.
Other features seem to have a bigger impact on whether someone is _endangered_ or not, e.g. _bmi_ and _bp_, where the _endangered_ group have higher values in both features.

### Handling Outliers
As we've seen in section _Univariate Analysis_ the dataset contains a few outliers. Simply removing them would only create new outliers in other features, and, since we have only a few _endangered_ samples in Scenario 2, I wanted to avoid removing samples. Instead, a winsorizing technique was used here that replaced the values outside the 5th and 95th percentiles with the 5th and 95th percentiles. For example, this step was important for the Neural Network model, and not so much for the Random Forest model.

### Standardization
As we've seen in section _Basic Information_, the features have different ranges, which could cause a lower model performance. To solve this issue, a standardization technique was used that transformed features to have a 0 mean and a 1 standard deviation.

### Dimensionality Reduction
The heatmap showed that there is a high (0.9) correlation between the features s1 and s2. However, to remove one of them confidently one should know more about the features, as they might correlate in some way, but represent different mechanisms.

A popular way to reduce the large number of features is PCA, that transforms the dataset to a lower dimension. In our case the models could not benefit from this reduction, therefore it was not used later. 

## Scenario 1 and Scenario 2
### Train-Test Split
After the train-test split, we check how many samples are in the train set (353), and how many in the test set (89). We can also check if the _endangered_ samples have similar ratio in each set than in the original set.
- Scenario 1
    - original dataset: 242-200, ~1.2x ratio
    - train dataset: 193-160, ~1.2x ratio
    - test dataset: 49-40, ~1.2x ratio
- Scenario 2
    - original dataset: 377-65, ~5.8x ratio
    - train dataset: 302-51, ~5.9x ratio
    - test dataset: 75-14, ~5.4x ratio

### Model Training
For both scenarios a group of classification models (LogisticRegression, Random Forest, SVM) and neural network (MLP) was used to look for the best results. All models were designed by scikit-learn.

### Model Evaluation
To evaluate the models' performances, the confusion matrix, the accuracy score (separated), a whole classification report and the ROC AUC Score were taking into account.

- Scenario 1
    - All models performed similarly. Where one found more TPs (e.g. SVM), the other found more TNs (e.g. Random Forest), resulting in similar metric scores. The accuracy score stayed around 77%, and if we look into the matrix and the precision/recall/f1-score metrics, we can see that the models labeled the _not endangered_ samples relatively correctly (~80-90% recall), but performed worse labeling the _endangered_ ones (~65% recall). The ROC AUC Score is around ~76%, which is moderately good, but only half-way between random guessing (50%) and perfect separation (100%).

- Scenario 2
    - In this case the models performed similarly to each other, but all had a main difference compared to Scenario 1. As the dataset contained less _endangered_ samples, the models were struggling with learning the patterns, causing the low recall scores (~20-40%) for _endangered_. In this case the accuracy is misleading as it shows a high value even if barely any _endangered_ samples were labeled correctly. The ROC AUC Score doesn't work well with highly imbalanced classes, the PR Score is more relevant, and it shows that the model didn't work well.

## Conclusion
The evaluation of Scenario 1 showed promising results with room for improvement in labeling _endangered_ samples. Scenario 2 on the other hand had the same type of issue, only with worse scores. In my opinion, there are 2 reasons for this result. First, the dataset doesn't contain enough samples (only 442) and the models cannot learn the patterns. And second, as we've seen on the pairplot in section _Multivariate Analysis_ the samples are overlapping each other, which makes learning more difficult.