---
layout: post
published: false
title: 'Technical Chronicles: ''Vanilla'' Python & SQL'
---
### My Last Interview

I scheduled my last mock interview with Springboard, and I figured a good way to do a final review was to create a blog post on all the technical subjects I revisited. The first part will be focused on SQL and then I'll turn my attention to Python. Now, there is a caveat when it comes to the Python portion: it's going to be the 'vanilla' version of Python. We're not allowed to use libraries like pandas or numpy. Instead, we need to be comfortable using the [Python Standard Library](https://docs.python.org/3/library/), in addition to the following concepts:

- **Data structures** (lists, tuples, dictionaries)
- **Flow control** (if-else, for, while loops)
- The **[`itertools`](https://pymotw.com/3/itertools/)** library
- The **[`collections`](https://pymotw.com/3/collections/)** library
- Working with files

Additionally, here are some of the more important SQL concepts that we'll primarily focus on:

- **Aggregation functions** (`COUNT()`, `SUM()`, etc.)
- **`CASE WHEN`** statements
- Various types of joins
- Filtering (`WHERE` and `HAVING`)

As a last note before I get started, this post will serve as a 'notepad' of sorts. In other words there will be no beginning, middle or end. Instead, it will be a conglomeration of information that I found useful in trying to understand the aforementioned Python/SQL concepts.

# SQL

Firstly, I'd like to acknowledge [Mode Analytics](https://mode.com/) and their SQL tutorials, which were a huge help! Additionally, a lot of the material for this section is based of said tutorials. 

## Aggregation functions in SQL

SQL is an excellent tool to aggregate data, and the following functions will be used often so you should get very comfortable with them. Below are some that we'll dive deeper into:

- [`COUNT`](https://mode.com/resources/sql-tutorial/sql-count/): counts the number of rows in a particular column
- [`SUM`](https://mode.com/resources/sql-tutorial/sql-sum/): totals the values in a given column
- [`MIN`](https://mode.com/resources/sql-tutorial/sql-min-max/): will return the lowest number, earliest data, or non-numerical value as close alphabetically to 'A' as possible
- [`MAX`](https://mode.com/resources/sql-tutorial/sql-min-max/): returns the largest number, latest data, or non-numerical value as far as possible alphabetically from 'A' as possible
- [`AVG`](https://mode.com/resources/sql-tutorial/sql-avg/): calculates the average of a selected group of values

### Examples of `COUNT`

__Counting All Rows__

		SELECT COUNT(*)
		FROM tutorial.aapl_historical_stock_price
        
__Counting Individual Columns__

		SELECT COUNT(high)
        FROM tutorial.aapl_historical_stock_price
        
__Practice Problem: Count number of non-null rows in `low` column__

		SELECT COUNT(low) 
		FROM tutorial.aapl_historical_stock_price
        

        

        
    
