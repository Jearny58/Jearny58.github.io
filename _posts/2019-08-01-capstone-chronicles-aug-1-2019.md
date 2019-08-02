---
layout: post
published: false
title: 'Capstone Chronicles: Aug. 1, 2019'
---
## Today's Focus

I'm going to pick right up where I left off in yesterday's [article](https://jearny58.github.io/2019-07-31-capstone-chronicles-july-31-2019/). I'm hoping to have most of the draft done today, but the world works in mysterious ways. Without further ado, let us jump back in!

## To Over or Under-sample? Dealing with Unbalanced Data (cont.)

6. Gather counts of class 0 and 1 observations and print out their values to ensure the split is approximately 50/50. 
7. Combine oversampled DataFrame - `df_test_over_sample` - with the validation data set. 
8. Return the combined data set, `full_df`. 

The resulting `full_df` has approximately 59,000 observations for both class 0 and class 1, for nearly 118,000 total observations. It looks like our data set is finally ready for the next step: deep learning! 

## The Alley-Oop - How to Prepare Data for Deep Learning

If you take a look at the [`trial30`](https://github.com/Jearny58/Springboard-DS-Portfolio/blob/master/capstone_2/trial30.ipynb) Jupyter Notebook and navigate to the section titled _Deep Learning Fast.ai Set Up_, you'll see three cells. 

Wait, how did you prep the data with so little code? 

Answer: more Python scripts! Similar to what we did during the data wrangling stage with [`sample.py`](https://github.com/Jearny58/Springboard-DS-Portfolio/blob/master/capstone_2/capstone/sample.py), we're going to import another Python script - [`replicate.py`](https://github.com/Jearny58/Springboard-DS-Portfolio/blob/master/capstone_2/capstone/replicate.py) - which contains functions to help us prep our images utilizing the `full_df` we just created. You can see the full command below:

`from capstone import replicate`

This command goes into the capstone folder in the main directory and loads in the `replicate.py` script, which has two particularly useful functions - `get_src()` and `get_data()` - which get our data in the proper format for the deep learning model. 

Let's turn our attention first to `get_src()` and the following line: 

`src = replicate.get_src(full_df, path, feature_col='Cardiomegaly')`

What exactly is going on in the backend? First, there are three arguments that we need to input; `full_df` which contains all the information on the observations we're going to feed into the model; `path` which, if you remember from earlier, contains information on where the images are stored; and `feature_col='Cardiomegaly'`, which is telling us which column to use as our target feature. 

With these arguments, the function can then go to work! This is how we convert our `full_df` pandas DataFrame into a fast.ai [`ImageList`](https://docs.fast.ai/vision.data.html#ImageList)

1. Use `ImageList.from_df()` to generate an ImageList class from our `full_df` with the path set to our `path` variable we created at the beginning of the project, `'/home/jupyter/springboard-capstone-2/data'`.
2. Split the data set into a training and validation set using the [`split_from_df()`](https://docs.fast.ai/data_block.html#ItemList.split_from_df) that takes 'valid' as an argument. 
    - Remember when we created the 'valid' column earlier? Told you it would come in handy sooner or later! 
    - Observations with a value of `True` in the 'valid' column are put into validation set. 
3. Label the images using the [`label_from_df()`](https://docs.fast.ai/data_block.html#ItemList.label_from_df) function, that takes the value from `feature_col` as input. 
    - To put it in more layman terms, for each observation and its corresponding image, it assigns a value of 0 or 1 to that image depending on the value present within the `Cardiomegaly` column. 
4. Returns the information as the variable `src`. 

We now have a proper `ImageList` however, we still have one more step which is actually to get the data (i.e., images), and that is what `get_data()` helps us accomplish. Let's take a look at the full line below:

`data = replicate.get_data(320, src, default_trans = False)`

The `get_data()` function takes three arguments: image size, an ImageList, and whether or not we want the default fast.ai image transformations. For the image size, we utilized `320`, which is the same size used by the team at Stanford; next, we pass in our `src` ImageList we created in the previous step to locate the images.

The last aspect - image transformations - is perhaps better known as [_data augmentation_](https://docs.fast.ai/vision.transform.html#Data-augmentation), which applies small random transformations to the images by doing such things like flipping them or making them brighter without changing the pixel values. By utilizing data augmentation, we can potentially help our model to generalize better to new images. 

Now that we're familiar with the inputs, we can see what `get_data()` does with them on the backend. 

1. Assigns batch size to either 32 or 16, based on available GPU memory.
    - Memory issues were pretty common during this project, which is the primary motivation for including this 'check'. 
    - Batch size 'is a hyperparameter that defines the number of samples to work through before updating the internal model parameters'. [(Source)](https://machinelearningmastery.com/difference-between-a-batch-and-an-epoch/)
    - Essentially it is the number of images we're going to feed into the model at a time before it updates its weights. 
    - Most of the time it set batch size equal to 16, which is technically called a 'mini-batch'.
    - [This article](https://machinelearningmastery.com/gentle-introduction-mini-batch-gradient-descent-configure-batch-size/) offers a great introduction into how the batch size works within deep learning. 
2. With `default_trans` equal to `False`, we use a minimal amount of data augmentation. The only random transformations applied are a slight increase in lighting. 
    - I believed this to be the best approach because medical images to my knowledge are not altered. 
    - If this had been set to `True`, there would have been more data augmentation involved. 
3. Sets image size equal to 320 and padding equal to `zeros`. 
    - Padding has to do with how we treat missing pixels within the images (which can result from applying transformations like a rotation). 
    - By setting it equal to `zeros`, we're telling it to input any missing pixels as black. 
4. Convert the original `ImageList` into a [`DataBunch`](https://docs.fast.ai/basic_data.html#DataBunch) which can then be passed into the PyTorch `Dataloader`.
    - Binds training, validation and test sets into a single data object. 
5. Normalizes the data using `imagenet_stats`. 
6. Returns the [`ImageDataBunch`](https://docs.fast.ai/vision.data.html#Computer-vision-data) stored in the variable `data`.

In cell block below the `get_data()` call, we can see the output of `data`. We can see that it is an `ImageDataBunch` that contains a training set of 117,848 items and a validation set of 234 items. Now it's time to create our deep learning model! 

## It's Alive - Bringing the DenseNet To Life

Now let's take a look at the code block underneath the _Create DenseNet Model_ in the [`trial30`](https://github.com/Jearny58/Springboard-DS-Portfolio/blob/master/capstone_2/trial30.ipynb) notebook. 

`learn = cnn_learner(data=data, base_arch=models.densenet121, 
                    metrics=[AUROC(), Recall(), Precision(), error_rate],
                    pretrained=True, ps=0.5, bn_final=True)`

By using fast.ai's [`cnn_learner`](https://docs.fast.ai/vision.learner.html#cnn_learner) method, we can create the learner for our deep learning model! However, as we can see, there are quite several things within the learner that we need to address. Let's walk through them one by one. 

The first argument, `data` takes in a `DataBunch`. This is where we input the `ImageDataBunch` we just created, which is conveniently stored in the variable `data`. 

The second argument, `base_arch`, is the architecture (i.e., backbone) of the deep learning model. For this project, we used [DenseNet](https://arxiv.org/abs/1608.06993), or a densely connected convolutional (neural) network, with 121 layers. Before we go any further though, let's get a high-level overview of what is going on with this particular architecture.

### CNN's and How DenseNet is (Slightly) Different

Before we dive into what a convolutional neural network is, I want to ask the following question: __what makes up the image below?__

![](/img/eagle_dl.jpeg)

One word: __pixels__! Or more specifically a _matrix_ (which is essentially a square-like object) of pixels. 

Below is a little better example of what is going on underneath the image. 

![](/img/matrix_pixel_values.png)
[_Source_](https://link.springer.com/article/10.1007/s13244-018-0639-9)

As you can see, a black pixel is assigned a value of 0, and a white pixel is assigned a value of 255, with a variety of numbers in-between indicating different shades.
