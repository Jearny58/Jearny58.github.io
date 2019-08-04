---
layout: post
published: false
title: 'Staying Accountable: Week of Aug. 4th'
---
### Staying Accountable
Today while I was driving I came up with the idea that at the start of every week, I should lay out a general game-plan of what I hope to accomplish. A little over three hours later here I am! Being the first post of its kind, I don't really know how to best approach it. Yet, it's always better to get something out there into the world than to let it stay locked up in your mind.

Without further ado, here we go!

## 3 Main Goals

__1. Complete the comprehensive analysis for my second capstone project__

At this moment, I am 88.2% complete with Springboard's Data Science Career Track! Hard to believe that this journey started last December...

Anyways, the last thing that I have to do is turn in the final submission of my project. The only thing left is the comprehensive analysis detailing the process I used to develop my deep learning model and the results it got. 

As you can see [here](https://jearny58.github.io/2019-07-31-capstone-chronicles-july-31-2019/), [here](https://jearny58.github.io/2019-08-01-capstone-chronicles-aug-1-2019/), and [here](https://jearny58.github.io/2019-08-02-capstone-chronicles-aug-2-2019/), there has been quite a bit of progress made on this particular front. Now the question is, what else do I need to cover?

- The performance metrics - AUROC, recall and precision - and what they tell us about the model's performance.
	- AUROC is the primary metric for assessing the model, but recall and precision gave us a little better idea of what was going on.
- Discuss the strategy I used for each round of training. Below are some characteristics of each round (that I'll expand upon in the comprehensive analysis). 
	- Rd. 1: Original data set, 5 epochs, utlized [`SaveModelCallback`](https://docs.fast.ai/callbacks.html#SaveModelCallback) to save best performing model based on AUROC
    - Rd. 2: Loaded best performing weights from rd. 1 (`trial30-rd1`), unfroze entire model allowing every layer group to be trainable ([`unfreeze()`](https://docs.fast.ai/basic_train.html#Learner.unfreeze), set learning rate to ~`6.31e-07` then passed [`slice()`] to allow for differential learning rate, trained for 5 epochs
    - Rd. 3

