# splunk-discriminatorybias

Use this example dashboard to identify discriminatory bias in your data.

This dashboard was demoed at Splunk .conf18 during the talk "Discriminatory Algorithms and Biased Data: Is the Future of Machine Learning Doomed?" as a proof-of-concept tool to detect bias in datasets and labels predicted by a classification model.

The tool identifies bias in datasets (representation bias, true outcomes bias) and in models (precision bias, recall bias).

## Data format expected by the dashboard

This dashboard only works on binary classification problems. A binary classification problem is when there are only two values for the real outcomes and the predicted outcomes. You must convert labels into a 1 or a 0 to use this dashboard. For example, | eval outcomes = if("SuccessfulPurchase", 1, 0)

Your data must contain a sensitive attribute that you want to identify whether or not the dataset contains bias against, as well as a reference group to compare the bias against (as bias is relative). For example, if you want to determine if your model is biased against protected age groups, you might use a reference group of "Middle Aged" and an audited field of "age_group".

### Identifying bias

The tables on the left show the percentage difference of the calculated metrics by group. In a perfectly unbiased dataset, metrics are the same for each category and all the values are 0.

A metric is biased if the percentage difference from the reference group exceeds a defined threshold. A data analyst or internal/external regulations or practices can define the threshold.

## Dashboard reference

The dashboard contains filters to specify the data you want to analyze, as well as panels that display the results of the bias analysis.

### Dashboard filter reference

Use the dashboard filters to identify your dataset and the fields you want to examine.

| Filter | Description | Example |
| -------- | --------- | -------- |
| Data Source | Search to specify the dataset to use in the dashboard. Can include transformations on the data. | sourcetype = "data.csv" \| apply("ClassificationModel") \| eval true_outcomes = if("SuccessfulPurchase", 1, 0) |
| Audited Field | Field that represents the sensitive attribute that identifies bias. Must be categorical, such as race, gender, or age group. | age_range |
| Reference Group | Field that specifies a group to reference, because bias is a relative measure. | Middle Aged |
| Real Outcomes | Field name for the real outcomes, to identify what is true in reality. Value of the field must be 1 or 0. | true_outcomes |
| Predicted Outcomes | Field name for the model output. Value of the field must be 1 or 0. | predicted_outcome |

### Dashboard panel reference

Use the dashboard panels to identify possible types of bias.

Representation refers to whether a dataset is balanced or not. If a dataset is representative, or balanced, it has the same number of data points for each category of the audited field.

True Labels refers to the true classification between groups.

| Dashboard panel name | Description |
| ------ | ------ |
| Representation by Category | Count of data points for each category. If these are unequal, representation bias might exist. |
| Representation Bias | Percentage difference of representation for each category in the audited field, relative to the reference group. |
| Real Outcomes by Category | Percentage of real outcomes for each category in the audited field. |
| Real Outcomes Bias | Percentage difference of real outcomes for each category in the audited field, relative to the reference group. |
| Confusion Matrix | Shows the distribution of true positives (TP), false positives (FP), true negatives (TN), and false negatives (FN) when applying a classification model to the dataset. |
| Confusion Matrix by Category | Shows the distribution of TP, FP, TN, and FN for each category of the audited field. |
| Precision Rate by Category | Shows how often the model is correct in predicting a positive outcome (ratio of true positives vs false positives), for each category of the audited field. |
| Precision Bias | Shows the percentage difference of precision for each category in the audited field, relative to the reference group. |
| Recall Rate by Category | Shows how often the model is missing a positive real outcome (ratio of true positives vs false negatives), for each category in the audited field. |
| Recall Bias | Shows how often the recall rate is correct for each category in the audited field, relative to the reference group. |
