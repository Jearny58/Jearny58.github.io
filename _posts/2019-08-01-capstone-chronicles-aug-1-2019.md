---
layout: post
published: false
title: 'Capstone Chronicles: Aug. 1, 2019'
---
## Today's Focus

I'm going to pick right up where I left off in yesterday's [article](https://jearny58.github.io/2019-07-31-capstone-chronicles-july-31-2019/). I'm hoping to have most of the draft done today but the world works in mysterious ways. Without further ado, lets jump back in!

## To Over or Under-sample? Dealing with Unbalanced Data (cont.)

6. Gather counts of class 0 and 1 observations and print out their values to ensure the split is approximately 50/50. 
7. Combine oversampled DataFrame - `df_test_over_sample` - with the validation data set. 
8. Return the combined data set, `full_df`. 

The resulting `full_df` has approximately 59,000 observations for both class 0 and class 1, for nearly 118,000 total observations. Looks like our data set is finally ready for the next step: deep learning! 

## The Alley-Oop - How to Prepare Data for Deep Learning

If you take a look at the [`trial30`](https://github.com/Jearny58/Springboard-DS-Portfolio/blob/master/capstone_2/trial30.ipynb) Jupyter Notebook and navigate to the section titled _Deep Learning Fast.ai Set Up_ you'll three cells. 

Wait, how did you prep the data with so little code? 

Answer: more Python scripts! Similar to what we did during the data wrangling stage with [`sample.py`](https://github.com/Jearny58/Springboard-DS-Portfolio/blob/master/capstone_2/capstone/sample.py), we're going to import another Python script - [`replicate.py`](https://github.com/Jearny58/Springboard-DS-Portfolio/blob/master/capstone_2/capstone/replicate.py) - which contains functions to help us prep our images utilizing the `full_df` we just created. You can see the full command below:

`from capstone import replicate`

This command goes into the capstone folder in the main directory and loads in the `replicate.py` script, which has two particularly useful functions - `get_src()` and `get_data()` - that will get out data in the proper format for the deep learning model. 

Let's turn our attention first to `get_src()` and the following line: 

`src = replicate.get_src(full_df, path, feature_col='Cardiomegaly')`

What exactly is going on in the backend? First, there are three arguments that we need to input; `full_df` which contains all the information on the observations we're going to feed into the model; `path` which, if you remember from earlier, contains information on where the images are stored; and `feature_col='Cardiomegaly'`, which is telling us which column to use as our target feature. 

Using these argurments, the function can then go to work! This is how we convert our `full_df` pandas DataFrame a fast.ai [`ImageList`](https://docs.fast.ai/vision.data.html#ImageList)

1. Use `ImageList.from_df()` to generate an ImageList class from our `full_df` with the path set to our `path` variable we created at the beginning of the project, `'/home/jupyter/springboard-capstone-2/data'`.
2. Split the data set into a training and validation set using the [`split_from_df()`](https://docs.fast.ai/data_block.html#ItemList.split_from_df) that takes 'valid' as an argument. 
	- Remember when we created the 'valid' column earlier? Told you it would come in handy sooner or later! 
    - Observations with a value of `True` in the 'valid' column will be put into validation set. 
3. Label the images using the [`label_from_df()`](https://docs.fast.ai/data_block.html#ItemList.label_from_df) function, that takes the value from `feature_col` as input. 
	- To put it in more layman terms, for each observation and its correpsonding image, it will assign a value of 0 or 1 to that image depending on the value present within the `Cardiomegaly` column. 
4. Returns the information as the variable `src`. 

We now have a proper `ImageList` however, we still have one more step which is to actually get the data and that is what `get_data()` helps us accomplish. Let's take a look at the full line below:

`data = replicate.get_data(320, src, default_trans = False)`

The `get_data()` function takes three arguments: image size, an ImageList, and whether or not we want the default fast.ai image transformations. The last aspect - image transformations - is perhaps better known as [_data augmentation_](https://docs.fast.ai/vision.transform.html#Data-augmentation), which applies small random transformations to the images by doing such things as flipping them or making them brighter, just to name a few, without changing the pixel values. By utilizing data augmentation we can potentially help our model to generize better to new images. 


First we utilize fast.ai's [`ImageList`](https://docs.fast.ai/vision.data.html#ImageList) class, which will allow us to turn an image file in `Path` object into an `Image` object. We're going to create this `ImageList` from our `full_df` using the `from_df()` function
