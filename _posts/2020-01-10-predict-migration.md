---
layout: post
title: Predict Migration
subtitle: Mission Impossible?
gh-repo: Okocha76
bigimg: /img/daniel-schludi-e1RI3wRelqM-unsplash.jpg
tags: [europe, migration]
---

_Migration is the main force behind population change in Europe.  This is one of the main conclusions from my previous blog post.  So would it be possible to predict future migration?_

The base predictive model uses data from Eurostat over the period 2000-2017. Numerous variables have an influence on migration.  So I collected and merged many datasets that might be useful. Cleaning the data was time-consuming, because of missing values and differences between datasets. I split the dataset into a train (2001-2012), validation (2013-2015) and test (2016) dataset. 

To predict whether the migration rate in an area will rise or fall in the next year is the target. For this classification problem, you would expect a majority baseline close to 50%. And indeed, this is the case, 51.4% migration rate decreases the next year.

In an attempt to beat this baseline prediction, I start out with  Logistic Regression. The model uses all the features in the dataset, One Hot Encoding categorical features. Model accuracy is 56.9%.

Next, I try a Random Forest Classifier and model accuracy drops to 55.6%. Feature importances gives some insight for feature selection, but permutation importances goes deeper (left). 

:-------------:|:-------------:
[ ![](/img/permutation_imp.png) ](/img/permutation_imp.png) | [ ![](/img/xgboost.png) ](/img/xgboost.png)

The next models consist of features with permutation importance > 0. Model accuracy rises to 61.4% using the selected features and a XGBoost Random Forest Classifier (right).

Partial Dependency Plots show the marginal effect one or two features have on the predicted outcome. Below on the left shows a single PDP for net migration rate. On the right, there is a PDP interact for net migration rate and GDP per inhabitant in percentage of the EU average. You can also fiddle with the PDP interact in 3D.

:-------------:|:-------------:
[ ![](/img/pdp_mig_rt.png) ](/img/pdp_mig_rt.png) | [ ![](/img/pdp_interact.png) ](/img/pdp_interact.png)

<iframe id="pdp-interact" scrolling="no" width="100%" height="500px" src="/img/3d-pdp.html"></iframe>

Shapley Value Plots help explain model predictions. With a prediction value < 0.5 the model expects migration rate to drop next year. Whereas a model outcome > 0.5 predicts a rise in migration rate.
![](/img/shapley2.png)

I rerun the Logistic Regression model with the selected features and model accuracy increases to 59.5%. Model coefficients below.
![](/img/logistic.png)

As a small bonus, below you see an interactive map from Eurostat.

<script src="https://d3js.org/d3.v4.min.js"></script>
<script src="https://d3js.org/d3-queue.v3.min.js"></script>
<script src="https://d3js.org/topojson.v1.min.js"></script>
<script src="https://d3js.org/d3-color.v1.min.js"></script>
<script src="https://d3js.org/d3-interpolate.v1.min.js"></script>
<script src="https://d3js.org/d3-scale-chromatic.v1.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/d3-legend/2.25.6/d3-legend.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/jsonstat@0.13.3/json-stat.js"></script>
<script src="https://cdn.jsdelivr.net/gh/eurostat/eurostat.js@0.9.15/js/eurostat-lib.js"></script>
<script src="https://cdn.jsdelivr.net/gh/eurostat/eurostat.js@0.9.15/js/eurostat-map.js"></script>
<script src="https://cdn.jsdelivr.net/gh/eurostat/eurostat.js@0.9.15/js/eurostat-tooltip.js"></script>

<svg id="map"></svg>

<script>
	EstLib.map()
	.width(900)
	.scale("10M")
	.NUTSyear(2016)
	.datasetCode("demo_r_gind3").nutsLvl(3).filters({ indic_de:"CNMIGRATRT", time : 2017 })	
	.classifMethod("threshold").threshold([-15,-10,-6,-4,-2,0,2,4,6,10,15])	
	.tooltipShowFlags(false)
	.unitText(["‰"])
	.legendTitleText("Crude rate of net migration 2017 (in ‰)")
	.legendLabelDecNb(0)
	.legendBoxHeight(340)
	.legendBoxWidth(150)
	.build();
</script>

**Source Datasets:** [Eurostat](https://ec.europa.eu/eurostat/web/main/home){:target="_blank"}  
**Notebook:** [![Colab Notebook](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github.com/Okocha76/Okocha76.github.io/blob/master/Migration_In_Europe.ipynb){:target="_blank"}
