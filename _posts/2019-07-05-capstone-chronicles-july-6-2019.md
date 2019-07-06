---
layout: post
published: true
title: 'Capstone Chronicles: July 5, 2019'
date: '2019-07-05'
image: /img/big_lebowski_confused.gif
---
Getting a little bit of a late start to this today, but there has been a minor breakthrough of sorts.

One of the entries into the CheXpert competition has made [their code available via GitHub](https://github.com/simongrest/chexpert-entries/blob/master/replicating_chexpert.ipynb), and it primarily served as a tutorial. I walked through it, step-by-step, in my own Jupyter Notebook to see if I got similar results. After a few tweaks (the data wasn't loading correctly), I was able to get everything up and running. To my surprise, the results were near-identical, and were better than anything I'd got up to this point!

What is different about this set-up versus mine that produces better (and faster) results? 

This is something that I'll have to take a further look into because when I tried to replicate the code - with some minor changes - it returned a bevy of error messages. I think it's around the `avg_auc_metric` for two reasons; one, it's a custom metric and may need some further tinkering with; two, there is something with how I am setting up my data set for processing, more specifically how I am labeling the target column which indicates the pathologies present. 

## Review of Today's Work

Today was frustrating, to say the least. A lot of error messages and confusion. Even notebooks that I had had no issues with previously were throwing up fateful errors. 

These days are going to happen, and the realization of this fact and that progress is not linear are two lessons that were reinforced during today's work. 

Looking to tomorrow, I think it might be useful to take a step back and revisit one of my older notebooks that focused on identifying only one pathology. Additionally, another thought that came to me was taking a look at batch size; in the walkthrough, the batch size was calculated a little differently, and that may have had an effect because during its training it did go slow and the number of total batches seemed awfully low. 

In summary, this was me most of the day...

![]({{site.baseurl}}/img/big_lebowski_confused.gif)

## Review of [CheXpert Walkthrough](https://github.com/simongrest/chexpert-entries/blob/master/replicating_chexpert.ipynb)

The notebook can be found here. 

Let's start from the beginning with the seed_everything function. For clarification, the input for is `seed`, a number selected by the user and inserted wherever `seed` is located.

## Function Examination: `def seed_everything(seed)`

- `random.seed(seed)`: with user-inputted seed, sets seed for Python's random module
- `os.environ[PYTHONHASHSEED] = str(seed)`: part of the `os` module, which provides a means of working with operating system functionality; for this instance specifically, it is setting the `PYTHONHASHSEED` in that OS environment to whatever value the user inputted which allows for repeatable hashing that allows python processes to share hash values (1)
- `np.random.seed(seed)`: similar to `random` but seeds the generator for the numpy module specifically
- `torch.manual_seed(seed)`: sets seed for PyTorch framework (which fast.ai is based on)
- `torch.cude.manual_seed(seed)`: similar to `torch.manual_seed()` above, but helps generate reproducible results when using random generation when using GPU(s)
- `torch.backends.cudnn.deterministic = True`: an additional source of non-determinism that when set to `True` allows for deterministic training