---
layout: post
published: false
title: 'Capstone Chronicles: July 9, 2019'
date: '2019-07-09'
subtitle: Notes & Updates from my Work on July 9th
---
Yesterday was a great day! It's interesting how you can go awhile with seemingly no progress and then all of a sudden the dots connect, and you wonder where it came from. Every time this happens, I'm always surprised when I shouldn't be. Our minds are great data-crunchers, but sometimes it takes them a little time to find a pattern or recognize a different approach to a particular problem. 

### When your model gets better and better during training...
![When your model gets better and better during training...]({{site.baseurl}}/img/shaq_cat.gif)

Moving on: yesterday, I tested out a resampling strategy for the CheXpert data set. More specifically, I used undersampling which consists of removing samples from the majority class, which in this case meant removing observations that were negative (i.e., did not indicate the presence of Cardiomegaly, the pathology of focus).

This resulting data set had an exact 50/50 split in terms of negative/positive (0/1) observations. After setting up the data using fast.ai's library, in five rounds of training, I was able to achieve the best results so far, with an AUROC of approximately 0.81 and precision of 0.65 (although it did dip to ~0.61 during the last round of training). 

These results are promising, but I'm doing my best to keep expectations low as resampling does have its weaknesses. After all, there is no free lunch! When it comes to undersampling, where we exclude observations, we cause loss of information as we're eliminating samples that more than likely would've contributed useful information. However, there are a few other techniques that we can use to resample that attempts to minimize its downsides. 

## Trial 26 Review

This particular notebook has shown the best results so far! The Trial 25 notebook served as a template of sorts, and piece by piece we were able to build a python script - `sample.py` - in the test_folder module custom-built for this project (I know it needs a name change!). 

Here is the step by step of how the function `prep_data()` within the `sample` script works.

1. Begin by calling the following - `from test_folder import sample` - which gives us access to the functions within the `sample.py` script.
2. After creating a `path` variable (I used fast.ai's `Config.data_path()`) I pass it to the following function: `sample.prep_data(path)'. 
3. This function does a lot of the 'grunt' work. In addition to reading in the data, it does the following:
    - adds a column called `valid` indicating if the observation is part of the validation set or not (this will be important later in the notebook)
    - Extracts patient ID and study number for each observation and assigns them into two new columns
    - Fills in the `NaN` values with 0 ([this](https://github.com/stanfordmlgroup/chexpert-labeler/issues/9) is what the team at Stanford did)
    - Converts all the pathology columns to integer types (for both training and validation sets)
    - Utilizes the U-Zero uncertainty approach for -1 (i.e., uncertain) values in Cardiomegaly column, which replaces -1 with 0 
4. After all of this, it returns a cleaned up training and validation set, which I assigned to the variables `train_df` and `valid_df`

The next function we'll review -- `sample.undersample_and_prep(train_df, valid_df)` -- takes both the training and validation sets and combines them into one giant data set. However, before we get into that, I want to address the `sample.set_seed(1)` cell within the notebook. Technically this is another function that, based on the number inputted, sets the randomization seed for the environment. Why is this important? It ensures reproducibility! 

The `undersample_and_prep` function involves some random sampling, but if we don't set the seed than the sample will be different each time we run it! Because of this, it will be hard for others to approximately reproduce our results (I say approximately because the numbers for performance will not be **exactly** the same, but they should be very close). By setting the seed, we're going to return the same samples each time, creating a more stable environment for our results. 

That being said let's return to the function `sample.undersample_and_prep(train_df, valid_df)`. As previously noted, we pass in the training and validation sets, and the function does the grunt work of combining these two DataFrames into one. Perhaps more importantly, it utilizes the undersampling method in the process. 

What does undersampling mean? It's a technique for us to handle high unbalanced datasets, which is the case here as approximately 80% of all the observations **don't** have Cardiomegaly. Up to this point, I have not been undersampling, and the previous models do a great job of picking up the cases where there isn't any Cardiomegaly but are downright terrible at inferring cases where Cardiomegaly is present. I initially believed that the networks would eventually pick up the subtleties between positive and negative examples, but it is evident to me now that this is **not** the case. 

The function undersamples using the following steps:
1. Creates two variables -- `count_class_0` & `count_class_1` -- that take in the number of observations within the training set that are either 0 (negative) or 1 (positive)
2. It then divides the data frame into two, with observations of class 0 being assigned to `df_class_0` and class 1 going to `df_class_1`, respectively. 
3. We then call the `sample()` function on `df_class_0` with `count_class_1` as an argument. What this does is gather randomly sampled observations from the negative data frame according to the number of positive observations (which came out to be ~27,000). In summary, it gathers 27,000 positive **and** 27,000 negative observations.
4. It then concats this sampled negative data set with the positive data set, `df_class_1`, and returns the full data frame

Now we have a fully prepared data set! There is one unfortunate side effect though that I want to point out up front and this is loss of information. The undersampling technique used here is probably the simplest one, and what we gain in ease of use we lose in information. There are more advanced undersampling techniques - namely Tomek Links or Cluster Centroids -- that work to mitigate the loss of data caused by undersampling. This is definitely something to revisit in the future to potentially make this a more robust model. 

At this point we have our data set ready to go, so what's next? Two words: deep learning. More specifically, we're going to use the data set we just created to train a deep learning model. 

In the custom test_folder module, there is a python script named `replicate.py` which contains two functions that we can make use of: `get_src()` and `get_data()`. These two work in tandem and utilize fast.ai's library to allow our deep learning 'learner' the ability to train on the CheXpert images. 

Firstly, we call `from test_folder import replicate` which allows us access to all the functions in the `replicate.py` script. Then we call `replicate.get_src(full_df, path, feature_col="Cardiomegaly")` which gives us our ImageList. Let's break this down a little further though. This function takes the `full_df` we created and generates the ImageList source from that according to the `path` variable. This is essentially telling the model where it needs to go to access the images so it can input them into the model. It then splits the data set based on the 'valid' column, generating a separate validation set from the observations that have a `True` value (told you it was going to come in handy eventually!). After that, we assign the labels for each of the images from our feature column -- 'Cardiomegaly' -- as it is the pathology we are trying to classify. 

Next, we call `replicate.get_data(128, src)`, which takes image size (128) and the ImageList (`src`) we just created as input. Before this function deals with the ImageList, it tests the available GPU memory and assigns either 32 or 16 to the variable `bs`, which stands for batch size. Now, what is batch size? Well, when we are training deep learning models, usually we'll have the model analyze a group (i.e., a batch) of images together. If we have more memory, we can generally analyze more images (i.e., larger batches) which are why batch size and GPU memory are linked. 

After the function assesses the available memory and assigns the batch size (which for most of my experience was set to 16), it takes `src` (i.e., the ImageList that stores information about our data set) and performs data augmentation on it. Data augmentation is a technique that transforms the images to induce variability within the image set. You can flip pictures, zoom in, and set the size. In our case, being medical images, we didn't do much to the images except add extra lighting (which was randomly assigned to a subset of the images) and assign the image size, which was one of the variables we passed into the function (i.e., 128). After the augmentations, it then creates a data bunch with the previously assigned batch size (which was calculated on available GPU memory) and normalized the images. Once this is all complete, the function returns an ImageDataBunch variable named `data` (very original I know...). 

With our data all set, all we need to do now is create the learner! 

But that will be for another day...which will most likely be tomorrow...

Until then, this is Joe signing off!





