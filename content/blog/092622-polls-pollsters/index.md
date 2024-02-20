---
title: "Polls and Forecasters"
author: "Vivian Nguyen"
date: '2022-09-26'
categories: 
  - Election Analytics
tags: ["polls", "FiveThirtyEight", "The Economist", "expert ratings"]
---











# The Plan This Week
So far, I've confirmed that the president’s party often performs poorly in the midterm House elections and that a combination of fundamental (Q8, national) economic variables, including GDP growth %, RDI change %, unemployment rate, and absolute GDP growth, are somewhat able to predict House incumbent party vote share. These two findings have led me to forecast that the incumbent (President and House) Democrats will lose House vote share in November. This week, I delve into how forecasting giants like [The Economist](https://www.economist.com/interactive/us-midterms-2022/forecast/house) and [FiveThirtyEight](https://projects.fivethirtyeight.com/2022-election-forecast/house/) incorporate polls into their House predictions. Then, I examine how polling data can improve my predictions by investigating how predictive it's been in past elections. After that, I will incorporate the latest polls to provide a numerical prediction for the Democrat House vote share. 

# What Do Forecasters Do? 
In general, election forecasters gather as much relevant data about elections as they can to predict the outcome of future ones. The prevailing question for them, and for students of government in Election Analytics, pertains to what data is relevant? 

## The Economist
According to [The Economist's forecasting model report](https://www.economist.com/the-economist-explains/2022/09/09/how-does-the-economists-midterms-election-model-work) this year, polls are the best indicator for the outcome of House elections. Especially informative is the "generic ballot" question, which asks "If the elections for U.S. Congress were being held today, would you vote for the Republican Party’s candidate or the Democratic Party’s candidate for Congress in your district?" (Pew Research Center, 2002). As the countdown to Election Day dwindles, poll results are weighed more heavily, as they're believed to be more reflective of the electorate than previous polls. Additionally, pollster quality over the years is also considered to correct for past estimation errors. The models also account for a slew of other predictors, including president's party performance in special elections, the midterm-incumbent disadvantage, state-level partisan lean, and campaign finances.

## FiveThirtyEight
FiveThirtyEight has become a renowned forecasting site, and is transparent about its [methodology](https://fivethirtyeight.com/methodology/how-fivethirtyeights-house-and-senate-models-work/). In all versions of their House forecast, they consider thousands of polls (district-level), each weighted with pollster rating and quality in mind. For districts with low or no polling, they use the CANTOR system, which "infers results ... from comparable districts that do have polling." More complex versions of their modeling include the fundamentals (such as economics, fundraising, past elections results), and expert forecasts ([Cook Political Report](https://www.cookpolitical.com/ratings/house-race-ratings), [Inside Elections](https://insideelections.com/ratings/house), and [Sabato's Crystal Ball](https://centerforpolitics.org/crystalball/2022-house/)). 

## Comparison and Insights
For the most part, The Economist and FiveThirtyEight methodologies appear to be similar. On a high level, they share many predictors: polls (weighted according to recency, pollster quality), previous election results, campaign finances, and other fundamentals, like the midterm-incumbent effect. Some small, but important, differences lie in the specifics of each predictor - for example, The Economist relies more on generic ballot polling data, while FiveThirtyEight utilizes district-level candidate polls. FiveThirtyEight includes expert ratings and forecasts in their model, which The Economist does not. 

I personally prefer the FiveThirtyEight model due to its inclusion of expert ratings. Though it seems initially a bit circular to build a predictive model based, in part, on others' predictions, it ultimately makes sense -- other experts have made their best guesses for election results, and on average, they probably are onto something. This idea is reminiscent of Galton's (1907) "wisdom of the crowds," but the crowds consist of pollsters and election forecasters here.  

# My 2022 Model and Forecast, Updated
I take inspiration from the aforementioned models and update my own, incorporating national economic variables, the midterm-incumbent effect, and generic ballot polling data in order to predict the House incumbent party vote share for the upcoming election. 

The economic variables are the exact same as the ones from last week; see [here](https://vivian-1372.github.io/Election-Analytics/post/2022-09-19-local-and-national-economy/) for more information. The `PresParty-HouseInc-Midterm` variable is meant to capture the midterm-incumbent effect, taking a value of: `1` for election years when there is a midterm election *and* the House incumbent party is the president's party, `0` otherwise. In these years, we'd expect the House incumbent party to suffer in vote share, as it's known that the president's party, which is the same as the House incumbent party, performs poorly in midterm years. The generic ballot poll responses are taken into account via the `Generic Ballot Average Support` variable, which averages the generic ballot responses for each election year with respect to weighting that boost polls closer to Election Day, and dampen those farther out (Gelman and King, 1993). 

Below is a regression table of several model editions I considered. 


![My Updated Model.](bp3_regression.png)
The "National Econ" Model comes from last week, and only considers the national economic variables of Gross Domestic Product growth (percentage and absolute), unemployment rate, and Real Disposable Income (RDI) change %. The adjusted R-squared is only 0.292, and few variables are clearly predictive of vote share. 

Next, I consider a model using only the midterm-incumbent effect and generic ballot support. Impressively, these two variables alone have an adjusted R-squared of 0.552. 

The next two models consider economic variables and either polls, or the midterm-incumbent effect, but not both. It appears that the generic ballot polls provide more predictive power to my model than the midterm effect alone. 

## My New Model
Lastly, my final model considers all the predictors we've conceptually explored up until this point - generic ballot polling data *and* fundamentals like national economic variables and midterm-incumbent effect. My adjusted R-squared is 0.769, and GDP growth %, RDI change %, GDP absolute growth, midterm-incumbency, and generic ballot support are all found to be significant predictors of House incumbent party vote share. When GDP growth % and generic ballot average support increase, we expect incumbent vote share to increase. 

Interestingly, negative RDI growth % and absolute GDP growth, as well as it not being a midterm year for an incumbent party that's the president's party, are associated with increases in incumbent vote share. I plan to further investigate GDP growth in absolute terms versus percentage in future weeks -- it's fascinating because even though the two measures are correlated (0.7), removing one or the other significantly reduces the R-squared value of my model (by a magnitude of ~0.10). However, including both measures makes my model's economic coefficients appear counterintuitive! In future weeks, I also hope to include expert predictions in my model as FiveThirtyEight does.  

## My New Forecast
Considering the current economic conditions, generic ballot polls, and the fact that this year, the President's party is trying to hold onto their House incumbency in the midterms, my model predicts **the House incumbent party (D) will have a (two-party) vote share of 49.82%, with a confidence interval of (47.97, 51.66).** My updated model predicts the Democrats will have a two-party seat share of 51.46%, (48.17, 54.75), which is roughly 223 seats (208.73, 237.57). As Steve Kornacki said, if the Democrats are to hold onto their House majority, it seems that it'll be by the slimmest of margins. 









---

**References**

[1] The Economist Newspaper. (2022). How does the Economist's midterms election model work? The Economist. https://www.economist.com/the-economist-explains/2022/09/09/how-does-the-economists-midterms-election-model-work 

[2] Pew Research Center. (2002). Why the generic ballot test? Pew Research Center - U.S. Politics & Policy. https://www.pewresearch.org/politics/2002/10/01/why-the-generic-ballot-test/ 

[3] Silver, Nate. (2022). How fivethirtyeight’s house, senate and governor models work. https://fivethirtyeight.com/methodology/how-fivethirtyeights-house-and-senate-models-work/

[4] Galton. (1907). Vox Populi. Nature (London), 75(1949), 450–451. https://doi.org/10.1038/075450a0

[5] Gelman, & King, G. (1993). Why Are American Presidential Election Campaign Polls So Variable When Votes Are So Predictable? British Journal of Political Science, 23(4), 409–451. https://doi.org/10.1017/S0007123400006682

