---
layout: post
published: false
title: 'Capstone Chronicles: July 31, 2019'
subtitle: Writings on My Second Capstone Project
date: '2019-07-31'
---
Today I am going to focus on analyzing the process for developing the deep learning model of my second capstone project for Springboard. As a reminder, this project utilizes Stanford's [CheXpert](https://stanfordmlgroup.github.io/competitions/chexpert/) data set, which is comprised of approximately 224,000 chest x-rays from Stanford Hospital performed between 2002 and 2017. 

The competition for this specific project was based around detecting five 'competition' pathologies - atelectasis, cardiomegaly, consolidation, edema and pleural effusion. However, I am a deep learning 'noob' so to speak, so I decided to focus on just one pathology, cardiomegaly. 

Without further ado, let's jump in!

## The Beginning

If you venture over to the repository for my second capstone, you'll notice a folder called [`playground_nbs`](https://github.com/Jearny58/Springboard-DS-Portfolio/tree/master/capstone_2/playground_nbs), which contains all of the experiments I conducted during the process of developing the deep learning model. In summary, some ideas worked and some didn't; those that did, I took to the next notebook and tinkered with another tactic to try and increase the models performance. 

It was a fascinating process and I really enjoyed the process of experimenting with things like different architectures, learning rates, data augmentation, over/undersampling, and numerous other processes that I described in more detail later. Sure, it was hard at times to wait for the training to finish and could be slightly demoralizing when the performance didn't improve or even got worse...

But that's what being a scientist is, testing hypotheses and seeing if they work because what may sound good in theory may not always turn out so great in practice! 

Before I go any further though, I want to give a shout out a few sources that were significant sources of help getting this project off the ground.

- [Simon Grest](https://github.com/simongrest), walking through your notebook and seeing the results was amazing! Additionally, the tactics you used to create the data set, sample it, and utilize the validation data set to test during training, were a significant help! If interested, click this [link](https://github.com/simongrest/chexpert-entries/blob/master/replicating_chexpert.ipynb) to check out his work. 
- [Kerem Turgutlu](https://github.com/KeremTurgutlu), your setup for the ImageDataBunch helped me get a better understanding of the parameters involved with `get_transforms` and allowed me to tinker when it came to data augmentation. Additionally, your [question](https://github.com/stanfordmlgroup/chexpert-labeler/issues/9) regarding what to do with the missing values helped me address right away the correct approach. If interested, click this [link](https://github.com/KeremTurgutlu/chexpert/tree/master) to check out his work. 

Lastly, to the team at [fast.ai](https://www.fast.ai/), thank you so much for all the work you've done and continue to do for those of us who are interested in learning how to do deep learning! After completing the [Practical Deep Learning for Coders](https://course.fast.ai/) course, I was able to put together this project and generate a model with relatively high performance. There is so much more to learn but you've been instrumental in setting a solid foundation for my future growth so thank you again!

## Learning To Fly

How many times do you think it took the Wright brothers to get an airplane airbourne? How many years did it take Einstein to develop the Theory of Relativity? Perhaps better yet, how many Jupyter Notebooks did it take Joe Earnshaw to create a good deep learning model?

Like the Wright brothers and Einstein, it wasn't an overnight process that's for sure! 

To start with, there were a few initial aspects we needed to address, including: 

- _Data wrangling_: We needed to make sure the accompanyiny CSVs for the training and validation sets wouldn't hinder the learner. In our case, since this a deep learning computer vision task, the CSVs are not the primary data source however it is always best practice to ensure data is as clean as possible. 
- _Reproducibility_: This is a crucial aspect considering that we're going to be randomly sampling from the data. Why is this so important? Well, if we want to accurately compare the performance for two different model architectures, for example, we need to input the same data for both. If this is not done, comparing performance is null and void because you're comparing the models based on two different data sets! 
- _Sampling_: 224,000 images is a lot, and this presented the potential for long training times. We want to be able to iterate quickly and test new techniques without having to wait too long for training. 
- _Uncertainty_: There were uncertain labels in the training data set, which means the labeller couldn't confidently tell from the medical documentation where there was a certain pathology present or not. We need to determine a strategy to address these observations with the hope of maintaining as much information as possible. 
- _Performance Metrics_: The team at Stanford utilized AUROC (Area Under the Receiver Operating Characteristics) which we'll definitely use. However, are there any other metrics we can use to help us get a better idea of the models performance? 

### Cowboy Up! - Wrangling the CheXpert Data Set

This was arguably the toughest task of the entire project and most of the experimental Jupyter Notebooks were devoted to determining different strategies to get the data in a format suitable for training the deep learning model on. All this experimentation though paid off though and culminated in the creation of the [`sample.py`](https://github.com/Jearny58/Springboard-DS-Portfolio/blob/master/capstone_2/capstone/sample.py) Python script. 

By simply calling `from capstone import sample` we were able to access all the custom functions created to handle wrangling the CheXpert CSV data. However, there was a prerequisite step that we had to take before we could utilize all these handy functions and that was to set the `path` variable. 

What this variable essentially does is take note of the location of our data, which in this specific case is `'/home/jupyter/springboard-capstone-2/data'`. This was configured using fast.ai's [`Config.data_path()`](https://forums.fast.ai/t/how-to-set-config-data-path-to-pwd/36297), which allows us to create a path both so we can access our data (i.e. the X-rays) and to save our model weights during training. By default it creates a temporary, and hidded, folder called `.fastai` so after a little bit of playing around we were able to set the path to the one mentioned previously. This is where I want to mention that played around with the path is somewhat tricky (and potentially lengthy) ordeal, especially for someone not used to dealing with folders and working directories. Another strategy that could be used here is to set the path variable using the [`os`](https://docs.python.org/3/library/os.html) Python library. There is more documentation for it which will probably make it easier to use as opposed to the `Config.data_path()` technique. 


