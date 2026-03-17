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
*Feature s6 - An example of a feature's distribution and its outliers*

Finally, we can see how the threshold affects the _endangered_ feature distribution. In Scenario 1 with a lower threshold the classes are somewhat balanced (_not endangered_: 242 and _endangered_: 200, 1.2x ratio), however in Scenario 2 where the threshold is higher, the dataset is more imbalanced (_not endangered_: 377 and _endangered_: 65, 5.8x ratio).
![Target distributions](https://github.com/engelmannakos/DiabetesHomeWork/blob/main/target_distributions.png)
*Number of Endangered-Not Endangered samples in both scenarios*

### Multivariate Analysis
The heatmap in this section shows the correlation between the features. We can see that usually they are not correlated, however in some cases (_s1 & s2_) a higher correlation can be seen.
![Small heatmap](https://github.com/engelmannakos/DiabetesHomeWork/blob/main/small_heatmap.png)
*_s1 & s2_ are highly correlated, while _s3 & s4_ are also somewhat correlated but negatively*

The pairplot shows low class separability, there are no separate groups in any feature combination. On the diagonal we can see that the _endangered_ samples sometimes have a similar distribution to the _not endangered_ ones, seemingly with similar mean/median/mode. For example, feature _age_ shows that both groups have only a few samples under 20 and over 80, but a lot more around 50.
Other features seem to have a bigger impact on whether someone is _endangered_ or not, e.g. _bmi_ and _bp_, where the _endangered_ group have higher values in both features.
![Small pairplot](https://github.com/engelmannakos/DiabetesHomeWork/blob/main/small_pairplot.png)
*Pairplot of the age, bmi and bp features in Scenario 1*

### Handling Outliers
As we've seen in section _Univariate Analysis_ the dataset contains a few outliers. Simply removing them would only create new outliers in other features, and, since we have only a few _endangered_ samples in Scenario 2, I wanted to avoid removing samples. Instead, a winsorizing technique was used here that replaced the values outside the 5th and 95th percentiles with the 5th and 95th percentiles. For example, this step was important for the Neural Network model, and not so much for the Random Forest model.

### Standardization
As we've seen in section _Basic Information_, the features have different ranges, which could cause a lower model performance. To solve this issue, StandardScaler was applied on the features to have a mean of 0 and a standard deviation of 1.

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
For both scenarios a group of classification models (LogisticRegression, Random Forest, SVM) and neural network (MLP) was used to look for the best results. All models were designed by scikit-learn, and tuning the hyperparameters did not result in any significant performance improvement most of the time.

### Model Evaluation
To evaluate the models' performances, the confusion matrix, the accuracy score (separated), a whole classification report and the ROC AUC Score were taking into account.

- Scenario 1
    - All models performed similarly. Where one found more TPs (e.g. SVM), the other found more TNs (e.g. Random Forest), resulting in similar metric scores. The accuracy score stayed around 77%, and if we look into the matrix and the precision/recall/f1-score metrics, we can see that the models labeled the _not endangered_ samples relatively correctly (~80-90% recall), but performed worse labeling the _endangered_ ones (~65% recall). The ROC AUC Score is around ~76%, which is moderately good, but only half-way between random guessing (50%) and perfect separation (100%).
![Scenario 1 RF and SVM confusion matrices](https://github.com/engelmannakos/DiabetesHomeWork/blob/main/matrices_scen1.png)
*Confusion matrices of the Random Forest and SVM models in Scenario 1*

- Scenario 2
    - In this case the models performed similarly to each other, but all had a main difference compared to Scenario 1. As the dataset contained less _endangered_ samples, the models were struggling with learning the patterns, causing the low recall scores (~20-40%) for _endangered_. In this case the accuracy is misleading as it shows a high value even if barely any _endangered_ samples were labeled correctly. The class_weight hyperparameter, however, is designed for occasions like this. Changing it to 'balanced' helped the LogisticRegressor and SVM models to improve their performance on _endangered_ samples, but not the Random Forest. The SVM and the LogisticRegressor model have a higher recall score for _endangered_ samples (~75%) than the other models (~40%), however this changed led to decline in their _not endangered_ recall score. The ROC AUC Score doesn't work well with highly imbalanced classes, the PR Score is more relevant. It shows that the model didn't work well in most cases, but the modified SVM model earned a 0.4 score, which is relatively good.
![Scenario 2 RF and SVM confusion matrices](https://github.com/engelmannakos/DiabetesHomeWork/blob/main/matrices_scen2.png)
*Confusion matrices of the Random Forest and SVM models in Scenario 2*

## Conclusion
The evaluation of Scenario 1 showed promising results with room for improvement in labeling _endangered_ samples. Scenario 2 on the other hand had the same type of issue, only with worse scores. In my opinion, there are 2 reasons for this result. First, the dataset doesn't contain enough samples (only 442) and the models cannot learn the patterns. And second, as we've seen on the pairplot in section _Multivariate Analysis_ the samples are overlapping each other, which makes learning more difficult.