---
layout: post
published: false
title: 'Capstone Chronicles: Aug. 5, 2019'
---
### Another Day, Another Capstone Chronicle!

As indicated in my [weekly accountability post](https://jearny58.github.io/2019-08-04-staying-accountable-week-of-aug-4th/), my main goal for this week is to finish the comprehensive analysis of my second capstone project. This will be the fourth, and hopefully last, section I'll be posting after which I will combine all these posts into one coherent analysis. 

I'm going to begin by talking a little about the performance metrics I used - AUROC, recall, and precision - what they mean, and why I chose them. Next I will discuss the strategy I used to train my deep learning model (i.e. the `DenseNet121`) to detect cardiomegaly within the images from CheXpert. Lastly, I'll briefly outline my results and any potential strategies I could utilize in the future  to try and improve my results.

Without further ado, let's jump right in!

## The Metrics: AUROC, Recall, and Precision

When it comes to classification problems, there are quite a few metrics one could use. The most obvious is accuracy, which simply takes the number of correct predictions and the total number of predictions (i.e. the number of data points). 

For a model that classifier that labels images as either a cat or a dog, accuracy may be a suitable metric. However, from the CheXpert data we are trying to build something that has the potential to assist medical professionals detects diseases and quite literally save lives. To say the stakes are a little higher may be an understatement. 

Due to this fact, we are going to use AUROC as the primary metric. This metric is an extension of the ROC curve (i.e. the green line below), which is plotted using the true positive rate on the y-axis and false positive rate on the x-axis (see below). In our case, a true positive is represented by positive prediction on an observation that did indeed have cardiomegaly; a false positive would be a positive prediction on an observation that did __not__ have cardiomegaly. 

![auroc_curve.png](/img/auroc_curve.png)

[Source](https://towardsdatascience.com/understanding-auc-roc-curve-68b2303cc9c5)

Now AUROC takes the ROC curve a step further and assesses the area underneath that curve, which it is otherwise known as __A__rea __U__nder the __R__eceiver __O__perating __C__haracteristics. 

A perfect classifier would have an area of 1 (i.e. the graph would go straight up and over, resembling a flipped L). What does the dashed line that cuts through the middle of the graph represent though? Simply put, this line represents an AUROC of 0.5, which would theoretically represent a classifier that's no better than random at detecting cardiomegaly. 

Now what are [recall and precision](https://scikit-learn.org/stable/auto_examples/model_selection/plot_precision_recall.html) and what do they have to do with this analysis? 

Recall represents the number of true positives divided by the sum of true positives and false negatives. Essentially what recall can tell us is how good the model is at finding the data points of interest (i.e. observations that have cardiomegaly). Precision, on the other hand, represents the value of true positives divided by the sum of true positives and false positives which tells us the proportion of observations that our model said was relevant that actually were. 

__Recall & Precision In Context__

- _RECALL_ = Cases of cardiomegaly correctly identified divided by (cardiomegaly cases correctly identified __+__ cardiomegaly cases incorrectly labeled as no cardiomegaly)
- _PRECISION_ = Cases of cardiomegaly correctly identified divided by (cardiomegaly cases correctly identified __+__ no cardiomegaly cases incorrectly labeled as having cardiomegaly)

Ideally, we want a high recall and precision as this indicates that the model has the ability to identify not just only relevant instances but all of them as well. 

## The Strategy: Training the Model

In the [`trial30`](https://github.com/Jearny58/Springboard-DS-Portfolio/blob/master/capstone_2/trial30.ipynb) Jupyter notebook, you can see there are five 'rounds' where we trained the model. Let's begin by taking a look at what is going on in the first round. 

![rd_1.png](/img/rd_1.png)



