---
layout: post
published: false
title: 'NLP Chronicles: Aug. 12th, 2019'
excerpt: It has come to my attention that I need a little work on my NLP (natural language processing) skills. I admit that I haven't been the best at keeping this particualr skillset up-to-date, but that is going to change and that change starts today. 
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

This _lexicon_ includes all the words used in our _corpus_. So this brings us back to our original question: how do you derive a vector from a document? So let's take the first sentence/document (i.e. d1) and, using our vocabulary (i.e. `V`) above, create a sparse vector of word counts:

`V(d1) = (4,1,0,0,0,1,1,1,0,1,1,0,1,0,1,1,0,0,0,0,2,1,0,1,0,0,1,0,0,1,1,1,0,0)`

Ok what are these random numbers? 

The represent the number of times a word in each index of the vocabulary appears in our first sentence/document. So the `4` at the beginning means that the letter `a` appears four times in our first document. If you keep going along, you find how many appearances each word in the vocabulary makes. Now that we see the connection, what does the `1` immediately to the right of `4` represent? Well if we look back up at `V` we see that the word `along` takes the second spot, and the subsequent `1` means that `along` appears once in document `d1` (i.e. the first sentence).

In other words, a our vector of word counts can be represented as the following list of tuples:

`[(0, 4), (1, 1), (5, 1), (6, 1), (7, 1), (9, 1), (10, 1), (12, 1), (14, 1), (15, 1), (20, 2), (21, 1), (23, 1), (26, 1), (29,1), (30, 1), (31, 1)]`

with the following dictionary (that is tied to the first number in each tuple and represents that word as an integer):

``
{
    0: a, 1: along, 5: beautiful, 6: branches, 7: bunch, 9: day, 10: fox, 12: from, 14: grapes, 15: hanging, 19: mouth, 20: of, 21: one, 23: ripe, 24: seemed, 25: spied, 26: the, 29:trained, 30: tree, 31: vine, 
}
``


