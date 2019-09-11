---
layout: post
published: false
title: 'Daily Chronicles: Working with Text Data'
---

# _Analyzing A Collection of Text Documents_

This notebook was adapted from scikit-learn's lesson titled ['Working With Text Data'](https://scikit-learn.org/stable/tutorial/text_analytics/working_with_text_data.html) which walks through the following:
- loading in the file contents & categories
- extracting feature vectors for machine learning
- training a linear model to perform categorization
- using a grid search strategy to find a good configuration of both feature extraction components and the classifier

### _Load in "Twenty Newsgroups" dataset_

Official Description:

"_The 20 Newsgroups data set is a collection of approximately 20,000 newsgroup documents, partitioned (nearly) evenly across 20 different newsgroups. To the best of our knowledge, it was originally collected by Ken Lang, probably for his paper “Newsweeder: Learning to filter netnews,” though he does not explicitly mention this collection. The 20 newsgroups collection has become a popular data set for experiments in text applications of machine learning techniques, such as text classification and text clustering._"

Additionally we will only be working with a partial dataset with 4 out of 20 categories for initial exploration.


```python
# create list of 4 categories
categories = ['alt.atheism', 'soc.religion.christian', 'comp.graphics', 'sci.med']
```


```python
# import data
from sklearn.datasets import fetch_20newsgroups

sample_train = fetch_20newsgroups(subset='train', categories=categories, shuffle=True, random_state=1)
```

`sample_train` is returned as a 'bunch': a simple holder object with fields that can be accessed by `dict` keys or `object` attributes for convenience.


```python
# access target names
for name in sample_train.target_names:
    print(name)
```

    alt.atheism
    comp.graphics
    sci.med
    soc.religion.christian



```python
print(len(sample_train.data))
print(len(sample_train.filenames))
```

    2257
    2257



```python
# print the first few lines of the first loaded file
print("\n".join(sample_train.data[0].split("\n")[:3]))
```

    From: jaeger@buphy.bu.edu (Gregg Jaeger)
    Subject: Re: The Inimitable Rushdie (Re: An Anecdote about Islam
    Organization: Boston University Physics Department



```python
# print the target of the above
print(sample_train.target_names[sample_train.target[0]])
```

    alt.atheism


### _Notes_

- supervised learning algorithms require a category label for each document in training set
    - in this case, category is the name of the newsgroup
- `scikit-learn` loads the target attribute as an array of integers that corresponds to the index of the category name in the `target_names` list (see below)


```python
# first ten targets
sample_train.target[:10]
```




    array([0, 2, 3, 1, 2, 1, 1, 1, 2, 3])




```python
# we can also see the category names as well
for t in sample_train.target[:10]:
    print(sample_train.target_names[t], sample_train.target[t])
```

    alt.atheism 0
    sci.med 3
    soc.religion.christian 1
    comp.graphics 2
    sci.med 3
    comp.graphics 2
    comp.graphics 2
    comp.graphics 2
    sci.med 3
    soc.religion.christian 1


# _Extracting features from text files_

In order to perform ML on text documents, we first need to turn text content into numerical feature vectors. 

### _Bag of words_

Most intuitive way is to use 'bag of words' representation
1. Assigns a fixed integer id to each word occurring in any document of the training set
2. For each document, it counts the number of occurrences of each word and stores that value

The thing is, most values will be 0 as for any given document, less than a few thousand distinct words will be used. In other words, bags of words are typically **high-dimensional sparse datasets**. We can save a lot of memory by only storing the non-zero parts of the feature vectors in memory.

`scipy.sparse` matrices are data structures that do exactly this, and `scikit-learn` has built-in support for these structures.

### Tokenizing text with `scikit-learn`
[`CountVectorizer`](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.CountVectorizer.html#sklearn.feature_extraction.text.CountVectorizer) includes text preprocessing, tokenizing, and filtering of stopwords. Additionally, it builds a dictionary of features and transforms documents to feature vectors (see below).


```python
from sklearn.feature_extraction.text import CountVectorizer

# create count vectorizer object
count_vect = CountVectorizer()

# fit and transform the text data using CountVectorizer object
X_train_counts = count_vect.fit_transform(sample_train.data)

# check out shape
X_train_counts.shape
```




    (2257, 35788)



### _Visualizing Token Frequencies_

Before looking into [`TfidfTransformer`](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfTransformer.html#sklearn.feature_extraction.text.TfidfTransformer), we're going to explore a method for visualizing the frequency of tokens within and across corpora using `Yellowbrick`'s [frequency distribution](scikit-yb.org/en/latest/api/text/freqdist.html).


```python
# load library
from yellowbrick.text import FreqDistVisualizer
import matplotlib.pyplot as plt
%config InlineBackend.figure_format = 'retina'
plt.style.use('ggplot')

# get features from previous count vectorizer
features = count_vect.get_feature_names()

# visualize count vectorizer features
plt.figure(figsize=(12,8))
visualizer = FreqDistVisualizer(features=features, n = 25, orient='h', color='steelblue')
visualizer.fit(X_train_counts)
plt.xticks(rotation=45, size='small')
visualizer.poof();
```


![png](/img/working-with-text-data_15_0.png)


So we visualized the occurrences and we can see that most of the words - like `the`, `of`, and `to` - give little to no information about context. Additionally, longer documents will have higher average count values than shorter documents, even though they may talk about the same topics. 

We can achieve both of these by using [`TfidfTransformer`](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfTransformer.html#sklearn.feature_extraction.text.TfidfTransformer). It begins by dividing the number of occurrences of each word in a document by the total number of words in the document. These new features are called `tf`, or term frequencies. 

After this, it downscales weights for words that occur in many documents in the corpus and are therefore less informative than those that occur in a smaller portion of the corpus. 

Hence the name "Term Frequency times Inverse Document Frequency". 


```python
from sklearn.feature_extraction.text import TfidfTransformer

# create tfidf transformer object and fit 
tfidf_transformer = TfidfTransformer()

# transform count-matrix to a tfidf representation
X_train_tfidf = tfidf_transformer.fit_transform(X_train_counts)
print(X_train_tfidf.shape)
```

    (2257, 35788)


Now we're going to do a slightly different visualization than the one above, more specifically we'll be creating what is called a t-distributed stochastic neighbor embedding, or t-SNE, Corpus Visualization.

It is implemented via `scikit-learn` using the [`sklearn.manifold.TSNE`](https://scikit-learn.org/stable/modules/generated/sklearn.manifold.TSNE.html) transformer. What it does is decompose high-dimensional document vectors into 2 dimensions using probability distributions, which allows documents to be visualized with a scatter plot. 

One key thing to remember is that this method is **very computationally expensive**; usually a simpler decomposition method such as SVD or PCA is applied first. Luckily, `Yellowbrick` allows us to not only create a pipeline that applies such a decomposition first, but it then performs the t-SNE embedding and plots the scatter plot, coloring by cluster/class.


```python
print(plt.style.available)
```

    ['seaborn-dark', 'seaborn-darkgrid', 'seaborn-ticks', 'fivethirtyeight', 'seaborn-whitegrid', 'classic', '_classic_test', 'fast', 'seaborn-talk', 'seaborn-dark-palette', 'seaborn-bright', 'seaborn-pastel', 'grayscale', 'seaborn-notebook', 'ggplot', 'seaborn-colorblind', 'seaborn-muted', 'seaborn', 'Solarize_Light2', 'seaborn-paper', 'bmh', 'tableau-colorblind10', 'seaborn-white', 'dark_background', 'seaborn-poster', 'seaborn-deep']



```python
label_names = list(sample_train.target_names); label_names
```




    ['alt.atheism', 'comp.graphics', 'sci.med', 'soc.religion.christian']




```python
%time
# import libraries
from sklearn.feature_extraction.text import TfidfVectorizer
from yellowbrick.text import TSNEVisualizer
plt.style.use('seaborn-whitegrid')

# create tfidf object
tfidf = TfidfVectorizer()

X = tfidf.fit_transform(sample_train.data)
y = sample_train.target

# create the visualizer and draw the vectors
tsne = TSNEVisualizer(decompose_by=25, random_state=1)
plt.figure(figsize=(12,8))
tsne.fit(X, y)
tsne.poof();
```

    CPU times: user 2 µs, sys: 0 ns, total: 2 µs
    Wall time: 5.01 µs



![png](/img/working-with-text-data_21_1.png)



```python
# as a reminder of what the numbers represent for the target variable
for t in sample_train.target[:10]:
    print(sample_train.target_names[t], sample_train.target[t])
```

    alt.atheism 0
    sci.med 3
    soc.religion.christian 1
    comp.graphics 2
    sci.med 3
    comp.graphics 2
    comp.graphics 2
    comp.graphics 2
    sci.med 3
    soc.religion.christian 1


### _The Whole Process: From Feature Extraction to Training a Classifier_

We are now going to bring everything together and go from start to beginning. The following are the steps we're going to take, from beginning to end:
1. Load in training and test data
2. Generate a pipeline that applies `TfidfVectorizer()` to the text, then feeds data into Naive Bayes model `MultinomialNB()`
3. Fit the model on training data
4. Predict on test data
5. Generate visualizations for results to assess performance


```python
# download the data
data = fetch_20newsgroups()
data.target_names
```




    ['alt.atheism',
     'comp.graphics',
     'comp.os.ms-windows.misc',
     'comp.sys.ibm.pc.hardware',
     'comp.sys.mac.hardware',
     'comp.windows.x',
     'misc.forsale',
     'rec.autos',
     'rec.motorcycles',
     'rec.sport.baseball',
     'rec.sport.hockey',
     'sci.crypt',
     'sci.electronics',
     'sci.med',
     'sci.space',
     'soc.religion.christian',
     'talk.politics.guns',
     'talk.politics.mideast',
     'talk.politics.misc',
     'talk.religion.misc']




```python
# let's select some new categories
categories = ['alt.atheism', 'comp.os.ms-windows.misc', 'rec.sport.hockey', 'sci.med', 
             'soc.religion.christian', 'talk.politics.guns']

# fetch training and test data
train = fetch_20newsgroups(subset='train', categories=categories, shuffle=True, random_state=1)
test = fetch_20newsgroups(subset='test', categories=categories, shuffle=True, random_state=1)
```

Let's check out an entry from the data.


```python
print(train.data[11])
```

    From: young@serum.kodak.com (Rich Young)
    Subject: Re: what are the problems with nutrasweet (aspartame)
    Originator: young@sasquatch
    Nntp-Posting-Host: sasquatch
    Reply-To: young@serum.kodak.com
    Organization: Clinical Diagnostics Division, Eastman Kodak Company
    Lines: 76
    
    In article <1993Apr17.181013.3743@uvm.edu> hbloom@moose.uvm.edu (*Heather*) writes:
    >Nutrasweet is a synthetic sweetener a couple thousand times sweeter than
    >sugar.  Some people are concerned about the chemicals that the  body produces 
    >when it degrades nutrasweet.  It is thought to form formaldehyde and known to
    >for methanol in the degredation pathway that the body uses to eliminate 
    >substances.  The real issue is whether the levels of methanol and formaldehyde
    >produced are high enough to cause significant damage, as both are toxic to
    >living cells.  All I can say is that I will not consume it.  
    
    [...]
    
       In the September 1992 issue of THE TUFTS UNIVERSITY DIET AND NUTRITION
       LETTER, there is a three page article about artificial sweeteners.  What
       follows are those excerpts which deal specifically with Nutrasweet.
    
       [Reproduced without permission]
    
    	   The controversy [over aspartame] began six years ago in England,
    	where a group of researchers found that aspartame, marketed under
    	the tradename Nutrasweet, appears to stimulate appetite and,
    	presumably, the eating of more calories in the long run than if
    	a person simply consumed sugar.  When researchers asked a group
    	of 95 people to drink plain water, aspartame-sweetened water, and
    	sugared water, they said that overall they felt hungriest after
    	drinking the artificially sweetened beverage.
    	   The study received widespread media attention and stirred a
    	good deal of concern among the artificial-sweetener-using public.
    	However, its results were questionable at best, since the researchers
    	did not go on to measure whether the increase in appetite did
    	actually translate into an increase in eating.  The two do not
    	necessarily go hand in hand.
    	   In the years that followed, more than a dozen studies examining
    	the effect of aspartame on appetite -- and eating -- were conducted.
    	And after reviewing every one of them, the director of the
    	Laboratory of the Study of Human Ingestive Behavior at Johns Hopkins
    	University, Barbara Rolls, Ph.D., concluded that consuming aspartame-
    	sweetened foods and drinks is not associated with any increase in
    	the amount of food eaten afterward.
    
    	   One artificial sweetener that is not typically accused of causing
    	cancer is aspartame.  But it most certainly has been blamed for a
    	host of other ills.  Since its introduction in 1981, the government
    	has received thousands of complaints accusing it of causing
    	everything from headaches to nausea to mood swings to anxiety.
    	Still, years of careful scientific study conducted both before and
    	after the sweetener's entering the market have failed to confirm
    	that it can bring about adverse health effects.  That's why the
    	Centers for Disease Control (the government agency charged with
    	monitoring public health), the American Medical Association's
    	Council on Scientific Affairs, and the Food and Drug Administration
    	have given aspartame, one of the most studied food additives, a
    	clean bill of health.
    	   Granted, the FDA has set forth an "acceptable daily intake" of
    	50 milligrams of aspartame per kilogram of body weight.  To exceed
    	the limit, however, a 120-pound (55 kg.) woman would have to take
    	in 2,750 milligrams of aspartame -- the amount in 15 cans of
    	aspartame-sweetened soda pop, 14 cups of gelatin, 22 cups of yogurt,
    	or 55 six-ounce servings of aspartame-containing hot cocoa,...
    	A 175-pound (80 kg.) man would have to consume some 4,000 milligrams
    	of the sweetener -- the amount in 22 cans of soda pop or 32 cups
    	of yogurt -- to go over the limit.  [chart with aspartame content
    	of selected foods omitted]
    	   Only one small group of people must be certain to stay away
    	from aspartame: those born with a rare metabolic disorder called
    	phenylketonuria, or PKU.  The estimated one person in every 12,000
    	to 15,000 who has it is unable to properly metabolize an essential
    	amino acid in aspartame called phenylalanine.  Once a child
    	consumes it, it builds up in the body and can ultimately cause
    	such severe problems as mental retardation.  To help people with
    	PKU avoid the substance, labels on cans of soda pop and other
    	aspartame-sweetened foods must carry the warning "Phenylketonurics:
    	Contains Phenylalanine."
    
    
    -Rich Young (These are not Kodak's opinions.)
    
    


Just for curiosities sake, what is the target for this observation?


```python
print(train.target[11])
```

    3


Ok so we have to remember that the targets are technically labeled as integers, but we can see the corresponding name for each of those using `train.target_names`.


```python
print(train.target_names)
```

    ['alt.atheism', 'comp.os.ms-windows.misc', 'rec.sport.hockey', 'sci.med', 'soc.religion.christian', 'talk.politics.guns']


From the target names, we can see that `'sci.med'` is the corresponding name for 3. When we scan the data, we can see that it is a discussion about the affect of artificial sweetners on cancer, which makes sense!

Ok so now what we will be doing is creating a pipeline that takes in a `TfidfVectorizer()`, which converts each document into a vector of numbers. This data will then be passed to our Naive Bayes classifier, which will then be trained.


```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.naive_bayes import MultinomialNB
from sklearn.pipeline import Pipeline

# generate pipeline for text classifer
text_clf = Pipeline([
    ('vect', TfidfVectorizer()),
    ('clf', MultinomialNB())
])
```

Just a reminder, `vect` and `clf` are arbitrary; they are required by `Pipeline` and just serve as placeholders for the action that the pipeline is conducting.


```python
# fit model to training data
text_clf.fit(train.data, train.target)

# predict labels for test data
labels = text_clf.predict(test.data)
```

With labels in hand, we can evaluate the performance of the classifier. Let's first take a look at the confusion matrix between the true and predicted labels for the test data.


```python
from sklearn.metrics import confusion_matrix
import seaborn as sns

matrix = confusion_matrix(test.target, labels)

# we'll use seaborn's heatmap to generate a visualization of the confusion matrix
plt.figure(figsize=(8,8))
sns.heatmap(matrix.T, square=True, annot=True, cbar=False, cmap='Blues', fmt='d',
            xticklabels=train.target_names, 
            yticklabels=train.target_names)
plt.xlabel('True Label')
plt.ylabel('Predicted Label');
```


![png](/img/working-with-text-data_37_0.png)



```python
# let's also take a quick peek at the accuracy
import numpy as np

print('Accuracy = ', round(np.mean(labels == test.target), 3))
```

    Accuracy =  0.865


For a first model, this isn't too shabby! However, this is a perfect example as to why accuracy is **not** always the best metric to use. By taking a look up at the confusion matrix, we can see that for the most part, the model does a decent job of classifying the correct label...except when it comes to `alt.atheism` and `soc.religion.christian`.

We can see that 132 observations whose true label was `alt.atheism` were incorrectly labeled as `soc.religion.christian`. Despite the fact that Christians and Atheists have very different beliefs when it comes to faith, this high level of misclassification may indicate that articles in each subject may not be as dissimilar as most would think. This is definitely something to look further into. 

However, let's wrap this notebook up by taking a look at the classification report to get a better idea of what is going on.


```python
from sklearn import metrics

# create variable that stores classification report
report = metrics.classification_report(test.target, labels, target_names=test.target_names)

# print out report
print(report)
```

                             precision    recall  f1-score   support
    
                alt.atheism       0.98      0.53      0.69       319
    comp.os.ms-windows.misc       0.97      0.88      0.92       394
           rec.sport.hockey       0.97      0.98      0.98       399
                    sci.med       0.96      0.79      0.86       396
     soc.religion.christian       0.62      0.98      0.76       398
         talk.politics.guns       0.93      0.96      0.95       364
    
                   accuracy                           0.86      2270
                  macro avg       0.90      0.86      0.86      2270
               weighted avg       0.90      0.86      0.86      2270
    


Alternatively, we can use `Yellowbricks` [Classification Report](https://www.scikit-yb.org/en/latest/api/classifier/classification_report.html).


```python
from yellowbrick.classifier import ClassificationReport

# create visualizer object
plt.figure(figsize=(12,8))
visualizer = ClassificationReport(text_clf, classes=train.target_names, support=True, cmap="Blues")
visualizer.fit(train.data, train.target)
visualizer.score(test.data, test.target)
visualizer.poof();
```


![png](/img/working-with-text-data_42_0.png)
