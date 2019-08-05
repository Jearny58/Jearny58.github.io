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
	- _Rd. 1_: Original data set, 5 epochs, utlized [`SaveModelCallback`](https://docs.fast.ai/callbacks.html#SaveModelCallback) to save best performing model based on AUROC
    - _Rd. 2_: Loaded best performing weights from rd. 1 (`trial30-rd1`), unfroze entire model allowing every layer group to be trainable [(`unfreeze()`)](https://docs.fast.ai/basic_train.html#Learner.unfreeze), set learning rate to ~`6.31e-07` then passed [`slice()`](https://docs.fast.ai/basic_train.html#Learner.lr_range) to allow for differential learning rate, trained for 5 epochs
    - _Rd. 3_: Set new seed, generated a new randomly sampled data set of images (~40,000), loaded best performing weights from rd. 2 (`best-dn121-trial30-rd2`), unfroze entire model to allow every layer to be trainable, set learning rate to ~`5.01e-07`) then passed to [`slice()`](https://docs.fast.ai/basic_train.html#Learner.lr_range) to allow for differential learning rates, trained for 5 epochs
    - _Rd. 4_: Set new seed, generated a new randomly sampled data set of images (~40,000), loaded best performing weights from rd. 3 (`best-dn121-trial30-rd3`), froze model up to last layer group [`freeze()`](https://docs.fast.ai/basic_train.html#Learner.freeze), set learning rate equal to `3e-06` then passed to [`slice()`](https://docs.fast.ai/basic_train.html#Learner.lr_range) to allow for differential learning rates, trained for 3 epochs
    - _Rd. 5_: Loaded best performing weights from rd. 4(`best-dn121-trial30-rd4`), unfroze model, set learning rate to ~`2.51e-07` then passed `slice(lr, 3e-4)` to allow for differential learning rates, trained for 3 epochs
- Discuss results and confusion matrix
- Discuss reasoning for utilizing new data in rounds three and four
- Discuss what you could've done differently and how it might've impacted performance

I think there is plenty here to keep you busy! So stay focused and get it done, you got this Joe!

__2. Review for mock interviews__

I guess I should've put an asterisk next to the percentage I mentioned earlier. While I am almost done with the curriculum, I still have three mock interviews I have to pass: coding, general data science, and a project walkthrough. 

To be completely honest, I'm a little bit nervous about these. Yet, they present awesome opportunities to get first-hand experience with the recruiting/interviewing process as well as the chance to maybe learn a thing or two from the Springboard mentors! Just have to refresh my 'vanilla' Python skills (no pandas, numpy, etc. are allowed!). 

__3. [A Cloud Guru](https://acloud.guru/)

Cloud computing isn't going anywhere and from what I've been picking up via Medium and assorted DS-related blog posts is that being comfortable in the cloud is going to be more and more important for data scientists. 

I came across A Cloud Guru awhile back and I love how their 'walkthrough'-like tutorials that give you the ability to work with things like AWS firsthand! Even better is that they offer a course for the [AWS Certified Machine Learning](https://aws.amazon.com/certification/certified-machine-learning-specialty/) certification, which I think will be extremely beneficial going forward. 


