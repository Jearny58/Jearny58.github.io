---
layout: post
published: false
title: 'Capstone Chronicles: July 10, 2019'
subtitle: 'Notes & Updates from my work on July 10, 2019'
---
Another day, some more deep learning! 

Let's review the work from yesterday though first. The biggest update was that we trained a model that utilized a undersampled data set. Since Cardiomegaly - our target pathology - was highly unbalanced towards negative cases, we decided to resample with the undersampling technique first. Undersampling involves taking the majority class and essentially resizing it so that it matches the number of cases of the minority class. The result was a data set that comprised 27,000 observations of both positive and negative labels, for a total of 54,000 observations! I want to point out though that this approach causes loss of information (as we ignored approximately 160k other observations). 

Additionally, we utilized a different architecture opting to utilize DenseNet161 versus DenseNet121, with the later serving somewhat as a standard for this particular project. The hypothesis was that DenseNet161 would potentially give us the ability to capture more nuanced features from the images due to the greater number of layers. 

The initial results with the undersampled data set and DenseNet161 were very promising and in regards to the metrics, it had been the best performing model up to that point. However, upon continuing our training today with a larger image size of 320 (which was the size used by Stanford) there were memory issues galore that prevented us from even starting! As a result, we've gone back to using DenseNet121 today and it looks like DenseNet161 was not the best choice. 

## Trial 27 & 28 Review

After failing to work around the memory issues caused by DenseNet161, we iterated again and started a new Jupyter notebook. Luckily our `sample.py` script makes the grunt work of data cleaning relatively straightforward and quick. Once we got the data cleaned up and into an ImageDataBunch, we began training a DenseNet121 model with a max learning rate of `1e-1`, which was gathered from our learning rate finder plot. 
