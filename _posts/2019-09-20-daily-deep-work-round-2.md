---
layout: post
published: true
title: 'Daily Deep Work: Round 2'
excerpt: >-
  In the previous notebook, we utilized a `resnet34` architecture and achieved
  admirable initial results. However, I need to revisit the
  documentation/lessons to see if there are any other strategies I could use to
  extract more performance from that particular architecture. 
date: '2019-09-20'
subtitle: Mmmm...garlic bread...
image: /img/trial3-food101_47_0.png
---
# _Trial 3: fellowship.ai Food-101 Challenge_

**_From fellowship.ai's website:_**

_Food-101 is a challenging vision problem, but everyone can relate to it.  Recent SoTA is ~80% top-1, 90% top-5.  These approaches rely on lots of TTA, large networks and  even novel architectures._

_Train a decent model >85% accuracy for top-1 for the test set, using a ResNet50 or smaller network with a reasonable set of augmentations._

## _Step 1: Set up environment_

To get things set-up, we need to set the `PATH` variable. This is the root directory for this particular environment and will be used to help us organize our environment. Perhaps most importantly, it is critical that we set this up to be able to create a direct line for our deep learning learner to access the `food-101` images. In summary, here is what we're going to do in the next few cells:
1. Get current directory and if not the one desired, change it to necessary location
2. Check out what files are located in main directory
3. Read in training data that has labels and location (i.e. file path) for each image


```python
%reload_ext autoreload
%autoreload 2
%matplotlib inline
%config InlineBackend.figure_format = 'retina'
```


```python
from fastai.vision import *
```


```python
# import necessary libraries to set path variable for environment
import os
from pathlib import Path

# get current working directory
os.getcwd()
```




    '/home/jupyter/projects/food-101/notebooks'




```python
# change to main directory: food-101
os.chdir('/home/jupyter/projects/food-101')

#create path variable to primary directory
PATH = Path(os.getcwd())
PATH
```




    PosixPath('/home/jupyter/projects/food-101')




```python
# set path for this particular project
path = PATH; path
```




    PosixPath('/home/jupyter/projects/food-101')




```python
path.ls()
```




    [PosixPath('/home/jupyter/projects/food-101/notebooks'),
     PosixPath('/home/jupyter/projects/food-101/models'),
     PosixPath('/home/jupyter/projects/food-101/test.txt'),
     PosixPath('/home/jupyter/projects/food-101/labels.txt'),
     PosixPath('/home/jupyter/projects/food-101/.ipynb_checkpoints'),
     PosixPath('/home/jupyter/projects/food-101/train.txt'),
     PosixPath('/home/jupyter/projects/food-101/test.json'),
     PosixPath('/home/jupyter/projects/food-101/images'),
     PosixPath('/home/jupyter/projects/food-101/train.json'),
     PosixPath('/home/jupyter/projects/food-101/h5'),
     PosixPath('/home/jupyter/projects/food-101/ranger'),
     PosixPath('/home/jupyter/projects/food-101/classes.txt')]



## _Load in Training Data_

Ok so we've got our `path` variable set to the correct directory, as evidenced by the output directly above this cell. We need to first gather the information about the training data we're going to use. This information can be accessed in either the `train.txt` or `train.json` files. We'll use the text file because JSONs can be slightly more difficult to deal with, due to nesting. So let's go ahead and load in our data using the extremely helpful `pandas` library. 


```python
# gather training data and put into pandas dataframe
train = pd.read_csv(path/'train.txt', delimiter='/', header=None, names=['label', 'name'])
# correct name column to generate file path for model data loader
train['name'] = train['label'].astype(str) + '/' + train['name'].astype(str) + '.jpg'
train.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>label</th>
      <th>name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>apple_pie</td>
      <td>apple_pie/1005649.jpg</td>
    </tr>
    <tr>
      <th>1</th>
      <td>apple_pie</td>
      <td>apple_pie/1014775.jpg</td>
    </tr>
    <tr>
      <th>2</th>
      <td>apple_pie</td>
      <td>apple_pie/1026328.jpg</td>
    </tr>
    <tr>
      <th>3</th>
      <td>apple_pie</td>
      <td>apple_pie/1028787.jpg</td>
    </tr>
    <tr>
      <th>4</th>
      <td>apple_pie</td>
      <td>apple_pie/1043283.jpg</td>
    </tr>
  </tbody>
</table>
</div>




```python
# reorder columns
train = train[['name', 'label']]; train.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>label</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>apple_pie/1005649.jpg</td>
      <td>apple_pie</td>
    </tr>
    <tr>
      <th>1</th>
      <td>apple_pie/1014775.jpg</td>
      <td>apple_pie</td>
    </tr>
    <tr>
      <th>2</th>
      <td>apple_pie/1026328.jpg</td>
      <td>apple_pie</td>
    </tr>
    <tr>
      <th>3</th>
      <td>apple_pie/1028787.jpg</td>
      <td>apple_pie</td>
    </tr>
    <tr>
      <th>4</th>
      <td>apple_pie/1043283.jpg</td>
      <td>apple_pie</td>
    </tr>
  </tbody>
</table>
</div>




```python
# return counts for each label type
Counter(train['label'])
```




    Counter({'apple_pie': 750,
             'baby_back_ribs': 750,
             'baklava': 750,
             'beef_carpaccio': 750,
             'beef_tartare': 750,
             'beet_salad': 750,
             'beignets': 750,
             'bibimbap': 750,
             'bread_pudding': 750,
             'breakfast_burrito': 750,
             'bruschetta': 750,
             'caesar_salad': 750,
             'cannoli': 750,
             'caprese_salad': 750,
             'carrot_cake': 750,
             'ceviche': 750,
             'cheesecake': 750,
             'cheese_plate': 750,
             'chicken_curry': 750,
             'chicken_quesadilla': 750,
             'chicken_wings': 750,
             'chocolate_cake': 750,
             'chocolate_mousse': 750,
             'churros': 750,
             'clam_chowder': 750,
             'club_sandwich': 750,
             'crab_cakes': 750,
             'creme_brulee': 750,
             'croque_madame': 750,
             'cup_cakes': 750,
             'deviled_eggs': 750,
             'donuts': 750,
             'dumplings': 750,
             'edamame': 750,
             'eggs_benedict': 750,
             'escargots': 750,
             'falafel': 750,
             'filet_mignon': 750,
             'fish_and_chips': 750,
             'foie_gras': 750,
             'french_fries': 750,
             'french_onion_soup': 750,
             'french_toast': 750,
             'fried_calamari': 750,
             'fried_rice': 750,
             'frozen_yogurt': 750,
             'garlic_bread': 750,
             'gnocchi': 750,
             'greek_salad': 750,
             'grilled_cheese_sandwich': 750,
             'grilled_salmon': 750,
             'guacamole': 750,
             'gyoza': 750,
             'hamburger': 750,
             'hot_and_sour_soup': 750,
             'hot_dog': 750,
             'huevos_rancheros': 750,
             'hummus': 750,
             'ice_cream': 750,
             'lasagna': 750,
             'lobster_bisque': 750,
             'lobster_roll_sandwich': 750,
             'macaroni_and_cheese': 750,
             'macarons': 750,
             'miso_soup': 750,
             'mussels': 750,
             'nachos': 750,
             'omelette': 750,
             'onion_rings': 750,
             'oysters': 750,
             'pad_thai': 750,
             'paella': 750,
             'pancakes': 750,
             'panna_cotta': 750,
             'peking_duck': 750,
             'pho': 750,
             'pizza': 750,
             'pork_chop': 750,
             'poutine': 750,
             'prime_rib': 750,
             'pulled_pork_sandwich': 750,
             'ramen': 750,
             'ravioli': 750,
             'red_velvet_cake': 750,
             'risotto': 750,
             'samosa': 750,
             'sashimi': 750,
             'scallops': 750,
             'seaweed_salad': 750,
             'shrimp_and_grits': 750,
             'spaghetti_bolognese': 750,
             'spaghetti_carbonara': 750,
             'spring_rolls': 750,
             'steak': 750,
             'strawberry_shortcake': 750,
             'sushi': 750,
             'tacos': 750,
             'takoyaki': 750,
             'tiramisu': 750,
             'tuna_tartare': 750,
             'waffles': 750})



## _Sample From Original Data For Faster Iteration_

Why do we want to do this? Well we are still very early in the exploration stage so iteration is key. We want to be able to experiment as much and as quickly as possible to see how we might be able to achieve the best model possible. Because more data usually takes more time to train on (for the most part), we're going to sample a subset of the data. This will equal quicker training and with transfer learning (i.e. using a pretrained deep learning model) we may not need as much data as expected.


```python
# gather a 20% sample of original training data set
train_sample = train.sample(frac=0.20, random_state=1)
```


```python
train_sample.shape
```




    (15150, 2)




```python
# return counts for each label type
Counter(train_sample['label'])
```




    Counter({'garlic_bread': 148,
             'caesar_salad': 154,
             'greek_salad': 157,
             'red_velvet_cake': 167,
             'takoyaki': 161,
             'gyoza': 138,
             'cheese_plate': 152,
             'ice_cream': 154,
             'french_toast': 151,
             'fish_and_chips': 163,
             'chicken_quesadilla': 167,
             'risotto': 142,
             'cannoli': 142,
             'caprese_salad': 139,
             'french_fries': 141,
             'macarons': 132,
             'fried_rice': 153,
             'churros': 153,
             'gnocchi': 143,
             'french_onion_soup': 133,
             'spaghetti_bolognese': 161,
             'tiramisu': 160,
             'macaroni_and_cheese': 147,
             'ravioli': 136,
             'bibimbap': 152,
             'bread_pudding': 160,
             'beignets': 156,
             'bruschetta': 140,
             'pad_thai': 144,
             'oysters': 160,
             'nachos': 137,
             'hot_and_sour_soup': 135,
             'pulled_pork_sandwich': 161,
             'sushi': 159,
             'pancakes': 144,
             'hot_dog': 152,
             'steak': 148,
             'beef_carpaccio': 148,
             'chicken_wings': 171,
             'ramen': 151,
             'ceviche': 158,
             'escargots': 155,
             'carrot_cake': 166,
             'deviled_eggs': 138,
             'seaweed_salad': 139,
             'foie_gras': 151,
             'peking_duck': 145,
             'edamame': 143,
             'panna_cotta': 138,
             'beet_salad': 148,
             'cup_cakes': 152,
             'croque_madame': 160,
             'huevos_rancheros': 158,
             'sashimi': 136,
             'frozen_yogurt': 136,
             'club_sandwich': 175,
             'lasagna': 160,
             'donuts': 141,
             'hamburger': 151,
             'cheesecake': 153,
             'apple_pie': 143,
             'chocolate_cake': 153,
             'poutine': 154,
             'grilled_cheese_sandwich': 154,
             'onion_rings': 165,
             'pho': 140,
             'shrimp_and_grits': 127,
             'guacamole': 148,
             'tuna_tartare': 161,
             'creme_brulee': 133,
             'dumplings': 154,
             'chocolate_mousse': 164,
             'samosa': 149,
             'chicken_curry': 132,
             'paella': 150,
             'filet_mignon': 162,
             'baby_back_ribs': 158,
             'lobster_roll_sandwich': 152,
             'spring_rolls': 149,
             'spaghetti_carbonara': 167,
             'strawberry_shortcake': 146,
             'eggs_benedict': 156,
             'grilled_salmon': 154,
             'omelette': 154,
             'pizza': 151,
             'baklava': 136,
             'tacos': 144,
             'clam_chowder': 145,
             'waffles': 159,
             'miso_soup': 163,
             'crab_cakes': 161,
             'pork_chop': 150,
             'prime_rib': 143,
             'beef_tartare': 144,
             'fried_calamari': 137,
             'breakfast_burrito': 148,
             'hummus': 157,
             'lobster_bisque': 148,
             'falafel': 131,
             'mussels': 142,
             'scallops': 151})



## _Input Data For Model_

We have a 20% subset of the data so now what? We need to input this data into a dataloader, in this case an `ImageDataBunch`. What is this? Well before any training can be done we have to be able to feed that data (in this case images of food) into the model. Essentially, what we're seeking to do is create a pipeline of sorts, that will feed the images into the model with any subsequent changes or adjustments we make to the data (i.e. data augmentation, normalization, etc.). 

With `fast.ai` this process is relatively straightforward. Utilizing the documentation [here](https://docs.fast.ai/data_block.html) is extremely helpful and gives the user a great high-level understanding of what is going on. In general, there are two ways we can approach creating a `data` object. There is a shortcut method, which calls `ImageDataBunch` directly, or first creating an `ImageList` then applying subsequent changes via other functions like `split_by_folder()` or `label_from_folder()` (both of which are crucial if you utilize this second option).

So to get things up and running we're going to take the shortcut (by using `ImageDataBunch`) and with minimal transformations. The data augmentations is something we'll revisit later on in development as it can potentially increase performance. But my hypothesis at this point in time is that it's more important at this current stage to focus on architecture. I'm getting ahead of myself though...


```python
# default transformations, minus random flipping of images
tfms = get_transforms(do_flip=False)

# create ImageDataBunch from our sample of the training data
data = ImageDataBunch.from_df(path=path/'images', # location of images
                              df=train_sample, # gives information on file path, and labels
                              valid_pct=0.2, # 20% of data will be used for validation
                              seed=1, # set random seed for reproducibility
                              fn_col=0, # index of the column containing file names
                              label_col=1, # index of column containing the labels
                              ds_tfms=tfms, # apply transformations
                              size=64, # set image size equal to 64 pixels and a batch size of 32
                              bs=32).normalize(imagenet_stats) # normalize images
```


```python
data.show_batch(rows=3, figsize=(8,8))
```


![png](/img/trial3-food101_17_0.png)



```python
print(data.c)
```

    101


## _Create `resnet50` Learner_

In the previous notebook, we utilized a `resnet34` architecture and achieved admirable initial results. However, I need to revisit the documentation/lessons to see if there are any other strategies I could use to extract more performance from that particular architecture. 

With that being said, I wanted to see what type of results I could get from a `resnet50` architecture. What is the difference between the two? Simple: this one has more layers. Essentially, the goal of adding layers is to hopefully extract more nuanced features that help the model better classify whatever it's trying to classify. 

This brings me to another subject: how are we going to determine the performance of our model? We're going to use what is called top-1 accuracy. What is this exactly? 

To answer this we have to dig a little deeper into what our model is doing when it makes a prediction. Technically our deep learning model outputs at the end is a vector of probabilities, which represent that probability that the input images is of that particular class. So in our case, The vector is represented by 101 numbers, since that is the number of classes of food we have. The corresponding number represents the model's confidence (as a probability) that the input image belongs to that class. 

Now where does top-1 accuracy come into play? Well basically it is testing to see if the model answer (i.e. the class with the highest probability) is the expected answer. Using the images above, say the model outputs the probability for the lasagna as 0.72, fried calamari as 0.12 and risotto as 0.10. With lasagna being the highest probability and being the expected answer, we have 100% accuracy! 


```python
# create cnn learner
resnet50 = cnn_learner(data=data, # ImageDataBunch we created earlier
                       base_arch=models.resnet50, # basic architecture, resnet50
                       metrics=[accuracy], # metrics we're going to judge performance
                       pretrained=True, # use pretrained model weights
                       bn_final=True, # batch normalize, puts input on same scale
                       path=path) # save model weights to this location
```

## _What are we about to do?_

Before going any further let's touch on what is about to go on in the following cells. One of the hyperparameters that must be chosen for deep learning is the learning rate. What is this? Basically it determines how quickly (or slowly) or neural net learns. A rate that is too low will learn too slow while one that is too high may cause your model to end up extremely far away from the minimum (which is even more undesireable). 

However this doesn't have to be a guessing game. `fast.ai` has a tool, called [`lr_find()`](https://docs.fast.ai/basic_train.html#lr_find), that allows us to explore a set of learning rates, tested on the data, with their resulting losses. Let's take a look at what the resulting graph looks like before I go any further as visualizations help a lot when explaining this topic. 


```python
# explore learning rates
resnet50.lr_find()
```





    LR Finder is complete, type {learner_name}.recorder.plot() to see the graph.



```python
# plot losses of set of learning rates above
resnet50.recorder.plot(suggestion=True)
```

    Min numerical gradient: 2.29E-02
    Min loss divided by 10: 6.31E-02



![png](/img/trial3-food101_23_1.png)


What exactly are we looking at here? On the X-axis, we have the learning rates, which starts at `1e-07` and ends at `10`. On the Y-axis, we have the `Loss`. Ok so we know that we're trying to minimize our `Loss` but what does `Loss` even mean?

This brings us to something called _Stochastic Gradient Descent_. Don't be worried; it sounds a lot scarier than it actually is. It is essentially the tool we are going to use to update the parameters of the model, which are the things that ultimately determine how good it is. As we start training the model, it's not going to be very good, meaning it's going to get (a lot of) things wrong. However, we keep training the model and this loss helps it determine how it needs to improve. What the learning rate does in this process is to tell SGD how much to adjust these parameters based on the losses it just had so as to avoid those losses in future tests. 

Now what does the graph have to do with this? It helps us determine the best learning rate at this point in time that'll hopefully lead us to an optimized loss function, which in the graph is the bottom of the crater. However we need to be careful here to not pick the learning rate that corresponds with the bottom of the crater, as this is **not** the best learning rate. What we want to do is pick a point that is slightly before the bottom, which ideally corresponds with the greatest slope. This results in a higher probability of our model finding that learning sweet-spot, where it isn't learning to fast or too slow, but just right.

In our case 'just right' is equal to the value pinpointed by the red dot (i.e. `2.75e-02`). Remember this is also just a suggestion, and its a good idea to experiment with learning rates to see how they affect training.


```python
# import callback to save model weights every time top_5_accuracy
from fastai.callbacks import SaveModelCallback

save_model = SaveModelCallback(resnet50, every='improvement', monitor='accuracy', name='best-resnet50')
```


```python
# train for 5 epochs with a learning rate of 5.5e-2, saving best performing model for top-5
resnet50.fit_one_cycle(cyc_len=10, max_lr=2.29e-02, callbacks=save_model)
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: left;">
      <th>epoch</th>
      <th>train_loss</th>
      <th>valid_loss</th>
      <th>accuracy</th>
      <th>time</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>3.721075</td>
      <td>3.415997</td>
      <td>0.199010</td>
      <td>00:48</td>
    </tr>
    <tr>
      <td>1</td>
      <td>3.689960</td>
      <td>4.329854</td>
      <td>0.103630</td>
      <td>00:50</td>
    </tr>
    <tr>
      <td>2</td>
      <td>3.671572</td>
      <td>3.768892</td>
      <td>0.145215</td>
      <td>00:51</td>
    </tr>
    <tr>
      <td>3</td>
      <td>3.502137</td>
      <td>3.439487</td>
      <td>0.197360</td>
      <td>00:50</td>
    </tr>
    <tr>
      <td>4</td>
      <td>3.357911</td>
      <td>3.269392</td>
      <td>0.226733</td>
      <td>00:50</td>
    </tr>
    <tr>
      <td>5</td>
      <td>3.227931</td>
      <td>3.642360</td>
      <td>0.275578</td>
      <td>00:49</td>
    </tr>
    <tr>
      <td>6</td>
      <td>3.087040</td>
      <td>2.869145</td>
      <td>0.305941</td>
      <td>00:49</td>
    </tr>
    <tr>
      <td>7</td>
      <td>2.914023</td>
      <td>2.837762</td>
      <td>0.332673</td>
      <td>00:51</td>
    </tr>
    <tr>
      <td>8</td>
      <td>2.766540</td>
      <td>2.712204</td>
      <td>0.338284</td>
      <td>00:49</td>
    </tr>
    <tr>
      <td>9</td>
      <td>2.684741</td>
      <td>2.950549</td>
      <td>0.343894</td>
      <td>00:49</td>
    </tr>
  </tbody>
</table>


    Better model found at epoch 0 with accuracy value: 0.19900989532470703.
    Better model found at epoch 4 with accuracy value: 0.22673267126083374.
    Better model found at epoch 5 with accuracy value: 0.2755775451660156.
    Better model found at epoch 6 with accuracy value: 0.3059405982494354.
    Better model found at epoch 7 with accuracy value: 0.3326732814311981.
    Better model found at epoch 8 with accuracy value: 0.33828383684158325.
    Better model found at epoch 9 with accuracy value: 0.3438943922519684.



```python
resnet50.recorder.plot_losses()
```


![png](/img/trial3-food101_27_0.png)



```python
# save model weights post training
resnet50.save('run1-resnet50');
```

## _Unfreeze Lower Layers and Continue Training_


```python
# load previous weights
resnet50.load('run1-resnet50');
```


```python
# unfreeze lower layers for disciminative layer training
resnet50.unfreeze()
```


```python
# check to see what learning rates we should use
resnet50.lr_find(start_lr=1e-8, end_lr=1e-2, num_it=100)
```





    LR Finder is complete, type {learner_name}.recorder.plot() to see the graph.



```python
# plot 1cycle policy from above
resnet50.recorder.plot(suggestion=True)
```

    Min numerical gradient: 4.57E-08
    Min loss divided by 10: 4.79E-08



![png](/img/trial3-food101_33_1.png)


## _Reset: Increase Image Size, Unfreeze Lower Layers and Continue Training_


```python
# load previous weights
resnet50.load('run1-resnet50');
```


```python
# increase image size to 224
data_224 = ImageDataBunch.from_df(path=path/'images', df=train_sample, valid_pct=0.2, seed=1,
                             fn_col=0, label_col=1, ds_tfms=tfms, size=224, bs=32).normalize(imagenet_stats)
```


```python
# assign new data to resnet34
resnet50.data = data_224
```


```python
data_224.show_batch(rows=3, figsize=(7,7))
```


![png](/img/trial3-food101_38_0.png)



```python
# unfreeze lower layers for disciminative layer training
resnet50.unfreeze()
```


```python
# check to see what learning rates we should use
resnet50.lr_find()
```





    LR Finder is complete, type {learner_name}.recorder.plot() to see the graph.



```python
# plot 1cycle policy from above
resnet50.recorder.plot(suggestion=True)
```

    Min numerical gradient: 5.25E-05
    Min loss divided by 10: 1.45E-04



![png](/img/trial3-food101_41_1.png)



```python
lr = resnet50.recorder.min_grad_lr; lr
```




    5.248074602497728e-05




```python
# train for 10 epochs with learning rate in above cell, saving best performing model for top-1
resnet50.fit_one_cycle(cyc_len=10, max_lr=slice(lr/5, lr), callbacks=save_model)
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: left;">
      <th>epoch</th>
      <th>train_loss</th>
      <th>valid_loss</th>
      <th>accuracy</th>
      <th>time</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>3.299402</td>
      <td>2.984344</td>
      <td>0.284488</td>
      <td>02:48</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2.912086</td>
      <td>2.594756</td>
      <td>0.364356</td>
      <td>02:47</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2.732785</td>
      <td>2.661637</td>
      <td>0.401320</td>
      <td>02:45</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2.580484</td>
      <td>2.337349</td>
      <td>0.435974</td>
      <td>02:47</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2.501576</td>
      <td>2.271135</td>
      <td>0.448185</td>
      <td>02:47</td>
    </tr>
    <tr>
      <td>5</td>
      <td>2.431066</td>
      <td>2.268961</td>
      <td>0.456766</td>
      <td>02:46</td>
    </tr>
    <tr>
      <td>6</td>
      <td>2.353289</td>
      <td>2.243566</td>
      <td>0.465677</td>
      <td>02:46</td>
    </tr>
    <tr>
      <td>7</td>
      <td>2.328480</td>
      <td>2.236106</td>
      <td>0.477228</td>
      <td>02:47</td>
    </tr>
    <tr>
      <td>8</td>
      <td>2.245538</td>
      <td>2.142384</td>
      <td>0.477558</td>
      <td>02:46</td>
    </tr>
    <tr>
      <td>9</td>
      <td>2.247908</td>
      <td>2.171129</td>
      <td>0.481848</td>
      <td>02:47</td>
    </tr>
  </tbody>
</table>


    Better model found at epoch 0 with accuracy value: 0.2844884395599365.
    Better model found at epoch 1 with accuracy value: 0.3643564283847809.
    Better model found at epoch 2 with accuracy value: 0.40132012963294983.
    Better model found at epoch 3 with accuracy value: 0.435973584651947.
    Better model found at epoch 4 with accuracy value: 0.4481848180294037.
    Better model found at epoch 5 with accuracy value: 0.45676568150520325.
    Better model found at epoch 6 with accuracy value: 0.46567657589912415.
    Better model found at epoch 7 with accuracy value: 0.47722771763801575.
    Better model found at epoch 8 with accuracy value: 0.4775577485561371.
    Better model found at epoch 9 with accuracy value: 0.48184818029403687.



```python
resnet50.recorder.plot_losses()
```


![png](/img/trial3-food101_44_0.png)



```python
# save model weights post-second training run
resnet50.save('run2-resnet50')
```

## _Play time: Implementing Data Augmentation_

Now that we've established a baseline model, let's look into data augmentation. What is this? According to `fast.ai` it "is perhaps the most important regularization technique when training a model for Computer Vision." Instead of feeding in the same pictures each time, we randomly transform them using things like brightness, zoom, and rotation so that while the picture is technically the same, the make up of the pixels is not. Now why is this important? 

Essentially what data augmentation is doing is creating new data from already existing data. It is taking the images and changing them slightly and while these changes may not be much in regards to how we (i.e. humans) interpret them, they can potentially have a very big effect on how the computer 'sees' the image. 

We have to remember that a computer sees an image as a box of pixel values essentially, and that it takes these very literally. By inducing some randomness, we're essentially keeping the deep learning model on its toes to make sure it's not just picking things up by repetition (which might happen when you feed it the same images over and over); we want it to be adaptable. What this means is that we want it to be able to pick up features that make up lasagna for example, no matter where the lasagna exists in the image. In summary, by using data augmentation we are helping our model gernalize better.

In the following cells we'll take a look at the entire list of transforms offered by `fast.ai` using an example image of `garlic_bread`, taken from the training data set.


```python
# access image from training data set
data_224.train_ds[0][0]
```




![png](/img/trial3-food101_47_0.png)




```python
# see what category the image is
data_224.train_ds[0][1]
```




    Category garlic_bread




```python
# generate function that pulls our garlic bread image to showcase transformations
def get_img():
    img = data_224.train_ds[0][0]
    return img
```

### _Transform 1: `brightness`_

This transform adjusts the brightness of the image (hence the name). Below is what our example image looks like at varying levels of brightness.


```python
import matplotlib.pyplot as plt
```


```python
fig, axs = plt.subplots(1,7,figsize=(14,6))
for change, ax in zip([0.0, 0.1, 0.25, 0.5, 0.75, 0.9, 1.0], axs):
    brightness(get_img(), change).show(ax=ax, title=f'change={change:.2f}')
```


![png](/img/trial3-food101_52_0.png)


### _Transform 2: `contrast`_

Adjusts the contrast of the image.
- Value set to 0 transforms image to grey
- Value set to 1 doesn't do anything
- Value > 1 will results in super-contrast


```python
fig, axs = plt.subplots(1,7,figsize=(14,6))
for scale, ax in zip(np.exp(np.linspace(log(0.25),log(2),7)), axs):
    contrast(get_img(), scale).show(ax=ax, title=f'scale={scale:.2f}')
```


![png](/img/trial3-food101_54_0.png)


### _Transform 3: `crop`_

Crops the image to return one of size given. Position is given by `(col_pct, row_pct)`, which are normalized between 0-1.


```python
fig, axs = plt.subplots(1,5,figsize=(12,4))
for center, ax in zip([[0.,0.], [0.,1.],[0.5,0.5],[1.,0.], [1.,1.]], axs):
    crop(get_img(), 300, *center).show(ax=ax, title=f'center=({center[0]}, {center[1]})')
```


![png](/img/trial3-food101_56_0.png)


These are the first three transformations, but we have a few left to review:
- `crop_pad`
- `dihedral`
- `dihedral_affine`
- `flip_lr`
- `flip_affine`
- `jitter`
- `pad`
- `perspective_warp`
- `resize`
- `rotate`
- `rgb_randomize`
- `skew`
- `squish`
- `symmetric_warp`
- `tilt`
- `zoom`
- `cutout`
We'll review these tomorrow. After we review the specifics of each, we'll see what the default transformations are for `fast.ai` and see what alterations we could implement to help the model.

Additionally, there are two more things I want to experiment with: the [`Ranger`](https://medium.com/@lessw/new-deep-learning-optimizer-ranger-synergistic-combination-of-radam-lookahead-for-the-best-of-2dc83f79a48d) optimizer and the [`Mish`](https://medium.com/@lessw/meet-mish-new-state-of-the-art-ai-activation-function-the-successor-to-relu-846a6d93471f) activation function. 

Onward and upwards!
