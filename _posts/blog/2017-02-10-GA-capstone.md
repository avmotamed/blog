---
layout: post
title: "GA Capstone Project - Predicting Recommended Content Selection"
modified:
categories: blog
excerpt:
tags: []
image:
  feature:
date: 2017-02-10T08:08:50-04:00
---

# ***Predicting Recommended Content Selection***


## Introduction
I was motivated by a [Kaggle](https://www.kaggle.com/c/outbrain-click-prediction) competition that worked with a very large set of data regarding the ability to predict which recommended content a user would select. For the first leg of this project, I wanted to use a local machine to prove the viability of different modeling methods. If I could show an improvement in predictability running on a single machine, then I could scale up and run larger data sets in a more robust environment, such as AWS or another distributed computing environment.

While this data and competition look at events where a selection of recommended content is made, the goal is to find factors that help better predict the kind of content would want to select. By doing this, you would be able to improve the 'clickability' of all the recommended content and therefore better match content to users and increase the overall likelihood of a click.


## Summary and Basic Findings
The aim for this analysis was to run it on a local machine and show an improvement over baseline prediction rates. I should note that the original sample size was almost 23 million events (clicks on recommended content), and that to run the preliminary analysis I randomly selected 10,000 events from US based users (US users made up over 18 million events). Each event had between two and 12 recommended pieces of content for the user, with the average being just over five items per page, bringing my dataset to a total of over 50,000 rows.

Using various classification models (Decision Tree, Random Forrest, and Gradient Boost), as well as a Logistic Regression, the model was able to correctly predict the selection 39% of the time, a noticeable improvement over the baseline of 19%. The figure below shows the prediction rate by content/ads per page. Note that for seven and 12 items, the prediction rate was fairly low as each of these two categories made up less than 1% of the data.

<figure>
	<img src="{{site.url}}/images/capstone_prediction.png" alt="image">
	<figcaption>Prediction Rate vs Baseline - Overall Rate = 39%</figcaption>
</figure>

## Description of Data and Feature Engineering

The data came from a Kaggle competition sponsored by Outbrain. It consisted of a number of tables that provided the following:
  * The content/ads provided on each source page
  * The topics of both the source page and the ads (and Outbrain's confidence in the relevance)
  * Data on the ad campaign
  * User data including unique ID, time of click, location, platform (pc, mobile, tablet)


### Selections/Ads per Source Page
The ads were mostly concentrated in groups of four or six, making up over 60% of the mix. The sample set matched the overall full data set profile.
<figure>
	<img src="{{site.url}}/images/capstone_per_page.png" alt="image">
	<figcaption></figcaption>
</figure>

### Time of day
The heatmap below shows when the clicks occurred. The full data set was taken over two weeks in June 2016, with weekend activity a little lighter than during the week. The greatest density was mid-day with another grouping in late evenings. I broke the time up into basic buckets - morning, afternoon, early evening and overnight.

<figure>
	<img src="{{site.url}}/images/capstone_click_map.png" alt="image">
	<figcaption></figcaption>
</figure>

### User Platform

The screen size and environment in which the user is browsing may speak to the type content and time and consideration they are willing to give to selecting recommended content. The data had a healthy split between mobile and desktop/laptop users, with tablet users making up less the 15% of the mix.

<figure>
	<img src="{{site.url}}/images/capstone_platform.png" alt="image">
	<figcaption></figcaption>
</figure>

## Model Building and Analysis

I began using a Random Forest Classification model to predict the probability of a user clicking on each add in the display, and then expanded to other classification models as well as a Logistic Regression. In moving onto grid searching, I found that using precision provided better results than accuracy. This is in part due to the unique circumstances of what is being predicted.

### Scoring The models

In using the standard accuracy/precision measures and confusion matrix, aside from accurate predictions you are also scoring the false positives and false negatives. In this case, since there is definitely a click for each group and not for each row of data, I needed an alternative method of scoring outside the methods built into Scikit Learn.

Taking the probability of a click on each row, I then examined each grouping and picked the item with the highest probability of being clicked as the choice, with all other selections on the page then being set to not clicked.

### Model Performance

As can be seen below, the Decision Tree Classifier converged on its results with the least number of features. The tradeoff on this convergence being the lower prediction rate versus the other models. The Random Forest had the highest accuracy, but took the largest set of features to achieve those results. The Gradient Boost Classifier had the best balance of prediction rate versus number of features required. The Logistic Regression achieved a 39% accuracy, slightly better than the Random Forrest, with a run time in-line with the other models.

<figure>
	<img src="{{site.url}}/images/capstone_models.png" alt="image">
	<figcaption></figcaption>
</figure>

### Feature Importance
Two of the most important groups of features in helping predict which content would be selected were:
  * Time of Day: afternoon and evening were the two most influential
  * Ads per Page: this group dominated five of the top 10 features.

## Further Work

The next step is to move to an expanded set of resources so be able to run a larger sample set, if not the entire event history provided.

Many users, almost 90%, only clicked on one content/ad selection, making it difficult to predict patterns for individual users. Outbrain did provide a larger data set on browsing history for users, but the size of the dataset (over two billion rows) made it very difficult to analyze on a local machine. This is an area to examine going forward with expanded resources.

The jupyter notebooks of the full code for these analysis can be found [here](http://localhost:8888/tree/Projects/Predicting_Click_Through).
