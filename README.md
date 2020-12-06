# Marketing Email Campaign

<p>
    <img src=https://storage.googleapis.com/sales.appinst.io/2016/07/8-Strategies-You-Can-Learn-From-These-Great-Email-Campaigns-2.png alt>
</p>
<p>
    <em>Chris Meier from <a href=https://appinstitute.com/great-email-campaigns>AppInstitute</a> </em>
</p>

## Objective

- Design a model to detect if a policy holder will make a service payment call.
- Send marketing material to entice the policy holder to use our self service payment channels.

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
