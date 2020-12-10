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
