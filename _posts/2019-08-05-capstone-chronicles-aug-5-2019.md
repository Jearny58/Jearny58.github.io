---
layout: post
published: true
title: 'Capstone Chronicles: Aug. 5, 2019'
subtitle: Last Part of Analysis for Second Capstone Project
date: '2019-08-05'
image: /img/auroc_curve.png
excerpt: >-
  As indicated in my weekly accountability post, my main goal for this week is
  to finish the comprehensive analysis of my second capstone project.
---
### Another Day, Another Capstone Chronicle!

As indicated in my [weekly accountability post](https://jearny58.github.io/2019-08-04-staying-accountable-week-of-aug-4th/), my main goal for this week is to finish the comprehensive analysis of my second capstone project. This is the fourth, and hopefully last, section I'll be posting after which I combine all these posts into one coherent analysis. 

I'm going to begin by talking a little about the performance metrics I used - AUROC, recall, and precision - what they mean, and why I chose them. Next, I discuss the strategy I used to train my deep learning model (i.e., the `DenseNet121`) to detect cardiomegaly within the images from CheXpert. Lastly, I'll briefly outline my results and any potential strategies I could utilize in the future to try and improve my results.

Without further ado, let's jump right in!

## The Metrics: AUROC, Recall, and Precision

When it comes to classification problems, there are quite a few metrics one could use. The most obvious is accuracy, which takes the number of correct predictions and the total number of predictions (i.e., the number of data points). 

For a model that classifier that labels images as either a cat or a dog, accuracy may be a suitable metric. However, from the CheXpert data, we are trying to build something that has the potential to assist medical professionals detects diseases and quite literally save lives. To say the stakes are a little higher may be an understatement. 

Due to this fact, we are going to use AUROC as the primary metric. This metric is an extension of the ROC curve (i.e., the green line below), which is plotted using the true positive rate on the y-axis and false positive rate on the x-axis (see below). In our case, a true positive is represented by positive prediction on an observation that did indeed have cardiomegaly; a false positive would be a positive prediction on an observation that did __not__ have cardiomegaly. 

![auroc_curve.png](/img/auroc_curve.png)

[Source](https://towardsdatascience.com/understanding-auc-roc-curve-68b2303cc9c5)

Now AUROC takes the ROC curve a step further and assesses the area underneath that curve, which it is otherwise known as **A**rea **U**nder the **R**eceiver **O**perating **C**haracteristics. 

A perfect classifier would have an area of 1 (i.e., the graph would go straight up and over, resembling a flipped L). What does the dashed line that cuts through the middle of the graph represent? This line represents an AUROC of 0.5, which would theoretically represent a classifier that's no better than random at detecting cardiomegaly. 

Now what are [recall and precision](https://scikit-learn.org/stable/auto_examples/model_selection/plot_precision_recall.html) and what do they have to do with this analysis? 

Recall represents the number of true positives divided by the sum of true positives and false negatives. What recall can tell us is how good the model is at finding the data points of interest (i.e., observations that have cardiomegaly). Precision, on the other hand, represents the value of true positives divided by the sum of true positives and false positives which tells us the proportion of observations that our model said was relevant that actually were. 

__Recall & Precision In Context__

- _RECALL_ = Cases of cardiomegaly correctly identified divided by (cardiomegaly cases correctly identified __+__ cardiomegaly cases incorrectly labeled as no cardiomegaly)
- _PRECISION_ = Cases of cardiomegaly correctly identified divided by (cardiomegaly cases correctly identified __+__ no cardiomegaly cases incorrectly labeled as having cardiomegaly)

Ideally, we want a high recall and precision as this indicates that the model can identify not just only relevant instances but all of them as well. 

## The Strategy: Training the Model

In the [`trial30`](https://github.com/Jearny58/Springboard-DS-Portfolio/blob/master/capstone_2/trial30.ipynb) Jupyter notebook, you can see there are five 'rounds' where we trained the model. Let's begin by getting a general overview of what is going on each round. 

![rd_1.png](/img/rd_1.png)

We utilized fast.ai's [`fit_one_cycle()`](https://docs.fast.ai/train.html#fit_one_cycle) method on our `learn` object, which contains our deep learning model. This particular method utilizes what is called the 1cycle policy, which can train complex models in significantly less time. It involves hyper-parameters such as learning rate, momentum, and weight decay, which are all subjects I do not feel quite qualified to discuss in detail. However, Sylvain Gugger, who is also a part of the fast.ai team, wrote a great [blog post](https://sgugger.github.io/the-1cycle-policy.html) on the 1cycle policy that you should check out if you're interested in learning more. 

The top cell includes the Python code we use to initiate training while the bottom cell corresponds to the output, where the `auroc`, `recall` and `precision` scores can be easily assessed. Also, `error_rate` is included to give an additional high-level perspective on how good our model is doing.

Within the coding cell, we can see that `fit_one_cycle()` includes the following: `5`, representing the number of epochs; `9e-3` which is the learning rate; and the `SaveModelCallback`, which allows us to save our best performing model (as judged by `auroc`). 

Before we go any further, let's answer the following questions: what is an epoch, and what is the learning rate? 

An epoch represents the case when an entire dataset is passed forward and backward through a neural network one time. Now, why did we train for five epochs? Good question. Unfortunately, to my knowledge, there is no right answer when it comes to the best number of epochs to use. Generally, things like training time and diversity of the data should be taken into consideration when considering epochs, but there is also quite a bit of experimentation required. I found 3-5 epochs to be the best range for this particular project, as it didn't require too much time, and the results tended to be good. 

Now, onto learning rate; what is it? 

Learning rate affects how much the weights of the network are adjusted with respect to the loss. This is otherwise known as [gradient descent](http://wiki.fast.ai/index.php/Gradient_Descent). 

![Gd_demystified.png](/img/Gd_demystified.png)

[Source](http://wiki.fast.ai/index.php/Gradient_Descent)

Take a look at the picture above. Essentially the goal of gradient descent, given some higher dimensional space, is to find the minimum point (i.e., the bottom of the curve). Once this minimum (i.e., the 'winner') is reached, the corresponding model and its weights are optimal and return the minimum loss possible for that given data set. Now the learning rate comes into play in how fast we descend to this minimum (indicated by the arrows in above image). Notice how the dots are progressively getting closer to the minimum/'winner' point. A lower learning rate would take much longer to reach this 'winner' point if it even reaches it at all. On the flip side, if the learning rate is set too high, it runs the possibility of overshooting the minimum or even diverging out of the curve entirely. 

With all the bases covered, let's take a look at the key characteristics from each round. 

- _Rd. 1_: Original data set, 5 epochs, utilized [`SaveModelCallback`](https://docs.fast.ai/callbacks.html#SaveModelCallback) to save best performing model based on AUROC
- _Rd. 2_: Loaded best performing weights from rd. 1 (`trial30-rd1`), unfroze entire model allowing every layer group to be trainable [(`unfreeze()`)](https://docs.fast.ai/basic_train.html#Learner.unfreeze), set learning rate to ~`6.31e-07` then passed [`slice()`](https://docs.fast.ai/basic_train.html#Learner.lr_range) to allow for differential learning rate, trained for 5 epochs
- _Rd. 3_: Set new seed, generated a new randomly sampled data set of images (~40,000), loaded best performing weights from rd. 2 (`best-dn121-trial30-rd2`), unfroze entire model to allow every layer to be trainable, set learning rate to ~`5.01e-07`) then passed to [`slice()`](https://docs.fast.ai/basic_train.html#Learner.lr_range) to allow for differential learning rates, trained for 5 epochs
- _Rd. 4_: Set new seed, generated a new randomly sampled data set of images (~40,000), loaded best performing weights from rd. 3 (`best-dn121-trial30-rd3`), froze model up to last layer group [`freeze()`](https://docs.fast.ai/basic_train.html#Learner.freeze), set learning rate equal to `3e-06` then passed to [`slice()`](https://docs.fast.ai/basic_train.html#Learner.lr_range) to allow for differential learning rates, trained for 3 epochs
- _Rd. 5_: Loaded best performing weights from rd. 4(`best-dn121-trial30-rd4`), unfroze model, set learning rate to ~`2.51e-07` then passed `slice(lr, 3e-4)` to allow for differential learning rates, trained for 3 epochs

## Results & Future Improvements

![last_round](/img/last_round.png)
(_Output from last round of training_)

The image above is the output from our fifth and final round of training. When can see that the `auroc` steadily increases from ~0.81 to ~0.83, indicating a gradual increase in overall performance. As a reminder that when utilizing the `U-Zeros` approach, the team at Stanford achieved an AUROC of 0.84, meaning that, as is, this model is nearly at the same level! 

However, there is still much room for improvement. Firstly, the `recall` score is pretty low,  indicating a model that is perhaps a little too picky. The `precision` score is relatively high (although it too could be higher) meaning that for the most part, the images it thinks contain cardiomegaly, do have cardiomegaly. Yet, it misses a lot of other actual cases of cardiomegaly because it's so picky. 

![confusion_matrix](/img/confuse_matrix_2.png)

For a visual representation lets take a look at a confusion matrix from the fourth round of training. (Side note: the reason for taking confusion matrix from the fourth round as opposed to the fifth is because the model achieved a `recall` score in the mid-0.50's, slightly higher than that in the fifth.) If you take a look at the bottom left corner, we find 30 false-negative cases. This is nearly 13% of the validation data set and is cause for serious concern as these 30 observations represent cases where cardiomegaly was present, but the model predicted that there was no cardiomegaly. In the context of the situation, a false negative is very bad; it means that a patient who is sick doesn't get the treatment they need. So for future iterations of this model, we would need to figure out how to reduce the number of false negatives to ensure more cases of cardiomegaly are captured. 

__Closing Thoughts__

Overall, this has been a very challenging but overall highly rewarding project. While the model we produced here is not ready to be used in any medical context, it highlights the enormous potential for artificial intelligence/deep learning within the healthcare space, especially with continued research. 

This is just the first step of my deep learning journey, though, and I'll be looking to continue to grow my body of knowledge related to the subject. In particular, I hope to continue to combine the technical with the medical and hopefully contribute to healthcare in such a way that helps people live healthier and happier lives. 

Thank you for taking the time to read this analysis and hope you got some valuable insights along the way. However, with that being said, it's time to get back to work!
