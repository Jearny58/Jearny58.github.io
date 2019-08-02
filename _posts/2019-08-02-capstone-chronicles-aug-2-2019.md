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

Below is visual representation of what is going on with convolutions, pooling and activation layers. 

![cnn_high_level.png](/img/cnn_high_level.png)

[(Source)](https://media.springernature.com/original/springer-static/image/art%3A10.1007%2Fs13244-018-0639-9/MediaObjects/13244_2018_639_Fig1_HTML.png)

Now take note: there are multiple convolutions, ReLUs and pooling layers above. Most convolutional neural networks have multiple layers, with 100+ layers not being uncommon for more complicated input images (like medical images). Additionally you may be wondering what is going on at the end of the picture with the 'FC' columns and back propagation. The FC's are the fully connected layers, which take an input from the previous layer and outputs a vector with N dimensions, with N representing the number of classes the model has to choose from. For example, if you were trying to determine whether an image was of a cat or a dog, N would be equal to 2 (i.e. cat represents one potential class, and dog represents another potential class). What is ouput to this N-dimensional vector is not a 'yes' or a 'no'; instead it outputs the probability of being in that particular class. To continue with our cat/dog example, it may output 0.50 for the dog class and 0.50 for the cat class meaning that the image has a 50% probability of being a dog or a cat (this is known as the softmax function, which you can find more information about [here](https://en.wikipedia.org/wiki/Softmax_function)). 

This brings us to  _back propogation_, which is essential to how neural networks 'learn'. Our process began when we input the image and it worked its way through the multiple layers. This is called the forward pass and results in us getting the N-dimensional vector of probabilites as previously mentioned. Let's go back to our dog/cat example and say our input image was in fact a dog. However, since the model wasn't very confident that it was a dog, it's going to go back and do what is called a backward pass through the network and update the weights (i.e. the red numbers in the kernel) to minimize its loss function. Essentially, this process allows the model to become more 'confident' that that image (and those similar to it) are of dogs. If everything goes right, with each additional round of training the model will become more confident that our original input image is a dog. This will be reflected in a higher probability for that class. 

So we have a general understanding of what a CNN is, so you may be wondering...

### What does DenseNet have to do with any of this?

Well, DenseNet takes our convolutional neural network and adds what is called a _dense block_. These blocks are interconnected with each other, which gives higher layers the ability to reuse features from lower layers. In essence, the network is better able to communicate what is important and then leverage it at any particular layer since each dense block is connected with each other. 

While this interconnectedness between layers may at first seem to increase complexity, it was found to actually _reduce_ the number of parameters __and__ increase performance (compared to ResNet). [(Source)](https://www.jeremyjordan.me/convnet-architectures/#densenet)

So a model architecture that's less complex and offers better performance? Sign me up!

(PS - It was also the same architecture the team at Stanford found to have the best performance during their CheXpert research...)

