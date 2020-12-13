# Policyholder payment call propensity score
## A Marketing Email Campaign

<p>
    <img src=https://storage.googleapis.com/sales.appinst.io/2016/07/8-Strategies-You-Can-Learn-From-These-Great-Email-Campaigns-2.png alt>
</p>
<p>
    <em>Chris Meier from <a href=https://appinstitute.com/great-email-campaigns>AppInstitute</a> </em>
</p>

## Objective

- Design a model to detect if a policyholder will make a service payment call.
- Send marketing material to entice the policyholder to use our self-service payment channels.

<p>
    <img src=images/Objective_steps.PNG alt>
</p>

## About the Data

<b>Policyholder Payment</b>: The data contains customers who had a bill due in the next 5 days and whether they made a service payment call. The transactions dates range between May 13 2014 – May 20 2014, with a total of 129,277 payments.
<table>
  <tr>
    <th>Type</th>
    <th>Count</th>
  </tr>
  <tr>
    <td>Boolean</td>
    <td>6</td>
  </tr>
  <tr>
    <td>Datetime</td>
    <td>1</td>
  </tr>
  <tr>
    <td>Numeric</td>
    <td>18</td>
  </tr>
  <tr>
    <td>Test</td>
    <td>4</td>
  </tr>
   <tr>
    <td>Grand Total</td>
    <td>29</td>
  </tr>
</table>

Note: Any inference that we uncover from this data will not represent the population of policyholder payments. There can be seasonality impacts in terms of service payment calls, that would change the distribution and underlying behaviors.

## Data Visulaization

### Feature Correlation
<p>
    <img src=images/corr_heatmap.png alt>
</p>

The payments made through an self service channel (SSC) within 6 months and within 3 months are highly correlated. (3m is a subset of 6m). Additionally, the feature EVENT1_30_FLAG as no color gradient. After further examination the feature has zero variance.

Attempted to remove the intersection between these features but the correlation persists (see below).
<p>
    <img src=images/corr_heatmap2.png alt>
</p>

### Age x Tenure
- There are no distinguishable clusters (Age x Tenure) that make more service payment calls.
- We would expect that older policy holders will make more service payment calls, but the chart also shows young adults (<=30) are still calling in. 

<p>
    <img src=images/ageXtenure.png alt>
</p>

<table>
  <tr>
    <th><img src=images/policy_age.png alt></th>
    <th><img src=images/CancelationXage.png alt></th>
  </tr>
</table>

### Service Calls- Class Imblanace

There is significant class imbalance in the dataset that we will neeed to deal with when creating the predictive model(s).

<table>
  <tr>
    <th><img src=images/service_call_plt.png style="width:50px;"></th>
    <th><img src=images/days_of_week.png alt></th>
  </tr>
</table>

### Prediction Summary (baseline) - Heldout test set
<table>
  <tr>
    <th>Model</th>
    <th>Balanced Class</th>
    <th>Cross Val</th>
    <th>Feature Engineering</th>
    <th>Roc AUC</th>
    <th>Precision-Recall AUC</th>
    <th>Runtime</th>
  </tr>
  <tr>
    <td>Logistic Regression(baseline)</td>
    <td align='center'>&#10003</td>
    <td></td>
    <td></td>
    <td>0.78</td>
    <td>0.12</td>
    <td>Neglibile</td>
  </tr>
</table>

<table>
  <tr>
    <th><img src=images/Baseline_Logistic_ROC.png></th>
    <th><img src=images/Baseline_Logistic_PR.png></th>
  </tr>
</table>

The ROC curve is an optimistic measure for an imbalanced class. Recall that the ROC curve measures how well the model is distinguishing both classes, but due to the over representation of the negative class, the FPT will be low for many thresholds while the TPR increases steadily. This type of measure does not tell us how well we are doing at correctly classifyin the treu cases.

The Precision-Recall curve demonstrates how well our model is doing at correctly detecting positive sample on two front:

- % of predicted (+) classes that are correct
- % of (+) cases that we correctly identify

### Modeling Pipeline

Can we do better?

A modeling pipeline is developed to enhance model performance. The pipeline will create multiple models for a set of hyperparameters and then choose the best performing combination of parameters on the validation set.

![](https://github.com/StevenLoaiza/Marketing/blob/main/images/Model%20Pipelne.png?raw=true)

### Feature Engineering
Below is a list with explanations of each new feature.

- Cancel_autom: Cancelled automated payments (0/1)
- SSP_6M: Percent of self-service payment in the last 6 months.
- SSP_15d: Was the payment made in the last 15 days potentially made through SSC? (0/1)
- Day : Day of the week for the transaction.
- State_grp : Rated State grouped by (ascending) Call_Flag=1.

### Final model outputs
<table>
  <tr>
    <th>Model</th>
    <th>Balanced Class</th>
    <th>Cross Val</th>
    <th>Feature Engineering</th>
    <th>Roc AUC</th>
    <th>Precision-Recall AUC</th>
    <th>Runtime</th>
  </tr>
  <tr>
    <td>Logistic Regression(baseline)</td>
    <td align='center'>&#10003</td>
    <td></td>
    <td></td>
    <td>0.78</td>
    <td>0.12</td>
    <td>Neglibile</td>
  </tr>
    <tr>
    <td>Logistic Regression v2</td>
    <td align='center'>&#10003</td>
    <td align='center'>&#10003</td>
    <td align='center'>&#10003</td>
    <td>0.83</td>
    <td>0.23</td>
    <td>18.3 min</td>
  </tr>
    <tr>
    <td>Random Forest</td>
    <td align='center'>&#10003</td>
    <td align='center'>&#10003</td>
    <td align='center'>&#10003</td>
    <td>0.84</td>
    <td>0.24</td>
    <td>46.5 min</td>
  </tr>
    <tr>
    <td>XgBoost</td>
    <td align='center'>&#10003</td>
    <td align='center'>&#10003</td>
    <td align='center'>&#10003</td>
    <td>0.87</td>
    <td>0.27</td>
    <td>4.9 min</td>
  </tr>
</table>

The hyper parameters tuned in the XGboost model (Optimal Performance and quickest runtime) are Number of Subsamples, Max features, depth, class positive weight.

<table>
  <tr>
    <th><img src=images/XGboost_ROC.png></th>
    <th><img src=images/XGboost_PR.png></th>
  </tr>
</table>

## Feature Importance- SHAPE Vaules

**Interpreting the plot**
- The feature are in descending order of importance.
- The distance from the center line show the pull toward lower (left) or higher (right) propensity to call.
- The gradient colors represent the feature value {blue: lower, magenta: higher}

Note: SHAP quantifies the contribution that each feature bring to the prediction made by the model.

Pros:
Shows the direction of influence
More info than standard importance plots

Cons:
Computation time increase with the number of coalitions (number of combinations).
Local interpretation only for the training dataset.
Does note tell us a marginal change in the global sense, like a parameter value in a linear regression.

![](https://github.com/StevenLoaiza/Marketing/blob/main/images/SHAP_values.png?raw=true)

## Model Deployment

Scoring Threshold Is pending – will need to discuss with the business to determine a meaningful threshold keeping in mind the tradeoff between precision and recall. 

There are two objectives we would like to monitor:
- Is the model performing well
- Is the campaign performing well

Each objective has opposing wants, if the campaign works well then we cant validate the model because everyone would pay online.

Instead we can randomly assign policy holders to two groups, control group and experiment group.

Control group: receive no email, can be used to monitor model performance
Experiment Group: The will receive the email to pay on SSC.

![](https://github.com/StevenLoaiza/Marketing/blob/main/images/model_deploy.PNG?raw=true)

## Next Steps
- Model v2. (Gather a larger history of payment data)
- Present the model to our business stakeholders and determine an acceptable threshold (trade-off  precision and recall)
- Discuss the format for the return scores (binary variable, return the model score, segmentation (No, Low, Med, High))

## Appendix

### Choose Threshold
Interpreting the chart
- At each threshold we calculate the precision and recall for the positive class.
- There will be a tradeoff, as we increase/decrease the threshold.

![](https://github.com/StevenLoaiza/Marketing/blob/main/images/threshold_chrt.PNG?raw=true)
