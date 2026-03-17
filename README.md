# Diabetes HomeWork
Note: Since the assignment was in English, I wrote this README and the comments in English as well.

## Exploratory Data Analysis
### Data Collection
In this section the original dataset was loaded and the datasets for the scenarios were created.

### Basic Information
In this section we can see that all features are **numeric** (float64), there are **no missing values**, and that the features have **different ranges**. 

### Univariate Analysis
Here we can check what **distribution** each feature has (usually normal with a left/right skew) and if they have **outliers**.

### Multivariate Analysis
The heatmap in this section shows the correlation between the features. We can see that usually they are **not correlated**, however in some cases (_s1 & s2_) a **higher correlation** can be seen.

The pairplot shows that the _endangered_ samples are usually **not distinct** from the others, there are **no separate groups** in any feature combination. On the diagonal we can see that the _endangered_ samples sometimes have a similar distribution to the _not endangered_ ones, seemingly with similar mean/median/mode. For example, feature _age_ shows that both groups have only a few samples under 20 and over 80, but a lot more around 50.
Other features seem to have a bigger impact on whether someone is _endangered_ or not, e.g. _bmi_ and _bp_, where the _endangered_ group have higher values in both features.


## Data Exploration Results
## Model Perormance Metrics
## Visualizations and their interpretations