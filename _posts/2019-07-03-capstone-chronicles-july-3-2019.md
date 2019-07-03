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

## Research on Metrics

Up to this point I've been using error rate and AUROC as two primary metrics for judging the performance of my model. However, I think I need to start assessing performance on a more granular level, namely by potentially utilizing precision, recall of F1. The following is my research on these respective metrics. 

Precision is the number of __True Positive__ observations divided by the sum of the __True Positive__ **and** __False Positive__ (i.e. the __total predicted positive__). A false positive in our situation would be classifying an image as positive for a specific pathology, when in fact it is negative. 
- The benefit of Precision is that it shows us how accurate the model is out of the predicted positive. This is extremely immportant when considering that the costs of a false positive in medicine can mean a diagnosis of a non-existent pathology. This can be extremely expensive (due to things like unneccasary tests) and dangerous (treating for a non-existent pathology). 

Recall is the number of __True Positive__ observations divided by the sum of the __True Positive__ **and** __False Negative__ (i.e. the __total of actual positives__). A false negative in our situation would mean classifying an image as negative (i.e. no presence of specific pathology) when in fact the image **does** indicate the presence of said pathology! This is also extremely important in healthcare as this situation prevents the individual from getting the necessary treatment. 

Now we're kind of at odds here; in our situation (i.e. classifying pathologies from medical images), both Recall and Precision are important metrics to consider. 

Lastly, there is the __F1 Score__. F1 is the Precision multiplied by the recall divided by the sum of Precision plus Recall, all multiplied by 2. It is a metric that seeks to find a balance between Precision and Recall, especially in the case of an uneven class distribution (which is very much the case when it comes to our medical images as most do not have a pathology). 

## Trial 20

So the set up for the trial 20 notebook was similar to trial 19. Below are the steps I took to set everything up for training:
1. Loaded in the data via the replicate.load_data() function, which takes as input the data path, a seed number and the percent of the data you want to gather from the original CheXpert data set (default percent is 5%, expressed as 0.05).
2.  I utilized the binary mapping approach for the uncertain observations, more specifically the __U-Zero__ approach which replaces the -1 value with 0.
3. Called the replicate.get_src() function which takes as input the sample dataframe, the data path, and the name of the pathology we are focusing on (which in this specific instance was Cardiomegaly) in string format. This gives us a variable named src, which is an ImageList object. 
4. Feed in src and the image size (we start with 64) to the replicate.get_data() function; this gives us an ImageDataBunch with a training set with 11,576 items (i.e. images) and a validation set of 234 items, and their respective labels. 
5. For the model, I decided to utilize a pretrained DenseNet121 architecture with both [dropout](http://wiki.fast.ai/index.php/Lesson_3_Notes#Dropout) and [batch normalization](http://wiki.fast.ai/index.php/Lesson_3_Notes#Batch_Normalization). 
6. Set the learning rate to 0.3, according to learning rate finder plot that test a variety of rates, from 1e-7 to 100. 

### Trial 20 Results

For the first round of training, we saw a very quick improvement in AUROC. It went from a value of ~0.45 in the first epoch to ~0.737 in the third epoch of this iteration. The one thing that stayed though was the error rate, which essentially stayed at 0.29 for every epoch. 

After this iteration, I increased the image size of the same data set to 128x128 and assigned it to the learner. I trained the model again for three epochs and the AUROC went from the low 0.50s to a value of ~0.749, which was a surprising jump. However, the error rate stayed the same at 0.29. 

Essentially the process was to train the model for three epochs at one image size, then increase image size and train further with the same learning rate. I followed this pattern until the image size was 320. Then during the fifth round of training, I utilized the [unfreeze() function](https://docs.fast.ai/basic_train.html#Learner.unfreeze) from fast.ai, which sets every layer group to trainable. The result of this action though was underwhelming, the highest value of AUROC achieved was ~0.71 and the error rate was no different (0.29).

For the sixth and final round of training, I utilized the [freeze() function](https://docs.fast.ai/basic_train.html#Learner.freeze), which sets every layer up to the last as untrainable, but the results were somewhat diappointing. The AUROC hovered between 0.67 to 0.68 and again had an average error rate of 0.29. Additionally, the validation loss was trending upwards while the training loss was trending downwards, indicating that the model might be starting to overfit. 


