# Bank term deposit subscription prediction

## Project details
The goal of this project is to predict if a client will subscribe to a term deposit or not. The dataset used was the Bank Marketing Data Set which was obtained from the [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/datasets/bank+marketing). This is a classification problem, and because the dataset has class imbalance, Class reweighing and undersampling were used to address this class imbalance issue.

## Dataset description
The Bank Marketing Dataset available on the UCI Machine Learning Repository is a collection of data related to telemarketing campaigns for a Portuguese banking institution. The dataset contains information about customer demographics, previous contact history, economic indicators, and the outcome of the marketing campaign (whether the customer subscribed to a term deposit or not).

Source: [Moro et al., 2014] S. Moro, P. Cortez and P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, In press, http://dx.doi.org/10.1016/j.dss.2014.03.001

## Models Used
1. LogisticRegression Model
2. RandomForestClassifier
3. LGBMClassifier

## Some insights from Eda
1. clients who identify as student subscribed more than other groups(31% of students subscribed) even though the number of students are realtively small compared to other groups
2. clients who identify as single have the highest proportion of subscribers.
3. clients who are illterate subscribed more than all other groups. clients with basic education have the least subscription rate of all the group
4. it was observed that the month of march,december,september and october had higher subscription rate, while the day of week with higher subscription is thursday.
5. for client who subscribed,the median contact duration is around 500seconds, while the median contact duration for non subscribers is less than 200 seconds. the maximum time duration spent with non subscribers was around 600s as opposed to around 1500s spent with subcribers.
6. clients with whom there was no previous campaign with, had higher subscription than others

## Modelling
The duration variable was dropped because according to the data source, "this attribute highly affects the output target (e.g., if duration=0 then y="no"). Yet, the duration is not known before a call is performed. Also, after the end of the call y is obviously known. Thus, this input should only be included for benchmark purposes and should be discarded if the intention is to have a realistic predictive model".

Evaluation metric: The chosen evaluation metric is recall, which focuses on minimizing false negatives to capture as many potential subscribers as possible. This approach aims to reduce the risk of missed opportunities and maximize the bank's ability to identify customers likely to subscribe to the term deposit. However, the choice of evaluation metric ultimately depends on the specific business goal. It is crucial to strike a reasonable balance between recall and precision.

Without dealing with imbalance, the evaluation result on 5-folds cross validation:

| Model              | Train Score | Validation Accuracy | Validation Precision (positive class) | Validation Recall (positive class) |
| ------------------ | ----------- | ------------------- | ----------------------------- | --------------------------- |
| LogisticRegression | 0.900880    | 0.900091            | 0.666021                      | 0.227707                    |
| RandomForest      | 0.994627    | 0.892350            | 0.542043                      | 0.291024                    |
| LightGBM           | 0.914329    | 0.900577            | 0.642937                      | 0.265430                    |


After Adjusting class weight(class reweighting) together with using random undersampling, the evaluation result on 5-folds cross validation:

| Model              | Train Score | Validation Accuracy | Validation Precision (positive class) | Validation Recall (positive class) |
| ------------------ | ----------- | ------------------- | ----------------------------- | --------------------------- |
| LogisticRegression | 0.734457    | 0.730935            | 0.253103                      | 0.710051                    |
| RandomForest      | 0.834851    | 0.776988            | 0.286994                      | 0.659122                    |
| LightGBM           | 0.769702    | 0.721767            | 0.248753                      | 0.726218                    |

it can be seen that addressing the class imbalance led to high increase in recall value of the positive(minority) class for all the models.For the LightGBM model, the value increases from about 27% to 73%, which means this model is now able to correctly identify potential customers about 73% of the time.



After hyperparameter tuning using optuna library, the evaluation result on separate test set is given below:

| Metric                      | Value   |
| -------------------------   | ------- |
| Test Accuracy               | 0.7037  |
| ROC AUC Score               | 0.7145  |
| Precision (Positive class)  | 0.2360  |
| Recall (Positive class)     | 0.7284  |
| F-score (Positive class)    | 0.6027  |


The confusion matrix is given below:

![confusion-matrix](https://github.com/vaadewoyin/Bank-term-deposit-subscription-prediction/blob/main/confusion_matrix.png)

## USING DURATION FOR BENCHMARK
After ading the duration column, addressing the class imbalance and evaluating the models on 5-folds cross validation, we have the result below:

| Model              | Train Score | Validation Accuracy | Validation Precision (Positive Class) | Validation Recall (Positive Class) |
| ------------------ | ----------- | ------------------- | ------------------------------------ | ---------------------------------- |
| LogisticRegression | 0.835610    | 0.835580            | 0.400136                             | 0.920232                           |
| RandomForest      | 0.873376    | 0.851032            | 0.425410                             | 0.917273                           |
| LightGBM           | 0.847905    | 0.845811            | 0.418371                             | 0.943682                           |

From the table above, it can been seen that with the inclusion of the duration column, we get high values on 5-fold cross validation, with recall of the minority class reaching up to 94% even without hyperparameter tuning.However, according to the data source, the duration varaiable should only be included for benchmark purposes and should be discarded if the intention is to have a realistic predictive model.

## Tableau Dashboard
The dashboard below was created using tableau.

![tableau dashboard](https://github.com/vaadewoyin/Bank-term-deposit-subscription-prediction/blob/main/Dashboard%201.png)
