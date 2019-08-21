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

__Example: Group Apple data by year__

	SELECT year, COUNT(*) AS count
    FROM tutorial.aapl_historical_stock_price
    GROUP BY year
    
You can group by multiple columns as well (just be sure to use a comma!). 

__Example: Group by multiple columns__

	SELECT year,
    	   month,
           COUNT(*) AS count
    FROM tutorial.aapl_historical_stock_price
    GROUP BY year, month
    
__Practice: Total number of shares traded each month, ordered chronologically__

	SELECT year, month, SUM(volume) AS shares_traded
    FROM tutorial.aapl_historical_stock_price
    GROUP BY year, month
    ORDER BY year, month
    
You can also substitute numbers for column names in the `GROUP BY` clause. However, this should only be used in situations where the `GROUP BY` clause is excessively long. Additionally, this functionality may not be supported by every flavor of SQL. 

__Practice: Calculate average daily price change, grouped by year__

	SELECT year, AVG(high - low) AS avg_daily_change
    FROM tutorial.aapl_historical_stock_price
    GROUP BY year
    ORDER BY year
    
__Practice: Calculate the lowest and highest prices that Apple stock achieved each month__

	SELECT year, month, MAX(high) AS high_price, MIN(low) AS low_price
    FROM tutorial.aapl_historical_stock_price
    GROUP BY year, month
    ORDER BY year, month
        
### SQL `HAVING`

Sometimes `GROUP BY` isn't enough. Sometimes you want to find more nuanced information, like months where Apple stock eclipsed $400 a share. The `WHERE` clause won't work on aggregate columns, and this is where `HAVING` clause comes in.

__Example: Months when AAPL eclipsed $400__

	SELECT year, month, MAX(high) AS monthly_high
    FROM tutorial.aapl_historical_stock_price
    GROUP BY year, month
    HAVING MAX(high) > 400
    ORDER BY year, month
    
**Note**: `HAVING` is a 'clean' way to filter an aggregated query, but this can also be done with a subquery. 

__Query Clause Order__

1. `SELECT`
2. `FROM`
3. `WHERE`
4. `GROUP BY`
5. `HAVING`
6. `ORDER BY`

### SQL `DISTINCT`

By using `SELECT DISTINCT`, you'll be able to look at the unique values within a particular column. 

__Example: Select unique months__

	SELECT DISTINCT month
    FROM tutorial.aapl_historical_stock_price
    
You can use two or more columns as well with `DISTINCT`; this will contain all unqiue pairs of those particular columns.

__Example: Unique pairs from multiple columns__

	SELECT DISTINCT year, month
    FROM tutorial.aapl_historical_stock_price
    
**Note**: You only need to include `DISTINCT` once in `SELECT` clause.

__Practice: Select unique values in `year` column, in chronological order__

	SELECT DISTINCT year
    FROM tutorial.aapl_historical_stock_price
    ORDER BY year
    
__Practice: Count the number of unique values in the `month` column for each year__

	SELECT year, COUNT(DISTINCT month) AS months
    FROM tutorial.aapl_historical_stock_price
    GROUP BY year
    ORDER BY year
    
__Practice: Count the number of unique values in `month` column and unique values in `year` column__

	SELECT COUNT(DISTINCT year) AS years_count, COUNT(DISTINCT month) AS months_count
    FROM tutorial.aapl_historical_stock_price
    
### SQL `CASE`

`CASE` statements are SQL's way of handling if/then logic. It is followed by at least one pair of `WHEN` and `THEN` statements. (FYI - you might be tempted to call is `CASE WHEN` but `CASE` is the accepted term.

__Example: Retrive College Players who are Seniors__

	SELECT player_name,
    	   year,
           CASE WHEN year = 'SR' THEN 'yes'
           		ELSE NULL END AS is_a_senior
	FROM benn.college_football_players
    
Here is what is happening in the example above:

1. `CASE` checks each row to see if conditional statement `year = 'SR'` is true
2. If statement is true, 'yes' gets printed in the column named `is_a_senior`
3. If statement is false, nothing happens and a null value is left in `is_a_senior`
4. Additionally, SQL is retrieving and displaying all values in `player_name` & `year` columns

Lastly, you can also use the value of `'no'` instead of `NULL` for players that aren't a senior. 

__Practice: Query that flags players from California, and sorts the results with those players first__

	SELECT player_name, state,
           CASE WHEN state = 'CA' THEN 'yes'
                ELSE 'no' END AS from_california
	FROM benn.college_football_players
    ORDER BY from_california DESC
    
You can also include as many `WHEN`/`THEN` statements as you'd like!

__Example: Categorizing players by weight__

	SELECT player_name, weight,
           CASE WHEN weight > 250 THEN 'over 250'
                WHEN weight > 200 AND weight <= 250 THEN '201-250'
                WHEN weight > 175 AND weight <= 200 THEN '176-200'
                ELSE '175 or under' END AS weight_group
    FROM benn.college_football_players
    
What is going on above is that all players and their weights will be returned with the addition of another column - `weight_group` - that categorizes them into a group based on their weight. Also it is best practice to not have overlapping values, which can get a little confusing. 

__Practice: Gather players and classify them into four categories based on height__

	SELECT player_name, height,
           CASE WHEN height >= 78 THEN '6ft 6in or above'
                WHEN height >= 74 AND height < 78 THEN 'Between 6ft 2in & 6ft 5in'
                WHEN height >= 68 AND height < 74 THEN 'Between 5ft 10in & 6ft 2in'
                ELSE 'Below 5ft 10in' END AS height_group
	FROM benn.college_football_players
	ORDER BY height DESC
    
Can also string mutliple statements together with `AND` and `OR`, similar to how you might use with a `WHERE` clause. 

__Example: Stringing together with `AND`__

	SELECT player_name,
           CASE WHEN year = 'FR' AND position = 'WR' THEN 'frosh_wr'
                ELSE NULL END AS sample_case_statement
    FROM benn.college_football_players
    
**Important Reminders for `CASE`**

- `CASE` always goes in the `SELECT` clause
- Must include following components: `WHEN`, `THEN`, and `END`
	- `ELSE` is optional
- Can make any conditional statement using any conditional operator between `WHEN` and `THEN`
- Can include multiple `WHEN`/`ELSE` statements

__Practice: Count the number of 300lb+ players by West Coast, Texas or Other Regions__

	SELECT CASE WHEN state = 'CA' OR state = 'OR' OR state = 'WA' THEN 'West Coast'
                WHEN state = 'TX' THEN 'Texas'
                ELSE 'Rest of USA' END AS players_region,
           COUNT(player_name)
    FROM benn.college_football_players
	WHERE weight >= 300
	GROUP BY players_region
    
__Practice: Get combined weight of under and upperclass players in California__

	SELECT CASE WHEN year IN ('FR', 'SO') THEN 'under classmen' 
                WHEN year IN ('JR', 'SR') THEN 'upper classmen'
                ELSE NULL END AS underclassmen_or_upperclassmen,
           SUM(weight) AS total_weight
	FROM benn.college_football_players
	WHERE state = 'CA'
	GROUP BY underclassmen_or_upperclassmen
    
__Practice: Gather number of players in each state that are FR, SO, JR, SR and the overall total__

	SELECT state,
           COUNT(CASE WHEN year = 'FR' THEN 1 ELSE NULL END) AS fr_count,
           COUNT(CASE WHEN year = 'SO' THEN 1 ELSE NULL END) AS so_count,
           COUNT(CASE WHEN year = 'JR' THEN 1 ELSE NULL END) AS jr_count,
           COUNT(CASE WHEN year = 'SR' THEN 1 ELSE NULL END) AS sr_count,
           COUNT(player_name) AS total_players
	FROM benn.college_football_players
	GROUP BY state
    
__Practice: Show number of players at schools with names starting with A-M and N-Z___

	SELECT CASE WHEN school_name < 'n' THEN 'A/M'
                WHEN school_name >= 'n' THEN 'N/Z'
                ELSE NULL END AS "School Name: Alphabetical",
           COUNT(player_name)
	FROM benn.college_football_players
	GROUP BY "School Name: Alphabetical"
	ORDER BY COUNT(player_name) DESC
    
## SQL `JOINS`

With SQL, you'll rarely be working with just one table (which we've been doing up to this point). But if we have more than one table, how do we access the information from each of them? One word: `JOINS`!

A `JOIN` is the 'joining' of two (or more) tables based  on a common id column. Let's continue to use the table of data on college football players - `benn.college_football_players` - and connect it to another table, `benn.college_football_teams`, which is a table with information on the school. 

Below is an example of a query that joina these two tables, then figures out which conference has the highest average weight!

__Example: Determine which football conference has the highest average weight__

	SELECT teams.conference AS conference,
    	   AVG(players.weight) AS average_weight
    FROM benn.college_football_players players
    JOIN benn.college_football_teams teams
    ON teams.school_name = players.school_name
    GROUP BY teams.conference
    ORDER BY AVG(players.weight) DESC
    
_Observations from the query above_

- Notice the aliases of the two tables, `players` for `benn.college_football_players` and `teams` for `benn.college_football_teams`
	- this makes writing out the query a lot easier!
    - you can refer to this alias throughout the query too!
    
__Practice: Select school name, player name, position and weight for every player in Georgia, ordered by weight (heaviest to lightest)__

	SELECT teams.school_name, players.player_name, players.position, players.weight
    FROM benn.college_football_players players
    JOIN benn.college_football_teams teams
    ON teams.school_name = players.school_name
    WHERE players.state = 'GA'
    ORDER BY players.weight DESC

_Observations from the query above_

- `ON` statement determines how the two tables relate to each other
	- in this example, the two tables have a common fields called `school_name`
    - sometimes the relational fields may be less obvious
    - these relationships are sometimes called 'mappings'
    - the two columns, `teams.school_name` and `players.school_name` are referred to as 'foreign keys`/'join keys'
- in this example, SQL will look up the school name in the `school_name` field of the `teams` table
	- if there is a match, SQL will take all columns from `teams` table and join to columns of `players` table
    
### SQL `INNER JOIN`

It's often the case that the tables being joined contain rows that don't match in the other table, and this situation can be handled through either an **inner join** or **outer join**.

An **inner join** returns the intersection of the two tables. If rows from both tables do not satisfy the `ON` statement, they are not returned. 

__Practice: Gather player names, school names and conferences for schools in FBS division__

	SELECT players.player_name, teams.school_name, teams.conference
    FROM benn.college_football_players players
    JOIN benn.college_football_teams teams
    ON teams.school_name = players.school_name
    WHERE teams.division = 'FBS (Division I-A Teams)'

__Visualization to help understand `JOIN`__

![](/img/visual-join.png)
[Source](https://mode.com/resources/sql-tutorial/sql-outer-joins/)

__Practice: Perform Inner Join between acquisitions and companies table, counting the number of non-null rows in each table__

	SELECT COUNT(acquire.company_name) AS acquire_rows,
           COUNT(company.name) AS company_rows
    FROM tutorial.crunchbase_companies company
    JOIN tutorial.crunchbase_acquisitions acquire
    ON company.name = acquire.company_name
    
### SQL `LEFT JOIN`

The `LEFT JOIN` command tells the database to return all rows in the table in the `FROM` clause, regardless of whether or not they have matches in the table in the `LEFT JOIN` clause. So in our example directly above, a `LEFT JOIN` would return all the rows in the `tutorial.crunchbase_companies` table, irregardless if they matched with rows in the `tutorial.crunchbase_acquisitions` table. 

__Practice: Count unique number of companies and acquired companies by state and order by number of acquired companies from highest to lowest (do not include results where there is no state data)

	SELECT acquire.company_state_code,
    	   COUNT(DISTINCT company.name) AS num_unique_companies,
           COUNT(DISTINCT acquire.company_name) AS num_unique_acquires
    FROM tutorial.crunchbase_companies company
    LEFT JOIN tutorial.crunchbase_acquisitions acquire
    ON company.name = acquire.company_name
    WHERE acquire.company_state_code IS NOT NULL
    GROUP BY acquire.company_state_code
    ORDER BY num_unique_acquires DESC
    
### SQL `RIGHT JOIN`

Similar to left joins but they return all rows from the table in the `RIGHT JOIN` clause and only matching rows from the table in the `FROM` clause. Important side note - `RIGHT JOIN` is rarely used because you can simply switch the two joined tables names in a `LEFT JOIN`.

__Practice: Using a `RIGHT JOIN` return the same results as the practice problem above__

	SELECT acquire.company_state_code,
           COUNT(DISTINCT company.name) AS num_unique_companies,
           COUNT(DISTINCT acquire.company_name) AS num_unique_acquires
    FROM tutorial.crunchbase_acquisitions acquire
    RIGHT JOIN tutorial.crunchbase_companies company
    ON company.name = acquire.company_name
    WHERE acquire.company_state_code IS NOT NULL
    GROUP BY acquire.company_state_code
    ORDER BY num_unique_acquires DESC
    
_Notice_ how we simply switched the tables positions!

__Practice: Return company's name, 'status' and number of unique investors in that company, order ny number of investors and limit to state of New York__