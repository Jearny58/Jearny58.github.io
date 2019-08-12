---
layout: post
published: false
title: 'NLP Chronicles: Aug. 12th, 2019'
---
### NLP Review

It has come to my attention that I need a little work on my NLP (natural language processing) skills. I admit that I haven't been the best at keeping this particualr skillset up-to-date, but that is going to change and that change starts today. 

I'm going to start by reviewing an assignment from Unit 10 of Springboard's Data Science course, [Basic Text Classification with Naive Bayes](https://nbviewer.jupyter.org/github/Jearny58/Springboard-DS-Portfolio/blob/master/unit10-machine-learning/naive-bayes-predicting-movie-ratings/naive_bayes/Mini_Project_Naive_Bayes.ipynb). This assignments uses a subset of movie reviews from [Rotten Tomatoes](https://www.rottentomatoes.com/) to classify whether or not a movie is 'fresh' or not.

Before I go any further, I first want to acknowledge and thank [Harvard's CS109 class](https://github.com/cs109/2015lab10), whose originally published this material and which this [Notebook](https://github.com/Jearny58/Springboard-DS-Portfolio/blob/master/unit10-machine-learning/naive-bayes-predicting-movie-ratings/naive_bayes/Mini_Project_Naive_Bayes.ipynb) is based off of.

## The Vector Space Model and a Search Engine

To begin, lets ask the following question: how do you derive a vector from a document? 

We'll first look at our corpus:

`A Fox one day spied a beautiful bunch of ripe grapes hanging from a vine trained along the branches of a tree. The grapes seemed ready to burst with juice, and the Fox's mouth watered as he gazed longingly at them.`

Typically, a corpus is a collections of documents, which can take various froms, from blog posts and Wikipedia articles to books. However, to be succint our corpus is just the two sentences above, with each sentence being an example of a document. They are bodies of text after all!

From this corpus, we get the following _lexicon_, which is essentially NLP fancy talk for vocabulary:

`V = {a, along, and, as, at, beautiful, branches, bunch, burst, day, fox, fox's, from, gazed, grapes, hanging, he, juice, longingly, mouth, of, one, ready, ripe, seemed, spied, the, them, to, trained, tree, vine, watered, with}`
