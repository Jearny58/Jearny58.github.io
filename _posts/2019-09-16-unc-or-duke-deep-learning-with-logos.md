---
layout: post
published: true
title: UNC or Duke?
subtitle: Developing a DL Model with fast.ai
date: '2019-09-16'
image: /img/unc-duke_45_0.png
---
<!--more-->
# _UNC or Duke: Deep Learning with Logos_

**Introduction:**
 <!--more-->
While listening to Lex Fridman and Jeremy Howard recently on a [podcast](https://www.youtube.com/watch?v=J6XcP4JOHmk) (highly recommend check it out!), I heard Jeremy mention that one of the best ways to become an expert DL practitioner was, essentially, to build a ton of models (with data your interested in of course)!

So that is the aim of this notebook: to generate a deep learning model to predict something I'm interested in. More specifically, we'll be using a data set of images that contain UNC's or Duke's logo, and training it to recognize each one! 

First thing we need to do though is ensure our `path` variable is set. What is this variable going to do? `path` will help us make sure we're in the main directory. It can get confusing once you get into a project to keep track of everything so knowing we always have a link back to the main directory helps big time. 


```python
# make sure PyTorch and fastai are up-to-date
!pip install torch -U
!pip install torchvision -U
!pip install fastai -U
```


```python
from fastai.vision import *
```

## _Set `path` and `dest` variables for environment_


```python
# mount your google drive so you can save to it
from google.colab import drive
drive.mount('/content/gdrive')
```

    Drive already mounted at /content/gdrive; to attempt to forcibly remount, call drive.mount("/content/gdrive", force_remount=True).



```python
# import os and Path from pathlib library
import os 
from pathlib import Path

# get current working directory
print('My current working directory is {}'.format(os.getcwd()))
```

    My current working directory is /content



```python
# change working directory to the one below
os.chdir('/content/gdrive/My Drive/projects/unc_duke')
```


```python
# check current working directory now
print('My current working directory is {}'.format(os.getcwd()))
```

    My current working directory is /content/gdrive/My Drive/projects/unc_duke



```python
#create path variable to primary directory
path = Path(os.getcwd())
print(path)
```

    /content/gdrive/My Drive/projects/unc_duke


## _Create directories for UNC/Duke images and Upload URLs file_


```python
# create folder and file_name for UNC upload
folder = 'unc'
file_name = 'unc_links.csv'
```


```python
# create destination variable
dest = path/folder
dest.mkdir(parents=True, exist_ok=True)
```

## _Download Images For UNC_


```python
classes = ['unc', 'duke']
```


```python
download_images(path/file_name, dest, max_pics=200)
```

## _Download Images For Duke_


```python
# create folder and file_name for Duke upload
folder = 'duke'
file_name = 'duke_links.csv'
```


```python
# create destination variable
dest = path/folder
dest.mkdir(parents=True, exist_ok=True)
```


```python
download_images(path/file_name, dest, max_pics=200)
```


```python
# check to make sure everything worked
path.ls()
```




    [PosixPath('/content/gdrive/My Drive/projects/unc_duke/unc_links.csv'),
     PosixPath('/content/gdrive/My Drive/projects/unc_duke/duke_links.csv'),
     PosixPath('/content/gdrive/My Drive/projects/unc_duke/models'),
     PosixPath('/content/gdrive/My Drive/projects/unc_duke/test'),
     PosixPath('/content/gdrive/My Drive/projects/unc_duke/train')]




```python
# remove any images that can't be opened
for c in classes:
    print(c)
    verify_images(path/c, delete=True, max_size=500)
```

## _View Data_


```python
np.random.seed(1)

tfms = get_transforms(do_flip=False, max_zoom=1, xtra_tfms=[crop_pad()])

data = ImageDataBunch.from_folder(path/'train', valid_pct=0.2,
        ds_tfms=tfms, size=320, num_workers=4, bs=32).normalize(imagenet_stats)
```


```python
data
```




    ImageDataBunch;
    
    Train: LabelList (164 items)
    x: ImageList
    Image (3, 320, 320),Image (3, 320, 320),Image (3, 320, 320),Image (3, 320, 320),Image (3, 320, 320)
    y: CategoryList
    unc,unc,unc,unc,unc
    Path: /content/gdrive/My Drive/projects/unc_duke/train;
    
    Valid: LabelList (40 items)
    x: ImageList
    Image (3, 320, 320),Image (3, 320, 320),Image (3, 320, 320),Image (3, 320, 320),Image (3, 320, 320)
    y: CategoryList
    duke,duke,unc,duke,duke
    Path: /content/gdrive/My Drive/projects/unc_duke/train;
    
    Test: None




```python
data.add_test(ImageList.from_folder(path/'test'))
```


```python
data
```




    ImageDataBunch;
    
    Train: LabelList (164 items)
    x: ImageList
    Image (3, 320, 320),Image (3, 320, 320),Image (3, 320, 320),Image (3, 320, 320),Image (3, 320, 320)
    y: CategoryList
    unc,unc,unc,unc,unc
    Path: /content/gdrive/My Drive/projects/unc_duke/train;
    
    Valid: LabelList (40 items)
    x: ImageList
    Image (3, 320, 320),Image (3, 320, 320),Image (3, 320, 320),Image (3, 320, 320),Image (3, 320, 320)
    y: CategoryList
    duke,duke,unc,duke,duke
    Path: /content/gdrive/My Drive/projects/unc_duke/train;
    
    Test: LabelList (10 items)
    x: ImageList
    Image (3, 320, 320),Image (3, 320, 320),Image (3, 320, 320),Image (3, 320, 320),Image (3, 320, 320)
    y: EmptyLabelList
    ,,,,
    Path: /content/gdrive/My Drive/projects/unc_duke/train




```python
data.classes
```




    ['duke', 'unc']




```python
data.show_batch(rows=3, figsize=(8,8))
```


![png](/img/unc-duke_27_0.png)



```python
# print out classes, number of classes and lengths of training/validation sets
data.classes, data.c, len(data.train_ds), len(data.valid_ds)
```




    (['duke', 'unc'], 2, 164, 40)



## _Create Initial Model: `resnet34`_


```python
from fastai.callbacks import SaveModelCallback

# create learner
learn = cnn_learner(data, models.resnet34,
                    metrics=accuracy,
                    pretrained=True,
                    bn_final=True,
                    path=path)
```


```python
# train model for 5 epochs, saving the model weights every time accuracy improves
learn.fit_one_cycle(5, callbacks=[(SaveModelCallback(learn, every='improvement', 
                                                     monitor='accuracy', 
                                                     name='unc-duke-trial1'))])
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
      <td>0.810690</td>
      <td>0.485516</td>
      <td>0.800000</td>
      <td>00:08</td>
    </tr>
    <tr>
      <td>1</td>
      <td>0.555058</td>
      <td>0.241592</td>
      <td>0.925000</td>
      <td>00:06</td>
    </tr>
    <tr>
      <td>2</td>
      <td>0.444780</td>
      <td>0.198738</td>
      <td>0.900000</td>
      <td>00:05</td>
    </tr>
    <tr>
      <td>3</td>
      <td>0.384025</td>
      <td>0.181156</td>
      <td>0.900000</td>
      <td>00:06</td>
    </tr>
    <tr>
      <td>4</td>
      <td>0.349751</td>
      <td>0.173979</td>
      <td>0.925000</td>
      <td>00:06</td>
    </tr>
  </tbody>
</table>


    Better model found at epoch 0 with accuracy value: 0.800000011920929.
    Better model found at epoch 1 with accuracy value: 0.925000011920929.



```python
# plot losses from training & validation
learn.recorder.plot_losses()
```


![png](/img/unc-duke_32_0.png)



```python
# save model weights after very last epoch
learn.save('unc-duke-rd1-end');
```


```python
# reload weights (this also helps keep our memory allocation from freaking out)
learn.load('unc-duke-trial1');
```


```python
# perform disciminative layer training, to allow lower layers weights to be updated to hopefully boost performance
learn.unfreeze()
```


```python
# using 1cycle policy, find best learning rate
learn.lr_find()
```





    LR Finder is complete, type {learner_name}.recorder.plot() to see the graph.



```python
# plot 1cycle policy from above
learn.recorder.plot(suggestion=True)
```

    Min numerical gradient: 4.37E-05
    Min loss divided by 10: 7.59E-04



![png](/img/unc-duke_37_1.png)



```python
# train for 5 more epochs, with smaller learning rate for lower layers, higher learning rate for higher layers
# and saving best performing model based on accuracy
learn.fit_one_cycle(5, max_lr=slice(7e-4, 1e-3), callbacks=[(SaveModelCallback(learn, 
                                                                               every='improvement', 
                                                                               monitor='accuracy', 
                                                                               name='unc-duke-trial2'))])
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
      <td>0.259112</td>
      <td>0.313412</td>
      <td>0.925000</td>
      <td>00:07</td>
    </tr>
    <tr>
      <td>1</td>
      <td>0.236715</td>
      <td>0.344866</td>
      <td>0.875000</td>
      <td>00:06</td>
    </tr>
    <tr>
      <td>2</td>
      <td>0.224685</td>
      <td>0.111119</td>
      <td>0.975000</td>
      <td>00:06</td>
    </tr>
    <tr>
      <td>3</td>
      <td>0.207349</td>
      <td>0.046151</td>
      <td>0.950000</td>
      <td>00:07</td>
    </tr>
    <tr>
      <td>4</td>
      <td>0.194271</td>
      <td>0.067002</td>
      <td>0.950000</td>
      <td>00:06</td>
    </tr>
  </tbody>
</table>


    Better model found at epoch 0 with accuracy value: 0.925000011920929.
    Better model found at epoch 2 with accuracy value: 0.9750000238418579.



```python
# save model weights after last epoch
learn.save('unc-duke-end-trial2')
```

## _Interpretation_


```python
# reload weights
learn.load('unc-duke-end-trial2');
```


```python
# create object that allows us to interpret performance of model
interp = ClassificationInterpretation.from_learner(learn)
```


```python
# plot confusion matrix which shows all true labels, and any false positive/negatives
interp.plot_confusion_matrix(figsize=(6,6))
```


![png](/img/unc-duke_43_0.png)



```python
# images that had highest loss (i.e. most wrong)
interp.plot_top_losses(4, figsize=(8,8), heatmap=False)
```


![png](/img/unc-duke_44_0.png)



```python
# same as above but also shows pixels that most contributed to model's decisions
interp.plot_top_losses(4, figsize=(8,8), heatmap=True)
```


![png](/img/unc-duke_45_0.png)



```python
# plot losses from training period
learn.recorder.plot_losses()
```


![png](/img/unc-duke_46_0.png)


## _Go Big...ger?_

Our `resnet34` has done pretty good with an accuracy of ~95%! Let's see what a `resnet50` might be able to do though.


```python
# create learner
learn_50 = cnn_learner(data, models.resnet50, # use a pretrained resnet50 architecture
                    metrics=accuracy,
                    pretrained=True,
                    bn_final=True, # batch normalization, allows each layer to learn a little more independently from the others
                    path=path)
```


```python
# train resnet50 for 5 epochs, saving most accurate model
learn_50.fit_one_cycle(5, callbacks=[(SaveModelCallback(learn_50, every='improvement', 
                                                     monitor='accuracy', 
                                                     name='unc-duke-trial1-resnet50'))])
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
      <td>0.748516</td>
      <td>0.741307</td>
      <td>0.800000</td>
      <td>00:23</td>
    </tr>
    <tr>
      <td>1</td>
      <td>0.497197</td>
      <td>0.315155</td>
      <td>0.925000</td>
      <td>00:08</td>
    </tr>
    <tr>
      <td>2</td>
      <td>0.397847</td>
      <td>0.182562</td>
      <td>0.925000</td>
      <td>00:08</td>
    </tr>
    <tr>
      <td>3</td>
      <td>0.345343</td>
      <td>0.160791</td>
      <td>0.925000</td>
      <td>00:08</td>
    </tr>
    <tr>
      <td>4</td>
      <td>0.312566</td>
      <td>0.158861</td>
      <td>0.925000</td>
      <td>00:08</td>
    </tr>
  </tbody>
</table>


    Better model found at epoch 0 with accuracy value: 0.800000011920929.
    Better model found at epoch 1 with accuracy value: 0.925000011920929.



```python
# save post-training weights
learn_50.save('unc-duke-end1-resnet50');
```


```python
# load post-training weights
learn_50.load('unc-duke-end1-resnet50');
```


```python
# unfreeze model weights to allow for disciminative layer training
learn_50.unfreeze()
```


```python
# using 1cycle policy, see what best learning rate is
learn_50.lr_find()
```





    LR Finder is complete, type {learner_name}.recorder.plot() to see the graph.



```python
# plot 1cycle results
learn_50.recorder.plot(suggestion=True)
```

    Min numerical gradient: 3.31E-06
    Min loss divided by 10: 2.51E-04



![png](/img/unc-duke_54_1.png)



```python
# with unfrozen architecture, train for 5 epochs, with a smaller learning rate for lower layers, higher learning rate for higher ones
learn_50.fit_one_cycle(5, max_lr=slice(1e-5, 1e-4), callbacks=[(SaveModelCallback(learn_50, every='improvement', 
                                                                                  monitor='accuracy', 
                                                                                  name='unc-duke-trial2-resnet50'))])
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
      <td>0.241894</td>
      <td>0.185680</td>
      <td>0.925000</td>
      <td>00:10</td>
    </tr>
    <tr>
      <td>1</td>
      <td>0.231957</td>
      <td>0.124357</td>
      <td>0.950000</td>
      <td>00:10</td>
    </tr>
    <tr>
      <td>2</td>
      <td>0.216164</td>
      <td>0.121021</td>
      <td>0.950000</td>
      <td>00:11</td>
    </tr>
    <tr>
      <td>3</td>
      <td>0.203820</td>
      <td>0.126861</td>
      <td>0.950000</td>
      <td>00:10</td>
    </tr>
    <tr>
      <td>4</td>
      <td>0.195850</td>
      <td>0.134475</td>
      <td>0.950000</td>
      <td>00:10</td>
    </tr>
  </tbody>
</table>


    Better model found at epoch 0 with accuracy value: 0.925000011920929.
    Better model found at epoch 1 with accuracy value: 0.949999988079071.



```python
# save post-training weights
learn_50.save('unc-duke-end2-resnet50')
```


```python
# load post-training weights
learn_50.load('unc-duke-end2-resnet50');
```

## _Interpretation: `resnet50`_


```python
# create object so we can assess performance of model
interp_50 = ClassificationInterpretation.from_learner(learn_50)
```


```python
# confusion matrix indicating all true labels and the false positives/negatives
interp_50.plot_confusion_matrix(figsize=(6,6))
```


![png](/img/unc-duke_60_0.png)



```python
# display images that had the top losses (i.e. most wrong)
interp_50.plot_top_losses(4, figsize=(8,8), heatmap=False)
```


![png](/img/unc-duke_61_0.png)



```python
# same as above, but shows pixels that contributed most towards the model's decision
interp_50.plot_top_losses(4, figsize=(8,8), heatmap=True)
```


![png](/img/unc-duke_62_0.png)



```python
learn_50.recorder.plot_losses()
```


![png](/img/unc-duke_63_0.png)


## _Predict on Test Set_


```python
# get predictions from best performing model
preds, y = learn_50.get_preds(DatasetType.Test)
```


```python
# names of the pictures in test set
for n in range(10):
    print(str(data.test_ds.x.items[n])[49:60])
```

    0000014.jpg
    0000023.jpg
    0000025.png
    0000060.jpg
    0000098.JPG
    0000008.jpg
    0000040.jpg
    0000050.jpg
    0000063.jpg
    0000093.jpg



```python
# extract picture names and append to list
picture_list = []

for n in range(10):
    pic_name = str(data.test_ds.x.items[n])[49:60]
    picture_list.append(pic_name)

picture_list
```




    ['0000014.jpg',
     '0000023.jpg',
     '0000025.png',
     '0000060.jpg',
     '0000098.JPG',
     '0000008.jpg',
     '0000040.jpg',
     '0000050.jpg',
     '0000063.jpg',
     '0000093.jpg']




```python
# return probabilities for each item in test set
preds
```




    tensor([[0.0556, 0.9444],
            [0.0076, 0.9924],
            [0.0537, 0.9463],
            [0.1602, 0.8398],
            [0.0270, 0.9730],
            [0.9979, 0.0021],
            [0.9061, 0.0939],
            [0.8905, 0.1095],
            [0.9047, 0.0953],
            [0.9077, 0.0923]])




```python
numpy_preds = preds.numpy(); numpy_preds
```




    array([[0.055649, 0.944351],
           [0.0076  , 0.9924  ],
           [0.053655, 0.946345],
           [0.160154, 0.839846],
           [0.027033, 0.972967],
           [0.997909, 0.002091],
           [0.906121, 0.093879],
           [0.890511, 0.109489],
           [0.904659, 0.095341],
           [0.907741, 0.092259]], dtype=float32)




```python
numpy_preds[1][0]
```




    0.007600217




```python
preds1 = []
# extract first column of prediction values
for n in range(10):
    x = numpy_preds[n][0]
    preds1.append(x)

preds1
```




    [0.05564865,
     0.007600217,
     0.053655036,
     0.16015372,
     0.027033059,
     0.9979091,
     0.9061212,
     0.89051133,
     0.90465873,
     0.9077409]




```python
preds2 = []

# extract second column of prediction values
for n in range(10):
    x = numpy_preds[n][1]
    preds2.append(x)

preds2
```




    [0.9443514,
     0.99239975,
     0.946345,
     0.83984625,
     0.97296697,
     0.0020907938,
     0.09387884,
     0.10948862,
     0.09534128,
     0.09225914]




```python
# convert probabilities into labels 
labels = np.argmax(preds, 1)
labels_with_names = [data.classes[i].replace('_', ' ') for i in labels]; labels_with_names
```




    ['unc', 'unc', 'unc', 'unc', 'unc', 'duke', 'duke', 'duke', 'duke', 'duke']




```python
# create a dictionary that will store all this information
test_dictionary = {'picture_file': picture_list, 'preds1': preds1, 'preds2': preds2, 'predictions': labels_with_names}; test_dictionary
```




    {'picture_file': ['0000014.jpg',
      '0000023.jpg',
      '0000025.png',
      '0000060.jpg',
      '0000098.JPG',
      '0000008.jpg',
      '0000040.jpg',
      '0000050.jpg',
      '0000063.jpg',
      '0000093.jpg'],
     'predictions': ['unc',
      'unc',
      'unc',
      'unc',
      'unc',
      'duke',
      'duke',
      'duke',
      'duke',
      'duke'],
     'preds1': [0.05564865,
      0.007600217,
      0.053655036,
      0.16015372,
      0.027033059,
      0.9979091,
      0.9061212,
      0.89051133,
      0.90465873,
      0.9077409],
     'preds2': [0.9443514,
      0.99239975,
      0.946345,
      0.83984625,
      0.97296697,
      0.0020907938,
      0.09387884,
      0.10948862,
      0.09534128,
      0.09225914]}




```python
test_results_df = pd.DataFrame(test_dictionary); test_results_df
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
      <th>picture_file</th>
      <th>preds1</th>
      <th>preds2</th>
      <th>predictions</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0000014.jpg</td>
      <td>0.055649</td>
      <td>0.944351</td>
      <td>unc</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0000023.jpg</td>
      <td>0.007600</td>
      <td>0.992400</td>
      <td>unc</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0000025.png</td>
      <td>0.053655</td>
      <td>0.946345</td>
      <td>unc</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0000060.jpg</td>
      <td>0.160154</td>
      <td>0.839846</td>
      <td>unc</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0000098.JPG</td>
      <td>0.027033</td>
      <td>0.972967</td>
      <td>unc</td>
    </tr>
    <tr>
      <th>5</th>
      <td>0000008.jpg</td>
      <td>0.997909</td>
      <td>0.002091</td>
      <td>duke</td>
    </tr>
    <tr>
      <th>6</th>
      <td>0000040.jpg</td>
      <td>0.906121</td>
      <td>0.093879</td>
      <td>duke</td>
    </tr>
    <tr>
      <th>7</th>
      <td>0000050.jpg</td>
      <td>0.890511</td>
      <td>0.109489</td>
      <td>duke</td>
    </tr>
    <tr>
      <th>8</th>
      <td>0000063.jpg</td>
      <td>0.904659</td>
      <td>0.095341</td>
      <td>duke</td>
    </tr>
    <tr>
      <th>9</th>
      <td>0000093.jpg</td>
      <td>0.907741</td>
      <td>0.092259</td>
      <td>duke</td>
    </tr>
  </tbody>
</table>
</div>




```python
# export dataframe above to csv
test_results_df.to_csv('unc-duke-resnet50-test-results.csv', header=list(test_results_df.columns))
```
