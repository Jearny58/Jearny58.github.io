---
layout: post
published: true
title: 'Technical Chronicles: Basic Python Data Structures'
subtitle: Review of Basic Data Structures in Python
date: '2019-08-22'
image: /img/study_data_structures.gif
excerpt: This notebook reviews the basic Python data structures: lists, tuples, dictionaries and sets. Before I begin though want to acknowledge Chris Albon...
---

# _Data Structure Basics_

This notebook reviews the basic Python data structures: lists, tuples, dictionaries and sets. 

Before I begin though want to acknowledge [Chris Albon](https://chrisalbon.com/) and his awesome Python tutorials! This and other notebooks have been adapted from a conglomeration of lessons on his website, which can be found [here](https://chrisalbon.com). There are slight tweaks but the foundation was definitely set by Chris, so thank you again for putting out this material. It is fantastic!

## _Basic Python Data Structures_

This section will review four primary data structures: _lists_, _tuples_, _dictionaries_, and _sets_.

We'll also take a look at _queues_ and _stacks_.

### _Lists_

"a collection which is ordered and changeable...[and] are written with square brackets." - [w3schools.com](https://www.w3schools.com/python/python_lists.asp)


```python
# create a list of cities
cities = ['Placerville', 'Sacramento', 'San Francisco', 'Los Angeles', 
         'Austin', 'Chapel Hill', 'Charlotte']
cities
```




    ['Placerville',
     'Sacramento',
     'San Francisco',
     'Los Angeles',
     'Austin',
     'Chapel Hill',
     'Charlotte']




```python
# what is the length of the list
len(cities)
```




    7




```python
# add a city to the list
cities.append('New York'); cities
```




    ['Placerville',
     'Sacramento',
     'San Francisco',
     'Los Angeles',
     'Austin',
     'Chapel Hill',
     'Charlotte',
     'New York',
     'New York']




```python
# sort the cities
cities.sort(); cities
```




    ['Austin',
     'Chapel Hill',
     'Charlotte',
     'Los Angeles',
     'Placerville',
     'Sacramento',
     'San Francisco']




```python
# reverse sort the list
cities.reverse(); cities
```




    ['San Francisco',
     'Sacramento',
     'Placerville',
     'Los Angeles',
     'Charlotte',
     'Chapel Hill',
     'Austin']




```python
# view the third item in the list
cities[2]
```




    'Placerville'




```python
# delete the last item in the list
del cities[-1]; cities
```




    ['San Francisco',
     'Sacramento',
     'Placerville',
     'Los Angeles',
     'Charlotte',
     'Chapel Hill']




```python
# add your favorite number to the cities list
cities.append(5); cities
```




    ['San Francisco',
     'Sacramento',
     'Placerville',
     'Los Angeles',
     'Charlotte',
     'Chapel Hill',
     5]




```python
# create a list of lists
city_list = [['Sacramento', 'Placerville', 'Auburn'],
            ['San Francisco', 'Oakland', 'San Jose'],
            ['Los Angeles', 'San Diego', 'Santa Barbara']]; city_list
```




    [['Sacramento', 'Placerville', 'Auburn'],
     ['San Francisco', 'Oakland', 'San Jose'],
     ['Los Angeles', 'San Diego', 'Santa Barbara']]




```python
# for each element in city_list, take each element in the list
flattened_city_list = [i for row in city_list for i in row]; flattened_city_list
```




    ['Sacramento',
     'Placerville',
     'Auburn',
     'San Francisco',
     'Oakland',
     'San Jose',
     'Los Angeles',
     'San Diego',
     'Santa Barbara']




```python
# create another cities list
cities_2 = ['Chapel Hill', 'Charlotte', 'Raleigh', 'Greensboro', 'Wilmington', 'Boone', 'Asheville']
cities_2
```




    ['Chapel Hill',
     'Charlotte',
     'Raleigh',
     'Greensboro',
     'Wilmington',
     'Boone',
     'Asheville']




```python
# iterate over both the lists of cities
for city1, city2 in zip(cities, cities_2):
    print(city1, city2)
    print('-' * 20)
```

    San Francisco Chapel Hill
    --------------------
    Sacramento Charlotte
    --------------------
    Placerville Raleigh
    --------------------
    Los Angeles Greensboro
    --------------------
    Charlotte Wilmington
    --------------------
    Chapel Hill Boone
    --------------------
    5 Asheville
    --------------------



```python
# select a random element from a list
from random import choice

choice(cities)
```




    'Charlotte'




```python
# selecting items in a list with filters
ages = [2, 55, 94, 10, 23, 93, 45, 34, 33, 31, 21, 19, 16, 10, 11, 13, 50, 54, 44, 43]

over_21 = list(filter((lambda x: x >= 21), ages)); over_21
```




    [55, 94, 23, 93, 45, 34, 33, 31, 21, 50, 54, 44, 43]




```python
# same as above but multi-line solution
ages = [2, 55, 94, 10, 23, 93, 45, 34, 33, 31, 21, 19, 16, 10, 11, 13, 50, 54, 44, 43]

# create a lambda function
older_21 = lambda x: x >= 21

# filter using lambda function
filtered_ages = filter(older_21, ages)

# convert filter results into a sorted list
over_21 = sorted(list(filtered_ages)); over_21
```




    [21, 23, 31, 33, 34, 43, 44, 45, 50, 54, 55, 93, 94]




```python
# sort a list of cities by length
sorted(cities_2, key=lambda x: len(x))
```




    ['Boone',
     'Raleigh',
     'Charlotte',
     'Asheville',
     'Greensboro',
     'Wilmington',
     'Chapel Hill']



### _Tuples_

"a collection which is ordered and unchangeable. Allows duplicate members." - [w3schools.com](https://www.w3schools.com/python/python_tuples.asp)


```python
# create a tuple of European cities
europe_cities = ('Tallinn', 'Berlin', 'Rome', 'Copenhagen', 'Amsterdam'); europe_cities
```




    ('Tallinn', 'Berlin', 'Rome', 'Copenhagen', 'Amsterdam')




```python
# create a tuple of European countries and add cities
europe_countries = ('Portugal', 'Switzerland', 'Latvia', 'Czech Republic', europe_cities); europe_countries
```




    ('Portugal',
     'Switzerland',
     'Latvia',
     'Czech Republic',
     ('Tallinn', 'Berlin', 'Rome', 'Copenhagen', 'Amsterdam'))




```python
# view last item of tuple
europe_countries[-1]
```




    ('Tallinn', 'Berlin', 'Rome', 'Copenhagen', 'Amsterdam')




```python
# view the third item of last item of tuple
europe_countries[-1][2]
```




    'Rome'




```python
# create tuple with country and capital
country_capital = [('Norway', 'Oslo'), ('Ireland', 'Dublin'), ('Poland', 'Warsaw'), ('Latvia', 'Riga')]

# unpack tuple above
for country, capital in country_capital:
    print(country, capital)

print('-'*30)

# unpack just the country
for country, _ in country_capital:
    print(country)
    
print('-' * 30)

# unpack the capital
for _, capital in country_capital:
    print(capital)
```

    Norway Oslo
    Ireland Dublin
    Poland Warsaw
    Latvia Riga
    ------------------------------
    Norway
    Ireland
    Poland
    Latvia
    ------------------------------
    Oslo
    Dublin
    Warsaw
    Riga



```python
# use named tuples to store data
from collections import namedtuple

Country = namedtuple('Country', 'country capital population region')

# create an entry
estonia = Country('Estonia', 'Tallinn', 1.3, 'Baltic')

print(estonia.capital)
print(estonia.population)
print(estonia)
```

    Tallinn
    1.3
    Country(country='Estonia', capital='Tallinn', population=1.3, region='Baltic')


### _Dictionaries_

"a collection which is unordered, changeable and indexed. In Python dictionaries are written with curly brackets, and they have keys and values." - [w3schools.com](https://www.w3schools.com/python/python_dictionaries.asp)


```python
# create a dictionary of european countries and their capitals
capitals = {'Sweden': 'Stockholm',
           'Germany': 'Berlin',
           'France': 'Paris',
           'Portugal': 'Lisbon',
           'Estonia': 'Tallinn',
           'Finland': 'Helsinki'}
```


```python
# print the value of using a key
capitals['France']
```




    'Paris'




```python
# delete an item based on key
del capitals['Finland']; capitals
```




    {'Sweden': 'Stockholm',
     'Germany': 'Berlin',
     'France': 'Paris',
     'Portugal': 'Lisbon',
     'Estonia': 'Tallinn'}




```python
# add an item to dictionary
capitals['Belgium'] = 'Brussels'; capitals
```




    {'Sweden': 'Stockholm',
     'Germany': 'Berlin',
     'France': 'Paris',
     'Portugal': 'Lisbon',
     'Estonia': 'Tallinn',
     'Belgium': 'Brussels'}




```python
# create another capitals dictionary
capitals_2 = {'Norway': 'Oslo',
             'Italy': 'Rome',
             'Czech Republic': 'Prague',
             'Germany': 'Berlin',
             'Spain': 'Madrid'}
```


```python
# find duplicate keys
capitals.keys() & capitals_2.keys()
```




    {'Germany'}




```python
# find difference in keys
capitals.keys() - capitals_2.keys()
```




    {'Belgium', 'Estonia', 'France', 'Portugal', 'Sweden'}




```python
# find key, value pairs in common
capitals.keys() & capitals_2.keys()
```




    {'Germany'}




```python
# create a dictionary of populations (in millions)
populations = {'Russia': 143, 
              'Romania': 19, 
              'Spain': 46,
              'Ukraine': 44,
              'Turkey': 81}
populations
```




    {'Russia': 143, 'Romania': 19, 'Spain': 46, 'Ukraine': 44, 'Turkey': 81}




```python
# find the max value in the populations dictionary
max(zip(populations.values(), populations.keys()))
```




    (143, 'Russia')




```python
# display all dictionary entries where key isn't Germany
{keys: capitals[keys] for keys in capitals if not keys.startswith('Germany')}
```




    {'Sweden': 'Stockholm',
     'France': 'Paris',
     'Portugal': 'Lisbon',
     'Estonia': 'Tallinn',
     'Belgium': 'Brussels'}



### _Sets_

"a collection which is unordered and unindexed. In Python sets are written with curly brackets." - [w3schools.com](https://www.w3schools.com/python/python_sets.asp)


```python
# create a set of African countries
africa = set(['Nigeria', 'Ethiopia', 'Egypt', 'South Africa']); africa
```




    {'Egypt', 'Ethiopia', 'Nigeria', 'South Africa'}




```python
# is Chine in africa?
'China' in africa
```




    False




```python
# is Egypt in Africa?
'Egypt' in africa
```




    True




```python
# create a copy of africa
africa_copy = africa.copy(); africa_copy
```




    {'Egypt', 'Ethiopia', 'Nigeria', 'South Africa'}




```python
# add Cameroon to africa
africa.add('Cameroon'); africa
```




    {'Cameroon', 'Egypt', 'Ethiopia', 'Nigeria', 'South Africa'}




```python
# remove South Africa from africa
africa.remove('South Africa'); africa
```




    {'Cameroon', 'Egypt', 'Ethiopia', 'Nigeria'}




```python
# what items are union between africa and africa_copy
africa & africa_copy
```




    {'Egypt', 'Ethiopia', 'Nigeria'}



### _Queues & Stacks_

"Queues are similar to stacks and the difference between them is in removing items: With a queue you remove the item least recently added (first-in, first-out or FIFO); and with a stack you remove the item most recently added (last-in, first-out or LIFO)." - [Dan Bader](https://dbader.org/blog/queues-in-python)


```python
from collections import deque
```


```python
# option 1 make a queue
queue = deque(range(15)); queue
```




    deque([0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14])




```python
# option 2 make a queue that if full discards item at opposite end to where you added an item
queue2 = deque(range(10), maxlen=10); queue2
```




    deque([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])




```python
# append an item to the right
queue2.append('A'); queue2
```




    deque([1, 2, 3, 4, 5, 6, 7, 8, 9, 'A'])




```python
# append an item to the left
queue2.appendleft('B'); queue2
```




    deque(['B', 1, 2, 3, 4, 5, 6, 7, 8, 9])




```python
# count number of B's in queue
queue2.count('B')
```




    1




```python
# remove and return right-most item
pop = queue2.pop(); print(pop, queue2)
```

    9 deque(['B', 1, 2, 3, 4, 5, 6, 7, 8], maxlen=10)



```python
# remove and return left-most item
queue2.popleft()
```




    'B'




```python
queue2
```




    deque([1, 2, 3, 4, 5, 6, 7, 8])




```python
# insert item to right of an item
queue2.insert(7, 'A'); queue2
```




    deque([1, 2, 3, 4, 5, 6, 7, 'A', 8])




```python
# reverse the queue
queue2.reverse(); queue2
```




    deque([8, 'A', 7, 6, 5, 4, 3, 2, 1])




```python
# delete all items
queue2.clear(); queue2
```




    deque([])
