---
layout: post
published: false
title: 'Dataquest: Sampling in Statistics'
date: '2019-09-14'
subtitle: Work from Dataquest's Lesson on Sampling in Statistics
image: /img/dataquest_sampling_44_0.pn
---

# _Sampling_

This notebook was adapted from Dataquest's first lesson on statistics, _Sampling_. In this lesson/notebook, we'll dive deeper into the various sampling methods in statistics. 

## _Introduction_

**Notes**
- going to go deeper into statistical theory
- learn new and more powerful statistical techniques & metrics, like:
    - standard deviation
    - z-scores
    - confidence intervals
    - probability estimation
    - hypothesis testing (including A/B testing)
- in this first part, focus we'll be around getting data for analysis
- we'll begin to understand the sort of problems that statistics can solve

## _Solving Problems with Statistics_

**Notes**
- Scenario: managing a small tech company with 7 original employees
    - years have gone by, now there are 231 employees
    - as company scales, analyzing employee data becomes next to impossible
- this is where statistics comes into play
    - we can organize, summarize, and visualize large amounts of data
    - find hidden patterns
- imagine that company is now 5000+ strong
    - ask data analyst to conduct a survey on work satisfaction
        - aren't going to survey all employees, only portion
    - analyst reports a _significant_ decrease in work satisfaction
        - means it's very unlikely to have by chance
- through statistics we can learn to use a smaller set of data to answer questions about larger sets of data


```python
import os

# get current working directory
os.getcwd()
```




    '/Users/jai/Documents/joes-dataquest/courses/probability-statistics'




```python
from pathlib import Path

#create path variable to primary directory
path = Path(os.getcwd())
path
```




    PosixPath('/Users/jai/Documents/joes-dataquest/courses/probability-statistics')



## _Populations and Sample_

**Notes**
- previous question was about _all_ people in company, which is the population
    - only selected a small group to answer though, which is called a sample
    - selecting a smaller group from a population = sampling
- populations do not necessarily consist of people
    - can be populations of monkeys, countries, soils, etc.
- now it's our turn to play data analyst
    - we collected data about the salary of all the individuals in the company working in IT roles
    - based on this data, we want to answer a series of questions
    - depending on question, our data is either a sample or a population

**Questions**
1. What's the average salary of individuals in our company working in IT roles? 
    - Data = population

2. What's the proportion of individuals in the IT department having salaries under $60000?
    - Data = population

3. What's the minimum salary in the entire company?
    - Data = sample

4. What's the minimum salary in the IT department of our company?
    - Data = population

5. What's the proportion of salaries under $20000 in the entire company?
    - Data = sample

## _Sampling Error_

**Notes**
- we should try to use the population
    - often in practice, not possible
    - getting data is not easy either
- sample, by definition, is incomplete set of data for question we're trying to answer
    - can be seen as error, which is called _sampling error_
- _parameter_: metric specific to a population
    - ex: average salary of all employees
- _statistic_: metric specific to a sample
- difference between the two
    - _sampling error_ = parameter - statistic
- The data set we'll be working with going forward on players in the WNBA
    - contains general information about players
    - along with metrics for the 16-17 season


```python
# import data set
import pandas as pd
pd.set_option('display.max_columns', None)

wnba = pd.read_csv(path/'data/wnba.csv')
```


```python
# check out first few rows
wnba.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Name</th>
      <th>Team</th>
      <th>Pos</th>
      <th>Height</th>
      <th>Weight</th>
      <th>BMI</th>
      <th>Birth_Place</th>
      <th>Birthdate</th>
      <th>Age</th>
      <th>College</th>
      <th>...</th>
      <th>OREB</th>
      <th>DREB</th>
      <th>REB</th>
      <th>AST</th>
      <th>STL</th>
      <th>BLK</th>
      <th>TO</th>
      <th>PTS</th>
      <th>DD2</th>
      <th>TD3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Aerial Powers</td>
      <td>DAL</td>
      <td>F</td>
      <td>183</td>
      <td>71.0</td>
      <td>21.200991</td>
      <td>US</td>
      <td>January 17, 1994</td>
      <td>23</td>
      <td>Michigan State</td>
      <td>...</td>
      <td>6</td>
      <td>22</td>
      <td>28</td>
      <td>12</td>
      <td>3</td>
      <td>6</td>
      <td>12</td>
      <td>93</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alana Beard</td>
      <td>LA</td>
      <td>G/F</td>
      <td>185</td>
      <td>73.0</td>
      <td>21.329438</td>
      <td>US</td>
      <td>May 14, 1982</td>
      <td>35</td>
      <td>Duke</td>
      <td>...</td>
      <td>19</td>
      <td>82</td>
      <td>101</td>
      <td>72</td>
      <td>63</td>
      <td>13</td>
      <td>40</td>
      <td>217</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Alex Bentley</td>
      <td>CON</td>
      <td>G</td>
      <td>170</td>
      <td>69.0</td>
      <td>23.875433</td>
      <td>US</td>
      <td>October 27, 1990</td>
      <td>26</td>
      <td>Penn State</td>
      <td>...</td>
      <td>4</td>
      <td>36</td>
      <td>40</td>
      <td>78</td>
      <td>22</td>
      <td>3</td>
      <td>24</td>
      <td>218</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Alex Montgomery</td>
      <td>SAN</td>
      <td>G/F</td>
      <td>185</td>
      <td>84.0</td>
      <td>24.543462</td>
      <td>US</td>
      <td>December 11, 1988</td>
      <td>28</td>
      <td>Georgia Tech</td>
      <td>...</td>
      <td>35</td>
      <td>134</td>
      <td>169</td>
      <td>65</td>
      <td>20</td>
      <td>10</td>
      <td>38</td>
      <td>188</td>
      <td>2</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Alexis Jones</td>
      <td>MIN</td>
      <td>G</td>
      <td>175</td>
      <td>78.0</td>
      <td>25.469388</td>
      <td>US</td>
      <td>August 5, 1994</td>
      <td>23</td>
      <td>Baylor</td>
      <td>...</td>
      <td>3</td>
      <td>9</td>
      <td>12</td>
      <td>12</td>
      <td>7</td>
      <td>0</td>
      <td>14</td>
      <td>50</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 32 columns</p>
</div>




```python
# check out last few rows
wnba.tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Name</th>
      <th>Team</th>
      <th>Pos</th>
      <th>Height</th>
      <th>Weight</th>
      <th>BMI</th>
      <th>Birth_Place</th>
      <th>Birthdate</th>
      <th>Age</th>
      <th>College</th>
      <th>...</th>
      <th>OREB</th>
      <th>DREB</th>
      <th>REB</th>
      <th>AST</th>
      <th>STL</th>
      <th>BLK</th>
      <th>TO</th>
      <th>PTS</th>
      <th>DD2</th>
      <th>TD3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>138</th>
      <td>Tiffany Hayes</td>
      <td>ATL</td>
      <td>G</td>
      <td>178</td>
      <td>70.0</td>
      <td>22.093170</td>
      <td>US</td>
      <td>September 20, 1989</td>
      <td>27</td>
      <td>Connecticut</td>
      <td>...</td>
      <td>28</td>
      <td>89</td>
      <td>117</td>
      <td>69</td>
      <td>37</td>
      <td>8</td>
      <td>50</td>
      <td>467</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>139</th>
      <td>Tiffany Jackson</td>
      <td>LA</td>
      <td>F</td>
      <td>191</td>
      <td>84.0</td>
      <td>23.025685</td>
      <td>US</td>
      <td>April 26, 1985</td>
      <td>32</td>
      <td>Texas</td>
      <td>...</td>
      <td>5</td>
      <td>18</td>
      <td>23</td>
      <td>3</td>
      <td>1</td>
      <td>3</td>
      <td>8</td>
      <td>28</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>140</th>
      <td>Tiffany Mitchell</td>
      <td>IND</td>
      <td>G</td>
      <td>175</td>
      <td>69.0</td>
      <td>22.530612</td>
      <td>US</td>
      <td>September 23, 1984</td>
      <td>32</td>
      <td>South Carolina</td>
      <td>...</td>
      <td>16</td>
      <td>70</td>
      <td>86</td>
      <td>39</td>
      <td>31</td>
      <td>5</td>
      <td>40</td>
      <td>277</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>141</th>
      <td>Tina Charles</td>
      <td>NY</td>
      <td>F/C</td>
      <td>193</td>
      <td>84.0</td>
      <td>22.550941</td>
      <td>US</td>
      <td>May 12, 1988</td>
      <td>29</td>
      <td>Connecticut</td>
      <td>...</td>
      <td>56</td>
      <td>212</td>
      <td>268</td>
      <td>75</td>
      <td>21</td>
      <td>22</td>
      <td>71</td>
      <td>582</td>
      <td>11</td>
      <td>0</td>
    </tr>
    <tr>
      <th>142</th>
      <td>Yvonne Turner</td>
      <td>PHO</td>
      <td>G</td>
      <td>175</td>
      <td>59.0</td>
      <td>19.265306</td>
      <td>US</td>
      <td>October 13, 1987</td>
      <td>29</td>
      <td>Nebraska</td>
      <td>...</td>
      <td>11</td>
      <td>13</td>
      <td>24</td>
      <td>30</td>
      <td>18</td>
      <td>1</td>
      <td>32</td>
      <td>151</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 32 columns</p>
</div>




```python
# find the number of rows and columns
wnba.shape
```




    (143, 32)



## _Checking the Documentation_

**Notes**

- G = Games Played
- MIN = Minutes Played
- FGM = Field Goals Made
- FGA = Field Goals Attempts
- FG% = Field Goals %
- 3PM = 3Points Made
- 3PA = 3Points Attempts
- 3P% = 3Points %
- FTM = Free Throws made
- FTA = Free Throws Attempts
- FT% = Free Throws %
- OREB = Offensive Rebounds
- DREB = Defensive Rebounds
- REB = Total Rebounds
- AST = Assists
- STL = Steals
- BLK = Blocks
- TO = Turnovers
- PTS = Total points
- DD2 = Double doubles
- TD3 = Triple doubles


```python
# find the max number of games played by a player in 16-17
parameter = max(wnba['Games Played']); parameter
```




    32




```python
# randomly sample 30 players from population
sample = wnba.sample(n=30, random_state=1); sample.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Name</th>
      <th>Team</th>
      <th>Pos</th>
      <th>Height</th>
      <th>Weight</th>
      <th>BMI</th>
      <th>Birth_Place</th>
      <th>Birthdate</th>
      <th>Age</th>
      <th>College</th>
      <th>...</th>
      <th>OREB</th>
      <th>DREB</th>
      <th>REB</th>
      <th>AST</th>
      <th>STL</th>
      <th>BLK</th>
      <th>TO</th>
      <th>PTS</th>
      <th>DD2</th>
      <th>TD3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>78</th>
      <td>Keisha Hampton</td>
      <td>CHI</td>
      <td>F</td>
      <td>185</td>
      <td>78.0</td>
      <td>22.790358</td>
      <td>US</td>
      <td>February 22, 1990</td>
      <td>27</td>
      <td>DePaul</td>
      <td>...</td>
      <td>36</td>
      <td>59</td>
      <td>95</td>
      <td>24</td>
      <td>20</td>
      <td>7</td>
      <td>21</td>
      <td>207</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>116</th>
      <td>Saniya Chong</td>
      <td>DAL</td>
      <td>G</td>
      <td>173</td>
      <td>64.0</td>
      <td>21.383942</td>
      <td>US</td>
      <td>June 27, 1994</td>
      <td>23</td>
      <td>Connecticut</td>
      <td>...</td>
      <td>9</td>
      <td>19</td>
      <td>28</td>
      <td>33</td>
      <td>21</td>
      <td>3</td>
      <td>23</td>
      <td>87</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>31</th>
      <td>Carolyn Swords</td>
      <td>SEA</td>
      <td>C</td>
      <td>198</td>
      <td>95.0</td>
      <td>24.232221</td>
      <td>US</td>
      <td>July 19, 1989</td>
      <td>28</td>
      <td>Boston College</td>
      <td>...</td>
      <td>10</td>
      <td>29</td>
      <td>39</td>
      <td>9</td>
      <td>5</td>
      <td>4</td>
      <td>22</td>
      <td>54</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Alexis Peterson</td>
      <td>SEA</td>
      <td>G</td>
      <td>170</td>
      <td>63.0</td>
      <td>21.799308</td>
      <td>US</td>
      <td>June 20, 1995</td>
      <td>22</td>
      <td>Syracuse</td>
      <td>...</td>
      <td>3</td>
      <td>13</td>
      <td>16</td>
      <td>11</td>
      <td>5</td>
      <td>0</td>
      <td>11</td>
      <td>26</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>125</th>
      <td>Stefanie Dolson</td>
      <td>CHI</td>
      <td>C</td>
      <td>196</td>
      <td>97.0</td>
      <td>25.249896</td>
      <td>US</td>
      <td>August 1, 1992</td>
      <td>25</td>
      <td>Connecticut</td>
      <td>...</td>
      <td>35</td>
      <td>121</td>
      <td>156</td>
      <td>65</td>
      <td>14</td>
      <td>37</td>
      <td>65</td>
      <td>398</td>
      <td>3</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 32 columns</p>
</div>




```python
# find max number of games using the sample
statistic = max(sample['Games Played']); statistic
```




    30




```python
# measure the sampling error
sampling_error = parameter - statistic; sampling_error
```




    2



## _Simple Random Sampling_

- when we sample, we want to minimize the sampling error as much as possible
    - ex: measuring mean height of US population
        - want our sample mean height to be as close as possible to population's parameter (i.e. mean height)
        - sample so that it is similar in structure with group forming population
- in other words, we want our samples to be representative
    - if sample is representative, sampling error is low
    - less representative, greater the sampling error
- simple random sampling (SRS): generate random numbers and use them to select sample units from population
    - `Series.sample()` methods performs SRS
    - when we use `random_state`, make generation of random numbers predictable
        - specifies initial value used by psuedorandom number generator
- In the cells below, we're going to visualize the discrepency between a parameter and its corresponding statistic in SRS


```python
import matplotlib.pyplot as plt
%config InlineBackend.figure_format = 'retina'
import numpy as np

mean_list = []
for n in range(100):
    # take sample of 10 values from PTS column, with random_state = n
    sample = wnba['PTS'].sample(n=10, random_state=n)
    # get mean from sample
    mean = np.mean(sample)
    # append mean to list
    mean_list.append(mean)
```


```python
# plot discrepency between the parameter an statistics
plt.figure(figsize=(8,6))
plt.scatter(x=np.arange(1,101), y = mean_list)
plt.axhline(np.mean(wnba['PTS']), color='red');
```


![png](/img/dataquest_sampling_18_0.png)


## _Stratified Sampling_

**Notes**
- entirely random nature of SRS could potentially leave out certain individuals of interest
    - in our example, different positions in basketball
        - metrics of players depend on position
        - not guaranteed that we'll have a representative sample that has observations for every position we want to analyze


```python
# there are five unique positions in data set
print(wnba['Pos'].unique())
```

    ['F' 'G/F' 'G' 'C' 'F/C']


- **F** = Forward
- **G** = Guard
- **C** = Center
- **G/F** = Guard/Forward
- **F/C** = Forward/Center

Let's take a quick look at our `sample` and see the breakdown by position.


```python
# group players by position then return counts by position
(sample.groupby('Pos')['Pos'].count()).sort_values(ascending=False)
```




    Pos
    G      11
    C       8
    F       6
    G/F     3
    F/C     2
    Name: Pos, dtype: int64



As we can see above there is an uneven distribution amongest the five positions. To address this we can change the sampling method to one where we organize our data into different groups then do SRS from each group.

In our specific example that would mean grouping players by position than sampling from that group. 

## _Stratified Sampling (cont.)_

**Notes/Instructions**
1. Create new column describing number of points player scored per game during the season.
2.


```python
wnba.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Name</th>
      <th>Team</th>
      <th>Pos</th>
      <th>Height</th>
      <th>Weight</th>
      <th>BMI</th>
      <th>Birth_Place</th>
      <th>Birthdate</th>
      <th>Age</th>
      <th>College</th>
      <th>Experience</th>
      <th>Games Played</th>
      <th>MIN</th>
      <th>FGM</th>
      <th>FGA</th>
      <th>FG%</th>
      <th>15:00</th>
      <th>3PA</th>
      <th>3P%</th>
      <th>FTM</th>
      <th>FTA</th>
      <th>FT%</th>
      <th>OREB</th>
      <th>DREB</th>
      <th>REB</th>
      <th>AST</th>
      <th>STL</th>
      <th>BLK</th>
      <th>TO</th>
      <th>PTS</th>
      <th>DD2</th>
      <th>TD3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Aerial Powers</td>
      <td>DAL</td>
      <td>F</td>
      <td>183</td>
      <td>71.0</td>
      <td>21.200991</td>
      <td>US</td>
      <td>January 17, 1994</td>
      <td>23</td>
      <td>Michigan State</td>
      <td>2</td>
      <td>8</td>
      <td>173</td>
      <td>30</td>
      <td>85</td>
      <td>35.3</td>
      <td>12</td>
      <td>32</td>
      <td>37.5</td>
      <td>21</td>
      <td>26</td>
      <td>80.8</td>
      <td>6</td>
      <td>22</td>
      <td>28</td>
      <td>12</td>
      <td>3</td>
      <td>6</td>
      <td>12</td>
      <td>93</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alana Beard</td>
      <td>LA</td>
      <td>G/F</td>
      <td>185</td>
      <td>73.0</td>
      <td>21.329438</td>
      <td>US</td>
      <td>May 14, 1982</td>
      <td>35</td>
      <td>Duke</td>
      <td>12</td>
      <td>30</td>
      <td>947</td>
      <td>90</td>
      <td>177</td>
      <td>50.8</td>
      <td>5</td>
      <td>18</td>
      <td>27.8</td>
      <td>32</td>
      <td>41</td>
      <td>78.0</td>
      <td>19</td>
      <td>82</td>
      <td>101</td>
      <td>72</td>
      <td>63</td>
      <td>13</td>
      <td>40</td>
      <td>217</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Alex Bentley</td>
      <td>CON</td>
      <td>G</td>
      <td>170</td>
      <td>69.0</td>
      <td>23.875433</td>
      <td>US</td>
      <td>October 27, 1990</td>
      <td>26</td>
      <td>Penn State</td>
      <td>4</td>
      <td>26</td>
      <td>617</td>
      <td>82</td>
      <td>218</td>
      <td>37.6</td>
      <td>19</td>
      <td>64</td>
      <td>29.7</td>
      <td>35</td>
      <td>42</td>
      <td>83.3</td>
      <td>4</td>
      <td>36</td>
      <td>40</td>
      <td>78</td>
      <td>22</td>
      <td>3</td>
      <td>24</td>
      <td>218</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Alex Montgomery</td>
      <td>SAN</td>
      <td>G/F</td>
      <td>185</td>
      <td>84.0</td>
      <td>24.543462</td>
      <td>US</td>
      <td>December 11, 1988</td>
      <td>28</td>
      <td>Georgia Tech</td>
      <td>6</td>
      <td>31</td>
      <td>721</td>
      <td>75</td>
      <td>195</td>
      <td>38.5</td>
      <td>21</td>
      <td>68</td>
      <td>30.9</td>
      <td>17</td>
      <td>21</td>
      <td>81.0</td>
      <td>35</td>
      <td>134</td>
      <td>169</td>
      <td>65</td>
      <td>20</td>
      <td>10</td>
      <td>38</td>
      <td>188</td>
      <td>2</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Alexis Jones</td>
      <td>MIN</td>
      <td>G</td>
      <td>175</td>
      <td>78.0</td>
      <td>25.469388</td>
      <td>US</td>
      <td>August 5, 1994</td>
      <td>23</td>
      <td>Baylor</td>
      <td>R</td>
      <td>24</td>
      <td>137</td>
      <td>16</td>
      <td>50</td>
      <td>32.0</td>
      <td>7</td>
      <td>20</td>
      <td>35.0</td>
      <td>11</td>
      <td>12</td>
      <td>91.7</td>
      <td>3</td>
      <td>9</td>
      <td>12</td>
      <td>12</td>
      <td>7</td>
      <td>0</td>
      <td>14</td>
      <td>50</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
list(wnba['Pos'].unique())
```




    ['F', 'G/F', 'G', 'C', 'F/C']




```python
# create a new column PPG, that combines PTS and Games Played columns
wnba['PPG'] = wnba['PTS'] / wnba['Games Played']
```


```python
# stratify position F
forward = wnba[wnba['Pos'] == 'F']
# stratify position G/F
guard_forward = wnba[wnba['Pos'] == 'G/F']
# stratify position G
guard = wnba[wnba['Pos'] == 'G']
# stratify position F
center = wnba[wnba['Pos'] == 'C']
# stratify position F
forward_center = wnba[wnba['Pos'] == 'F/C']

# loop through the strata and sample 10 observations from each
mean_points = {}

for strata, position in [(forward, 'F'), (guard_forward, 'G/F'), 
                         (guard, 'G'), (center, 'C'), (forward_center, 'F/C')]:
    # sample 10 observations using simple random sampling
    sample = strata.sample(n=10, random_state=0)
    # get mean PPG
    mean_ppg = np.mean(sample['PPG'])
    # store mean in mean_points dictionary with associated dataframe
    mean_points[position] = mean_ppg
```


```python
# sort dictionary according to values
sorted_points = sorted((value, key) for (key, value) in mean_points.items()); sorted_points
```




    [(6.817264935760487, 'G/F'),
     (7.092926195632343, 'G'),
     (8.702175158545568, 'F'),
     (9.059118773946361, 'F/C'),
     (9.833761394334251, 'C')]




```python
# return position of the max value in mean_points dictionary
max(mean_points, key=mean_points.get)
```




    'C'




```python
# store value
position_most_points = max(mean_points, key=mean_points.get)
```

## _Proportional Stratified Sampling_

This is a good start however we have overlooked one variable that is a signficant influence in regards to point scored: number of games played in. 


```python
print('Min:', wnba['Games Played'].min(), '\nMax:', wnba['Games Played'].max())
```

    Min: 2 
    Max: 32


Now let's dive deeper into this and gain a better understanding of the distribution of the games played.


```python
wnba['Games Played'].value_counts(bins = 3, normalize = True) * 100
```




    (22.0, 32.0]     72.727273
    (12.0, 22.0]     18.181818
    (1.969, 12.0]     9.090909
    Name: Games Played, dtype: float64



As we can see above, `72.72%` of players had more than 23 games played. This particular group of players is probably exerting some influence on the means of the points scored that we previously got. 

Additionally, we have to think of this number when it comes to sampling. We could either get a sample that has a lower, higher, or similar rate of players from this group, which will have an effect on the variance of the mean (see scatter plot above). 

Knowing this, we can stratify our data set by number of games played and then randomly sample from each a _proportional_ number of observations. 


```python
# stratify according to games played
low_games = wnba[wnba['Games Played'] <= 12]
mid_games = wnba[(wnba['Games Played'] > 12) & (wnba['Games Played'] <= 22)]
high_games = wnba[wnba['Games Played'] > 22]
```


```python
games_played_mean = []

# perform stratified proportional sampling 100 times
for n in range(100):
    # gather samples from each data set
    s1 = low_games.sample(n = 1, random_state = n)
    s2 = mid_games.sample(n = 2, random_state = n)
    s3 = high_games.sample(n = 7, random_state = n)
    # concatenate all together
    data = pd.concat([s1, s2, s3])
    # compute the mean
    mean = np.mean(data['PTS'])
    # append mean to list
    games_played_mean.append(mean)
```


```python
# plot discrepency between the parameter an statistics
plt.figure(figsize=(8,6))
plt.scatter(x=np.arange(1,101), y = games_played_mean)
plt.axhline(np.mean(wnba['PTS']), color='red');
```


![png](/img/dataquest_sampling_39_0.png)


## _Choosing the Right Strate_

Our results don't look any better than the previous one that utilized SRS. Why is this? Well, when it comes to games played, a player is considered to have played one game even if it was only for 1-2 minutes! Based off of this fact, we can see that `Games Played` may not be the best metric to stratify by. Instead we can stratify according to minutes played, which gives a better picture of exactly how much a player played. 

**Guidelines for choosing good strata:**
1. _Minimize the variability within each stratum_
    - if the variability is high, it might be a sign that you either need a more granular stratification or change the criterion of stratification
2. _Maximize the variability between strata_
    - good strata are different from one another
3. _Stratification criterion should be strongly correlated with the property you're trying to measure_
    - In our example, the column describing minutes played (the criterion) should be strongly correlated with the number of total points (property we want to measure)


```python
# get breakdown of Minutes Played
wnba['MIN'].value_counts(bins = 3, normalize = True) * 100
```




    (347.333, 682.667]    34.965035
    (10.993, 347.333]     33.566434
    (682.667, 1018.0]     31.468531
    Name: MIN, dtype: float64




```python
# stratify according to minutes
min_low = wnba[wnba['MIN'] <= 347.333]
min_mid = wnba[(wnba['MIN'] > 347.333) & (wnba['MIN'] <= 682.667)]
min_high = wnba[wnba['MIN'] > 682.667]
```


```python
minutes_played_mean = []

# perform stratified sampling according to minutes played
for n in range(100):
    # gather samples from each data set
    s1 = min_low.sample(n = 3, random_state = n)
    s2 = min_mid.sample(n = 4, random_state = n)
    s3 = min_high.sample(n = 3, random_state = n)
    # concatenate all together
    data = pd.concat([s1, s2, s3])
    # compute the mean
    mean = np.mean(data['PTS'])
    # append mean to list
    minutes_played_mean.append(mean)
```


```python
# plot discrepency between the parameter an statistics
plt.figure(figsize=(8,6))
plt.scatter(x=np.arange(1,101), y = minutes_played_mean)
plt.axhline(np.mean(wnba['PTS']), color='red');
```


![png](/img/dataquest_sampling_44_0.png)


## _Cluster Sampling_

**Notes**
- Common in data science that data is scattered in such a way that it comes from disparate sources
    - what you could do in this case is list all the data sources and then randomly pick a few of them to collect data from
- Theoretical situation: had to collect data from each team's website
    - each team's website is considered a cluster


```python
# lets see each team
wnba['Team'].unique()
```




    array(['DAL', 'LA', 'CON', 'SAN', 'MIN', 'SEA', 'PHO', 'CHI', 'WAS', 'NY',
           'ATL', 'IND'], dtype=object)



For cluster sampling in our theoretical situation, we'd find a way to randomly pick a few clusters from our listing, and we need to be sure that we avoid picking a cluster twice.


```python
# on way to sample four clusers randomly
teams = pd.Series(wnba['Team'].unique()).sample(4, random_state = 0); teams
```




    6     PHO
    11    IND
    4     MIN
    10    ATL
    dtype: object




```python
list(teams)[0]
```




    'PHO'



With this knowledge, we have a lot of options at our disposal. We can either collect all the data from each cluster, or just sample; additionally we can use different sampling methods for different clusters. As an example, we could use stratified sampling on the first two clusters and SRS on the other two. 


```python
# from the four teams above, create data sets for each (i.e. the clusters)
t1 = wnba[wnba['Team'] == list(teams)[0]]
t2 = wnba[wnba['Team'] == list(teams)[1]]
t3 = wnba[wnba['Team'] == list(teams)[2]]
t4 = wnba[wnba['Team'] == list(teams)[3]]

# combine all together
data = pd.concat([t1, t2, t3, t4]); data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Name</th>
      <th>Team</th>
      <th>Pos</th>
      <th>Height</th>
      <th>Weight</th>
      <th>BMI</th>
      <th>Birth_Place</th>
      <th>Birthdate</th>
      <th>Age</th>
      <th>College</th>
      <th>Experience</th>
      <th>Games Played</th>
      <th>MIN</th>
      <th>FGM</th>
      <th>FGA</th>
      <th>FG%</th>
      <th>15:00</th>
      <th>3PA</th>
      <th>3P%</th>
      <th>FTM</th>
      <th>FTA</th>
      <th>FT%</th>
      <th>OREB</th>
      <th>DREB</th>
      <th>REB</th>
      <th>AST</th>
      <th>STL</th>
      <th>BLK</th>
      <th>TO</th>
      <th>PTS</th>
      <th>DD2</th>
      <th>TD3</th>
      <th>PPG</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>6</th>
      <td>Alexis Prince</td>
      <td>PHO</td>
      <td>G</td>
      <td>188</td>
      <td>81.0</td>
      <td>22.917610</td>
      <td>US</td>
      <td>February 5, 1994</td>
      <td>23</td>
      <td>Baylor</td>
      <td>R</td>
      <td>16</td>
      <td>112</td>
      <td>9</td>
      <td>34</td>
      <td>26.5</td>
      <td>4</td>
      <td>15</td>
      <td>26.7</td>
      <td>2</td>
      <td>2</td>
      <td>100.0</td>
      <td>1</td>
      <td>14</td>
      <td>15</td>
      <td>5</td>
      <td>4</td>
      <td>3</td>
      <td>3</td>
      <td>24</td>
      <td>0</td>
      <td>0</td>
      <td>1.500000</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Angel Robinson</td>
      <td>PHO</td>
      <td>F/C</td>
      <td>198</td>
      <td>88.0</td>
      <td>22.446689</td>
      <td>US</td>
      <td>August 30, 1995</td>
      <td>21</td>
      <td>Arizona State</td>
      <td>1</td>
      <td>15</td>
      <td>237</td>
      <td>25</td>
      <td>44</td>
      <td>56.8</td>
      <td>1</td>
      <td>1</td>
      <td>100.0</td>
      <td>7</td>
      <td>7</td>
      <td>100.0</td>
      <td>16</td>
      <td>42</td>
      <td>58</td>
      <td>8</td>
      <td>1</td>
      <td>11</td>
      <td>16</td>
      <td>58</td>
      <td>0</td>
      <td>0</td>
      <td>3.866667</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Brittney Griner</td>
      <td>PHO</td>
      <td>C</td>
      <td>206</td>
      <td>93.0</td>
      <td>21.915355</td>
      <td>US</td>
      <td>October 18, 1990</td>
      <td>26</td>
      <td>Baylor</td>
      <td>5</td>
      <td>22</td>
      <td>682</td>
      <td>167</td>
      <td>293</td>
      <td>57.0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
      <td>127</td>
      <td>154</td>
      <td>82.5</td>
      <td>43</td>
      <td>129</td>
      <td>172</td>
      <td>39</td>
      <td>13</td>
      <td>54</td>
      <td>52</td>
      <td>461</td>
      <td>6</td>
      <td>0</td>
      <td>20.954545</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Camille Little</td>
      <td>PHO</td>
      <td>F</td>
      <td>188</td>
      <td>82.0</td>
      <td>23.200543</td>
      <td>US</td>
      <td>January 18, 1985</td>
      <td>32</td>
      <td>North Carolina</td>
      <td>11</td>
      <td>30</td>
      <td>759</td>
      <td>93</td>
      <td>219</td>
      <td>42.5</td>
      <td>9</td>
      <td>52</td>
      <td>17.3</td>
      <td>33</td>
      <td>52</td>
      <td>63.5</td>
      <td>42</td>
      <td>71</td>
      <td>113</td>
      <td>42</td>
      <td>28</td>
      <td>13</td>
      <td>50</td>
      <td>228</td>
      <td>0</td>
      <td>0</td>
      <td>7.600000</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Cayla George</td>
      <td>PHO</td>
      <td>C</td>
      <td>193</td>
      <td>87.0</td>
      <td>23.356332</td>
      <td>AU</td>
      <td>April 20, 1987</td>
      <td>30</td>
      <td>Georgia</td>
      <td>1</td>
      <td>28</td>
      <td>365</td>
      <td>40</td>
      <td>105</td>
      <td>38.1</td>
      <td>13</td>
      <td>45</td>
      <td>28.9</td>
      <td>7</td>
      <td>12</td>
      <td>58.3</td>
      <td>10</td>
      <td>71</td>
      <td>81</td>
      <td>15</td>
      <td>9</td>
      <td>11</td>
      <td>13</td>
      <td>100</td>
      <td>1</td>
      <td>0</td>
      <td>3.571429</td>
    </tr>
  </tbody>
</table>
</div>



We're now going to use the data collected to estimate the mean for the following player attributes:
- Height
- Age
- BMI
- Total points


```python
# sampling error for height
sampling_error_height = np.mean(wnba['Height']) - np.mean(data['Height'])
# sampling error for age
sampling_error_age = np.mean(wnba['Age']) - np.mean(data['Age'])
# sampling error for BMI
sampling_error_BMI = np.mean(wnba['BMI']) - np.mean(data['BMI'])
# sampling error for total points
sampling_error_points = np.mean(wnba['PTS']) - np.mean(data['PTS'])

print('Sampling error for height:' , sampling_error_height, 
      '\nSampling error for age:', sampling_error_age, 
      '\nSampling error for BMI:', sampling_error_BMI, 
      '\nSampling error for total points:', sampling_error_points)
```

    Sampling error for height: -0.06400121617511445 
    Sampling error for age: -1.401337792642142 
    Sampling error for BMI: 0.23095444165950596 
    Sampling error for total points: -27.79674673152934


## _Sampling in Data Science Practice_

**Notes**
- Say you work for e-commerce company that has table in database with 10+ million rows of online transactions
    - job is to find categories of customers with low buying rate
    - sampling allows us to work on and gain valuable insights on significantly less than 10+ million rows (which will save you a lot of code!)
- Another situation, collecting data from an API that either has usage limit or is not free
    - forced to sample, knowing how and what to sample can be of great use
- Also, when data is scattered across different locations, sampling can be a valuable tool
    - clustering sampling would be great choice in this scenario
  
## Descriptive and Inferential Statistics
- when describing a sample/population (by measuring averages, proportions, and other metrics) we do **descriptive statistics**
- when trying to use a sample to draw conclusions about a population --> inferential statistics
    - we're _inferring_ information from the sample about the population
