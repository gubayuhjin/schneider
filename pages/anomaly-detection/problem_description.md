# Problem description

The overall objective is to provide methods for automatically detecting abnormal energy consumption data in buildings, as well as potential savings opportunities with all relevant details. Beyond detecting overconsumption, it is important to provide correct interpretation of overconsumption and, when possible, provide actionable recommendations.

<div class="container">
	<div class="row">
		<div class="col-xs-3">
			<ul style="list-style: none">
				<li><strong>The Data</strong></li>
				<li><a href="#features_list">Overview</a></li>
				<li><a href="#datasets">Datasets</a></li>
				<li><a href="#external">External data</a></li>
			</ul>
		</div>
		<div class="col-xs-3">
			<ul style="list-style: none">
				<li><strong>Evaluation</strong></li>
				<li><a href="#metric">Assessment</a></li>
				<li><a href="#format">Format</a></li>
			</ul>
		</div>
	</div>
</div>

<a id="features_list"></a>

## Overview

-----

A few data sets, corresponding to different types of building sites from different geographies, will be provided. Some building sites will correspond to a unique main activity (office) while some will be mixed (office, research, cafeteria). The goal is to cover various situations and test solutions in differing conditions.

Data sets will include global energy consumption in Watt-hours (Wh), at intervals of 10, 15, or 30 minutes (depending on local utility standards). Some will also optionally include sub-metering information along with some usage data (daily building occupancy, schedule, weather). Data sets may include missing and incorrect values.

Your goal is to identify, without access to training labels, anomalies in the dataset.


<a id="datasets"></a>

## Datasets

### Consumption Values

A selected time series of consumption data for selected meters within 3 different sites.

 * `obs_id` - An arbitrary ID for an observation
 * `meter_id` - An arbitrary ID number for a meter in a building (site). A building can have multiple meters. This id matches across datasets
 * `Timestamp` - The time of the measurement
 * `Values` - A measure of consumption for that meter

### Building Metadata

Additional information about the included buildings.

 * `meter_id` - An arbitrary meter ID. Allows matching of datasets indexed with the `SiteId` (e.g., holidays, weather) with the `meter_id` in the Consumption Values.
 * `site_id` - An arbitrary ID number for the building, matches across datasets. (Note: These Site IDs are randomly generated per competition, and they do not match sites for either the forecasting or optimization challenges.)
 * `meter_description` - A description of the environment/context of the meter
 * `units` - The units of the meter measurement
 * `surface` - The surface area of the building (site)
 * `activity` - Metadata about the kind of building

### Historical Weather Data

This dataset contains temperature data from several stations near some of the provided sites. Not all sites appear in the weather dataset. For each site several temperature measurements were retrieved from stations in a radius of 30 km.

 * `row_id` - An arbitrary id for each row
 * `site_id` - An arbitrary ID number for the building, matches across datasets
 * `Timestamp` - The time of the measurement
 * `Temperature` - The temperature as measured at the weather station
 * `Distance` - The distance in km from the weather station to the building in km

### Public Holidays

Public holidays at _some_ of the sites included in the dataset, which may be helpful for identifying days where consumption may be lower than expected.

 * `row_id` - An arbitrary id for each row
 * `site_id` - An arbitrary ID number for the building, matches across datasets
 * `Date` - The date of the holiday
 * `Holiday` - The name of the holiday

<a id="external"></a>

## External data

For this competition, use of external data is prohibited, with the sole exception of pre-trained Neural Networks that are included on the External Data thread in the forum.


<a id="metric"></a>

## Assessment

-----

There are two "tracks" for this competition. The first is an algorithm for anomaly detection that approaches the behavior of expert-system-detected and hand-labeled anomalies from Schneider Electric. A fraction of the labels will be used for a public leaderboard, and other labels for a private leaderboard. The second is a submitted report. These reports will suggest anomaly detection methodology and outline classes of anomalies identified by the algorithms. The reports will be reviewed by an expert judging panel. The algorithm submissions will be scored using a weighted combination of precision and recall:

$$
WPR = \frac{1}{5} \bigg(\frac{T_p}{T_p + F_n} \bigg) + \frac{4}{5} \bigg(\frac{T_p}{T_p + F_p} \bigg)
$$

 * |$T_p$| - is the number of true positives. That is, the number of anomalies in the labeled set that also appear in the predictions.
 * |$F_n$| - is the number of false negatives. That is, the number of anomalies in the labeled set that do not appear in the predictions.
 * |$F_p$| - is the number of false positives. That is, the number of anomalies that are predicted, but are not anomalies in the labeled set.

If either denominator evaluates to zero, a score of 0 (rather than a NaN) will be returned. WPR will be calculated for each building site and the final score will be the average of the per-building `WPR` score.


## Submission format

-----

Submitted reports must be no more than 7 pages and contain information on the methods and kinds of anomalies detected. See the [Submit Report](https://www.drivendata.org/competitions/52/submissions/extra/) page for more details.

Submitted predictions are a submission file has the following columns:

 * `obs_id` - a random id for the observation
 * `meter_id` - the ID of the meter
 * `Timestamp` - the time of the observation
 * `is_abnormal` - Should be `True` or `False` based on if the value is abnormal (an anomaly) or not

We only are evaluating `is_abnormal` against a subset of the `meter_id`s that are in the consumption dataset. Competitors can use the entire consumption dataset to create their algorithms, but should only submit the meters and times that appear in the submission format.

A portion of the submitted predictions will be used to compute a public leaderboard score against a set of expert-system-detected and hand-labeled anomalies. The remaining submission will be used to compute a private score (to avoid overfitting).

<a id="sub_values"></a>

<div class="well">

For example, if you predicted...

<table class="table">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>meter_id</th>
      <th>Timestamp</th>
      <th>is_abnormal</th>
    </tr>
    <tr>
      <th>obs_id</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>171227</th>
      <td>234_203</td>
      <td>2013-11-03 19:00:00</td>
      <td>False</td>
    </tr>
    <tr>
      <th>177020</th>
      <td>234_203</td>
      <td>2013-11-03 19:30:00</td>
      <td>False</td>
    </tr>
    <tr>
      <th>165375</th>
      <td>234_203</td>
      <td>2013-11-03 20:00:00</td>
      <td>False</td>
    </tr>
    <tr>
      <th>207176</th>
      <td>234_203</td>
      <td>2013-11-03 20:30:00</td>
      <td>False</td>
    </tr>
    <tr>
      <th>353253</th>
      <td>234_203</td>
      <td>2013-11-03 21:00:00</td>
      <td>False</td>
    </tr>
  </tbody>
</table>

</div>

Your `.csv` file that you submit would look like:

	obs_id,meter_id,Timestamp,is_abnormal
	171227,234_203,2013-11-03 19:00:00,False
	177020,234_203,2013-11-03 19:30:00,False
	165375,234_203,2013-11-03 20:00:00,False
	207176,234_203,2013-11-03 20:30:00,False
	353253,234_203,2013-11-03 21:00:00,False


## Good luck!

--------

Good luck and enjoy this problem! If you have any questions you can always visit the [user forum](http://community.drivendata.org/)!
