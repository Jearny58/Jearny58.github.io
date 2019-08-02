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

![cnn_kernel.gif]({{site.baseurl}}/img/cnn_kernel.gif)

[(Source)](https://hackernoon.com/visualizing-parts-of-convolutional-neural-networks-using-keras-and-cats-5cc01b214e59)

The green box is the image with each of its pixels, the yellow box is our kernel, and the pink box is our convolved feature (which I'll get to in a second). Now, the kernel can be viewed kind of like a flashlight, where it steadily works its way across the entire width and height of the image until it has shined its light on every part of it. Guess you could call it a deep learning version of 'the shining'. 




Shining performing matrix multiplication along at each step. Notice that red numbers in the bottom right corner of the kernelwork its way from the top left of the image to the bottom right, performing a matrix multiplication each step along the way. 