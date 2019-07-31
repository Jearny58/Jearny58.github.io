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

## Starting the Climb

