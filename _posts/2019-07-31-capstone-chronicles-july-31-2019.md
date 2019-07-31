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

By simply calling `from capstone import sample` we are able to access all the custom functions created to handle wrangling the CheXpert CSV data. However, there is a prerequisite step that has to done before utilizing all these handy functions and that is to set the `path` variable. 

What this variable essentially does is take note of the location of our data, which in this specific case is `'/home/jupyter/springboard-capstone-2/data'`. This was configured using fast.ai's [`Config.data_path()`](https://forums.fast.ai/t/how-to-set-config-data-path-to-pwd/36297), which allows us to create a path both so we can access the location where our data (i.e. the X-rays) is stored and to save our model weights during training. By default it creates a temporary, and hidden, folder called `.fastai` but after a little bit of playing around we set the path to the one mentioned above. The main benefit of this is that we were able to access a permanent folder where we had originally stored the data. This version of the CheXpert data set is approximately 11GB, so it would've been extremely time-consuming to have to re-upload it every day to the default temporary folder just to work on it. 

This is where I want to mention though that playing around with the path is somewhat tricky (and potentially lengthy) ordeal, especially for someone not used to dealing with folders and working directories. Another strategy that could be used here is to set the path variable using the [`os`](https://docs.python.org/3/library/os.html) Python library. There is more documentation for it which makes it more approachable as opposed to the `Config.data_path()` technique. 

At this point we now have our `path` variable which is set to `'/home/jupyter/springboard-capstone-2/data'` and this gives us the ability to finally use one of our custom functions! The following code is all we need to address the vast majority of our data wrangling needs:

- `train_df, valid_df = sample.prep_data(path);`

Yes, one line is all we need! How is this possible you may ask? Well do you remember the call we made above, `from capstone import sample`? This command allows us to access the custom functions in the `sample.py` Python script. Pretty cool, huh?!

The only required input for the `prep_data()` function is the path variable, and once it has that it can go to work. Below is the steps it takes to return a cleaned up training and validation pandas DataFrame:

1. Reads in the `train.csv` and `valid.csv` files using `pd.read_csv()` and stores them in `train_df` and `valid_df`, respectively. 
2. Adds a `valid` column to both data sets, with the value of `False` being assigned to the training set and `True` being assigned to the validation set. 
3. Extracts the patient id from the `Path` column and assigns to a new column called `patient`. 
4. Creates a list of all the pathologies and fills any `NaN` values with 0 (which is the same approach that the team at Stanford used). 
5. Converts all the pathology columns to integer types (were originally floats). 
6. Gathers the counts of the values (i.e., 0, 1, or -1) in the `Cardiomegaly` column.
	- As a reminder, this is our target variable, as we're trying to develop a model that detects this particular pathology within the X-rays.
7. Replaces the -1 (i.e. uncertain) labels within the training set with 0 (i.e. negative) label.
	- This particular strategy for addressing the uncertain labels was known as `U-Zero` in the Stanford paper. I'll go into further details in a little bit but for now know this strategy produced some of the best results for Stanford's model. 
8. Asserts that the replacement of uncertain labels to negative was done correctly (i.e. returns a True/False statement). 
9. Presents further detailed information related to reclassification of uncertain labels, from both pre and post-relabeling.
10. Returns `train_df` and `valid_df`

### Are you sure about that? - Approaches to Uncertain Labels

Before we go any further, I want to take a deep dive into what we did with the uncertain labels within the training set. As mentioned above, we replaced all the Cardiomegaly observations that had a value of -1 with 0. Where did this idea come from? In their [paper](https://arxiv.org/pdf/1901.07031.pdf), the team at Stanford mentions a few different approaches that could be used for the uncertain labels, which include:

1. __Ignoring__
2. __Binary Mapping__
3. __Self-Training__
4. __3-Class Classification__

The first one approach, ignoring, is simplest: drop all uncertain labels during training. However, its simplicity is overcome by its potential to induce significant information loss. The second approach, binary mapping, is the one we used. It says to map all uncertain labels to either 0 (_U-Zeroes_, which we used) or 1 (_U-Ones_). This is still relatively simple to implement and allows us to keep all our images. However, there is the potential for distortion due to images being mislabeled (was labeled as negative when in actuality it was positive) which is essentially sending the learner mixed signals and thus degrading its performance. 

While they were not used for this specific project, there are two more advanced approaches that could be undertaken to address the uncertainty: self-training and 3-class classification. With the self-training approach, you train an initial model using the ignoring method until it reaches some performance threshold and using this model, relabel the uncertain labels with either 0 or 1. 3-class classification takes the next step and treats the uncertain labels as their own class, turning what was a binary classification problem into a multi-class one (as indicated by its name). 

So in the end why did I choose to utilize the _U-Zero_ uncertainty approach? Well, it had the second highest performance in terms of AUROC amongst the various approaches, plus its simplicity made it a logical first choice. However, there is one other route that would be interesting to explore in further analysis. During the [exploratory data analysis portion](https://github.com/Jearny58/Springboard-DS-Portfolio/blob/master/capstone_2/exploration/chexpert-eda.ipynb) of this project, we're able to create a random forest model utilizing the data from the training data set that could predict cardiomegaly with an AUROC of 0.896, which is very strong. In theory, we could use this model to relabel the uncertain cardiomegaly observations, which is similar to the self-training approach. It would be interesting to see if this could provide any significant bumps in performance. 

### Fool Me Once - The Issue of Reproducibility

Since we will be using samples of the data set, it is important that we set the randomization seed for the environment. This helps not only with the stability of whatever particular environment we're working in, but to keep results consistent across multiple trials. 

To put it more clearly, I'll use a theoretical situation where we're trying to compare the difference between model X and model Y. Each uses a different architectures with model X being a ResNet and model Y being a DenseNet. We train the models and then get the results, with model Y performing slightly better than model X. There's one caveat though: during training, each model utilized a different sample from the data set. Now the question becomes, can you compare accurately assess the performance of the two models? 

The answer is 'not really' because they were trained on different samples. Now model Y could very well be the better performer but you can't say that with too much confidence because the data set it used may have contained better information than model X's data. To fix this situation, you would want to train both model X and model Y on the same sample of the data. If model Y's performance is again higher, than you can more confidently say it is the better of the two. 

So how do we ensure we generate the same random sample? We set the randomization seed. This is done through our imported `sample.py` script with the following line of code:

`sample.set_seed(58)`

This sets the seed for all of the following modules within our environment:

- [`random.seed`](https://pynative.com/python-random-seed/)
- [`os.environ['PYTHONHASHSEED']`](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONHASHSEED)
- [`np.random.seed`](https://docs.scipy.org/doc/numpy-1.15.1/reference/generated/numpy.random.seed.html)
- Multple components of [`torch`](https://pytorch.org/docs/stable/notes/randomness.html)

In summary, by using this function we're maximizing our reproducibility which gives us more confidence in determining what might be helping or hurting a respective model's performance.

### To Over or Under-sample? Dealing with Unbalanced Data

To be blunt, the models weren't very good during the early stages of this project. At first, I was a little confused by this but then I decided to test the hypothesis that the unbalanced data might be negatively impacting the model's performance. The reason I did not address this imbalance upfront was due to the assumption that the deep learning model would account for this. However, with ~88% of the observations being negative, I realize how misguided my initial approach was. 

Realizing the fallacy of my original though process, I decided to look into resampling strategies, namely over and under-sampling. Both involve randomly sampling from the data set, but approach it from different perspectives: under-sampling involves removing samples from the majority class to match the minority class while over-sampling is the opposite, adding more examples from the minority class until it matches the majority class. 

After initial exploration with both techniques, oversampling tended to produce slightly better results so it was the approach we primarily used going forward. 






