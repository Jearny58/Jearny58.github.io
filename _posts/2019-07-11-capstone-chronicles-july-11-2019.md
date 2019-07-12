---
layout: post
published: true
title: 'Capstone Chronicles: July 11, 2019'
subtitle: Notes & Updates from my work on July 11
date: '2019-07-11'
image: /img/heart_rate.jpeg
---
Here we go! Another day, some more work on my second capstone project. It'll be interesting to see what's in store for today. After the progress yesterday, I'm excited to see if there can be further improvements made to the model and its ability to correctly classify cardiomegaly.


![heart_rate.jpeg]({{site.baseurl}}/img/heart_rate.jpeg)


Yesterday I wrapped up my post rather abruptly. Due to an error I made, I overwrote the weights from my best model, which is a reminder always to be careful when you copy and paste! Unfortunately, I was not able to replicate the results either with further training. Albeit, I was still able to produce a model that had an AUROC of ~0.81, which is a lot better than my efforts from the previous weeks. Additionally, I'd like to point out an interesting observation during my training in regards to the following metrics: precision and recall. 

It seems that at the early stages of training recall will be higher than precision, but as the model runs through more epochs, precision gets higher and recall goes down. I wonder why this is happening? 

Here are a few other questions regarding training/data that I hope to address today/near future:

- At what stage should I institute custom learning rates versus letting the model use the default of 0.003?
- When do I use custom learning rates?
- When should I utilize the slice() function, to train lower and higher layers at different rates (i.e., discriminative layer training)?
- Are there any other callbacks that I can utilize to ensure I'm gathering the best possible weights for the model? 
- Is there a way to create a smaller sample data set from the undersampled data (which has ~54k observations) and will this open the possibility to the model overfitting? 
- Are there any strategies I could utilize in regards to data augmentation? 

## Today's Game Plan

The first thing I want to do is to continue training the model from yesterday (i.e., from trial 28) with an image size of 320. As I've said in previous posts, this is the size that the team at Stanford used for their training, and based on this fact, my model will need to perform well on this image size. After getting the image data set up, assigning it to the learner, and loading yesterday's weights, I'll explore the effect of various learning rates on the model. Additionally, this will be done with a 'frozen' and 'unfrozen' model, in that we'll be seeing the learning rates effect on a model where every layer group is trainable ('unfrozen'), and only the last layer group is trainable ('frozen'). 

As gains in performance start to get harder to achieve, we'll have to explore many options to see what works best for fine-tuning the model. With that being said, I think the 'best' place to start would be to utilize fast.ai's `slice(lr1, lr2)` function. This allows us to assign two learning rates, with the `lr1` being the learning rate assigned to the first group and `lr2` being assigned to the last group with the remaining being evenly spaced. 