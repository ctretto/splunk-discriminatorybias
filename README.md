# splunk-discriminatorybias
Example dashboard to look for discriminatory bias in your data

This dashboard was demoed during the talk "Discriminatory Algorithms and Biased Data: Is the Future of Machine Learning Doomed?" as a proof of concept tool to detect bias in datasets, and labels predicted by a classification model.

Note that this dashboard works on binary classification problems.
A binary classification problem is when your true labels and model predictions have only two values. You need to convert your labels into a 1 or a 0 (e.g. | eval true_labels = if("Successfullpurchase", 1, 0))

Where to find bias:
the tables on the left show the % difference of the calculated metrics by group. In a perfectly unbiased dataset, metrics are the same for each category and all the values are 0.
A metric is biased if the percentage difference from the reference group exceeds a defined threshold. The threshold is defined by the data analyst or internal/external regulations or practices.


Fields:

Source Type:
specifies datasource fields
can include transformations on the data that will be used
(e.g. sourcetype = "data.csv" | apply("ClassificationModel") | eval true_labels = if("Successfullpurchase", 1, 0))

Audited Field:
Select field of the sensitive attribute. Must be categorical, such as race, sex, age group.

Reference Group:
Bias is a relative measure, so you need to specify a reference group.

True Labels
Specify field name for the "true category" (accepted values are 1 or 0)

Prediction Field
Specify field name for the model output (accepted values are 1 or 0)


Dashboard contents:

Representation by Category / Representation Bias
By representation we mean whether a dataset is balanced or not. A perfectly balanced dataset will have the same number of datapoints for each category of the audited field.

True Labels by Category / True Labels Bias
Shows the difference in the true classification between groups


Confusion Matrix
Shows the distribution of TP, FP, TN, FN for the entirety of the model

Confusion Matrix by Category
Shows the distribution of TP, FP, TN, FN for each category of the audited field of the model

Precision Rate by Category

Recall Rate by Category
