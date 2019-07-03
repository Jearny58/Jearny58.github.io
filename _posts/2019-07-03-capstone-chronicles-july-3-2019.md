---
layout: post
published: false
title: 'Capstone Chronicles: July 3, 2019'
date: '2019-07-03'
---
## Recap 

Let's begin with a little recap of where I currently am in regards to my second capstone project. 

1. I've experimented with quite a few set-ups in regards to data input for my model. Most of them have involved taking a random sample (~5-10%) of the CheXpert data set.
2. Additionally, most of the models have been focused on detecting just one pathology, not multiple. The two pathologies that I've been focusing on primarily are [atelectasis](https://my.clevelandclinic.org/health/diseases/17699-atelectasis) and [cardiomegaly](https://en.wikipedia.org/wiki/Cardiomegaly).
3. Most models up to this week have seemed to top out with an [AUROC](https://developers.google.com/machine-learning/crash-course/classification/roc-and-auc) of ~0.65.
4. This week I've been able to develop models that have increased the AUROC performance to around 0.75 (with general fluctuations being between 0.73 and 0.79), which is encouraging. 
5. However, yesterday I realized there was a potential flaw in my data set-up when it came to uncertainty. As mentioned in the [CheXpert paper](https://arxiv.org/abs/1901.07031), there are a significant portion of labels in the data that are classified as uncertain, which means that the medical documentation (as picked up by the labeler) indicated the presence of a potential diagnosis but wasn't entirely confident in that finding. 
6. The ML team at Stanford indicated multiple approaches on how to address these uncertain labels: ignoring, binary mapping, self-training, and multiclass classification.

	- Ignoring: simplest approach, ignore the uncertain (__u__) labels during training; best serves as a baseline to other approaches, due to it causing a significant portion of observations for certain pathologie to be dropped from the data set
	- Binary Mapping: map all uncertain instances to either 0 (negative) or 1 (positive); similar to zero imputation strategies in statistics, mimics approaches in multi-label classification methods [where missing examples are used as negative labels](https://www.hindawi.com/journals/cmmm/2014/781807/abs/); does have downside of potentially distorting the classifier especially if, despite their uncertainty, the uncertain observations convey useful information to the classifier
	- Self-Training: involves first training a model to convergence utilizing the ignore approach mentioned above; then this model is used ro re-label the uncertain labels (does **not** replace any of the instances alread labeled 1 or 0) 
	- Multiclass Classification: treats the __u__ label as its own class; the paper hypothesizes that this approach can "better incorporate information from the image by supervising uncertainty, allowing the network to find its own representation of uncertainty on different pathologies" ([Source](https://arxiv.org/abs/1901.07031))

7. Now to keep things simple I decided to utilize the __"Ignoring"__ and __"Binary Mapping"__ approaches, with the Binary option being preferred (as I didn't want to lose any potential useful information it could relay to the classifier).  
8. Unfortunately, this is where I realized I had made a mistake. After relabeling the uncertain labels as either 0 or 1, which depended on the pathology, I would then create my training and validation sets. To create the validation set, I took a random sample of 20% of the observations from the original sample data set. This is the part I overlooked: the validation set most likely contained observations that had been re-labelled. The issue here is that despite binary mapping being a valid approach to uncertainty, your introducing this uncertainty into the validation set. Due to the liklihood that we re-labelled an observation as 1 when in fact it should have been a 0 (and vice versa), and that this observation(s) could be in our validation set does not bode well for our model. Essentially, we are sending it mixed signals. 
9. Luckily, included with the CheXpert data was a validation set of images which contains 200 studies from 200 randomly-sampled patients that were reviewed by three board-certified radiologists. The results were binarized, meaning all observations had a label of either 1 (i.e. positive) or 0 (i.e. negative). Due to a signficantly lower level of uncertainty, I decided to utilize this as my validation set. 
10. I wrote a script (with the help of [Simon Grest's work](https://github.com/simongrest/chexpert-entries/blob/master/replicating_chexpert.ipynb)) that took a different approach to building the data set. It allowed my to more easily attach this validation set via fast.ai's [ImageList](https://docs.fast.ai/vision.data.html#ImageList) and utilize it in the validation phase of model training. 

## Approach for Today

The first thing I want to explore is adding a new sample of data after each round of training. Additionally, the Stanford paper utilized three epochs (i.e. how many times the data set is passed through the neural network) for each iteration of model training. Up to this point, I've been using 5, which may tend to start overfitting to that particular sample data set. By introducing a new sample every three epochs, I hope to mitigate any potential for overfitting while at the same time adding more data for the model to train on (which is beneficial, especially when it comes to deep learning) 

Another thing to potentially explore is the model architecture. The past few days I have been utilizing the [DenseNet121](https://www.kaggle.com/pytorch/densenet121) neural network architecture, which has produced the best performance so far (and was Stanford's architecture of choice). However, ResNet152 has shown decent performance (low 0.70 AUROC scores) so it might be worth revisiting. Additionally, [DenseNet161](https://www.kaggle.com/pytorch/densenet161) may be worth a shot as well but I'll have to be careful in regards to memory usage (DenseNet is very computationally expensive!). 

Additionally, I have to continue with the EDA of the data set. It could potentially provide some insights into how to best approach uncertainty within the data set.
