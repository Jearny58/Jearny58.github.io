---
layout: post
published: false
title: 'Springboard Chronicles: Must-Know Data Science Questions'
image: /img/question_mark.jpeg
date: '2019-07-06'
subtitle: Notes From KDNuggets "21 Must-Know Data Science Interview Questions" article
---
The following is my review of KDNuggets article titled __[21 Must-Know Data Science Interview Questions and Answers](https://www.kdnuggets.com/2016/02/21-data-science-interview-questions-answers.html)__, by Gregory Piatetsky. 

This is another post that serves more as a reference, in that it will be in a note-taking format, as opposed to a traditional blog post with a beginning, middle, and end. Additionally, the notes will be from the article and other miscellaneous sources, which will be cited. 

## Question 1: Explain what regularization is and why it is useful

- With machine learning, we want to prevent overfitting (i.e. model is predicting for that specific data set, is not generalizable to new, unseen data)
- Regularization adds a tuning parameter in an attempt to prevent overfitting
- Most often done by adding constant multiple to existing weight vector
- Constants often used are [L1 (Lasso) and L2 (Ridge)](https://www.quora.com/What-is-the-difference-between-L1-and-L2-regularization-How-does-it-solve-the-problem-of-overfitting-Which-regularizer-to-use-and-when)
- L1 (Lasso): absolute value of magnitude of coefficient added as penalty term to loss function
- Lasso penalizes the sum of their absolute values, meaning coefficients can be driven to 0 under lasso (which is not the case with Ridge)
- L2 (Ridge): squared magnitude of coefficient added as penalty term to loss function
- Ridge involves use of lambda parameter, which is the regularization penalty and encourages weight values toward 0 (but not exactly 0)

## Question 2: Which data scientists do you admire most? Which startups? 

_Data Scientists_
- Renée DiResta: currently a 2019 Mozilla Fellow in Media, Misinformation, and Trust, whose background alos includes work at companies like [New Knowledge](https://www.newknowledge.com/), [O'Reilly AlphaTech Ventures](http://oatv.com/), and [Jane Street](https://www.janestreet.com/)
- Andrew Ng: a leader in ML/AI research, ex-head of [Google Brain](https://ai.google/research/teams/brain/), adjunct profressor at Stanford University, co-founder of [Coursera](https://www.coursera.org/instructor/andrewng)
- Jeremy Howard: former President of [Kaggle](https://www.kaggle.com/), founder of Enlitic, co-founder of [fast.ai](https://www.fast.ai/about/#jeremy)
- Rachel Thomas: professor at University of San Francisco's Data Institute, co-founder of [fast.ai](https://www.fast.ai/about/#rachel)

_AI/ML Companies & Startups_
- [DeepMind](https://deepmind.com/): focused on artificial intelligence research and its application for positive impact
- [OpenAI](https://openai.com/): focused on artifical general intelligence and ensuring that its application benefits humanity
- [Enlitic](https://www.enlitic.com/): uses data to advance medical diagnostics and improve patient outcomes
- [AYLIEN](https://aylien.com/): uses AI and NLP to analyze and understand human-generated textual content
- [Sword Health](https://swordhealth.com/): AI-powered digital therapists work with clincal teams to allow physical therapy patients the ability to conduct sessions at home
- [ASAPP](https://www.asapp.com/?no-cache=1): Builds machine learning products for companies
- [Starship](https://www.starship.xyz/): autonomous delivery robots
- [Comodule](https://www.comodule.com/): Internet of Things solutions for mobility
- [Owkin](https://owkin.com/): AI/ML to augment medical/biology research

## Question 3: How would you validate a model you created to generate a predictive model of a quantitative outcome variable using multiple regression? 

- Option 1: Train model using cross-validation with k-folds, and a separate test set not used at all during training which the model can then be tested on to access its performance
- Option 2: If no validation done during training phase, examine the parameters and check for multi-collinearity between the independent variables

For both the options above, we can use either R-squared or mean squared error (alternatively, root mean squared error if we want average error expressed in same units as model) to assess performance. 

## Question 4: Explain what precision and recall are. How do they relate to the ROC Curve?

- There are four ways of being right and wrong:
	- True Negative: case was negative and predicted negative
	- True Positive: case was positive and predicted positive
    - False Negative: case was positive but predicted negative
    - False Positive: case was negative but predicted positive
- Hypothetical predictions with 10,000 cases, 100 of which are positive:
	- 9,760 True Negatives
    - 60 True Positives
    - 40 False Negatives
    - 140 False Positives
- What percent of the predictions were correct? The 'accuracy' was (9,760 + 60) out of 10,000 = 98.2%
- What percent of the positive cases did you catch? The 'recall' was 60 out of 100 = 60%
- What percent of positive predictions were correct? The 'precision' was 60 out of 100 = 30%
- ROC Curve represents relation between sensitivity (RECALL) and specificity (NOT PRECISION)
	- commonly used to measure performance of binary classifiers
    - when dealing with highly-skewed datasets, [Precision-Recall](http://pages.cs.wisc.edu/~jdavis/davisgoadrichcamera2.pdf) curves offer better representation of performance
    - another [source](https://www.quora.com/What-is-the-difference-between-a-ROC-curve-and-a-precision-recall-curve-When-should-I-use-each) on ROC curves and precision-recall curves
    
## Question 5: How can you prove that one improvement you've brought to an algorithm is really an improvement over not doing anything?

- Data scientists **do not** let their human emotions overrun their logical reasoning
- Exact approach depends on actual case but the following are some common guidelines:
	- Ensure no [selection bias](https://en.wikipedia.org/wiki/Selection_bias) in test data
    - Ensure sufficient variety in test data (to better replicate real-word data)
    - "Controlled experiment" principles are followed, i.e. test environment is exactly the same when running original and new algorithm
    - Ensure repeatability (results are approximately the same)
    - Examine whether results reflect local max/min or global max/min
- A/B testing: common tactic for comparison, both versions of algorithms are kept running in similar environment and input is randomly split between the two (particularly common in web analytics)

## Question 6: What is root cause analysis?

- From [Wikipedia](https://en.wikipedia.org/wiki/Root_cause_analysis):
	- "a method of problem solving used for identifying the root causes of faults or problems. A factor is considered a root cause if removal thereof from the problem-fault squence prevents the final undesirable event from recurring; whereas a causal factor is one that affects an event's outcome, but is not a root cause"
- Can find the root cause of a problem, and show relationship of causes, by repeatedly asking the question "why?" until you find the root of the problem

![five-whys.gif]({{site.baseurl}}/img/five-whys.gif)
- [Source](http://asq.org/quality-progress/2015/02/back-to-basics/the-art-of-root-cause-analysis.html) of picture above

## Question 7: Are you familiar with price optimization, price elasticity, inventory management, competitive intelligence? Give examples.

- economics refresher! (not frequently asked of data scientists but useful to know)
- [price optimization](https://en.wikipedia.org/wiki/Price_optimization): mathematical tools to determine customer response to different prices
- Commonly refers to [price elasticity of demand](https://en.wikipedia.org/wiki/Price_elasticity_of_demand): shows responsiveness (i.e. elasticity) of quantity demanded of something subject to a change in its price (holding everything else constant)
	- computed as: % change in quantity demanded / % change in price
- [inventory management](https://www.investopedia.com/terms/i/inventory-management.asp): overseeing processes related to storing and utilizing components in companies production of its sellable items and their quantity
- [competitive intelligence](https://en.wikipedia.org/wiki/Competitive_intelligence): gathering and analyzing information about market environment which help support strategic decision making for an organization

## Question 8: What is statistical power?

- the probability that a binary hypothesis test correctly rejects the null hypothesis (H0) when the alternative hypothesis (H1) is true [source](https://en.wikipedia.org/wiki/Power_(statistics))
- in other words, the liklihood that a study will detect an effect when the effect is present
- inversely related to beta, i.e. the probability of making a [Type II error](https://effectsizefaq.com/2010/05/31/i-always-get-confused-about-type-i-and-ii-errors-can-you-show-me-something-to-help-me-remember-the-difference/)

## Question 9: Explain what resampling methods are and why they are useful. Also explain their limitations.

- [Overview of Sampling Statistics and Types of Resampling Methods](http://userwww.sfsu.edu/efc/classes/biol710/boots/rs-boots.htm)
- Resampling refers to methods for doing one of these:
	- estimating sample statistics using subsets of data (jackknifing) or drawing randomly with replacement from a set of data points (bootstrapping)
    - exchanging labels on data points when performing significance tests (permutation tests, also called randomization tests)
    - validating models using random subsets (bootstrapping or cross-validation)
    
## Question 10: Is it better to have too many false positives, or too many false negatives?

- Depends on context
- Example: medical testing
	- false negatives create situation where disease is said to not be present when it actually is
    - patients receive inadequate treatment
    - false positives > false negatives
- Example: spam filtering
	- false positive: legitimate email is classified as spam
    - interferes with delivery
    - false negatives > false positives
    
## Question 11: What is selection bias, why is it important and how can you avoid it? 

- situation when error is introduced due to non-random population sample
- avoiding non-random samples is best way to deal with bias
	- when impractical, resampling, boosting and weighting can be introduced to deal with non-randomness
    
## Question 12: Give an example of how you would use experimental design to answer a question about user behavior? 

- Formulate Research Question: What are effects of page load times on satisfaction ratings?
- Identify variables: independent variable = page load time, dependent variable = user satisfaction rating
- Generate hypothesis: Lower page download time will have more effect on user satisfaction rating for a web page
- Determine Experimental Design: vary one/multiple factor at a time, design is based on type of objective (comparitive, screening, response surface) & number of factors, also idenfity within-participants , between-participants, and mixed model
- Develop experimental task & procedure: detailed steps involved in the experiment
- Determine Manipulation & Measurements: one level of factor will be controlled and other will be manipulated
- Analyze results: support the hypothesis or contradict according to the observations made

## Question 13: What is the difference between "long/tall" and "wide" format data?

- more records/rows than features/columns = "long/tall" data
- only have small number of records (for example number of patients) but many observations for each one

## Question 14: What method do you use to determine whether the statistics published in an article (or appeared in a newspaper or other media) are either wrong or presented to support the author's point of view, rather than correct, comprehensive factual information on a specific subject?

- Simple rule, statistics published in newspapers are wrong (suggested by Zack Lipton)
- Determining validity of published statistics in any article, example publishing agency and target audience
- Will notice that news story (and its stats) will be published very differently according to media organization
- another common case of erratic reporting: jounalists pick up an insight from 1-2 paragraphs of a published research paper
- reliable article must not have any unsubstantiated claims, must be backed with reference to past research
- just because article is referring to renowned research paper, doesn't mean they're using it appropriately
- don't skip the details about the research!

## Question 15: Explain Edward Tufte's concept of "chart junk."

- refers to all visual elements in charts/graphs not necessary to comprehend information in said graph
- in other words, they distract the viewer from the information
- basically, don't force readers to work harder to discover meaning of data! 

## Question 16: How would you screen for outliers and what should you do if you find one? 

- assorted methods to screen outliers:
	- z-scores
    - modified z-score
    - box plots
    - Grubb's test
    - Tietjen-Moore test
    - Kimber test
    - moving window filter algorithm
- two most robust methods
	- Inter-quartile range: outlier lies over 1.5 IQRs below the first first quartile or above third quartile in a given data set
    	- High = (Q3) + 1.5 IQR
        - Low = (Q1) - 1.5 IQR
    - Tukey Method: similar to method above but with "fences", anything outside of fences are outliers
    	- Low outliers = Q1 - 1.5(Q3 - Q1) = Q1 - 1.5(IQR)
        - High outliers = Q3 - 1.5(Q3 - Q1) = Q3 + 1.5(IQR)
- should not remove outliers without qualitative assessment because you are altering data, no longer pure
- "Why an outlier is different from other data points?"
- Outliers could be attributed to error or signify a new trend (in which case you should retain them)

## Question 17: How would you use either the extreme value theory, Monte Carlo simulations or mathematical statistics to correctly estimate the chance of a very rare event?

- [Extreme value theory](https://en.wikipedia.org/wiki/Extreme_value_theory) focuses on rare events and extremes, states their are 3 types of distributions needed to model extreme data points: Gumble, Frechet and Weibull distributuons
- Classical approaches to statistics concentrate on average behaviors

## Question 18: What is a recommendation engine? How does it work?

- typically produce recommendations in one of two ways: collaborative or content-based filtering
- [Colaborative filtering](https://en.wikipedia.org/wiki/Collaborative_filtering): build model on users past behavior and use decisions made by current and other users which is then used to predict items that the user may be interested in
- [Content-based filter](https://en.wikipedia.org/wiki/Recommender_system#Content-based_filtering): uses features of an item to recommed additional items with similar properties
- Above approaches often combined into **Hybrid Recommender Systems**

## Question 19: Explain what a false positive and false negative are. Why is it important to differentiate these from each other?

- False positive: presence of a condition, when in reality it is absent (**type I error**)
- False negative: absence of a condition, when in reality it is present (**type II error**)
- Example on importance: medical diagnostics
	- false positive = test says disease, but person is healthy - results in extra unneccesary tests
    - false negative = test says no disease, but person is sick - no treatment and person may die
    
## Question 20: Which tools do you use for visualization?

- Python: matplotlib and seaborn
- there are other data visualization tools in R, in addition to Tableau
- [List of data visualization tools, courtesy of KDNuggets](https://www.kdnuggets.com/2015/05/21-essential-data-visualization-tools.html)

## Bonus Question (#21): Explain what overfitting is and how would you control for it.

- Overfitting: finding spurious results due to chance and that cannot be reproduced
- Several methods can be used to avoid 'overfitting' the data
	- find the simplest possible hypothesis
    - Regularization (adding a penalty for complexity)
    - Randomization Testing
    - Nested cross-validation (do feature selection on one level, then run entrie method in cross-validation on outer level
    - Adjusting the [False Discovery Rate](https://en.wikipedia.org/wiki/False_discovery_rate)
    - Using the [reusable holdout method](https://www.kdnuggets.com/2015/08/feldman-avoid-overfitting-holdout-adaptive-data-analysis.html)
