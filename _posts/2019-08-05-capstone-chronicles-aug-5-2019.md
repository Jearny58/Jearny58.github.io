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

Due to this fact, we are going to use AUROC (otherwise known as __Area Under the Receiver Operating Characteristics__) as the primary metric. This metric is an extension of the ROC curve, which is plotted using the true positive rate on the y-axis and false positive rate on the x-axis (see below). 

