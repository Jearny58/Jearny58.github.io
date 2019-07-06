---
layout: post
published: false
title: 'Capstone Chronicles: July 6, 2019'
---
Getting a little bit of a late start to this today, but there has been a minor breakthrough of sorts. 

One of the entries into the CheXpert competition has made their code available via GitHub and it essentially served as a tutorial of sorts. I walked through it, step-by-step, in my own Jupyter Notebook to see if I got similar results. After a few tweaks (the data wasn't loading correctly), I was able to get it up and running. To my surprise, the results were near identical, and were better than anything I'd got up to this point!

What is different about this set-up versus mine that produces better (and faster) results? 

## Deep Dive

Let's start from the beginning with the seed_everything function. For clarification, the input for this function is `seed`, which is a number selected by the user and inserted wherever `seed` is located.

### `def seed_everything(seed)`

- `random.seed(seed)`: with user inputed seed, sets seed for Python's random module
- `os.environ['PYTHONHASHSEED'] = str(seed)`: part of the `os` module, which provides a means of working with operating system functionality; for this instance specifically, it is setting the `PYTHONHASHSEED` in that OS environment to whatever value the user inputed which allows for repeatable hashing that allows python processes to share hash values (1)
- `np.random.seed(seed)`: similar to `random` but seeds the generator for the numpy module specifically
- `torch.manual_seed(seed)`: sets seed for PyTorch framework (which fast.ai is based off of)
- `torch.cude.manual_seed(seed)`: similar to `torch.manual_seed()` above, but helps generate reproducible results when using random generation when using GPU(s)
- `torch.backends.cudnn.deterministic = True`: additional source of non-determinism that is when set to `True` allows for deterministic training






