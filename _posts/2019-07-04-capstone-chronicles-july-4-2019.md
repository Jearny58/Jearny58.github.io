---
layout: post
published: false
title: 'Capstone Chronicles: July 4, 2019'
date: '2019-07-04'
subtitle: Updates from my work on July 4th
image: /img/ibm_medical_image_analysis.jpeg
---
Another day, some more deep learning!

As a quick recap, yesterday's work had some highs and lows:

1. We were able to get the AUROC score into the mid-high 0.70s with less training. 
2. Increasing image size progressively with training iterations resulted in big jumps but appeared to offer little benefit as AUROC would plateau with more training (and image size held the same). 
3. The script looks to be working nicely! It has given us the ability to minimize the code in each Jupyter Notebook, which makes it more aesthetically pleasing. 
4. For the most part, we heled the learning rate constant at 0.3. I think that holding the rate constant for each iteration of training might be the right call. There is something called super-convergence, and a paper on the subject seems to suggest that by utilizing one learning rate cycle and a high maximum learning rate, one could achieve speedy training of neural networks. It is something I'll have to look more into though. 

## Plan For Today

Today I want to experiment with three primary factors in our quest to build a high-performing model.

1. Sample Size
2. Dealing with Uncertainty (U-Zero vs. U-Ignore)
3. Image Size

My hypothesis for the day is to take a slightly larger sample size, say around 10-15% of the original CheXpert data set instead of the default of 5%. Why? Because yesterday I used the U-Zero method, which replaced uncertain labels with the label of 0 (i.e., no indication of the pathology). Today I want to explore the U-Ignore method, which drops all the uncertain observations. 

To mitigate the fact that we'll be decreasing our effective data set size with the U-Ignore method (versus simply relabeling them), I will increase the sample data size. I think a sample size of 12% is a good start, as it essentially doubles the data set compared to the one we used yesterday by accounting for the observations we'll lose due to using the U-Ignore method. 

Lastly, I think that instead of starting with an image size of 64 (as we did yesterday) and progressively increasing the size with each iteration of training, we'll start with an image size of 320. The rationale behind this is that the team at Stanford utilized this image size throughout their model training phase. Why I'm just now testing this hypothesis is twofold: first, larger images equal more training time which equals more money spent; secondly, starting with smaller image size sand increasing them progressively was an effective deep learning tactic in fast.ai's course. 

In summary, our training today will revolve around observing the effects of the following:

1. A sample size of 12% 
2. U-Ignore Uncertainty approach
3. Image size of 320 throughout training

Also, what about potentially experimenting with model architecture? Might be cool to see what a DenseNet161 can do, just saying...

## Metric Research

Today I want to focus on AUROC because I feel like my overall understanding of it is not as deep as it should be. 

To begin, let's first take a look at the ROC Curve, otherwise known as the receiver operating characteristic curve. Try to say that three times as fast...

A [ROC curve](https://developers.google.com/machine-learning/crash-course/classification/roc-and-auc) is a graph that helps visualize the performance of a classification model by plotting two parameters:

1. True Positive Rate 
2. False Positive Rate 

Quick reminder: True Postive Rate is the same as Recall.

TPR = TP / (TP + FN)

The equation for the false positive rate (FPR) can be seen here:

FPR = FP / (FP + TN)

This is where AUROC (or just AUC) comes into play as it measures the area underneath the entire ROC curve. It ranges in value from 0 to 1, with a model having an AUC value of 1.0 if its predictions are 100% correct, and an AUC of 0.0 if it's predictions are 100% wrong. 

## Today's Results

To be honest, disappointing. I got the AUROC up into the mid-0.70 range. However, I came to the realization today that my model is almost worthless. It was correctly predicting the instances where there was no pathology but only predicted three out of a possible 68 instances of where there was a positive label for the presence of the pathology. 

This was a wake-up call and highlighted the critical importance of checking the results to see what they are! Don't just look at the numbers, actually go into the results and see what they're producing. 

However, there was one exciting development in today's experimenting. I focused on my main points of increasing sample size, utilizing the U-Ignore approach, and setting image size at 320. The fascinating development was when I did not set a specific learning rate at all; instead, I used fast.ai's defaults. When I did this, the AUROC trended upward while the error rate did tick downwards slightly below 0.28, which was a first. 

I think a good starting point for tomorrow would be to continue with the 'trial21' notebook and continue training to see what I can find. I may be abandoning these models too quickly and not giving the learner's enough time to learn! 
