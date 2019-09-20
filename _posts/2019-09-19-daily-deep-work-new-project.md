---
layout: post
published: true
title: 'Daily Deep Work: New Project'
subtitle: Initial Development of a DL Model on Food-101 Dataset
date: '2019-09-19'
excerpt: >-
  Food-101 is a challenging vision problem, but everyone can relate to it. 
  Recent SoTA is ~80% top-1, 90% top-5.  These approaches rely on lots of TTA,
  large networks and even novel architectures.
image: /img/trial2_food101_57_1.png
---
# _Trial 2: fellowship.ai Food-101 Challenge_

**_From fellowship.ai's website:_**

_Food-101 is a challenging vision problem, but everyone can relate to it.  Recent SoTA is ~80% top-1, 90% top-5.  These approaches rely on lots of TTA, large networks and even novel architectures._

_Train a decent model >85% accuracy for top-1 for the test set, using a ResNet50 or smaller network with a reasonable set of augmentations._


```python
# import necessary libraries to set path variable for environment
import os
from pathlib import Path

# get current working directory
os.getcwd()
```




    '/home/jupyter/projects'




```python
#create path variable to primary directory
PATH = Path(os.getcwd())
PATH
```




    PosixPath('/home/jupyter/projects')



## _Download Food-101_


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
# load and untar data (if this is first time)
path = untar_data(URLs.FOOD, dest=PATH)
```


```python
path = PATH/'food-101'; path
```




    PosixPath('/home/jupyter/projects/food-101')




```python
path.ls()
```




    [PosixPath('/home/jupyter/projects/food-101/test.txt'),
     PosixPath('/home/jupyter/projects/food-101/labels.txt'),
     PosixPath('/home/jupyter/projects/food-101/train.txt'),
     PosixPath('/home/jupyter/projects/food-101/test.json'),
     PosixPath('/home/jupyter/projects/food-101/images'),
     PosixPath('/home/jupyter/projects/food-101/train.json'),
     PosixPath('/home/jupyter/projects/food-101/h5'),
     PosixPath('/home/jupyter/projects/food-101/classes.txt')]



## _Load in Training Data_


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


```python
# gather a 25% sample of original training data set
train_sample = train.sample(frac=0.25, random_state=1)
```


```python
train_sample.shape
```




    (18938, 2)



## _Input Data For Model_


```python
tfms = get_transforms(do_flip=False)

# create ImageDataBunch from train df
data = ImageDataBunch.from_df(path=path/'images', df=train_sample, valid_pct=0.2, seed=1,
                             fn_col=0, label_col=1, ds_tfms=tfms, size=64, bs=32).normalize(imagenet_stats)
```


```python
data.show_batch(rows=3, figsize=(7,7))
```


![png](/img/trial2_food101_18_0.png)



```python
print(data.c)
```

    101


## _Create Baseline Learner: `resnet34`_


```python
# create top-5 metric
top_5_accuracy = partial(top_k_accuracy, k=5)
```


```python
# create cnn learner
resnet34 = cnn_learner(data=data, base_arch=models.resnet34, metrics=[accuracy, top_5_accuracy], 
                       pretrained=True, bn_final=True, path=path)
```


```python
resnet34.lr_find()
```





    LR Finder is complete, type {learner_name}.recorder.plot() to see the graph.



```python
# plot 1cycle policy from above
resnet34.recorder.plot(suggestion=True)
```

    Min numerical gradient: 6.31E-07
    Min loss divided by 10: 9.12E-02



![png](/img/trial2_food101_24_1.png)



```python
# import callback to save model weights every time top_5_accuracy
from fastai.callbacks import SaveModelCallback

save_model = SaveModelCallback(resnet34, every='improvement', monitor='top_k_accuracy', name='best')
```


```python
# train for 5 epochs with a learning rate of 5.5e-2, saving best performing model for top-5
resnet34.fit_one_cycle(cyc_len=5, max_lr=5.5e-2, callbacks=save_model)
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: left;">
      <th>epoch</th>
      <th>train_loss</th>
      <th>valid_loss</th>
      <th>accuracy</th>
      <th>top_k_accuracy</th>
      <th>time</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>3.948081</td>
      <td>4.325855</td>
      <td>0.082123</td>
      <td>0.270399</td>
      <td>00:59</td>
    </tr>
    <tr>
      <td>1</td>
      <td>4.035301</td>
      <td>4.128549</td>
      <td>0.097175</td>
      <td>0.280169</td>
      <td>01:01</td>
    </tr>
    <tr>
      <td>2</td>
      <td>3.856627</td>
      <td>3.774067</td>
      <td>0.135727</td>
      <td>0.359123</td>
      <td>01:00</td>
    </tr>
    <tr>
      <td>3</td>
      <td>3.595999</td>
      <td>4.397167</td>
      <td>0.193557</td>
      <td>0.444943</td>
      <td>00:59</td>
    </tr>
    <tr>
      <td>4</td>
      <td>3.465780</td>
      <td>3.336941</td>
      <td>0.211777</td>
      <td>0.470821</td>
      <td>01:01</td>
    </tr>
  </tbody>
</table>


    Better model found at epoch 0 with top_k_accuracy value: 0.27039873600006104.
    Better model found at epoch 1 with top_k_accuracy value: 0.2801690101623535.
    Better model found at epoch 2 with top_k_accuracy value: 0.3591233193874359.
    Better model found at epoch 3 with top_k_accuracy value: 0.44494321942329407.
    Better model found at epoch 4 with top_k_accuracy value: 0.47082123160362244.



```python
resnet34.recorder.plot_losses()
```


![png](/img/trial2_food101_27_0.png)



```python
# save model weights post training
resnet34.save('run1-resnet34');
```

## _Unfreeze Lower Layers and Continue Training_


```python
# load previous weights
resnet34.load('run1-resnet34');
```


```python
# unfreeze lower layers for disciminative layer training
resnet34.unfreeze()
```


```python
# check to see what learning rates we should use
resnet34.lr_find()
```





    LR Finder is complete, type {learner_name}.recorder.plot() to see the graph.



```python
# plot 1cycle policy from above
resnet34.recorder.plot(suggestion=True)
```

    Min numerical gradient: 7.59E-07
    Min loss divided by 10: 4.79E-07



![png](/img/trial2_food101_33_1.png)


## _Reset: Increase Image Size, Unfreeze Lower Layers and Continue Training_


```python
# load previous weights
resnet34.load('run1-resnet34');
```


```python
# increase image size to 224
data_224 = ImageDataBunch.from_df(path=path/'images', df=train_sample, valid_pct=0.2, seed=1,
                             fn_col=0, label_col=1, ds_tfms=tfms, size=224, bs=32).normalize(imagenet_stats)
```


```python
# assign new data to resnet34
resnet34.data = data_224
```


```python
data_224.show_batch(rows=3, figsize=(7,7))
```


![png](/img/trial2_food101_38_0.png)



```python
# unfreeze lower layers for disciminative layer training
resnet34.unfreeze()
```


```python
# check to see what learning rates we should use
resnet34.lr_find()
```





    LR Finder is complete, type {learner_name}.recorder.plot() to see the graph.



```python
# plot 1cycle policy from above
resnet34.recorder.plot(suggestion=True)
```

    Min numerical gradient: 6.31E-07
    Min loss divided by 10: 3.02E-04



![png](/img/trial2_food101_41_1.png)



```python
# train for 5 epochs with a learning rate of 5.5e-2, saving best performing model for top-5
resnet34.fit_one_cycle(cyc_len=5, max_lr=slice(5.5e-5, 5.5e-4), callbacks=save_model)
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: left;">
      <th>epoch</th>
      <th>train_loss</th>
      <th>valid_loss</th>
      <th>accuracy</th>
      <th>top_k_accuracy</th>
      <th>time</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>3.446962</td>
      <td>3.603327</td>
      <td>0.212041</td>
      <td>0.474518</td>
      <td>02:02</td>
    </tr>
    <tr>
      <td>1</td>
      <td>3.335187</td>
      <td>3.533222</td>
      <td>0.200687</td>
      <td>0.482968</td>
      <td>02:04</td>
    </tr>
    <tr>
      <td>2</td>
      <td>3.154774</td>
      <td>3.294439</td>
      <td>0.284394</td>
      <td>0.592553</td>
      <td>02:03</td>
    </tr>
    <tr>
      <td>3</td>
      <td>3.035071</td>
      <td>2.895250</td>
      <td>0.331661</td>
      <td>0.634011</td>
      <td>02:03</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2.972645</td>
      <td>2.831399</td>
      <td>0.333773</td>
      <td>0.648534</td>
      <td>02:03</td>
    </tr>
  </tbody>
</table>


    Better model found at epoch 0 with top_k_accuracy value: 0.4745180904865265.
    Better model found at epoch 1 with top_k_accuracy value: 0.4829680621623993.
    Better model found at epoch 2 with top_k_accuracy value: 0.5925534963607788.
    Better model found at epoch 3 with top_k_accuracy value: 0.6340110898017883.
    Better model found at epoch 4 with top_k_accuracy value: 0.6485344767570496.



```python
resnet34.recorder.plot_losses()
```


![png](/img/trial2_food101_43_0.png)



```python
# save model weights post-second training run
resnet34.save('run2-resnet34')
```

## _Training Check: How much further can we go?_

From the second run of training, we were able to get out `top_k_accuracy` up to ~0.64, which is decent after only 10 epochs. 

But how much further can we go? Our graph plotting training and validation losses seemed to be flattening out. However the training loss is still above validation so I'm hypothesizing that we might be able to squeeze in a few more rounds of training before we move into data augmentation.


```python
# load model weights post-second training run
resnet34.load('run2-resnet34');
```


```python
# freeze lower layers
resnet34.freeze()
```


```python
# check to see what learning rates we should use
resnet34.lr_find()
```





    LR Finder is complete, type {learner_name}.recorder.plot() to see the graph.



```python
# plot 1cycle policy from above
resnet34.recorder.plot(suggestion=True)
```

    Min numerical gradient: 3.98E-06
    Min loss divided by 10: 6.31E-08



![png](/img/trial2_food101_49_1.png)


From the graph above, it indicates we have reached a minima but I highly doubt it is the global one. I'm going to implement a new deep learning optimizer called _Ranger_, courtesy of [Less Wright](https://medium.com/@lessw/new-deep-learning-optimizer-ranger-synergistic-combination-of-radam-lookahead-for-the-best-of-2dc83f79a48d). We'll see if we can get any performance boosts from implementing Ranger.


```python
# import Ranger
from ranger import Ranger
```


```python
# prep Ranger to be used in model
optar = partial(Ranger.Ranger)
```


```python
# assign Ranger to resnet34
resnet34.opt_func = optar
```

## _With `Ranger`, test to see if we can train further_


```python
# load model weights post-second training run
resnet34.load('run2-resnet34');
```


```python
# check to see what learning rates we should use
resnet34.lr_find()
```





    LR Finder is complete, type {learner_name}.recorder.plot() to see the graph.



```python
# plot 1cycle policy from above
resnet34.recorder.plot(suggestion=True)
```

    Min numerical gradient: 1.91E-06
    Min loss divided by 10: 3.31E-05



![png](/img/trial2_food101_57_1.png)



```python
# train for 5 epochs with a learning rate of 5.5e-2, saving best performing model for top-5
resnet34.fit_one_cycle(cyc_len=5, max_lr=1e-4, callbacks=SaveModelCallback(resnet34, 
                                                                           every='improvement', 
                                                                           monitor='top_k_accuracy', 
                                                                           name='best-run3'))
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: left;">
      <th>epoch</th>
      <th>train_loss</th>
      <th>valid_loss</th>
      <th>accuracy</th>
      <th>top_k_accuracy</th>
      <th>time</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>2.949650</td>
      <td>2.826079</td>
      <td>0.334566</td>
      <td>0.650647</td>
      <td>01:51</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2.946477</td>
      <td>2.807351</td>
      <td>0.331661</td>
      <td>0.651967</td>
      <td>01:49</td>
    </tr>
    <tr>
      <td>2</td>
      <td>2.956119</td>
      <td>3.389427</td>
      <td>0.334566</td>
      <td>0.648006</td>
      <td>01:49</td>
    </tr>
    <tr>
      <td>3</td>
      <td>2.934148</td>
      <td>3.240390</td>
      <td>0.337470</td>
      <td>0.650911</td>
      <td>01:49</td>
    </tr>
    <tr>
      <td>4</td>
      <td>2.904360</td>
      <td>6.754512</td>
      <td>0.333773</td>
      <td>0.649855</td>
      <td>01:51</td>
    </tr>
  </tbody>
</table>


    Better model found at epoch 0 with top_k_accuracy value: 0.6506469249725342.
    Better model found at epoch 1 with top_k_accuracy value: 0.6519672274589539.



```python

```
