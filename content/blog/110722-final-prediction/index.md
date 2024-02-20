---
title: "Final Prediction"
author: "Vivian Nguyen"
date: '2022-11-07'
categories: 
  - Election Analytics
tags: ["national model", "final model", "district models"]
---

# Overview

Welcome to the final election forecasting update of this election cycle! It’s been a long journey of election research and data analysis, but alas, we are one day out from Election Day. In today’s blog post, I am going to present my final predictions in detail, which will involve model formulas, model descriptions, model coefficients, model interpretations, model validation, prediction confidence intervals, and visualizations for my national and district-level models.

<div class="figure">

<img src="election.png" alt="Source: The Economist." width="95%" />
<p class="caption">
Figure 1: Source: The Economist.
</p>

</div>

# National Model

Starting in [week 2](https://vivian-1372.github.io/Election-Analytics/post/2022-09-19-local-and-national-economy/), I wanted to build out a good national model with a mix of fundamental variables to predict the national two-party vote shares of the parties. I ultimately chose to stick with fundamentals because in [weeks 5](https://vivian-1372.github.io/Election-Analytics/post/2022-10-10-the-air-war/) and [6](https://vivian-1372.github.io/Election-Analytics/post/2022-10-19-the-ground-game/), I found that campaign activity, whether it be on air or on the ground, didn’t significantly or directly impact electoral outcomes. In [week 7](https://vivian-1372.github.io/Election-Analytics/post/2022-10-24-shocks/), I reached the same conclusion regarding shocks during the election cycle.

The best iteration of my model is the most recent one, which includes only 4 predictors: absolute GDP growth (Quarter 8, the last of the election cycle), the average generic ballot support of the House incumbent party (weighted by recency of the poll), an indicator for whether or not the House incumbent party is also the president’s party during a midterm year, and the average congressional approval rate. Absolute GDP growth is the economic variable I chose to capture how voters reward and punish the incumbent House party, and it came about after multiple considerations of other economic variables like unemployment rate, GDP growth percentage, and RDI change percentage. Average generic ballot support of the party in control of the House, introduced in [week 3](https://vivian-1372.github.io/Election-Analytics/post/2022-09-26-polls-and-pollsters/), reflects how much the nation wants the currently-leading party to retain that congressional power. The binary indicator for `President's-Party-House-Incumbent-Midterm` is my attempt to capture the well-known fact that the president’s party consistently performs poorly in midterm elections, which I covered in [week 1](https://vivian-1372.github.io/Election-Analytics/post/2022-09-15-analzying-2020-house-vote-shares/). The indicator is equal to 1 if the party in control of the House happens to be the president’s party in a midterm year, and to 0 otherwise. Lastly, the average congressional approval rate was included because I believe it gives us a sense of how satisfied voters are with Congress, generally. This is distinct from the generic ballot support metric, with the former telling us how much voters like Congress’s performance, which (perhaps unintuitively) doesn’t match 1-to-1 with how much voters want the incumbent party to stay in power.

<div class="figure">

<img src="CA_GB.png" alt="Disconnect between Congressional Approval and Incumbent Generic Ballot." width="75%" />
<p class="caption">
Figure 2: Disconnect between Congressional Approval and Incumbent Generic Ballot.
</p>

</div>

As shown above, generic ballot support for the incumbent party in the House doesn’t move with general approval of Congress! This was surprising to me at first, but was a good sign that I wasn’t introducing too much collinearity into my model by including both measures.

## Model Details

### Vote Share Formula

I wanted to use this model to predict both national incumbent two-party vote share and national incumbent seat count, so I came up with two final regression formulas that incorporated the aforementioned fundamental predictors.

<figure>
<img src="nat_VS_formula.png" alt="National Democratic Two-Party Vote Share Regression Formula." />
<figcaption aria-hidden="true">National Democratic Two-Party Vote Share Regression Formula.</figcaption>
</figure>

### Vote Share Regression

In modeling the incumbent party’s two-party vote share, which is the Democrat’s vote share this election, we get some interesting coefficients. See below for this model’s coefficient values, variable significance, and the model’s overall evaluation metrics (like R-squared and adjusted R-squared).

<div class="figure">

<img src="nat_VS_reg.png" alt="National Democratic Two-Party Vote Share Regression Output." width="75%" />
<p class="caption">
Figure 3: National Democratic Two-Party Vote Share Regression Output.
</p>

</div>

The small, but negative, coefficient attached to absolute GDP is rather unintuitive, but my hypothesis is that for GDP, voters respond to levels and relative change differently. Previous weeks’ work has shown that positive percentage change is associated with higher incumbent vote share, as we’d expect, but positive absolute change is negatively correlated with incumbent party vote share perhaps because voters slightly punish incumbents when they deem the absolute change is not “large enough.” For every additional billion dollars of GDP change between the 7th and 8th quarters, the incumbent party loses 0.014 percentage points.

The generic ballot support variable has a coefficient of 0.472, which makes sense - if voters generally support the incumbent party in polls, the party can expect to do well on Election Day. For every percentage point higher the generic ballot support is for the incumbent party in polls, the incumbent party earns an additional 0.472 percentage point of vote share.

The only variable with a larger coefficient than generic ballot support is the president’s-party-incumbent-midterm variable. Midterm years in which the incumbent party is the president’s party see the incumbent party losing 2.788 percentage points of vote share. This is consistent with the decades of poor president’s party House performance we’ve observed.

Lastly, with every 1 percentage point increase in congressional approval, the incumbent party gains 0.092 percentage points in vote share. Again, this makes sense, as more voter satisfaction with the current Congress should be correlated with higher electoral support for the House incumbent party to remain in power.

I would like to quickly note my model’s final adjusted R-squared, 0.872, which is a nice improvement from [week 2’s](https://vivian-1372.github.io/Election-Analytics/post/2022-09-19-local-and-national-economy/) 0.292. This value indicates that the model, with its 4 predictors, can explain about 87.2% of the variance in incumbent party vote share.

### Vote Share Validation

<div class="figure">

<img src="nat_VS_val.png" alt="National Democratic Two-Party Vote Share Model Validation." width="344" />
<p class="caption">
(#fig:vote share validation printout)National Democratic Two-Party Vote Share Model Validation.
</p>

</div>

Above is the histogram of the mean out-of-sample residuals over 1,000 runs of cross-validation for my model. The distribution looks roughly normally distributed, with a mean a little below 0, tiny tails, and most residuals within \[-2, 2\].

### Seat Count Formula

Next, I ran the same model again, but this time with incumbent party seat count as the response variable. Below are the regression formula and regression output for this model.
![National Democratic Party Seat Count Regression Formula.](nat_S_formula.png)

### Seat Count Regression

<div class="figure">

<img src="nat_S_reg.png" alt="National Democratic Seat Count Regression Output." width="75%" />
<p class="caption">
Figure 4: National Democratic Seat Count Regression Output.
</p>

</div>

The model coefficients of the seat count model have the same signs as those of the vote share model, but different magnitudes because of the switch from predicting vote share, which can only be in the interval \[0, 100\], to predicting seat count, which can be \[0, 435\]. It is really the sign of the coefficients that matter for interpretation, and since those haven’t changed, I will only briefly comment on this regression output.

It’s helpful to see the relationship between the president’s-party-incumbent-midterm effect and *seat count* because now, it becomes clear that when the president’s party is trying to defend its House incumbency in midterm years, like this year, it faces a steep uphill battle - when the indicator variable is 1, and everything else is held constant, the incumbent party is predicted to lose around 29 seats.

### Seat Count Validation

<div class="figure">

<img src="nat_S_val.png" alt="National Democratic Party Seat Count Model Validation." width="75%" />
<p class="caption">
(#fig:seat count validation printout)National Democratic Party Seat Count Model Validation.
</p>

</div>

Above is the histogram of the mean out-of-sample residuals over 1,000 runs of cross-validation for my model again, this time for prediction of incumbent seat count. The distribution looks roughly normally distributed, with a mean at around 0 and most residuals within \[-20, 20\]. Neither our vote share nor our seat count model seem to perform poorly when tested with out-of-sample data, so we may move forward with prediction now!

## National Predictions

Using the two models above, and the newest data for the 2022 midterms, I predict that the House incumbent (Democratic Party) two-party vote share will be **48.56%** (47.36, 49.83) and the seat count will be **209** (198, 221).

The GDP data comes from [FRED](https://fred.stlouisfed.org/series/GDPC1), the generic ballot data comes from [FiveThirtyEight](https://projects.fivethirtyeight.com/polls/generic-ballot/), and the congressional approval numbers come from [Gallup](https://news.gallup.com/poll/1600/congress-public.aspx).

<div class="figure">

<img src="nat_model_viz.png" alt="435 Seats Up For Grabs." width="398" />
<p class="caption">
Figure 5: 435 Seats Up For Grabs.
</p>

</div>

# District Models

Since [week 4](https://vivian-1372.github.io/Election-Analytics/post/2022-10-03-incumbency/), I have been slowly working towards building 435 models for the 435 district races. After much consideration, I decided to build two types of models: (1) the competitive type, which involves the average of many expert predictions and incumbency, and (2) the non-competitive type, which involves past election results and incumbency.

Most expert ratings were pulled from Wikipedia’s election ratings pages for each election (see this year’s page [here](https://en.wikipedia.org/wiki/2022_United_States_House_of_Representatives_election_ratings)), and districts are determined to be competitive if one of the major rating groups considered them not “safe” or “solid”.

## Model Details

The main difference in variable selection between the competitive and non-competitive models boils down to the fact that naturally, there is a lot more coverage of the competitive ones, meaning more expert rating availability. Because of this, the competitive district models feature an average of about 7 expert ratings, whereas the non-competitive ones feature none.

For the competitive districts, I used the following regression formula:
![Competitive District Model Formula.](dist_formula.png)
These models ended up with an average R-squared of 0.88 and average adjusted R-squared of 0.49.

Developing models for the non-competitive models was a journey - over the weeks, I realized that good district-level data is hard to come by, and with Election Day drawing near, I felt there was a choice between building out a self-made model and building a highly predictive one to be made. In the end, I decided to go with the former, in the name of learning. The models then are fundamentals-based than the competitive ones, using the major vote share of the last Democrat candidate in addition to the incumbency of the current candidate.

<figure>
<img src="dist_NC_formula.png" alt="Non-competitive District Model Formula." />
<figcaption aria-hidden="true">Non-competitive District Model Formula.</figcaption>
</figure>

I chose to use these variables because House elections have historically favored incumbent candidates, and I believe that how the Democrat performed in each district in previous years can tell us a lot about how the Democratic candidate will perform this year.

![Coefficients for Incumbent Variable.](dist_incumb_coef.png)
The histogram above illustrates the coefficient values for the incumbent variable over the many non-competitive districts. The average coefficient value is a little under 16, and it indicates that on average, when a Democratic candidate is an incumbent, their vote share is expected to be about 16 points higher than had they not been an incumbent. This definitely tracks with what we know about the incumbency advantage for House members.

![Coefficients for Last Democrat Vote Share Variable.](dist_VS_coef.png)
This histogram shows the coefficient values for the last Democrat’s vote share (in the previous election), and the mean is value is 0.36. The numerical interpretation of this one is a little confusing, but the main takeaway is that the vast majority of these coefficients are positive, indicating that the higher the last Democrat’s vote share, the higher we can expect this year’s Democratic vote share to be.

These models have an average R-squared of 0.50 and average adjusted R-squared of 0.45.

## District Predictions

Below are my predictions for the 94 most competitive districts, using expert ratings and incumbency:

<div id="aewqqfhknk" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}
&#10;#aewqqfhknk .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}
&#10;#aewqqfhknk .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}
&#10;#aewqqfhknk .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}
&#10;#aewqqfhknk .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}
&#10;#aewqqfhknk .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}
&#10;#aewqqfhknk .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#aewqqfhknk .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}
&#10;#aewqqfhknk .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}
&#10;#aewqqfhknk .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}
&#10;#aewqqfhknk .gt_column_spanner_outer:first-child {
  padding-left: 0;
}
&#10;#aewqqfhknk .gt_column_spanner_outer:last-child {
  padding-right: 0;
}
&#10;#aewqqfhknk .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}
&#10;#aewqqfhknk .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  text-align: left;
}
&#10;#aewqqfhknk .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}
&#10;#aewqqfhknk .gt_from_md > :first-child {
  margin-top: 0;
}
&#10;#aewqqfhknk .gt_from_md > :last-child {
  margin-bottom: 0;
}
&#10;#aewqqfhknk .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}
&#10;#aewqqfhknk .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#aewqqfhknk .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}
&#10;#aewqqfhknk .gt_row_group_first td {
  border-top-width: 2px;
}
&#10;#aewqqfhknk .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#aewqqfhknk .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}
&#10;#aewqqfhknk .gt_first_summary_row.thick {
  border-top-width: 2px;
}
&#10;#aewqqfhknk .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#aewqqfhknk .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#aewqqfhknk .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}
&#10;#aewqqfhknk .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}
&#10;#aewqqfhknk .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#aewqqfhknk .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}
&#10;#aewqqfhknk .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#aewqqfhknk .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}
&#10;#aewqqfhknk .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#aewqqfhknk .gt_left {
  text-align: left;
}
&#10;#aewqqfhknk .gt_center {
  text-align: center;
}
&#10;#aewqqfhknk .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}
&#10;#aewqqfhknk .gt_font_normal {
  font-weight: normal;
}
&#10;#aewqqfhknk .gt_font_bold {
  font-weight: bold;
}
&#10;#aewqqfhknk .gt_font_italic {
  font-style: italic;
}
&#10;#aewqqfhknk .gt_super {
  font-size: 65%;
}
&#10;#aewqqfhknk .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}
&#10;#aewqqfhknk .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}
&#10;#aewqqfhknk .gt_indent_1 {
  text-indent: 5px;
}
&#10;#aewqqfhknk .gt_indent_2 {
  text-indent: 10px;
}
&#10;#aewqqfhknk .gt_indent_3 {
  text-indent: 15px;
}
&#10;#aewqqfhknk .gt_indent_4 {
  text-indent: 20px;
}
&#10;#aewqqfhknk .gt_indent_5 {
  text-indent: 25px;
}
</style>
<table class="gt_table">
  &#10;  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col" id="state">state</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="district">district</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="fitted">fitted</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="lower">lower</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="upper">upper</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="winner">winner</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td headers="state" class="gt_row gt_left">Alaska</td>
<td headers="district" class="gt_row gt_center"></td>
<td headers="fitted" class="gt_row gt_center">48.03</td>
<td headers="lower" class="gt_row gt_center">39.87</td>
<td headers="upper" class="gt_row gt_center">56.19</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Arizona</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">50.69</td>
<td headers="lower" class="gt_row gt_center">48.53</td>
<td headers="upper" class="gt_row gt_center">52.85</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Arizona</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">48.63</td>
<td headers="lower" class="gt_row gt_center">46.86</td>
<td headers="upper" class="gt_row gt_center">50.41</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Arizona</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">48.19</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">63.11</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">9</td>
<td headers="fitted" class="gt_row gt_center">56.05</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">21</td>
<td headers="fitted" class="gt_row gt_center">50.42</td>
<td headers="lower" class="gt_row gt_center">40.34</td>
<td headers="upper" class="gt_row gt_center">60.50</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">22</td>
<td headers="fitted" class="gt_row gt_center">60.08</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">25</td>
<td headers="fitted" class="gt_row gt_center">58.31</td>
<td headers="lower" class="gt_row gt_center">56.56</td>
<td headers="upper" class="gt_row gt_center">60.06</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">26</td>
<td headers="fitted" class="gt_row gt_center">54.77</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">45</td>
<td headers="fitted" class="gt_row gt_center">51.94</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">47</td>
<td headers="fitted" class="gt_row gt_center">58.68</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">49</td>
<td headers="fitted" class="gt_row gt_center">50.06</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Colorado</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">49.12</td>
<td headers="lower" class="gt_row gt_center">44.16</td>
<td headers="upper" class="gt_row gt_center">54.08</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Colorado</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">55.14</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Connecticut</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">49.58</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Connecticut</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">54.31</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">53.01</td>
<td headers="lower" class="gt_row gt_center">37.20</td>
<td headers="upper" class="gt_row gt_center">68.81</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">60.77</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">13</td>
<td headers="fitted" class="gt_row gt_center">48.43</td>
<td headers="lower" class="gt_row gt_center">40.72</td>
<td headers="upper" class="gt_row gt_center">56.13</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">15</td>
<td headers="fitted" class="gt_row gt_center">48.92</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">16</td>
<td headers="fitted" class="gt_row gt_center">42.36</td>
<td headers="lower" class="gt_row gt_center">14.85</td>
<td headers="upper" class="gt_row gt_center">69.86</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">22</td>
<td headers="fitted" class="gt_row gt_center">47.55</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">27</td>
<td headers="fitted" class="gt_row gt_center">44.33</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Georgia</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">52.09</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Georgia</td>
<td headers="district" class="gt_row gt_center">12</td>
<td headers="fitted" class="gt_row gt_center">53.79</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">53.55</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">30.01</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">11</td>
<td headers="fitted" class="gt_row gt_center">47.95</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">13</td>
<td headers="fitted" class="gt_row gt_center">49.98</td>
<td headers="lower" class="gt_row gt_center">44.12</td>
<td headers="upper" class="gt_row gt_center">55.85</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">14</td>
<td headers="fitted" class="gt_row gt_center">47.81</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">17</td>
<td headers="fitted" class="gt_row gt_center">48.01</td>
<td headers="lower" class="gt_row gt_center">42.06</td>
<td headers="upper" class="gt_row gt_center">53.96</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Iowa</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">45.37</td>
<td headers="lower" class="gt_row gt_center">42.62</td>
<td headers="upper" class="gt_row gt_center">48.13</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Iowa</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">50.21</td>
<td headers="lower" class="gt_row gt_center">46.16</td>
<td headers="upper" class="gt_row gt_center">54.25</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Iowa</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">44.55</td>
<td headers="lower" class="gt_row gt_center">40.28</td>
<td headers="upper" class="gt_row gt_center">48.83</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Kansas</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">43.95</td>
<td headers="lower" class="gt_row gt_center">41.48</td>
<td headers="upper" class="gt_row gt_center">46.41</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Maine</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">52.23</td>
<td headers="lower" class="gt_row gt_center">39.17</td>
<td headers="upper" class="gt_row gt_center">65.29</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Maryland</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">-34.28</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Michigan</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">47.56</td>
<td headers="lower" class="gt_row gt_center">46.27</td>
<td headers="upper" class="gt_row gt_center">48.86</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Michigan</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">45.56</td>
<td headers="lower" class="gt_row gt_center">37.74</td>
<td headers="upper" class="gt_row gt_center">53.37</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Michigan</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">46.25</td>
<td headers="lower" class="gt_row gt_center">39.60</td>
<td headers="upper" class="gt_row gt_center">52.90</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Michigan</td>
<td headers="district" class="gt_row gt_center">11</td>
<td headers="fitted" class="gt_row gt_center">46.06</td>
<td headers="lower" class="gt_row gt_center">45.27</td>
<td headers="upper" class="gt_row gt_center">46.85</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Minnesota</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">51.53</td>
<td headers="lower" class="gt_row gt_center">48.75</td>
<td headers="upper" class="gt_row gt_center">54.31</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Minnesota</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">48.04</td>
<td headers="lower" class="gt_row gt_center">42.69</td>
<td headers="upper" class="gt_row gt_center">53.39</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Minnesota</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">40.77</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Minnesota</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">48.83</td>
<td headers="lower" class="gt_row gt_center">45.40</td>
<td headers="upper" class="gt_row gt_center">52.25</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Missouri</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">46.53</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Nebraska</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">48.86</td>
<td headers="lower" class="gt_row gt_center">40.07</td>
<td headers="upper" class="gt_row gt_center">57.65</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Nevada</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">50.52</td>
<td headers="lower" class="gt_row gt_center">46.07</td>
<td headers="upper" class="gt_row gt_center">54.97</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Nevada</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">47.87</td>
<td headers="lower" class="gt_row gt_center">41.31</td>
<td headers="upper" class="gt_row gt_center">54.42</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Hampshire</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">48.43</td>
<td headers="lower" class="gt_row gt_center">46.18</td>
<td headers="upper" class="gt_row gt_center">50.68</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Hampshire</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">53.41</td>
<td headers="lower" class="gt_row gt_center">49.42</td>
<td headers="upper" class="gt_row gt_center">57.40</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Jersey</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">46.43</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Jersey</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">49.81</td>
<td headers="lower" class="gt_row gt_center">47.96</td>
<td headers="upper" class="gt_row gt_center">51.67</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Jersey</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">49.09</td>
<td headers="lower" class="gt_row gt_center">43.58</td>
<td headers="upper" class="gt_row gt_center">54.59</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Jersey</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">46.93</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Jersey</td>
<td headers="district" class="gt_row gt_center">11</td>
<td headers="fitted" class="gt_row gt_center">60.44</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Mexico</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">48.06</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Mexico</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">46.11</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">49.26</td>
<td headers="lower" class="gt_row gt_center">44.71</td>
<td headers="upper" class="gt_row gt_center">53.82</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">46.52</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">46.90</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">53.62</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">11</td>
<td headers="fitted" class="gt_row gt_center">46.78</td>
<td headers="lower" class="gt_row gt_center">36.22</td>
<td headers="upper" class="gt_row gt_center">57.33</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">18</td>
<td headers="fitted" class="gt_row gt_center">49.85</td>
<td headers="lower" class="gt_row gt_center">48.17</td>
<td headers="upper" class="gt_row gt_center">51.53</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">19</td>
<td headers="fitted" class="gt_row gt_center">47.34</td>
<td headers="lower" class="gt_row gt_center">42.75</td>
<td headers="upper" class="gt_row gt_center">51.93</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">22</td>
<td headers="fitted" class="gt_row gt_center">47.50</td>
<td headers="lower" class="gt_row gt_center">45.59</td>
<td headers="upper" class="gt_row gt_center">49.41</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">25</td>
<td headers="fitted" class="gt_row gt_center">49.11</td>
<td headers="lower" class="gt_row gt_center">22.92</td>
<td headers="upper" class="gt_row gt_center">75.29</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">North Carolina</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">53.43</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">North Carolina</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">50.56</td>
<td headers="lower" class="gt_row gt_center">43.87</td>
<td headers="upper" class="gt_row gt_center">57.24</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">North Carolina</td>
<td headers="district" class="gt_row gt_center">9</td>
<td headers="fitted" class="gt_row gt_center">49.77</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">North Carolina</td>
<td headers="district" class="gt_row gt_center">13</td>
<td headers="fitted" class="gt_row gt_center">48.93</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Ohio</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">46.03</td>
<td headers="lower" class="gt_row gt_center">44.22</td>
<td headers="upper" class="gt_row gt_center">47.85</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Ohio</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">62.47</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Ohio</td>
<td headers="district" class="gt_row gt_center">10</td>
<td headers="fitted" class="gt_row gt_center">20.01</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Ohio</td>
<td headers="district" class="gt_row gt_center">13</td>
<td headers="fitted" class="gt_row gt_center">58.82</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Ohio</td>
<td headers="district" class="gt_row gt_center">15</td>
<td headers="fitted" class="gt_row gt_center">47.43</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Oregon</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">52.66</td>
<td headers="lower" class="gt_row gt_center">51.18</td>
<td headers="upper" class="gt_row gt_center">54.14</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">50.84</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">51.10</td>
<td headers="lower" class="gt_row gt_center">50.18</td>
<td headers="upper" class="gt_row gt_center">52.03</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">43.89</td>
<td headers="lower" class="gt_row gt_center">36.33</td>
<td headers="upper" class="gt_row gt_center">51.44</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">49.88</td>
<td headers="lower" class="gt_row gt_center">45.92</td>
<td headers="upper" class="gt_row gt_center">53.83</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">10</td>
<td headers="fitted" class="gt_row gt_center">43.75</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">12</td>
<td headers="fitted" class="gt_row gt_center">48.24</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">17</td>
<td headers="fitted" class="gt_row gt_center">41.72</td>
<td headers="lower" class="gt_row gt_center">33.97</td>
<td headers="upper" class="gt_row gt_center">49.46</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">South Carolina</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">49.78</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">23</td>
<td headers="fitted" class="gt_row gt_center">48.19</td>
<td headers="lower" class="gt_row gt_center">45.35</td>
<td headers="upper" class="gt_row gt_center">51.03</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Virginia</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">48.85</td>
<td headers="lower" class="gt_row gt_center">48.41</td>
<td headers="upper" class="gt_row gt_center">49.30</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Virginia</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">49.62</td>
<td headers="lower" class="gt_row gt_center">45.13</td>
<td headers="upper" class="gt_row gt_center">54.11</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Virginia</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">50.98</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Virginia</td>
<td headers="district" class="gt_row gt_center">10</td>
<td headers="fitted" class="gt_row gt_center">37.52</td>
<td headers="lower" class="gt_row gt_center">32.60</td>
<td headers="upper" class="gt_row gt_center">42.44</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Washington</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">50.43</td>
<td headers="lower" class="gt_row gt_center">46.39</td>
<td headers="upper" class="gt_row gt_center">54.47</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Washington</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">47.24</td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Wisconsin</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">44.73</td>
<td headers="lower" class="gt_row gt_center">34.39</td>
<td headers="upper" class="gt_row gt_center">55.07</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
  </tbody>
  <tfoot class="gt_sourcenotes">
    <tr>
      <td class="gt_sourcenote" colspan="6"></td>
    </tr>
  </tfoot>
  &#10;</table>
</div>
And the non-competitive district predictions, using past election results and incumbency:

<div class="figure">

<img src="dist_viz.png" alt="Note: Visualization of District-Level Predictions; Several Districts Mapped Imperfectly." width="361" />
<p class="caption">
(#fig:district model viz)Note: Visualization of District-Level Predictions; Several Districts Mapped Imperfectly.
</p>

</div>

Both my national and district-level work has indicated that the Republicans are likely to win the House back once the votes have all been counted. We will see soon if these predictions are correct, and precisely how correct or incorrect they were. Below you can find the rest of the district predictions, complete with 95% confidence intervals.

It’s been a great 8 weeks, see you on the other side of the 2022 midterms!

<div id="rkpauxejmf" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>html {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}
&#10;#rkpauxejmf .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}
&#10;#rkpauxejmf .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}
&#10;#rkpauxejmf .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}
&#10;#rkpauxejmf .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}
&#10;#rkpauxejmf .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}
&#10;#rkpauxejmf .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#rkpauxejmf .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}
&#10;#rkpauxejmf .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}
&#10;#rkpauxejmf .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}
&#10;#rkpauxejmf .gt_column_spanner_outer:first-child {
  padding-left: 0;
}
&#10;#rkpauxejmf .gt_column_spanner_outer:last-child {
  padding-right: 0;
}
&#10;#rkpauxejmf .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}
&#10;#rkpauxejmf .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  text-align: left;
}
&#10;#rkpauxejmf .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}
&#10;#rkpauxejmf .gt_from_md > :first-child {
  margin-top: 0;
}
&#10;#rkpauxejmf .gt_from_md > :last-child {
  margin-bottom: 0;
}
&#10;#rkpauxejmf .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}
&#10;#rkpauxejmf .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#rkpauxejmf .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}
&#10;#rkpauxejmf .gt_row_group_first td {
  border-top-width: 2px;
}
&#10;#rkpauxejmf .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#rkpauxejmf .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}
&#10;#rkpauxejmf .gt_first_summary_row.thick {
  border-top-width: 2px;
}
&#10;#rkpauxejmf .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#rkpauxejmf .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#rkpauxejmf .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}
&#10;#rkpauxejmf .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}
&#10;#rkpauxejmf .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}
&#10;#rkpauxejmf .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}
&#10;#rkpauxejmf .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-left: 4px;
  padding-right: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#rkpauxejmf .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}
&#10;#rkpauxejmf .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}
&#10;#rkpauxejmf .gt_left {
  text-align: left;
}
&#10;#rkpauxejmf .gt_center {
  text-align: center;
}
&#10;#rkpauxejmf .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}
&#10;#rkpauxejmf .gt_font_normal {
  font-weight: normal;
}
&#10;#rkpauxejmf .gt_font_bold {
  font-weight: bold;
}
&#10;#rkpauxejmf .gt_font_italic {
  font-style: italic;
}
&#10;#rkpauxejmf .gt_super {
  font-size: 65%;
}
&#10;#rkpauxejmf .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 75%;
  vertical-align: 0.4em;
}
&#10;#rkpauxejmf .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}
&#10;#rkpauxejmf .gt_indent_1 {
  text-indent: 5px;
}
&#10;#rkpauxejmf .gt_indent_2 {
  text-indent: 10px;
}
&#10;#rkpauxejmf .gt_indent_3 {
  text-indent: 15px;
}
&#10;#rkpauxejmf .gt_indent_4 {
  text-indent: 20px;
}
&#10;#rkpauxejmf .gt_indent_5 {
  text-indent: 25px;
}
</style>
<table class="gt_table">
  &#10;  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col" id="state">state</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="district">district</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="fitted">fitted</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="lower">lower</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="upper">upper</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="winner">winner</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td headers="state" class="gt_row gt_left">Alabama</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">35.8209911426668</td>
<td headers="lower" class="gt_row gt_center">30.3492467481642</td>
<td headers="upper" class="gt_row gt_center">41.2927355371693</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Alabama</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">34.3093540663604</td>
<td headers="lower" class="gt_row gt_center">26.6941902418465</td>
<td headers="upper" class="gt_row gt_center">41.9245178908743</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Alabama</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">22.3011379691614</td>
<td headers="lower" class="gt_row gt_center">12.5044846222749</td>
<td headers="upper" class="gt_row gt_center">32.0977913160478</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Alabama</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">10.4128988441607</td>
<td headers="lower" class="gt_row gt_center">-8.05270427652569</td>
<td headers="upper" class="gt_row gt_center">28.8785019648471</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Alabama</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">93.2928632136297</td>
<td headers="lower" class="gt_row gt_center">86.3765995763489</td>
<td headers="upper" class="gt_row gt_center">100.20912685091</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Alaska</td>
<td headers="district" class="gt_row gt_center">0</td>
<td headers="fitted" class="gt_row gt_center">49.3074191911636</td>
<td headers="lower" class="gt_row gt_center">41.025678739835</td>
<td headers="upper" class="gt_row gt_center">57.5891596424921</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Arizona</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">43.2322077253388</td>
<td headers="lower" class="gt_row gt_center">36.8648108137247</td>
<td headers="upper" class="gt_row gt_center">49.5996046369529</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Arizona</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">50.2487705474986</td>
<td headers="lower" class="gt_row gt_center">44.270307771624</td>
<td headers="upper" class="gt_row gt_center">56.2272333233732</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Arizona</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">36.1314983668014</td>
<td headers="lower" class="gt_row gt_center">23.7112010280699</td>
<td headers="upper" class="gt_row gt_center">48.5517957055328</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Arizona</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">30.8027663461725</td>
<td headers="lower" class="gt_row gt_center">25.2790751875119</td>
<td headers="upper" class="gt_row gt_center">36.326457504833</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Arizona</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">36.8843361982729</td>
<td headers="lower" class="gt_row gt_center">32.6069017881481</td>
<td headers="upper" class="gt_row gt_center">41.1617706083977</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Arizona</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">41.8000884324885</td>
<td headers="lower" class="gt_row gt_center">29.9498136546879</td>
<td headers="upper" class="gt_row gt_center">53.6503632102891</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Arizona</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">99.2510961397863</td>
<td headers="lower" class="gt_row gt_center">48.7594496716176</td>
<td headers="upper" class="gt_row gt_center">149.742742607955</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Arkansas</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">18.4445655984287</td>
<td headers="lower" class="gt_row gt_center">-5.08664147336356</td>
<td headers="upper" class="gt_row gt_center">41.9757726702211</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Arkansas</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">48.6877849893362</td>
<td headers="lower" class="gt_row gt_center">37.9460500775253</td>
<td headers="upper" class="gt_row gt_center">59.429519901147</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Arkansas</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">28.0789772154322</td>
<td headers="lower" class="gt_row gt_center">19.8076557402918</td>
<td headers="upper" class="gt_row gt_center">36.3502986905727</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Arkansas</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">49.7307894449163</td>
<td headers="lower" class="gt_row gt_center">31.1967691610262</td>
<td headers="upper" class="gt_row gt_center">68.2648097288063</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">44.0407305825661</td>
<td headers="lower" class="gt_row gt_center">38.5816048021305</td>
<td headers="upper" class="gt_row gt_center">49.4998563630016</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">73.4535074861966</td>
<td headers="lower" class="gt_row gt_center">68.0933536248188</td>
<td headers="upper" class="gt_row gt_center">78.8136613475744</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">50.0353443359912</td>
<td headers="lower" class="gt_row gt_center">43.6553094126238</td>
<td headers="upper" class="gt_row gt_center">56.4153792593586</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">38.6064959275096</td>
<td headers="lower" class="gt_row gt_center">32.4019247801518</td>
<td headers="upper" class="gt_row gt_center">44.8110670748675</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">78.3125994842789</td>
<td headers="lower" class="gt_row gt_center">69.5924652581795</td>
<td headers="upper" class="gt_row gt_center">87.0327337103783</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">55.8663031030718</td>
<td headers="lower" class="gt_row gt_center">47.2637016393912</td>
<td headers="upper" class="gt_row gt_center">64.4689045667525</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">56.3256581887714</td>
<td headers="lower" class="gt_row gt_center">50.3632822242407</td>
<td headers="upper" class="gt_row gt_center">62.2880341533022</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">34.4899772519486</td>
<td headers="lower" class="gt_row gt_center">22.4783068618213</td>
<td headers="upper" class="gt_row gt_center">46.5016476420759</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">9</td>
<td headers="fitted" class="gt_row gt_center">59.147949644123</td>
<td headers="lower" class="gt_row gt_center">51.7866680024635</td>
<td headers="upper" class="gt_row gt_center">66.5092312857826</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">10</td>
<td headers="fitted" class="gt_row gt_center">42.2992455045551</td>
<td headers="lower" class="gt_row gt_center">36.2587238187866</td>
<td headers="upper" class="gt_row gt_center">48.3397671903235</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">11</td>
<td headers="fitted" class="gt_row gt_center">52.239889707979</td>
<td headers="lower" class="gt_row gt_center">43.0727676005689</td>
<td headers="upper" class="gt_row gt_center">61.4070118153892</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">12</td>
<td headers="fitted" class="gt_row gt_center">50.1847518421379</td>
<td headers="lower" class="gt_row gt_center">33.6249024469313</td>
<td headers="upper" class="gt_row gt_center">66.7446012373445</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">13</td>
<td headers="fitted" class="gt_row gt_center">64.3524099516813</td>
<td headers="lower" class="gt_row gt_center">48.5857081322373</td>
<td headers="upper" class="gt_row gt_center">80.1191117711253</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">14</td>
<td headers="fitted" class="gt_row gt_center">63.2946939384045</td>
<td headers="lower" class="gt_row gt_center">52.9859325420058</td>
<td headers="upper" class="gt_row gt_center">73.6034553348032</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">15</td>
<td headers="fitted" class="gt_row gt_center">60.7883987326406</td>
<td headers="lower" class="gt_row gt_center">60.7883987326406</td>
<td headers="upper" class="gt_row gt_center">50.7245052794437</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">16</td>
<td headers="fitted" class="gt_row gt_center">50.7855413950748</td>
<td headers="lower" class="gt_row gt_center">50.7855413950748</td>
<td headers="upper" class="gt_row gt_center">45.0266083184428</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">17</td>
<td headers="fitted" class="gt_row gt_center">71.5212667677362</td>
<td headers="lower" class="gt_row gt_center">66.4969329787458</td>
<td headers="upper" class="gt_row gt_center">76.5456005567265</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">18</td>
<td headers="fitted" class="gt_row gt_center">47.016832253898</td>
<td headers="lower" class="gt_row gt_center">26.4309021216321</td>
<td headers="upper" class="gt_row gt_center">67.602762386164</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">19</td>
<td headers="fitted" class="gt_row gt_center">46.8252350830672</td>
<td headers="lower" class="gt_row gt_center">34.3114599309491</td>
<td headers="upper" class="gt_row gt_center">59.3390102351854</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">20</td>
<td headers="fitted" class="gt_row gt_center">50.2829869818862</td>
<td headers="lower" class="gt_row gt_center">38.456847895036</td>
<td headers="upper" class="gt_row gt_center">62.1091260687364</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">21</td>
<td headers="fitted" class="gt_row gt_center">47.1286244268812</td>
<td headers="lower" class="gt_row gt_center">39.8074216026583</td>
<td headers="upper" class="gt_row gt_center">54.449827251104</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">22</td>
<td headers="fitted" class="gt_row gt_center">38.979714428092</td>
<td headers="lower" class="gt_row gt_center">32.5784727732382</td>
<td headers="upper" class="gt_row gt_center">45.3809560829458</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">23</td>
<td headers="fitted" class="gt_row gt_center">33.9426068251481</td>
<td headers="lower" class="gt_row gt_center">28.3164859848794</td>
<td headers="upper" class="gt_row gt_center">39.5687276654169</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">24</td>
<td headers="fitted" class="gt_row gt_center">63.6068951930678</td>
<td headers="lower" class="gt_row gt_center">57.8403579673423</td>
<td headers="upper" class="gt_row gt_center">69.3734324187933</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">25</td>
<td headers="fitted" class="gt_row gt_center">36.84826948895</td>
<td headers="lower" class="gt_row gt_center">29.9848157341424</td>
<td headers="upper" class="gt_row gt_center">43.7117232437576</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">26</td>
<td headers="fitted" class="gt_row gt_center">68.0120226347371</td>
<td headers="lower" class="gt_row gt_center">62.1586830170236</td>
<td headers="upper" class="gt_row gt_center">73.8653622524506</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">27</td>
<td headers="fitted" class="gt_row gt_center">51.8073779495515</td>
<td headers="lower" class="gt_row gt_center">42.8672582478456</td>
<td headers="upper" class="gt_row gt_center">60.7474976512574</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">28</td>
<td headers="fitted" class="gt_row gt_center">51.2194968433467</td>
<td headers="lower" class="gt_row gt_center">40.8665632772219</td>
<td headers="upper" class="gt_row gt_center">61.5724304094715</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">29</td>
<td headers="fitted" class="gt_row gt_center">90.6162042591707</td>
<td headers="lower" class="gt_row gt_center">88.922626641211</td>
<td headers="upper" class="gt_row gt_center">81.606356734081</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">30</td>
<td headers="fitted" class="gt_row gt_center">57.4897419220079</td>
<td headers="lower" class="gt_row gt_center">57.4897419220079</td>
<td headers="upper" class="gt_row gt_center">47.7182389635892</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">31</td>
<td headers="fitted" class="gt_row gt_center">54.5606224698468</td>
<td headers="lower" class="gt_row gt_center">38.0856106516215</td>
<td headers="upper" class="gt_row gt_center">71.0356342880722</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">32</td>
<td headers="fitted" class="gt_row gt_center">27.5371379401095</td>
<td headers="lower" class="gt_row gt_center">8.49934780001673</td>
<td headers="upper" class="gt_row gt_center">46.5749280802024</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">33</td>
<td headers="fitted" class="gt_row gt_center">54.5830980457084</td>
<td headers="lower" class="gt_row gt_center">46.3033415938914</td>
<td headers="upper" class="gt_row gt_center">62.8628544975254</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">34</td>
<td headers="fitted" class="gt_row gt_center">97.803348301848</td>
<td headers="lower" class="gt_row gt_center">91.7416158942861</td>
<td headers="upper" class="gt_row gt_center">89.7562908092092</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">35</td>
<td headers="fitted" class="gt_row gt_center">73.0323166943683</td>
<td headers="lower" class="gt_row gt_center">64.2825396077326</td>
<td headers="upper" class="gt_row gt_center">81.782093781004</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">36</td>
<td headers="fitted" class="gt_row gt_center">50.9116908350581</td>
<td headers="lower" class="gt_row gt_center">44.3971436784179</td>
<td headers="upper" class="gt_row gt_center">57.4262379916984</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">37</td>
<td headers="fitted" class="gt_row gt_center">74.389922133636</td>
<td headers="lower" class="gt_row gt_center">74.389922133636</td>
<td headers="upper" class="gt_row gt_center">59.7209155145873</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">38</td>
<td headers="fitted" class="gt_row gt_center">85.2214534488229</td>
<td headers="lower" class="gt_row gt_center">71.0584914575238</td>
<td headers="upper" class="gt_row gt_center">99.384415440122</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">39</td>
<td headers="fitted" class="gt_row gt_center">42.0512124305803</td>
<td headers="lower" class="gt_row gt_center">35.2311807111417</td>
<td headers="upper" class="gt_row gt_center">48.8712441500188</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">40</td>
<td headers="fitted" class="gt_row gt_center">27.9923017874743</td>
<td headers="lower" class="gt_row gt_center">15.3800361405358</td>
<td headers="upper" class="gt_row gt_center">40.6045674344129</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">41</td>
<td headers="fitted" class="gt_row gt_center">43.468951398726</td>
<td headers="lower" class="gt_row gt_center">32.5077946574524</td>
<td headers="upper" class="gt_row gt_center">54.4301081399997</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">42</td>
<td headers="fitted" class="gt_row gt_center">32.9235602551072</td>
<td headers="lower" class="gt_row gt_center">26.3437901045675</td>
<td headers="upper" class="gt_row gt_center">39.5033304056469</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">43</td>
<td headers="fitted" class="gt_row gt_center">72.7411886638342</td>
<td headers="lower" class="gt_row gt_center">63.0239560544911</td>
<td headers="upper" class="gt_row gt_center">82.4584212731773</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">44</td>
<td headers="fitted" class="gt_row gt_center">98.6387051664574</td>
<td headers="lower" class="gt_row gt_center">85.4554058566115</td>
<td headers="upper" class="gt_row gt_center">111.822004476303</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">45</td>
<td headers="fitted" class="gt_row gt_center">41.4659037679246</td>
<td headers="lower" class="gt_row gt_center">28.9712150072395</td>
<td headers="upper" class="gt_row gt_center">53.9605925286097</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">46</td>
<td headers="fitted" class="gt_row gt_center">65.8234611709979</td>
<td headers="lower" class="gt_row gt_center">50.4560173347867</td>
<td headers="upper" class="gt_row gt_center">81.190905007209</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">47</td>
<td headers="fitted" class="gt_row gt_center">43.8803413413898</td>
<td headers="lower" class="gt_row gt_center">30.3715617305162</td>
<td headers="upper" class="gt_row gt_center">57.3891209522633</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">48</td>
<td headers="fitted" class="gt_row gt_center">40.2779795031078</td>
<td headers="lower" class="gt_row gt_center">26.0050482447021</td>
<td headers="upper" class="gt_row gt_center">54.5509107615135</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">49</td>
<td headers="fitted" class="gt_row gt_center">52.8745062012548</td>
<td headers="lower" class="gt_row gt_center">20.594163434016</td>
<td headers="upper" class="gt_row gt_center">85.1548489684936</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">50</td>
<td headers="fitted" class="gt_row gt_center">39.5325762490992</td>
<td headers="lower" class="gt_row gt_center">31.6403798764041</td>
<td headers="upper" class="gt_row gt_center">47.4247726217943</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">51</td>
<td headers="fitted" class="gt_row gt_center">56.4895088548744</td>
<td headers="lower" class="gt_row gt_center">40.8144614482704</td>
<td headers="upper" class="gt_row gt_center">72.1645562614784</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">California</td>
<td headers="district" class="gt_row gt_center">52</td>
<td headers="fitted" class="gt_row gt_center">34.1354352336354</td>
<td headers="lower" class="gt_row gt_center">10.281799219052</td>
<td headers="upper" class="gt_row gt_center">57.9890712482188</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Colorado</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">71.9285529176943</td>
<td headers="lower" class="gt_row gt_center">68.0168146242181</td>
<td headers="upper" class="gt_row gt_center">75.8402912111705</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Colorado</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">62.7028831197105</td>
<td headers="lower" class="gt_row gt_center">59.6976695817788</td>
<td headers="upper" class="gt_row gt_center">65.7080966576421</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Colorado</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">43.9570088623374</td>
<td headers="lower" class="gt_row gt_center">40.4678810100945</td>
<td headers="upper" class="gt_row gt_center">47.4461367145802</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Colorado</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">37.2887109850333</td>
<td headers="lower" class="gt_row gt_center">33.193488317238</td>
<td headers="upper" class="gt_row gt_center">41.3839336528287</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Colorado</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">29.0261087004721</td>
<td headers="lower" class="gt_row gt_center">21.7405455466624</td>
<td headers="upper" class="gt_row gt_center">36.3116718542818</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Colorado</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">59.4430745588938</td>
<td headers="lower" class="gt_row gt_center">34.6862874501546</td>
<td headers="upper" class="gt_row gt_center">84.199861667633</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Colorado</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">44.1289992738984</td>
<td headers="lower" class="gt_row gt_center">24.710521659271</td>
<td headers="upper" class="gt_row gt_center">63.5474768885259</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Connecticut</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">65.5794730285818</td>
<td headers="lower" class="gt_row gt_center">63.1399056960594</td>
<td headers="upper" class="gt_row gt_center">68.0190403611041</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Connecticut</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">60.4879708559261</td>
<td headers="lower" class="gt_row gt_center">57.281574197135</td>
<td headers="upper" class="gt_row gt_center">63.6943675147171</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Connecticut</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">62.3553223681903</td>
<td headers="lower" class="gt_row gt_center">59.3861726176221</td>
<td headers="upper" class="gt_row gt_center">65.3244721187586</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Connecticut</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">59.5466144954434</td>
<td headers="lower" class="gt_row gt_center">53.5327808583912</td>
<td headers="upper" class="gt_row gt_center">65.5604481324955</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Connecticut</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">53.9131091797012</td>
<td headers="lower" class="gt_row gt_center">50.0351624216207</td>
<td headers="upper" class="gt_row gt_center">57.7910559377816</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Delaware</td>
<td headers="district" class="gt_row gt_center">0</td>
<td headers="fitted" class="gt_row gt_center">58.8292802782751</td>
<td headers="lower" class="gt_row gt_center">54.7312401516721</td>
<td headers="upper" class="gt_row gt_center">62.9273204048781</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">31.773549512209</td>
<td headers="lower" class="gt_row gt_center">24.6137400891068</td>
<td headers="upper" class="gt_row gt_center">38.9333589353112</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">14.4300158417241</td>
<td headers="lower" class="gt_row gt_center">-7.9175510048378</td>
<td headers="upper" class="gt_row gt_center">36.7775826882861</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">44.1908829612601</td>
<td headers="lower" class="gt_row gt_center">21.5608150856759</td>
<td headers="upper" class="gt_row gt_center">66.8209508368442</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">32.1950335184117</td>
<td headers="lower" class="gt_row gt_center">21.5923660614284</td>
<td headers="upper" class="gt_row gt_center">42.7977009753949</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">32.1162067162512</td>
<td headers="lower" class="gt_row gt_center">19.5029354140611</td>
<td headers="upper" class="gt_row gt_center">44.7294780184413</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">35.0326760638276</td>
<td headers="lower" class="gt_row gt_center">26.3389931788607</td>
<td headers="upper" class="gt_row gt_center">43.7263589487946</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">9</td>
<td headers="fitted" class="gt_row gt_center">66.3721996275771</td>
<td headers="lower" class="gt_row gt_center">47.0423325646035</td>
<td headers="upper" class="gt_row gt_center">85.7020666905507</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">10</td>
<td headers="fitted" class="gt_row gt_center">31.6397034311496</td>
<td headers="lower" class="gt_row gt_center">20.3907807542469</td>
<td headers="upper" class="gt_row gt_center">42.8886261080523</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">11</td>
<td headers="fitted" class="gt_row gt_center">36.6555556834594</td>
<td headers="lower" class="gt_row gt_center">24.4901067991663</td>
<td headers="upper" class="gt_row gt_center">48.8210045677525</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">12</td>
<td headers="fitted" class="gt_row gt_center">30.4422845478808</td>
<td headers="lower" class="gt_row gt_center">22.0108769256925</td>
<td headers="upper" class="gt_row gt_center">38.8736921700691</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">13</td>
<td headers="fitted" class="gt_row gt_center">26.8967212086085</td>
<td headers="lower" class="gt_row gt_center">13.2023033288087</td>
<td headers="upper" class="gt_row gt_center">40.5911390884083</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">14</td>
<td headers="fitted" class="gt_row gt_center">71.4118938781899</td>
<td headers="lower" class="gt_row gt_center">61.393922925136</td>
<td headers="upper" class="gt_row gt_center">81.4298648312438</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">15</td>
<td headers="fitted" class="gt_row gt_center">35.7044096240082</td>
<td headers="lower" class="gt_row gt_center">28.4697095530071</td>
<td headers="upper" class="gt_row gt_center">42.9391096950093</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">16</td>
<td headers="fitted" class="gt_row gt_center">33.6783275810462</td>
<td headers="lower" class="gt_row gt_center">19.0461061523074</td>
<td headers="upper" class="gt_row gt_center">48.310549009785</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">17</td>
<td headers="fitted" class="gt_row gt_center">36.8004282355068</td>
<td headers="lower" class="gt_row gt_center">19.4327343151135</td>
<td headers="upper" class="gt_row gt_center">54.1681221559</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">19</td>
<td headers="fitted" class="gt_row gt_center">38.7825679046646</td>
<td headers="lower" class="gt_row gt_center">26.8349975200337</td>
<td headers="upper" class="gt_row gt_center">50.7301382892955</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">20</td>
<td headers="fitted" class="gt_row gt_center">85.4250256664195</td>
<td headers="lower" class="gt_row gt_center">75.1388764386155</td>
<td headers="upper" class="gt_row gt_center">95.7111748942235</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">21</td>
<td headers="fitted" class="gt_row gt_center">24.8402365941386</td>
<td headers="lower" class="gt_row gt_center">2.60302685572626</td>
<td headers="upper" class="gt_row gt_center">47.0774463325509</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">22</td>
<td headers="fitted" class="gt_row gt_center">41.5613805641729</td>
<td headers="lower" class="gt_row gt_center">22.9010320055855</td>
<td headers="upper" class="gt_row gt_center">60.2217291227602</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">23</td>
<td headers="fitted" class="gt_row gt_center">70.5583818815858</td>
<td headers="lower" class="gt_row gt_center">55.0814787950969</td>
<td headers="upper" class="gt_row gt_center">86.0352849680747</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">24</td>
<td headers="fitted" class="gt_row gt_center">73.2549580600787</td>
<td headers="lower" class="gt_row gt_center">34.7967054389878</td>
<td headers="upper" class="gt_row gt_center">111.71321068117</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">25</td>
<td headers="fitted" class="gt_row gt_center">25.042381678861</td>
<td headers="lower" class="gt_row gt_center">-7.24466271332463</td>
<td headers="upper" class="gt_row gt_center">57.3294260710466</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">26</td>
<td headers="fitted" class="gt_row gt_center">45.7162375637691</td>
<td headers="lower" class="gt_row gt_center">6.9368447992281</td>
<td headers="upper" class="gt_row gt_center">84.49563032831</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Florida</td>
<td headers="district" class="gt_row gt_center">27</td>
<td headers="fitted" class="gt_row gt_center">26.9202194115153</td>
<td headers="lower" class="gt_row gt_center">-392.754473490067</td>
<td headers="upper" class="gt_row gt_center">446.594912313098</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Georgia</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">37.7499352805081</td>
<td headers="lower" class="gt_row gt_center">29.272350806821</td>
<td headers="upper" class="gt_row gt_center">46.2275197541951</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Georgia</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">63.8000594456077</td>
<td headers="lower" class="gt_row gt_center">56.3212299045578</td>
<td headers="upper" class="gt_row gt_center">71.2788889866576</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Georgia</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">33.3116940313038</td>
<td headers="lower" class="gt_row gt_center">24.8513140855965</td>
<td headers="upper" class="gt_row gt_center">41.7720739770112</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Georgia</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">79.4364736103832</td>
<td headers="lower" class="gt_row gt_center">73.6471073775201</td>
<td headers="upper" class="gt_row gt_center">85.2258398432463</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Georgia</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">85.9852567092583</td>
<td headers="lower" class="gt_row gt_center">80.74944352646</td>
<td headers="upper" class="gt_row gt_center">91.2210698920565</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Georgia</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">51.8851319143455</td>
<td headers="lower" class="gt_row gt_center">42.5632864304882</td>
<td headers="upper" class="gt_row gt_center">61.2069773982028</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Georgia</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">51.4863671007747</td>
<td headers="lower" class="gt_row gt_center">45.7304591168591</td>
<td headers="upper" class="gt_row gt_center">57.2422750846903</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Georgia</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">37.1553256419096</td>
<td headers="lower" class="gt_row gt_center">26.5520758808121</td>
<td headers="upper" class="gt_row gt_center">47.7585754030071</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Georgia</td>
<td headers="district" class="gt_row gt_center">9</td>
<td headers="fitted" class="gt_row gt_center">23.4164861184848</td>
<td headers="lower" class="gt_row gt_center">13.975554953859</td>
<td headers="upper" class="gt_row gt_center">32.8574172831105</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Georgia</td>
<td headers="district" class="gt_row gt_center">10</td>
<td headers="fitted" class="gt_row gt_center">41.570834843374</td>
<td headers="lower" class="gt_row gt_center">32.1364028311503</td>
<td headers="upper" class="gt_row gt_center">51.0052668555977</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Georgia</td>
<td headers="district" class="gt_row gt_center">11</td>
<td headers="fitted" class="gt_row gt_center">27.0867343391508</td>
<td headers="lower" class="gt_row gt_center">15.1092695696899</td>
<td headers="upper" class="gt_row gt_center">39.0641991086116</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Georgia</td>
<td headers="district" class="gt_row gt_center">12</td>
<td headers="fitted" class="gt_row gt_center">43.040326764297</td>
<td headers="lower" class="gt_row gt_center">33.709815307121</td>
<td headers="upper" class="gt_row gt_center">52.3708382214731</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Georgia</td>
<td headers="district" class="gt_row gt_center">13</td>
<td headers="fitted" class="gt_row gt_center">81.5759810901158</td>
<td headers="lower" class="gt_row gt_center">69.5075728865501</td>
<td headers="upper" class="gt_row gt_center">93.6443892936816</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Georgia</td>
<td headers="district" class="gt_row gt_center">14</td>
<td headers="fitted" class="gt_row gt_center">11.7649857933979</td>
<td headers="lower" class="gt_row gt_center">-40.6786087357528</td>
<td headers="upper" class="gt_row gt_center">64.2085803225485</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Hawaii</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">71.7225383893505</td>
<td headers="lower" class="gt_row gt_center">64.8686046422387</td>
<td headers="upper" class="gt_row gt_center">78.5764721364623</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Hawaii</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">72.4243584280948</td>
<td headers="lower" class="gt_row gt_center">59.7716978310684</td>
<td headers="upper" class="gt_row gt_center">85.0770190251211</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Idaho</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">35.2969119023373</td>
<td headers="lower" class="gt_row gt_center">30.3559051019178</td>
<td headers="upper" class="gt_row gt_center">40.2379187027569</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Idaho</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">36.1219651789184</td>
<td headers="lower" class="gt_row gt_center">33.0007239464793</td>
<td headers="upper" class="gt_row gt_center">39.2432064113575</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">80.0067424514319</td>
<td headers="lower" class="gt_row gt_center">70.8298018167565</td>
<td headers="upper" class="gt_row gt_center">89.1836830861073</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">78.9538316758049</td>
<td headers="lower" class="gt_row gt_center">76.3792469577275</td>
<td headers="upper" class="gt_row gt_center">81.5284163938823</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">59.3326710658686</td>
<td headers="lower" class="gt_row gt_center">50.8859505771902</td>
<td headers="upper" class="gt_row gt_center">67.7793915545469</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">84.6995206450092</td>
<td headers="lower" class="gt_row gt_center">79.4941282530248</td>
<td headers="upper" class="gt_row gt_center">89.9049130369935</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">71.8274225775902</td>
<td headers="lower" class="gt_row gt_center">68.3428445003215</td>
<td headers="upper" class="gt_row gt_center">75.3120006548589</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">55.5781122849981</td>
<td headers="lower" class="gt_row gt_center">49.3963942967609</td>
<td headers="upper" class="gt_row gt_center">49.5907407021302</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">84.3060760523335</td>
<td headers="lower" class="gt_row gt_center">81.477701564092</td>
<td headers="upper" class="gt_row gt_center">87.1344505405749</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">87.774729870428</td>
<td headers="lower" class="gt_row gt_center">77.3997438249223</td>
<td headers="upper" class="gt_row gt_center">98.1497159159337</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">9</td>
<td headers="fitted" class="gt_row gt_center">69.6946034457064</td>
<td headers="lower" class="gt_row gt_center">66.7550713073454</td>
<td headers="upper" class="gt_row gt_center">72.6341355840674</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">10</td>
<td headers="fitted" class="gt_row gt_center">58.2376685980614</td>
<td headers="lower" class="gt_row gt_center">48.8539659320101</td>
<td headers="upper" class="gt_row gt_center">67.6213712641127</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">11</td>
<td headers="fitted" class="gt_row gt_center">60.9451072041548</td>
<td headers="lower" class="gt_row gt_center">57.1621265587473</td>
<td headers="upper" class="gt_row gt_center">64.7280878495624</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">12</td>
<td headers="fitted" class="gt_row gt_center">34.9676318342942</td>
<td headers="lower" class="gt_row gt_center">29.2868780199294</td>
<td headers="upper" class="gt_row gt_center">40.6483856486591</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">13</td>
<td headers="fitted" class="gt_row gt_center">40.1272675205195</td>
<td headers="lower" class="gt_row gt_center">36.1307960313692</td>
<td headers="upper" class="gt_row gt_center">44.1237390096697</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">14</td>
<td headers="fitted" class="gt_row gt_center">51.8785872005387</td>
<td headers="lower" class="gt_row gt_center">43.8972486548029</td>
<td headers="upper" class="gt_row gt_center">59.8599257462745</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">15</td>
<td headers="fitted" class="gt_row gt_center">31.0123386984267</td>
<td headers="lower" class="gt_row gt_center">26.2536752003736</td>
<td headers="upper" class="gt_row gt_center">35.7710021964798</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">16</td>
<td headers="fitted" class="gt_row gt_center">31.8446011359436</td>
<td headers="lower" class="gt_row gt_center">27.7599679575127</td>
<td headers="upper" class="gt_row gt_center">35.9292343143745</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Illinois</td>
<td headers="district" class="gt_row gt_center">17</td>
<td headers="fitted" class="gt_row gt_center">40.4100771807251</td>
<td headers="lower" class="gt_row gt_center">33.6972347717355</td>
<td headers="upper" class="gt_row gt_center">47.1229195897148</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Indiana</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">65.3765276357575</td>
<td headers="lower" class="gt_row gt_center">60.9762407109675</td>
<td headers="upper" class="gt_row gt_center">69.7768145605475</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Indiana</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">44.5438077651303</td>
<td headers="lower" class="gt_row gt_center">41.3784516120122</td>
<td headers="upper" class="gt_row gt_center">47.7091639182484</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Indiana</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">35.3129379144286</td>
<td headers="lower" class="gt_row gt_center">31.0736917310246</td>
<td headers="upper" class="gt_row gt_center">39.5521840978326</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Indiana</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">36.7989952804922</td>
<td headers="lower" class="gt_row gt_center">33.8220160938352</td>
<td headers="upper" class="gt_row gt_center">39.7759744671492</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Indiana</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">40.2385389906369</td>
<td headers="lower" class="gt_row gt_center">35.6627109047238</td>
<td headers="upper" class="gt_row gt_center">44.8143670765499</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Indiana</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">30.5791641407656</td>
<td headers="lower" class="gt_row gt_center">27.6731869326705</td>
<td headers="upper" class="gt_row gt_center">33.4851413488606</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Indiana</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">61.1099582214346</td>
<td headers="lower" class="gt_row gt_center">58.0718827566726</td>
<td headers="upper" class="gt_row gt_center">64.1480336861966</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Indiana</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">40.6068750771903</td>
<td headers="lower" class="gt_row gt_center">34.2823175278833</td>
<td headers="upper" class="gt_row gt_center">46.9314326264972</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Indiana</td>
<td headers="district" class="gt_row gt_center">9</td>
<td headers="fitted" class="gt_row gt_center">41.7971529613673</td>
<td headers="lower" class="gt_row gt_center">34.7625677214978</td>
<td headers="upper" class="gt_row gt_center">48.8317382012368</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Iowa</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">44.1608959351508</td>
<td headers="lower" class="gt_row gt_center">39.8305494369955</td>
<td headers="upper" class="gt_row gt_center">48.4912424333061</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Iowa</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">47.2396011604874</td>
<td headers="lower" class="gt_row gt_center">45.0932270022402</td>
<td headers="upper" class="gt_row gt_center">49.3859753187345</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Iowa</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">60.0270156825687</td>
<td headers="lower" class="gt_row gt_center">53.8807811193411</td>
<td headers="upper" class="gt_row gt_center">66.1732502457962</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Iowa</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">42.1063609419002</td>
<td headers="lower" class="gt_row gt_center">38.0625472121078</td>
<td headers="upper" class="gt_row gt_center">46.1501746716927</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Kansas</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">27.1821454093416</td>
<td headers="lower" class="gt_row gt_center">21.9153188056006</td>
<td headers="upper" class="gt_row gt_center">32.4489720130826</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Kansas</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">43.0833670291437</td>
<td headers="lower" class="gt_row gt_center">40.0351861025728</td>
<td headers="upper" class="gt_row gt_center">46.1315479557147</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Kansas</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">56.4344888040392</td>
<td headers="lower" class="gt_row gt_center">45.2706664543975</td>
<td headers="upper" class="gt_row gt_center">67.5983111536809</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Kansas</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">37.821572148992</td>
<td headers="lower" class="gt_row gt_center">35.037546279167</td>
<td headers="upper" class="gt_row gt_center">40.6055980188169</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Kentucky</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">28.8659149670805</td>
<td headers="lower" class="gt_row gt_center">19.2970522731544</td>
<td headers="upper" class="gt_row gt_center">38.4347776610065</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Kentucky</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">38.8565017003654</td>
<td headers="lower" class="gt_row gt_center">27.0921578789195</td>
<td headers="upper" class="gt_row gt_center">50.6208455218114</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Kentucky</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">52.0398544294988</td>
<td headers="lower" class="gt_row gt_center">46.2812668855834</td>
<td headers="upper" class="gt_row gt_center">57.7984419734141</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Kentucky</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">38.5734393607653</td>
<td headers="lower" class="gt_row gt_center">31.6104076350254</td>
<td headers="upper" class="gt_row gt_center">45.5364710865052</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Kentucky</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">20.150980080935</td>
<td headers="lower" class="gt_row gt_center">15.0867005449548</td>
<td headers="upper" class="gt_row gt_center">25.2152596169152</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Kentucky</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">34.3938714697607</td>
<td headers="lower" class="gt_row gt_center">25.2695245952735</td>
<td headers="upper" class="gt_row gt_center">43.5182183442479</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Louisiana</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">16.1351393325053</td>
<td headers="lower" class="gt_row gt_center">11.3633749468541</td>
<td headers="upper" class="gt_row gt_center">20.9069037181564</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Louisiana</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">84.0981735895407</td>
<td headers="lower" class="gt_row gt_center">78.2877277610722</td>
<td headers="upper" class="gt_row gt_center">89.9086194180093</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Louisiana</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">20.8424121934389</td>
<td headers="lower" class="gt_row gt_center">20.8424121934389</td>
<td headers="upper" class="gt_row gt_center">10.9241085867131</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Louisiana</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">27.1336875030574</td>
<td headers="lower" class="gt_row gt_center">20.623488332126</td>
<td headers="upper" class="gt_row gt_center">27.1336875030574</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Maine</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">61.9048034260677</td>
<td headers="lower" class="gt_row gt_center">58.8519740813645</td>
<td headers="upper" class="gt_row gt_center">64.9576327707708</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Maine</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">60.4150756430722</td>
<td headers="lower" class="gt_row gt_center">54.8697625339826</td>
<td headers="upper" class="gt_row gt_center">65.9603887521617</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Maryland</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">36.6985563025831</td>
<td headers="lower" class="gt_row gt_center">33.4439500452952</td>
<td headers="upper" class="gt_row gt_center">39.953162559871</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Maryland</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">66.4322739430312</td>
<td headers="lower" class="gt_row gt_center">62.7893978905666</td>
<td headers="upper" class="gt_row gt_center">70.0751499954958</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Maryland</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">75.3480167792374</td>
<td headers="lower" class="gt_row gt_center">70.220116014631</td>
<td headers="upper" class="gt_row gt_center">80.4759175438438</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Maryland</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">72.3463962357923</td>
<td headers="lower" class="gt_row gt_center">62.6462935505353</td>
<td headers="upper" class="gt_row gt_center">82.0464989210493</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Maryland</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">69.3148268026951</td>
<td headers="lower" class="gt_row gt_center">65.1008456797489</td>
<td headers="upper" class="gt_row gt_center">73.5288079256413</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Maryland</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">62.4861496565109</td>
<td headers="lower" class="gt_row gt_center">56.3113679668099</td>
<td headers="upper" class="gt_row gt_center">68.6609313462118</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Maryland</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">76.46306577453</td>
<td headers="lower" class="gt_row gt_center">72.6540421328833</td>
<td headers="upper" class="gt_row gt_center">80.2720894161767</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Maryland</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">70.7814139358317</td>
<td headers="lower" class="gt_row gt_center">66.245938360011</td>
<td headers="upper" class="gt_row gt_center">75.3168895116524</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Massachusetts</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">91.0882722178656</td>
<td headers="lower" class="gt_row gt_center">80.1516041700695</td>
<td headers="upper" class="gt_row gt_center">102.024940265662</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Massachusetts</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">75.9621757771398</td>
<td headers="lower" class="gt_row gt_center">67.3839421592609</td>
<td headers="upper" class="gt_row gt_center">84.5404093950188</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Massachusetts</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">81.6273419799914</td>
<td headers="lower" class="gt_row gt_center">72.7678776355883</td>
<td headers="upper" class="gt_row gt_center">90.4868063243945</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Massachusetts</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">74.4008912980333</td>
<td headers="lower" class="gt_row gt_center">66.6775072731782</td>
<td headers="upper" class="gt_row gt_center">82.1242753228884</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Massachusetts</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">81.0129970253729</td>
<td headers="lower" class="gt_row gt_center">73.1980334951345</td>
<td headers="upper" class="gt_row gt_center">88.8279605556114</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Massachusetts</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">67.1372154990941</td>
<td headers="lower" class="gt_row gt_center">59.5919136838048</td>
<td headers="upper" class="gt_row gt_center">74.6825173143833</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Massachusetts</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">88.9797106213786</td>
<td headers="lower" class="gt_row gt_center">81.0984230070085</td>
<td headers="upper" class="gt_row gt_center">96.8609982357487</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Massachusetts</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">96.3052281943723</td>
<td headers="lower" class="gt_row gt_center">91.0812107200073</td>
<td headers="upper" class="gt_row gt_center">101.529245668737</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Massachusetts</td>
<td headers="district" class="gt_row gt_center">9</td>
<td headers="fitted" class="gt_row gt_center">73.8011083417849</td>
<td headers="lower" class="gt_row gt_center">64.3616122187454</td>
<td headers="upper" class="gt_row gt_center">83.2406044648244</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Michigan</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">36.1610505842057</td>
<td headers="lower" class="gt_row gt_center">28.9528197479485</td>
<td headers="upper" class="gt_row gt_center">43.3692814204628</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Michigan</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">38.4606312650524</td>
<td headers="lower" class="gt_row gt_center">36.4678158379958</td>
<td headers="upper" class="gt_row gt_center">40.453446692109</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Michigan</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">43.090401573205</td>
<td headers="lower" class="gt_row gt_center">39.8772900428437</td>
<td headers="upper" class="gt_row gt_center">46.3035131035664</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Michigan</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">34.2245242690152</td>
<td headers="lower" class="gt_row gt_center">31.5209395999864</td>
<td headers="upper" class="gt_row gt_center">36.928108938044</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Michigan</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">49.7384424524699</td>
<td headers="lower" class="gt_row gt_center">42.0621965421105</td>
<td headers="upper" class="gt_row gt_center">57.4146883628293</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Michigan</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">40.3406346269215</td>
<td headers="lower" class="gt_row gt_center">36.4621852862449</td>
<td headers="upper" class="gt_row gt_center">44.2190839675981</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Michigan</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">41.4314461448954</td>
<td headers="lower" class="gt_row gt_center">36.0325404939105</td>
<td headers="upper" class="gt_row gt_center">46.8303517958804</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Michigan</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">44.1448483070602</td>
<td headers="lower" class="gt_row gt_center">38.8866190189888</td>
<td headers="upper" class="gt_row gt_center">49.4030775951316</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Michigan</td>
<td headers="district" class="gt_row gt_center">9</td>
<td headers="fitted" class="gt_row gt_center">47.0468293757013</td>
<td headers="lower" class="gt_row gt_center">39.477516709855</td>
<td headers="upper" class="gt_row gt_center">54.6161420415477</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Michigan</td>
<td headers="district" class="gt_row gt_center">10</td>
<td headers="fitted" class="gt_row gt_center">35.8995172266516</td>
<td headers="lower" class="gt_row gt_center">33.3734248005506</td>
<td headers="upper" class="gt_row gt_center">38.4256096527526</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Michigan</td>
<td headers="district" class="gt_row gt_center">11</td>
<td headers="fitted" class="gt_row gt_center">50.3850326086867</td>
<td headers="lower" class="gt_row gt_center">45.535145177978</td>
<td headers="upper" class="gt_row gt_center">39.5726781981859</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Michigan</td>
<td headers="district" class="gt_row gt_center">12</td>
<td headers="fitted" class="gt_row gt_center">54.9442297017103</td>
<td headers="lower" class="gt_row gt_center">48.6841210097274</td>
<td headers="upper" class="gt_row gt_center">61.2043383936931</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Michigan</td>
<td headers="district" class="gt_row gt_center">13</td>
<td headers="fitted" class="gt_row gt_center">87.1228785849766</td>
<td headers="lower" class="gt_row gt_center">79.3355429869048</td>
<td headers="upper" class="gt_row gt_center">94.9102141830484</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Mississippi</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">28.5112307771927</td>
<td headers="lower" class="gt_row gt_center">21.2119871186575</td>
<td headers="upper" class="gt_row gt_center">35.8104744357279</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Mississippi</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">74.2763307083926</td>
<td headers="lower" class="gt_row gt_center">68.0937785856656</td>
<td headers="upper" class="gt_row gt_center">80.4588828311197</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Mississippi</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">32.1084705535728</td>
<td headers="lower" class="gt_row gt_center">23.1269076646377</td>
<td headers="upper" class="gt_row gt_center">41.0900334425079</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Mississippi</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">14.1327926856558</td>
<td headers="lower" class="gt_row gt_center">1.18596552824793</td>
<td headers="upper" class="gt_row gt_center">27.0796198430636</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Missouri</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">74.1693563022415</td>
<td headers="lower" class="gt_row gt_center">69.658783712561</td>
<td headers="upper" class="gt_row gt_center">78.679928891922</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Missouri</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">41.9334415391372</td>
<td headers="lower" class="gt_row gt_center">38.6415257432091</td>
<td headers="upper" class="gt_row gt_center">45.2253573350654</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Missouri</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">31.8065039118088</td>
<td headers="lower" class="gt_row gt_center">23.7410504936613</td>
<td headers="upper" class="gt_row gt_center">39.8719573299563</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Missouri</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">33.9038762956554</td>
<td headers="lower" class="gt_row gt_center">26.5205576634777</td>
<td headers="upper" class="gt_row gt_center">41.287194927833</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Missouri</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">63.3456130515771</td>
<td headers="lower" class="gt_row gt_center">61.3538139041693</td>
<td headers="upper" class="gt_row gt_center">65.3374121989848</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Missouri</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">36.4667548932291</td>
<td headers="lower" class="gt_row gt_center">31.9934231975627</td>
<td headers="upper" class="gt_row gt_center">40.9400865888955</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Missouri</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">32.6796460429088</td>
<td headers="lower" class="gt_row gt_center">28.4759288180597</td>
<td headers="upper" class="gt_row gt_center">36.8833632677579</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Missouri</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">30.0252401160988</td>
<td headers="lower" class="gt_row gt_center">23.4154102336512</td>
<td headers="upper" class="gt_row gt_center">36.6350699985464</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Montana</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center"></td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Montana</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center"></td>
<td headers="lower" class="gt_row gt_center"></td>
<td headers="upper" class="gt_row gt_center"></td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Nebraska</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">35.8615573333087</td>
<td headers="lower" class="gt_row gt_center">32.3422146095701</td>
<td headers="upper" class="gt_row gt_center">39.3809000570472</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Nebraska</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">44.3918411744127</td>
<td headers="lower" class="gt_row gt_center">41.1299508369658</td>
<td headers="upper" class="gt_row gt_center">47.6537315118596</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Nebraska</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">22.815700588735</td>
<td headers="lower" class="gt_row gt_center">17.4801009364644</td>
<td headers="upper" class="gt_row gt_center">28.1513002410057</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Nevada</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">64.0546995451066</td>
<td headers="lower" class="gt_row gt_center">60.7428675909069</td>
<td headers="upper" class="gt_row gt_center">67.3665314993064</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Nevada</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">35.1716075391212</td>
<td headers="lower" class="gt_row gt_center">28.6231630337251</td>
<td headers="upper" class="gt_row gt_center">41.7200520445173</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Nevada</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">50.0133428443719</td>
<td headers="lower" class="gt_row gt_center">39.3556186391435</td>
<td headers="upper" class="gt_row gt_center">60.6710670496002</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Nevada</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">49.5047491608178</td>
<td headers="lower" class="gt_row gt_center">13.5454665913228</td>
<td headers="upper" class="gt_row gt_center">85.4640317303128</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Hampshire</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">55.2592922648234</td>
<td headers="lower" class="gt_row gt_center">49.704541156959</td>
<td headers="upper" class="gt_row gt_center">60.8140433726879</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Hampshire</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">55.3842908507335</td>
<td headers="lower" class="gt_row gt_center">50.2529506220348</td>
<td headers="upper" class="gt_row gt_center">60.5156310794322</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Jersey</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">66.0463834667063</td>
<td headers="lower" class="gt_row gt_center">60.6039669356472</td>
<td headers="upper" class="gt_row gt_center">71.4887999977655</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Jersey</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">40.6963636117762</td>
<td headers="lower" class="gt_row gt_center">36.3671702515409</td>
<td headers="upper" class="gt_row gt_center">45.0255569720115</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Jersey</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">55.7146344748592</td>
<td headers="lower" class="gt_row gt_center">52.4925360379009</td>
<td headers="upper" class="gt_row gt_center">58.9367329118175</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Jersey</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">38.4206650410741</td>
<td headers="lower" class="gt_row gt_center">35.8625109369007</td>
<td headers="upper" class="gt_row gt_center">40.9788191452475</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Jersey</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">54.7975530426215</td>
<td headers="lower" class="gt_row gt_center">46.3700766354036</td>
<td headers="upper" class="gt_row gt_center">63.2250294498393</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Jersey</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">62.327332981788</td>
<td headers="lower" class="gt_row gt_center">59.4990238951595</td>
<td headers="upper" class="gt_row gt_center">65.1556420684165</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Jersey</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">50.2491288242636</td>
<td headers="lower" class="gt_row gt_center">43.3627271442403</td>
<td headers="upper" class="gt_row gt_center">57.1355305042869</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Jersey</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">56.4200991219706</td>
<td headers="lower" class="gt_row gt_center">47.9208917800785</td>
<td headers="upper" class="gt_row gt_center">64.9193064638627</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Jersey</td>
<td headers="district" class="gt_row gt_center">9</td>
<td headers="fitted" class="gt_row gt_center">66.13185127441</td>
<td headers="lower" class="gt_row gt_center">63.1547919347534</td>
<td headers="upper" class="gt_row gt_center">69.1089106140666</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Jersey</td>
<td headers="district" class="gt_row gt_center">10</td>
<td headers="fitted" class="gt_row gt_center">85.9794380223796</td>
<td headers="lower" class="gt_row gt_center">83.5297084185198</td>
<td headers="upper" class="gt_row gt_center">88.4291676262394</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Jersey</td>
<td headers="district" class="gt_row gt_center">11</td>
<td headers="fitted" class="gt_row gt_center">55.2930110926371</td>
<td headers="lower" class="gt_row gt_center">51.0686694686876</td>
<td headers="upper" class="gt_row gt_center">59.5173527165867</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Jersey</td>
<td headers="district" class="gt_row gt_center">12</td>
<td headers="fitted" class="gt_row gt_center">66.7030747837688</td>
<td headers="lower" class="gt_row gt_center">61.7641662352049</td>
<td headers="upper" class="gt_row gt_center">71.6419833323327</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Mexico</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">58.1070973848439</td>
<td headers="lower" class="gt_row gt_center">49.5601248254514</td>
<td headers="upper" class="gt_row gt_center">66.6540699442363</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Mexico</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">40.0045576646613</td>
<td headers="lower" class="gt_row gt_center">32.7028931047514</td>
<td headers="upper" class="gt_row gt_center">47.3062222245711</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New Mexico</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">67.6069873956946</td>
<td headers="lower" class="gt_row gt_center">60.9801852738821</td>
<td headers="upper" class="gt_row gt_center">74.2337895175071</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">44.7605412659422</td>
<td headers="lower" class="gt_row gt_center">41.3085067483294</td>
<td headers="upper" class="gt_row gt_center">48.2125757835549</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">38.9890936477983</td>
<td headers="lower" class="gt_row gt_center">35.1704005914171</td>
<td headers="upper" class="gt_row gt_center">42.8077867041795</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">44.7534180224276</td>
<td headers="lower" class="gt_row gt_center">40.0796109115175</td>
<td headers="upper" class="gt_row gt_center">49.4272251333376</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">45.9362257627002</td>
<td headers="lower" class="gt_row gt_center">40.6177579694472</td>
<td headers="upper" class="gt_row gt_center">51.2546935559531</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">92.3637332770366</td>
<td headers="lower" class="gt_row gt_center">82.3008996476169</td>
<td headers="upper" class="gt_row gt_center">102.426566906456</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">73.4777789529832</td>
<td headers="lower" class="gt_row gt_center">65.9413211050773</td>
<td headers="upper" class="gt_row gt_center">81.0142368008892</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">83.5521134198734</td>
<td headers="lower" class="gt_row gt_center">78.2153870141042</td>
<td headers="upper" class="gt_row gt_center">88.8888398256425</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">82.1257963166889</td>
<td headers="lower" class="gt_row gt_center">77.8714605740095</td>
<td headers="upper" class="gt_row gt_center">86.3801320593683</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">9</td>
<td headers="fitted" class="gt_row gt_center">82.1256884419828</td>
<td headers="lower" class="gt_row gt_center">76.0191301774585</td>
<td headers="upper" class="gt_row gt_center">88.2322467065072</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">10</td>
<td headers="fitted" class="gt_row gt_center">88.7977811786681</td>
<td headers="lower" class="gt_row gt_center">75.278625164655</td>
<td headers="upper" class="gt_row gt_center">102.316937192681</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">11</td>
<td headers="fitted" class="gt_row gt_center">49.5617987859299</td>
<td headers="lower" class="gt_row gt_center">39.4333275281307</td>
<td headers="upper" class="gt_row gt_center">59.6902700437291</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">12</td>
<td headers="fitted" class="gt_row gt_center">74.8557445147647</td>
<td headers="lower" class="gt_row gt_center">67.7708349282819</td>
<td headers="upper" class="gt_row gt_center">81.9406541012476</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">13</td>
<td headers="fitted" class="gt_row gt_center">88.2237170514063</td>
<td headers="lower" class="gt_row gt_center">81.7446459233152</td>
<td headers="upper" class="gt_row gt_center">94.7027881794974</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">14</td>
<td headers="fitted" class="gt_row gt_center">72.1731545292488</td>
<td headers="lower" class="gt_row gt_center">67.9673896165919</td>
<td headers="upper" class="gt_row gt_center">76.3789194419058</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">15</td>
<td headers="fitted" class="gt_row gt_center">86.4348181476833</td>
<td headers="lower" class="gt_row gt_center">80.5825939480532</td>
<td headers="upper" class="gt_row gt_center">92.2870423473133</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">16</td>
<td headers="fitted" class="gt_row gt_center">97.6808476048258</td>
<td headers="lower" class="gt_row gt_center">92.6325378486564</td>
<td headers="upper" class="gt_row gt_center">102.729157360995</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">17</td>
<td headers="fitted" class="gt_row gt_center">50.4087819676281</td>
<td headers="lower" class="gt_row gt_center">42.0990941774366</td>
<td headers="upper" class="gt_row gt_center">58.7184697578196</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">18</td>
<td headers="fitted" class="gt_row gt_center">66.1693256590387</td>
<td headers="lower" class="gt_row gt_center">51.8482511481022</td>
<td headers="upper" class="gt_row gt_center">80.4904001699753</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">19</td>
<td headers="fitted" class="gt_row gt_center">49.4385519348339</td>
<td headers="lower" class="gt_row gt_center">43.6538485323131</td>
<td headers="upper" class="gt_row gt_center">55.2232553373548</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">20</td>
<td headers="fitted" class="gt_row gt_center">66.737595911756</td>
<td headers="lower" class="gt_row gt_center">61.5818307780837</td>
<td headers="upper" class="gt_row gt_center">71.8933610454282</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">21</td>
<td headers="fitted" class="gt_row gt_center">39.5232738673833</td>
<td headers="lower" class="gt_row gt_center">31.8750356838923</td>
<td headers="upper" class="gt_row gt_center">47.1715120508742</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">22</td>
<td headers="fitted" class="gt_row gt_center">39.2415310347931</td>
<td headers="lower" class="gt_row gt_center">30.5003099670883</td>
<td headers="upper" class="gt_row gt_center">47.9827521024978</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">23</td>
<td headers="fitted" class="gt_row gt_center">38.2312022839545</td>
<td headers="lower" class="gt_row gt_center">32.5076381925004</td>
<td headers="upper" class="gt_row gt_center">43.9547663754086</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">24</td>
<td headers="fitted" class="gt_row gt_center">39.9706089364291</td>
<td headers="lower" class="gt_row gt_center">34.6621584116495</td>
<td headers="upper" class="gt_row gt_center">45.2790594612087</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">25</td>
<td headers="fitted" class="gt_row gt_center">56.5454703679797</td>
<td headers="lower" class="gt_row gt_center">46.051687553584</td>
<td headers="upper" class="gt_row gt_center">67.0392531823754</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">New York</td>
<td headers="district" class="gt_row gt_center">26</td>
<td headers="fitted" class="gt_row gt_center">66.1106586259751</td>
<td headers="lower" class="gt_row gt_center">57.8027988669464</td>
<td headers="upper" class="gt_row gt_center">74.4185183850038</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">North Carolina</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">51.5033070884819</td>
<td headers="lower" class="gt_row gt_center">34.5266478335952</td>
<td headers="upper" class="gt_row gt_center">68.4799663433686</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">North Carolina</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">69.4724000576962</td>
<td headers="lower" class="gt_row gt_center">62.2867967930401</td>
<td headers="upper" class="gt_row gt_center">76.6580033223523</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">North Carolina</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">36.4453569739483</td>
<td headers="lower" class="gt_row gt_center">27.3594329780753</td>
<td headers="upper" class="gt_row gt_center">45.5312809698212</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">North Carolina</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">58.7754348898891</td>
<td headers="lower" class="gt_row gt_center">45.616890151465</td>
<td headers="upper" class="gt_row gt_center">71.9339796283131</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">North Carolina</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">34.2980038947389</td>
<td headers="lower" class="gt_row gt_center">28.3222233072602</td>
<td headers="upper" class="gt_row gt_center">40.2737844822177</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">North Carolina</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">67.4270605708022</td>
<td headers="lower" class="gt_row gt_center">58.5999749592506</td>
<td headers="upper" class="gt_row gt_center">76.2541461823537</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">North Carolina</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">43.9134742243069</td>
<td headers="lower" class="gt_row gt_center">32.5211876634947</td>
<td headers="upper" class="gt_row gt_center">55.305760785119</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">North Carolina</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">45.9322935376723</td>
<td headers="lower" class="gt_row gt_center">42.9177475674847</td>
<td headers="upper" class="gt_row gt_center">48.94683950786</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">North Carolina</td>
<td headers="district" class="gt_row gt_center">9</td>
<td headers="fitted" class="gt_row gt_center">37.2098951357091</td>
<td headers="lower" class="gt_row gt_center">32.2272229542664</td>
<td headers="upper" class="gt_row gt_center">42.1925673171517</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">North Carolina</td>
<td headers="district" class="gt_row gt_center">10</td>
<td headers="fitted" class="gt_row gt_center">32.9655970244616</td>
<td headers="lower" class="gt_row gt_center">28.4633590768433</td>
<td headers="upper" class="gt_row gt_center">37.4678349720799</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">North Carolina</td>
<td headers="district" class="gt_row gt_center">11</td>
<td headers="fitted" class="gt_row gt_center">45.8731019703976</td>
<td headers="lower" class="gt_row gt_center">40.9818131535668</td>
<td headers="upper" class="gt_row gt_center">50.7643907872285</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">North Carolina</td>
<td headers="district" class="gt_row gt_center">12</td>
<td headers="fitted" class="gt_row gt_center">82.0148683923769</td>
<td headers="lower" class="gt_row gt_center">57.948796761409</td>
<td headers="upper" class="gt_row gt_center">106.080940023345</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">North Carolina</td>
<td headers="district" class="gt_row gt_center">13</td>
<td headers="fitted" class="gt_row gt_center">43.8543564096832</td>
<td headers="lower" class="gt_row gt_center">27.0317217373086</td>
<td headers="upper" class="gt_row gt_center">60.6769910820578</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Ohio</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">43.1619557870637</td>
<td headers="lower" class="gt_row gt_center">38.3641488580048</td>
<td headers="upper" class="gt_row gt_center">47.9597627161226</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Ohio</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">37.154618174495</td>
<td headers="lower" class="gt_row gt_center">34.6767746118676</td>
<td headers="upper" class="gt_row gt_center">39.6324617371224</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Ohio</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">74.3626364980084</td>
<td headers="lower" class="gt_row gt_center">66.5649101544223</td>
<td headers="upper" class="gt_row gt_center">82.1603628415946</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Ohio</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">30.7464561007561</td>
<td headers="lower" class="gt_row gt_center">27.0594435365971</td>
<td headers="upper" class="gt_row gt_center">34.4334686649151</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Ohio</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">32.6272274965647</td>
<td headers="lower" class="gt_row gt_center">29.9743077213472</td>
<td headers="upper" class="gt_row gt_center">35.2801472717821</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Ohio</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">30.1220573390168</td>
<td headers="lower" class="gt_row gt_center">24.276941840175</td>
<td headers="upper" class="gt_row gt_center">35.9671728378585</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Ohio</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">31.2022900973447</td>
<td headers="lower" class="gt_row gt_center">20.8528370386938</td>
<td headers="upper" class="gt_row gt_center">41.5517431559956</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Ohio</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">29.4497163333515</td>
<td headers="lower" class="gt_row gt_center">25.7667974077913</td>
<td headers="upper" class="gt_row gt_center">33.1326352589118</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Ohio</td>
<td headers="district" class="gt_row gt_center">9</td>
<td headers="fitted" class="gt_row gt_center">64.3207007738512</td>
<td headers="lower" class="gt_row gt_center">61.1082202212777</td>
<td headers="upper" class="gt_row gt_center">67.5331813264247</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Ohio</td>
<td headers="district" class="gt_row gt_center">10</td>
<td headers="fitted" class="gt_row gt_center">37.004087496169</td>
<td headers="lower" class="gt_row gt_center">32.4418040983427</td>
<td headers="upper" class="gt_row gt_center">41.5663708939954</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Ohio</td>
<td headers="district" class="gt_row gt_center">11</td>
<td headers="fitted" class="gt_row gt_center">80.2899268841036</td>
<td headers="lower" class="gt_row gt_center">75.2823213849685</td>
<td headers="upper" class="gt_row gt_center">85.2975323832386</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Ohio</td>
<td headers="district" class="gt_row gt_center">12</td>
<td headers="fitted" class="gt_row gt_center">40.3987531168034</td>
<td headers="lower" class="gt_row gt_center">37.6308458740059</td>
<td headers="upper" class="gt_row gt_center">43.166660359601</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Ohio</td>
<td headers="district" class="gt_row gt_center">13</td>
<td headers="fitted" class="gt_row gt_center">49.2717627737141</td>
<td headers="lower" class="gt_row gt_center">43.5572778985667</td>
<td headers="upper" class="gt_row gt_center">54.9862476488615</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Ohio</td>
<td headers="district" class="gt_row gt_center">14</td>
<td headers="fitted" class="gt_row gt_center">40.9570999905242</td>
<td headers="lower" class="gt_row gt_center">37.5623232763742</td>
<td headers="upper" class="gt_row gt_center">44.3518767046742</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Ohio</td>
<td headers="district" class="gt_row gt_center">15</td>
<td headers="fitted" class="gt_row gt_center">36.1073641421649</td>
<td headers="lower" class="gt_row gt_center">33.5933219914589</td>
<td headers="upper" class="gt_row gt_center">38.621406292871</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Oklahoma</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">34.7349800818934</td>
<td headers="lower" class="gt_row gt_center">30.2647787205574</td>
<td headers="upper" class="gt_row gt_center">39.2051814432293</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Oklahoma</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">26.8185375938243</td>
<td headers="lower" class="gt_row gt_center">18.5379533149381</td>
<td headers="upper" class="gt_row gt_center">35.0991218727104</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Oklahoma</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">20.8166720552196</td>
<td headers="lower" class="gt_row gt_center">13.868776305007</td>
<td headers="upper" class="gt_row gt_center">27.7645678054322</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Oklahoma</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">31.6772366368585</td>
<td headers="lower" class="gt_row gt_center">22.7032221130105</td>
<td headers="upper" class="gt_row gt_center">40.6512511607066</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Oklahoma</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">37.7434629814629</td>
<td headers="lower" class="gt_row gt_center">31.2890577862224</td>
<td headers="upper" class="gt_row gt_center">44.1978681767034</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Oregon</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">63.8647953111693</td>
<td headers="lower" class="gt_row gt_center">59.3729328092291</td>
<td headers="upper" class="gt_row gt_center">68.3566578131095</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Oregon</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">37.6351267105726</td>
<td headers="lower" class="gt_row gt_center">34.3539807465305</td>
<td headers="upper" class="gt_row gt_center">40.9162726746148</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Oregon</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">77.3348808740063</td>
<td headers="lower" class="gt_row gt_center">73.6151672468783</td>
<td headers="upper" class="gt_row gt_center">81.0545945011344</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Oregon</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">46.4038515922662</td>
<td headers="lower" class="gt_row gt_center">39.4759448515309</td>
<td headers="upper" class="gt_row gt_center">53.3317583330014</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Oregon</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">50.3947661966852</td>
<td headers="lower" class="gt_row gt_center">46.4898828313839</td>
<td headers="upper" class="gt_row gt_center">54.2996495619866</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">41.6775320661226</td>
<td headers="lower" class="gt_row gt_center">30.9673678660206</td>
<td headers="upper" class="gt_row gt_center">52.3876962662245</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">74.2499814629447</td>
<td headers="lower" class="gt_row gt_center">71.1461343657808</td>
<td headers="upper" class="gt_row gt_center">77.3538285601085</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">64.4579693013803</td>
<td headers="lower" class="gt_row gt_center">53.032422506386</td>
<td headers="upper" class="gt_row gt_center">75.8835160963746</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">60.4908502323619</td>
<td headers="lower" class="gt_row gt_center">55.5590280950531</td>
<td headers="upper" class="gt_row gt_center">65.4226723696708</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">62.006719847292</td>
<td headers="lower" class="gt_row gt_center">49.7518010400665</td>
<td headers="upper" class="gt_row gt_center">74.2616386545175</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">58.5738119253224</td>
<td headers="lower" class="gt_row gt_center">53.8511439656539</td>
<td headers="upper" class="gt_row gt_center">63.2964798849909</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">52.5703659303086</td>
<td headers="lower" class="gt_row gt_center">47.6335031638623</td>
<td headers="upper" class="gt_row gt_center">57.5072286967549</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">53.9046318102666</td>
<td headers="lower" class="gt_row gt_center">50.5370474644702</td>
<td headers="upper" class="gt_row gt_center">57.2722161560629</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">9</td>
<td headers="fitted" class="gt_row gt_center">30.1802714114429</td>
<td headers="lower" class="gt_row gt_center">25.0395955828437</td>
<td headers="upper" class="gt_row gt_center">35.3209472400421</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">10</td>
<td headers="fitted" class="gt_row gt_center">38.9577880061171</td>
<td headers="lower" class="gt_row gt_center">32.1759566716265</td>
<td headers="upper" class="gt_row gt_center">45.7396193406077</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">11</td>
<td headers="fitted" class="gt_row gt_center">41.1514238453821</td>
<td headers="lower" class="gt_row gt_center">31.1614494668841</td>
<td headers="upper" class="gt_row gt_center">51.1413982238802</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">12</td>
<td headers="fitted" class="gt_row gt_center">39.1409984477325</td>
<td headers="lower" class="gt_row gt_center">31.9180374976037</td>
<td headers="upper" class="gt_row gt_center">46.3639593978612</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">15</td>
<td headers="fitted" class="gt_row gt_center">37.4427857415466</td>
<td headers="lower" class="gt_row gt_center">29.7129787319894</td>
<td headers="upper" class="gt_row gt_center">45.1725927511039</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">16</td>
<td headers="fitted" class="gt_row gt_center">36.3049188818504</td>
<td headers="lower" class="gt_row gt_center">32.651074664485</td>
<td headers="upper" class="gt_row gt_center">39.9587630992158</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Pennsylvania</td>
<td headers="district" class="gt_row gt_center">17</td>
<td headers="fitted" class="gt_row gt_center">40.7390578045984</td>
<td headers="lower" class="gt_row gt_center">33.4069539311406</td>
<td headers="upper" class="gt_row gt_center">48.0711616780562</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Rhode Island</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">77.4784053722163</td>
<td headers="lower" class="gt_row gt_center">62.9042941268612</td>
<td headers="upper" class="gt_row gt_center">92.0525166175714</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Rhode Island</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">59.260237088716</td>
<td headers="lower" class="gt_row gt_center">52.1692037922258</td>
<td headers="upper" class="gt_row gt_center">66.3512703852061</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">South Carolina</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">38.7479745889196</td>
<td headers="lower" class="gt_row gt_center">31.0469464372883</td>
<td headers="upper" class="gt_row gt_center">46.4490027405508</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">South Carolina</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">36.1280221901981</td>
<td headers="lower" class="gt_row gt_center">29.0053948118326</td>
<td headers="upper" class="gt_row gt_center">43.2506495685635</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">South Carolina</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">47.5651724181321</td>
<td headers="lower" class="gt_row gt_center">33.9913997093311</td>
<td headers="upper" class="gt_row gt_center">61.1389451269332</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">South Carolina</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">73.2011968633397</td>
<td headers="lower" class="gt_row gt_center">67.3306207337419</td>
<td headers="upper" class="gt_row gt_center">79.0717729929376</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">South Carolina</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">39.1579796666067</td>
<td headers="lower" class="gt_row gt_center">34.8231076186712</td>
<td headers="upper" class="gt_row gt_center">43.4928517145421</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Tennessee</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">23.201055671483</td>
<td headers="lower" class="gt_row gt_center">18.5893458120334</td>
<td headers="upper" class="gt_row gt_center">27.8127655309326</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Tennessee</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">19.2082773836914</td>
<td headers="lower" class="gt_row gt_center">13.3136974095141</td>
<td headers="upper" class="gt_row gt_center">25.1028573578688</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Tennessee</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">34.5329504896137</td>
<td headers="lower" class="gt_row gt_center">28.2282512569893</td>
<td headers="upper" class="gt_row gt_center">40.837649722238</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Tennessee</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">34.8560410568278</td>
<td headers="lower" class="gt_row gt_center">26.5414879527326</td>
<td headers="upper" class="gt_row gt_center">43.1705941609229</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Tennessee</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">55.5486325427936</td>
<td headers="lower" class="gt_row gt_center">39.3658701219576</td>
<td headers="upper" class="gt_row gt_center">71.7313949636296</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Tennessee</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">25.6844944863634</td>
<td headers="lower" class="gt_row gt_center">10.8877778905748</td>
<td headers="upper" class="gt_row gt_center">40.4812110821521</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Tennessee</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">27.5572648186585</td>
<td headers="lower" class="gt_row gt_center">20.4042872011307</td>
<td headers="upper" class="gt_row gt_center">34.7102424361863</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Tennessee</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">35.3315015047816</td>
<td headers="lower" class="gt_row gt_center">22.2864163412877</td>
<td headers="upper" class="gt_row gt_center">48.3765866682755</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Tennessee</td>
<td headers="district" class="gt_row gt_center">9</td>
<td headers="fitted" class="gt_row gt_center">81.8323184206495</td>
<td headers="lower" class="gt_row gt_center">75.788145816785</td>
<td headers="upper" class="gt_row gt_center">87.876491024514</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">29.4947566333411</td>
<td headers="lower" class="gt_row gt_center">18.4829211851793</td>
<td headers="upper" class="gt_row gt_center">40.506592081503</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">39.002486625761</td>
<td headers="lower" class="gt_row gt_center">27.206526024168</td>
<td headers="upper" class="gt_row gt_center">50.798447227354</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">38.27405956975</td>
<td headers="lower" class="gt_row gt_center">30.6787717115779</td>
<td headers="upper" class="gt_row gt_center">45.869347427922</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">20.8592870922315</td>
<td headers="lower" class="gt_row gt_center">12.6025578095322</td>
<td headers="upper" class="gt_row gt_center">29.1160163749309</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">35.5845422476395</td>
<td headers="lower" class="gt_row gt_center">27.9174972036403</td>
<td headers="upper" class="gt_row gt_center">43.2515872916387</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">65.7374424509436</td>
<td headers="lower" class="gt_row gt_center">50.0211999550915</td>
<td headers="upper" class="gt_row gt_center">81.4536849467957</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">23.7578099528975</td>
<td headers="lower" class="gt_row gt_center">16.4550026981904</td>
<td headers="upper" class="gt_row gt_center">31.0606172076046</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">9</td>
<td headers="fitted" class="gt_row gt_center">80.3111901167849</td>
<td headers="lower" class="gt_row gt_center">73.8602499662556</td>
<td headers="upper" class="gt_row gt_center">86.7621302673141</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">10</td>
<td headers="fitted" class="gt_row gt_center">44.707216557339</td>
<td headers="lower" class="gt_row gt_center">34.0734943692419</td>
<td headers="upper" class="gt_row gt_center">55.3409387454361</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">12</td>
<td headers="fitted" class="gt_row gt_center">35.2001352941129</td>
<td headers="lower" class="gt_row gt_center">26.6188692806139</td>
<td headers="upper" class="gt_row gt_center">43.7814013076119</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">13</td>
<td headers="fitted" class="gt_row gt_center">19.3330787515235</td>
<td headers="lower" class="gt_row gt_center">12.7652687031554</td>
<td headers="upper" class="gt_row gt_center">25.9008887998916</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">14</td>
<td headers="fitted" class="gt_row gt_center">38.9958525487628</td>
<td headers="lower" class="gt_row gt_center">31.374780127793</td>
<td headers="upper" class="gt_row gt_center">46.6169249697327</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">15</td>
<td headers="fitted" class="gt_row gt_center">58.7263997790002</td>
<td headers="lower" class="gt_row gt_center">41.8032285592593</td>
<td headers="upper" class="gt_row gt_center">75.6495709987411</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">16</td>
<td headers="fitted" class="gt_row gt_center">81.9094519542985</td>
<td headers="lower" class="gt_row gt_center">73.0955763840294</td>
<td headers="upper" class="gt_row gt_center">90.7233275245677</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">17</td>
<td headers="fitted" class="gt_row gt_center">37.6489262694013</td>
<td headers="lower" class="gt_row gt_center">26.0740596578811</td>
<td headers="upper" class="gt_row gt_center">49.2237928809216</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">18</td>
<td headers="fitted" class="gt_row gt_center">79.9891980386141</td>
<td headers="lower" class="gt_row gt_center">71.4594827843236</td>
<td headers="upper" class="gt_row gt_center">88.5189132929047</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">20</td>
<td headers="fitted" class="gt_row gt_center">89.1054858004606</td>
<td headers="lower" class="gt_row gt_center">78.0643391017768</td>
<td headers="upper" class="gt_row gt_center">100.146632499144</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">21</td>
<td headers="fitted" class="gt_row gt_center">37.5779819211498</td>
<td headers="lower" class="gt_row gt_center">28.9488355505742</td>
<td headers="upper" class="gt_row gt_center">46.2071282917253</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">22</td>
<td headers="fitted" class="gt_row gt_center">38.1775129276866</td>
<td headers="lower" class="gt_row gt_center">31.3585779446625</td>
<td headers="upper" class="gt_row gt_center">44.9964479107108</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">23</td>
<td headers="fitted" class="gt_row gt_center">50.0816445948573</td>
<td headers="lower" class="gt_row gt_center">40.8338309220934</td>
<td headers="upper" class="gt_row gt_center">59.3294582676212</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">24</td>
<td headers="fitted" class="gt_row gt_center">38.2083420040379</td>
<td headers="lower" class="gt_row gt_center">26.8881136472523</td>
<td headers="upper" class="gt_row gt_center">49.5285703608235</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">27</td>
<td headers="fitted" class="gt_row gt_center">39.9641786854479</td>
<td headers="lower" class="gt_row gt_center">27.216810159278</td>
<td headers="upper" class="gt_row gt_center">52.7115472116178</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">28</td>
<td headers="fitted" class="gt_row gt_center">87.8446810207225</td>
<td headers="lower" class="gt_row gt_center">70.8408360575154</td>
<td headers="upper" class="gt_row gt_center">104.84852598393</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">29</td>
<td headers="fitted" class="gt_row gt_center">84.4846880722272</td>
<td headers="lower" class="gt_row gt_center">72.3714258979426</td>
<td headers="upper" class="gt_row gt_center">96.5979502465118</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">30</td>
<td headers="fitted" class="gt_row gt_center">89.1242396478508</td>
<td headers="lower" class="gt_row gt_center">83.2203601689796</td>
<td headers="upper" class="gt_row gt_center">95.0281191267219</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">32</td>
<td headers="fitted" class="gt_row gt_center">45.9341248935865</td>
<td headers="lower" class="gt_row gt_center">15.8217439755424</td>
<td headers="upper" class="gt_row gt_center">76.0465058116306</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">33</td>
<td headers="fitted" class="gt_row gt_center">85.050545977004</td>
<td headers="lower" class="gt_row gt_center">46.7311791040989</td>
<td headers="upper" class="gt_row gt_center">123.369912849909</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">34</td>
<td headers="fitted" class="gt_row gt_center">57.84228285127</td>
<td headers="lower" class="gt_row gt_center">36.2105673792089</td>
<td headers="upper" class="gt_row gt_center">79.4739983233311</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">35</td>
<td headers="fitted" class="gt_row gt_center">68.4819514039519</td>
<td headers="lower" class="gt_row gt_center">59.1456471964385</td>
<td headers="upper" class="gt_row gt_center">77.8182556114653</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Texas</td>
<td headers="district" class="gt_row gt_center">36</td>
<td headers="fitted" class="gt_row gt_center">17.101818064568</td>
<td headers="lower" class="gt_row gt_center">-18.1205119417118</td>
<td headers="upper" class="gt_row gt_center">52.3241480708477</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Utah</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">33.5003550943386</td>
<td headers="lower" class="gt_row gt_center">30.203197519667</td>
<td headers="upper" class="gt_row gt_center">36.7975126690103</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Utah</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">41.9116913178793</td>
<td headers="lower" class="gt_row gt_center">38.3448181294415</td>
<td headers="upper" class="gt_row gt_center">45.4785645063171</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Utah</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">32.1950812490566</td>
<td headers="lower" class="gt_row gt_center">24.7256910492445</td>
<td headers="upper" class="gt_row gt_center">39.6644714488687</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Utah</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">45.7515935802513</td>
<td headers="lower" class="gt_row gt_center">5.08881851723252</td>
<td headers="upper" class="gt_row gt_center">86.41436864327</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Vermont</td>
<td headers="district" class="gt_row gt_center">0</td>
<td headers="fitted" class="gt_row gt_center">31.2355642403066</td>
<td headers="lower" class="gt_row gt_center">12.3073005666031</td>
<td headers="upper" class="gt_row gt_center">50.1638279140101</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Virginia</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">38.0818889047093</td>
<td headers="lower" class="gt_row gt_center">29.3138377733018</td>
<td headers="upper" class="gt_row gt_center">46.8499400361168</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Virginia</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">71.7560003057676</td>
<td headers="lower" class="gt_row gt_center">60.875373243256</td>
<td headers="upper" class="gt_row gt_center">82.6366273682791</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Virginia</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">83.1633106328297</td>
<td headers="lower" class="gt_row gt_center">74.3090939686297</td>
<td headers="upper" class="gt_row gt_center">92.0175272970298</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Virginia</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">89.6773728259282</td>
<td headers="lower" class="gt_row gt_center">79.1928314960366</td>
<td headers="upper" class="gt_row gt_center">100.16191415582</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Virginia</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">47.7321699858899</td>
<td headers="lower" class="gt_row gt_center">37.3161694059217</td>
<td headers="upper" class="gt_row gt_center">58.1481705658581</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Virginia</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">22.6580864990865</td>
<td headers="lower" class="gt_row gt_center">14.5709742926685</td>
<td headers="upper" class="gt_row gt_center">30.7451987055046</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Virginia</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">69.7651824895453</td>
<td headers="lower" class="gt_row gt_center">56.3678723078662</td>
<td headers="upper" class="gt_row gt_center">83.1624926712243</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Virginia</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">74.3596569972998</td>
<td headers="lower" class="gt_row gt_center">68.704810477678</td>
<td headers="upper" class="gt_row gt_center">80.0145035169215</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Virginia</td>
<td headers="district" class="gt_row gt_center">9</td>
<td headers="fitted" class="gt_row gt_center">38.2682940150493</td>
<td headers="lower" class="gt_row gt_center">21.6862650707698</td>
<td headers="upper" class="gt_row gt_center">54.8503229593288</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Virginia</td>
<td headers="district" class="gt_row gt_center">10</td>
<td headers="fitted" class="gt_row gt_center">54.8948253145391</td>
<td headers="lower" class="gt_row gt_center">44.4095385287813</td>
<td headers="upper" class="gt_row gt_center">65.380112100297</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Virginia</td>
<td headers="district" class="gt_row gt_center">11</td>
<td headers="fitted" class="gt_row gt_center">66.5287875517572</td>
<td headers="lower" class="gt_row gt_center">48.6215102217121</td>
<td headers="upper" class="gt_row gt_center">84.4360648818023</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Washington</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">58.1340855144015</td>
<td headers="lower" class="gt_row gt_center">53.4551807190631</td>
<td headers="upper" class="gt_row gt_center">62.8129903097399</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Washington</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">64.0564693714472</td>
<td headers="lower" class="gt_row gt_center">59.6137738827589</td>
<td headers="upper" class="gt_row gt_center">68.4991648601355</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Washington</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">44.2111575281935</td>
<td headers="lower" class="gt_row gt_center">40.8853607661472</td>
<td headers="upper" class="gt_row gt_center">47.5369542902397</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Washington</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">33.732599216752</td>
<td headers="lower" class="gt_row gt_center">29.8213016403805</td>
<td headers="upper" class="gt_row gt_center">37.6438967931235</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Washington</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">40.0070089837569</td>
<td headers="lower" class="gt_row gt_center">36.9441875192177</td>
<td headers="upper" class="gt_row gt_center">43.0698304482961</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Washington</td>
<td headers="district" class="gt_row gt_center">6</td>
<td headers="fitted" class="gt_row gt_center">62.1710455907136</td>
<td headers="lower" class="gt_row gt_center">59.0432039543065</td>
<td headers="upper" class="gt_row gt_center">65.2988872271208</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Washington</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">81.1410439976331</td>
<td headers="lower" class="gt_row gt_center">76.2684188928644</td>
<td headers="upper" class="gt_row gt_center">86.0136691024017</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Washington</td>
<td headers="district" class="gt_row gt_center">8</td>
<td headers="fitted" class="gt_row gt_center">51.5105175451409</td>
<td headers="lower" class="gt_row gt_center">37.0087480110036</td>
<td headers="upper" class="gt_row gt_center">66.0122870792782</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Washington</td>
<td headers="district" class="gt_row gt_center">9</td>
<td headers="fitted" class="gt_row gt_center">70.9412489271708</td>
<td headers="lower" class="gt_row gt_center">62.418379497164</td>
<td headers="upper" class="gt_row gt_center">79.4641183571777</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Washington</td>
<td headers="district" class="gt_row gt_center">10</td>
<td headers="fitted" class="gt_row gt_center">53.8803450310701</td>
<td headers="lower" class="gt_row gt_center">-768.9791556493</td>
<td headers="upper" class="gt_row gt_center">876.73984571144</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">West Virginia</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">35.9673864156404</td>
<td headers="lower" class="gt_row gt_center">27.3310848113565</td>
<td headers="upper" class="gt_row gt_center">44.6036880199243</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">West Virginia</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">41.6247429094646</td>
<td headers="lower" class="gt_row gt_center">35.4286829759707</td>
<td headers="upper" class="gt_row gt_center">47.8208028429585</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Wisconsin</td>
<td headers="district" class="gt_row gt_center">1</td>
<td headers="fitted" class="gt_row gt_center">41.7273927438849</td>
<td headers="lower" class="gt_row gt_center">37.8069861766334</td>
<td headers="upper" class="gt_row gt_center">45.6477993111363</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Wisconsin</td>
<td headers="district" class="gt_row gt_center">2</td>
<td headers="fitted" class="gt_row gt_center">66.6749759723476</td>
<td headers="lower" class="gt_row gt_center">62.3439257946047</td>
<td headers="upper" class="gt_row gt_center">71.0060261500906</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Wisconsin</td>
<td headers="district" class="gt_row gt_center">3</td>
<td headers="fitted" class="gt_row gt_center">39.3775308999212</td>
<td headers="lower" class="gt_row gt_center">33.1724723067254</td>
<td headers="upper" class="gt_row gt_center">45.582589493117</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Wisconsin</td>
<td headers="district" class="gt_row gt_center">4</td>
<td headers="fitted" class="gt_row gt_center">76.5855652031481</td>
<td headers="lower" class="gt_row gt_center">71.7195638245943</td>
<td headers="upper" class="gt_row gt_center">81.4515665817019</td>
<td headers="winner" class="gt_row gt_center">Democrat</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Wisconsin</td>
<td headers="district" class="gt_row gt_center">5</td>
<td headers="fitted" class="gt_row gt_center">36.0448931244835</td>
<td headers="lower" class="gt_row gt_center">27.9699073361302</td>
<td headers="upper" class="gt_row gt_center">44.1198789128369</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Wisconsin</td>
<td headers="district" class="gt_row gt_center">7</td>
<td headers="fitted" class="gt_row gt_center">38.5194433890199</td>
<td headers="lower" class="gt_row gt_center">34.4700443282218</td>
<td headers="upper" class="gt_row gt_center">42.5688424498179</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
    <tr><td headers="state" class="gt_row gt_left">Wyoming</td>
<td headers="district" class="gt_row gt_center">0</td>
<td headers="fitted" class="gt_row gt_center">30.501963354234</td>
<td headers="lower" class="gt_row gt_center">26.8337491926436</td>
<td headers="upper" class="gt_row gt_center">34.1701775158243</td>
<td headers="winner" class="gt_row gt_center">Republican</td></tr>
  </tbody>
  <tfoot class="gt_sourcenotes">
    <tr>
      <td class="gt_sourcenote" colspan="6"></td>
    </tr>
  </tfoot>
  &#10;</table>
</div>

------------------------------------------------------------------------

**References**

\[1\] U.S. Bureau of Economic Analysis, Real Gross Domestic Product \[GDPC1\], retrieved from FRED, Federal Reserve Bank of St. Louis; https://fred.stlouisfed.org/series/GDPC1.

\[2\] FiveThirtyEight, Generic Ballot. https://projects.fivethirtyeight.com/polls/generic-ballot/

\[3\] Gallup, Congress and the Public. https://news.gallup.com/poll/1600/congress-public.aspx
