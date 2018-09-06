# splunk-discriminatorybias
Example dashboard to look for discriminatory bias in your data

Queries in the dashboard:

This baseSearch query will calculate the confusion matrix for the model result
id="baseSearch"
    <query>$source_tok$
    | eval true_positive = if($predicted_tok$ = 1 and $true_labels_tok$ = 1, 1, 0)
    | eval false_positive = if($predicted_tok$ = 1 and $true_labels_tok$ = 0, 1, 0)
    | eval true_negative = if($predicted_tok$ = 0 and $true_labels_tok$ = 0, 1, 0)
    | eval false_negative = if($predicted_tok$ = 0 and $true_labels_tok$ = 1, 1, 0)
    | stats count, sum($true_labels_tok$) as true_labels, sum(true_positive) as true_positives, sum(false_positive) as false_positives, sum(true_negative) as true_negatives, sum(false_negative) as false_negatives
    | eval true_positives_perc = round(true_positives / count * 100, 2)
    | eval true_negatives_perc = round(true_negatives / count * 100, 2)
    | eval false_positives_perc = round(false_positives / count * 100, 2)
    | eval false_negatives_perc = round(false_negatives / count * 100, 2)
    | fields *
    </query>

This baseSearchByAuditedField query will calculate the confusion matrix for the model result grouped by sensititve attribute
  <search id="baseSearchByAuditedField">
    <query>$source_tok$
    | eval true_positive = if($predicted_tok$ = 1 and $true_labels_tok$ = 1, 1, 0)
    | eval false_positive = if($predicted_tok$ = 1 and $true_labels_tok$ = 0, 1, 0)
    | eval true_negative = if($predicted_tok$ = 0 and $true_labels_tok$ = 0, 1, 0)
    | eval false_negative = if($predicted_tok$ = 0 and $true_labels_tok$ = 1, 1, 0)
    | stats count, sum($true_labels_tok$) as true_labels, sum(true_positive) as true_positives, sum(false_positive) as false_positives, sum(true_negative) as true_negatives, sum(false_negative) as false_negatives by $audit_tok$
    | join reference [search $source_tok$
      |  where $audit_tok$=$reference_tok$
      |  eval true_positive_r = if($predicted_tok$ = 1 and $true_labels_tok$ = 1, 1, 0)  |  eval false_positive_r = if($predicted_tok$ = 1 and $true_labels_tok$ = 0, 1, 0)
      |  eval true_negative_r = if($predicted_tok$ = 0 and $true_labels_tok$ = 0, 1, 0)  |  eval false_negative_r = if($predicted_tok$ = 0 and $true_labels_tok$ = 1, 1, 0)
      |  stats count as count_r, sum($true_labels_tok$) as true_labels_r, sum(true_positive_r) as true_positives_r, sum(false_positive_r) as false_positives_r, sum(true_negative_r) as true_negatives_r, sum(false_negative_r) as false_negatives_r by $audit_tok$
      |  stats sum(count_r) as count_ref, sum(true_labels_r) as true_labels_ref, sum(true_positives_r) as true_positives_ref, sum(true_negatives_r) as true_negatives_ref, sum(false_positives_r) as false_positives_ref, sum(false_negatives_r) as false_negatives_ref]
    |  eval true_positives_perc = round(true_positives / count * 100, 2)
    |  eval false_positives_perc = round(false_positives / count * 100, 2)
    |  eval true_negatives_perc = round(true_negatives / count  * 100, 2)
    |  eval false_negatives_perc = round(false_negatives / count  * 100, 2)
    |  eval true_positives_perc_ref = round(true_positives_ref / count_ref * 100, 2)
    |  eval false_positives_perc_ref = round(false_positives_ref / count_ref * 100, 2)
    |  eval true_negatives_perc_ref = round(true_negatives_ref / count_ref  * 100, 2)
    |  eval false_negatives_perc_ref = round(false_negatives_ref / count_ref  * 100, 2)
    | fields * </query>


Count of data points for each category
        <search base="baseSearchByAuditedField">
          <query>search
          |  stats sum(count) as count by $audit_tok$
          |  sort -count
          |  rename count as "Total by Category"
          |  table $audit_tok$, "Total by Category"
          </query>
