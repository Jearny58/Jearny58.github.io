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

__Practice Problem: Write a query that determines counts of every single column__

	SELECT COUNT(year) AS year,
           COUNT(month) AS month,
           COUNT(open) AS open,
           COUNT(high) AS high,
           COUNT(low) AS low,
           COUNT(close) AS close,
           COUNT(volume) AS volume
  	FROM tutorial.aapl_historical_stock_price
        
__Important Reminders for `COUNT`__

- it counts the total number of non-null rows, not the distinct values
- its good practice to name columns so they make more sense to anyone else viewing the work

### Examples of `SUM`

__Sum of the `volume` column from [Apple stock prices dataset](https://mode.com/resources/sql-tutorial/sql-aggregate-functions/#the-apple-stock-prices-dataset)__

	SELECT SUM(volume)
    FROM tutorial.aapl_historical_stock_price

__Practice: Calculate average opening `AAPL` price__

	SELECT SUM(open) / COUNT(open) AS avg_opening_price
    FROM tutorial.aapl_historical_stock_price
        
__Important Reminders for `SUM`__

- can only use on columns containing numerical values
- treats nulls as `0`

### Examples of `MIN`/`MAX`

__Practice: What was Apple's lowest stock price?__

	SELECT MIN(low) as lowest_stock_price
    FROM tutorial.aapl_historical_stock_price
        
__Practice: What was the highest single-day increase in Apple's share value?__

	SELECT MAX(close - open)
    FROM tutorial.aapl_historical_stock_price

__Important Reminders for `MIN`/`MAX`__

- return lowest/highest values in particular column
- can be used on non-numerical columns

### Examples of `AVG`

__Practice: Calculate the average daily trade volumne for Apple stock__

	SELECT AVG(volumne) as avg_volume
    FROM tutorial.aapl_historical_stock_price

__Important Reminders for `AVG`__

- calculates average of selected group of values
- can only be used on numerical columns
- ignores nulls completely
	- some cases where you'll want to treat null values as 0
    - will need to write a statement to convert nulls to 0
    
### SQL `GROUP BY`

The above functions have something in common: they aggregate across the entire table. However, what if you want to aggregate only part of it? That is where `GROUP BY` comes into play, as it allows data to be separated into groups then aggregated independently of each other. 



        

        
    
