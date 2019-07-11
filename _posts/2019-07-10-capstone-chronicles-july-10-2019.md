---
layout: post
published: false
title: 'Capstone Chronicles: July 10, 2019'
subtitle: 'Notes & Updates from my work on July 10, 2019'
---
Another day, some more deep learning! 

Let's review the work from yesterday though first. The most significant update was that we trained a model that utilized an undersampled data set. Since Cardiomegaly - our target pathology - was highly unbalanced towards negative cases, we decided to resample with the undersampling technique first. Undersampling involves taking the majority class and essentially resizing it so that it matches the number of cases of the minority class. The result was a data set that comprised 27,000 observations of both positive and negative labels, for a total of 54,000 observations! I want to point out though that this approach causes loss of information (as we ignored approximately 160k other observations). 

Additionally, we utilized a different architecture opting to use DenseNet161 versus DenseNet121, with the later serving somewhat like a standard for this particular project. The hypothesis was that DenseNet161 would potentially give us the ability to capture more nuanced features from the images due to the higher number of layers. 

The initial results with the undersampled data set and DenseNet161 were, and in regards to the metrics, it had been the best performing model up to that point. However, upon continuing our training today with a larger image size of 320 (which was the size used by Stanford), there were memory issues galore that prevented us from even starting! As a result, we've gone back to using DenseNet121 today, and it looks like DenseNet161 was not the best choice. 

## Trial 27 & 28 Review

After failing to work around the memory issues caused by DenseNet161, we iterated again and started a new Jupyter notebook (i.e., Trial 27). Luckily our `sample.py` script makes the grunt work of data cleaning relatively straightforward and quick. Once we got the data cleaned up and into an ImageDataBunch, we began training a DenseNet121 model with a max learning rate of `1e-1`, which was gathered from our learning rate finder plot.

It performed somewhat admirably, achieving an AUROC of 0.807 in only ~18 minutes of training. But then I remembered the other technique we could use in the case of highly unbalanced data: oversampling. This is the opposite of undersampling, which means that we resample the minority class to match the number of the majority class. Now you may be wondering, how exactly do we 'add' approximately 150k positive images to our data set? Two words: random sampling!

In the `sample.py` script there is a function that takes in `train_df` and `valid_df` - similar to the function `undersample_and_prep` - with an additional input called `frac`. What `frac` represents is the fraction of the data because when we use oversampling with the CheXpert data set, we're going to get roughly 360k observations and for training, we only want to take a fraction of that, and the default is set to 50% (or 0.5). 

I'm getting a little ahead of myself though. Let's walk through what `undersample_and_prep` does, step by step:

1. Stores how many observations are in each class (i.e. 0 or 1) into two variables - `count_class_0` & `count_class_1`
2. Divides the training data set by class, so one data set contains all the 0 observations, `df_class_0`, and the other one has all the 1 observations, `df_class_1`
3. Samples, with replacement, from the `df_class_1` data frame according to the count of class 0
4. Using `pandas.concat()`, combines the 0 observation data set `df_class_0` and oversampled data set containing only cases of 1, `df_class_1_over`
5. It then takes a sample of this combined data set according to `frac` (whose default is 0.5, which represents 50%)
6. To indicate what the value counts look like for the new oversampled data set, it outputs the total number of 0 and 1 observations
7. Combines the oversampled data set with the valid_df

From the output within the notebook, we can see that the original randomly oversampled data set contained ~196,000 cases of both 0 and 1! However, it then condensed it down to **only** ~98,000 for each class. 

We then got the data set up using our `get_src()` and `get_data()` functions that I went over yesterday. In tandem, these functions output our dataset that assigns the labels to each image and allows the learner to access them in our data directory. Additionally, we'll begin with an image size of 64. Why so small? Well, any longer and our model would take a **long** time to train. Ideally, the point of this first iteration of training is to give the model a chance to get a general idea of the images. 

With that, I'm going to end this post abruptly. I know it is random, but unfortunately, I realized that during my last iteration of training, I accidentally saved over the model weights that had produced the best performing model so far with an AUROC of ~0.85.

### What happens when you forget to change the name of your callback so that it overwrites your best performing model...

![doh_homer.gif]({{site.baseurl}}/img/doh_homer.gif)

Onward and upwards though! It surely won't be my last 'doh!' moment. Right now, I'm going through the Trial 28 notebook and re-training the model (with some slight learning rate tweaks). We'll see how that goes, and I'll report back tomorrow! 

Until then, Joe out! 



