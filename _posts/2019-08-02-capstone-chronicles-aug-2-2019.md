---
layout: post
published: false
title: 'Capstone Chronicles: Aug. 2, 2019'
subtitle: Writings for Comprehensive Analysis of Second Capstone Project
date: '2019-08-02'
---
This is the third and potentially final post of the comprehensive analysis for my second capstone project. Yesterday, I had just started to talk about covolutional neural networks and what they were so that's where we'll pick back up! 

Hope you enjoy!

## CNN's and How DenseNet is (Slightly) Different

As you can see, a black pixel is assigned a value of 0, and a white pixel is assigned a value of 255, with a variety of numbers in-between indicating different shades. Now what does this have to do with a CNN?

Glad you asked! The first part of the CNN is called the _Convolutional Layer_, which uses a kernel to iterate over a pixel space like the one with the number 8 above. In an attempt to be as user-friendly as possible, a visualization my help to better understand this process.

![cnn_kernel.gif](/img/cnn_kernel.gif)

[(Source)](https://hackernoon.com/visualizing-parts-of-convolutional-neural-networks-using-keras-and-cats-5cc01b214e59)

The green box is the image with each of its pixels, the yellow box is our kernel, and the pink box is our convolved feature (which I'll get to in a second). Now, the kernel can be viewed kind of like a flashlight, where it steadily works its way across the entire width and height of the image until it has shined its light on every part of it. Guess you could call it a deep learning version of 'the shining'. 

![the_shining.jpg](/img/the_shining.jpg)

[(Source)](https://www.amazon.com/Shining-POSTER-Movie-11-Inches/dp/B00KK6JLZY)

What this kernel (or 'shining') is doing each step of the way is performing matrix multiplication between its weights/parameters (i.e., the red numbers in the bottom right corner) and the pixel values in the image.

Shining performing matrix multiplication along at each step. Notice that red numbers in the bottom right corner of the kernelwork its way from the top left of the image to the bottom right, performing a matrix multiplication each step along the way. If you are unfamiliar with matrix multiplication I highly suggest checking out this [website](http://matrixmultiplication.xyz/). It provides a great visualization of how it works. 

This is where the pink box comes into play. These matrix multiplications are then all summed up and output to the convolved feature. For example, the kernel starts in the upper left corner, performs matrix multiplication after which it sums these numbers together and outputs that number (i.e., 4) to the convolved feature (i.e. pink box).

The main goal of this convolutional layer is to filter, as it checks for patterns in each section of the image. This brings us to the pooling layer, which is done in a similar way to the convolution in that another kernel is involved except we're attempting to further reduce the spatial size of our convolved feature. There are two primary approachs for pooling - max and average - which either returns the max value or the average of all the values from a particular section covered by the pooling kernel. The last main layer is the activation layer, which takes these linear outputs and squashes them into a range using a nonlinear function like ReLu (rectified linear unit). A ReLu sounds a lot scarier than it really is. Mathematically, it can be defined as _y = max(0,x)_, meaning that if the number is negative it is 0, otherwise it takes whatever value x is. 



Now the convolutional and pooling layer together form a layer of a neural network. Most of the time, there are many of these layers in any convolutional neural network, with 100+ layers not being uncommon for more complicated data (like medical images). 



convolved feature will then be passed into a pooling layer, which is similar to the convolutional layer. However, it is describes each section of the image that using a single value (which is usually the max or average of that window). 