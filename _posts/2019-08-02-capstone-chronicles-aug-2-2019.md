---
layout: post
published: true
title: 'Capstone Chronicles: Aug. 2, 2019'
subtitle: Continued Analysis of Second Capstone Project
date: '2019-08-02'
image: /img/math_dl.gif
---
### Another day, so more writing!
This is the third and potentially final post of the comprehensive analysis for my second capstone project. Yesterday, I had just started to talk about convolutional neural networks, and what they were so that's where we'll pick back up! 

I hope you enjoy it!

## CNN's and How DenseNet is (Slightly) Different

As you can see, a black pixel is assigned a value of 0, and a white pixel is assigned a value of 255, with a variety of numbers in-between indicating different shades. Now, what does this have to do with a CNN?

Glad you asked! The first part of the CNN is called the _Convolutional Layer_. This layer uses a kernel to iterate over a pixel space like the one with the number 8 above. In an attempt to be as user-friendly as possible, a visualization my help to better understand this process.

![cnn_kernel.gif](/img/cnn_kernel.gif)
[(Source)](https://hackernoon.com/visualizing-parts-of-convolutional-neural-networks-using-keras-and-cats-5cc01b214e59)

The green box is the image with each of its pixels, the yellow box is our kernel, and the pink box is our convolved feature (which I'll get to in a second). Now, the kernel can be viewed kind of like a flashlight, where it steadily works its way across the entire width and height of the image until it has shined its light on every part of it. Guess you could call it a deep learning version of 'the shining.' 

![the_shining.jpg](/img/the_shining.jpg)
[(Source)](https://www.amazon.com/Shining-POSTER-Movie-11-Inches/dp/B00KK6JLZY)

What this kernel (or 'shining') is doing each step of the way is performing matrix multiplication between its weights/parameters (i.e., the red numbers in the bottom right corner) and the pixel values in the image.

If you are unfamiliar with matrix multiplication, I highly suggest checking out this [website](http://matrixmultiplication.xyz/). It provides a great visualization of how it works. 

At this point, we can turn our attention to the pink box. These matrix multiplications are then all summed up and output to the convolved feature. For example, the kernel starts in the upper left corner, performs matrix multiplication after which it sums these numbers together and outputs that number (i.e., 4) to the convolved feature (i.e., pink box).

The convolutional layer's main goal is to filter, as it checks for patterns in each section of the image. This brings us to the next layer, which is called the pooling layer. It's similar to the convolution in that it too utilizes a kernel. However, we're using this kernel to reduce the spatial size of our convolved feature further. There are two primary approaches for pooling - max and average - which returns either the max value or the average of all the values from a particular section covered by the pooling kernel. The last main layer is the activation layer, which takes these linear outputs and squashes them into a range using a nonlinear function like ReLu (rectified linear unit). A ReLu sounds a lot scarier than it is. Mathematically, it can be defined as _y = max(0,x)_, meaning that if the number is negative, it returns 0; otherwise, it takes whatever value x is. 

Below is a visual representation of what is going on with convolutions, pooling, and activation layers. 

![cnn_high_level.png](/img/cnn_high_level.png)
[(Source)](https://media.springernature.com/original/springer-static/image/art%3A10.1007%2Fs13244-018-0639-9/MediaObjects/13244_2018_639_Fig1_HTML.png)

Now take note: there are multiple convolutions, ReLUs, and pooling layers above. Most convolutional neural networks have multiple layers, with 100+ layers not being uncommon for more complicated input images (like medical images). Additionally, you may be wondering what is going on at the end of the picture with the 'FC' columns and backpropagation. The FC's are the fully connected layers, which take input from the previous layer and outputs a vector with N dimensions, with N representing the number of classes the model has to choose from. For example, if you were trying to determine whether an image was of a cat or a dog, N would be equal to 2 (i.e., cat represents one potential class, and dog represents another potential class). What is output to this N-dimensional vector is not a 'yes' or a 'no'; instead, it outputs the probability of being in that particular class. To continue with our cat/dog example, it may output 0.50 for the dog class and 0.50 for the cat class meaning that the image has a 50% probability of being a dog or a cat (this is known as the softmax function, which you can find more information about [here](https://en.wikipedia.org/wiki/Softmax_function)). 

This brings us to _back propogation_, which is essential to how neural networks 'learn.' Our process began when we input the image, and it worked its way through the multiple layers. This beginning stage is called the forward pass and results in us getting the N-dimensional vector of probabilities as previously mentioned. Let's go back to our dog/cat example and say our input image was, in fact, a dog. However, since the model wasn't very confident that it was a dog, it's going to go back and do what is called a backward pass through the network and update the weights (i.e., the red numbers in the kernel) to minimize its loss function. Essentially, this process allows the model to become more 'confident' that that image (and those similar to it) are of dogs. If everything goes right, with each additional round of training, the model becomes more confident that our original input image is a dog by minimizing its loss function. This higher confidence is reflected in a higher probability for that class. 

So we have a general understanding of what a CNN is, so now you may be wondering...

### What does DenseNet have to do with any of this?

Well, DenseNet takes our convolutional neural network and adds what is called a _dense block_. These blocks are interconnected with each other, which gives higher layers the ability to reuse features from lower layers. In essence, the network is better able to communicate what is essential and then leverage it at any particular layer since each dense block is connected. 

![densenet.png](/img/densenet.png)
[(Source)](https://arxiv.org/pdf/1608.06993.pdf)

_Image - Each color is a block of layers; notice the red and green lines that show how each block is interconnected._

While this interconnectedness between layers may at first seem to increase complexity, it was found to _reduce_ the number of parameters __and__ increase performance (compared to ResNet). [(Source)](https://www.jeremyjordan.me/convnet-architectures/#densenet)

So a model architecture that's less complex and offers better performance? Sign me up!

(PS - It was also the same architecture the team at Stanford found to have the best performance during their CheXpert research...)

### Last Remarks on CNN/DenseNet

There are two features - dropout and batch normalization - that I want to touch upon really quickly before we look into the performance metrics, model training process, and the results. Both are relatively new techniques that help prevent a deep learning model from overfitting the data. Dropout accomplishes this by randomly removing a percentage of the activations to prevent the model from relying to heavily on any one feature from an image. 

Batch normalization 'normalizes' the output data from the previous activation layer. What exactly does this mean? Essentially it "allows each layer of the network to learn by itself a little bit more independently of other layers." [(Source)](https://towardsdatascience.com/batch-normalization-in-neural-networks-1ac91516821c) Since no any one feature can have an overwhelming impact, resulting in its weights cascading down through the network, the model is more stable during training. Additionally, it gives us the added benefit of being able to use higher learning rates, which allows us to train our model faster as well. [(Source)](http://wiki.fast.ai/index.php/Lesson_3_Notes#Batch_Normalization). 

Below are the commands withing `cnn_learner()` for dropout and batch normalization:

- `ps=0.5`: dropout, 0.5 is the probability that an activation is dropped
- `bn_final=True`: batch normalization

### That's it for today, looks like there'll be more writing this weekend!
