# Reshape Pandas Warmup

![](viz/pandas_exercise.gif)

Don't worry about completing this if you're in a time-crunch with other material!  The strong panda will be here when you get back.


```python
# Run this cell w/o changes to load tests

from test_background import pkl_dump, test_obj_dict, run_test_dict, run_test
```

## Imports

Import:
- Pandas as the alias pd

- Numpy as the alias np

- Matplotlib.pyplot as the alias plt

Run %matplotlib inline


```python
#Your code here
```

Read in the csv 'econ_stats' from the data folder as a dataframe

Assign it to the variable econ_stats


```python
#Your code here
```

## Data Exploration

Take a look at `econ_stats.head()`, `econ_stats.info()` and `econ_stats.describe(include='all')`

How many unique values are in the categorical variables `Country` and `Stat`?  If some repeat, what are they?

How many unique values in the numerical variables `Year` and `Data`?  If some repeat, what are they?

How does the data appear to be organized?


```python
#Your code here
```

## Data Reshaping

In order to make this data easier to manipulate and analyze, we want to turn it from "long" to "wide"

In other words, we want to make each row an observation which has:
- 1 country
- 1 year
- data values for each of the 3 stats



making a total of 5 columns

We already have "Country" and "Year", so let's work on making the additional 3 columns.

In class, we've gone over `.pivot()` to reshape.

We're going to make each of these three columns a different way.  

## Way 1: list comprehension 

To make the `Domestic Wheat Price` column, do the following:

- create a column named `Domestic Wheat Price` that is filled with nulls
- write a function that:
    - takes input parameters `row`, `df`
    - finds the `Year` value in `df` for that `row`
    - finds the `Country` in `df` for that `row`
    - finds the value of `Domestic Wheat Price` in `df` for those `Year` and `Country` values
    - returns that value
- make a list comprehension that uses your function to fill `Domestic Wheat Price`
- check to make sure `Domestic Wheat Price` no longer has nulls!

(notice that there are repeat values in `Domestic Wheat Price`, because we have multiple rows with the same country and year values)


```python
#Your code here
```


```python
#run this cell to check your work

run_test(econ_stats['Domestic Wheat Price'], 'domestic_wheat_price_column')
```

## Way 2: Split-Apply-Combine

To make the `Wheat Exports` column, do the following:

- create an empty list called `dfs`
- create a `for loop` cycling through the unique `Country` names
- inside the loop, for each country:
    - Assign to a variable `temp` all rows from `econ_stats` where:
        - the value in the `Country` column is the country in the current loop
        - the value in the `Stat` column is 'Wheat Exports'
    - Rename the `data` column to 'Wheat Exports'
    - drop extraneous columns (everything but `Country`, `Year` and `Wheat Exports`) from `temp`
    - Append `temp` to `dfs`
- check to make sure you have all the dataframes you expect to have in `dfs`
- assign to `wheat_exports` the [concatenation](https://pandas.pydata.org/pandas-docs/version/0.25.0/reference/api/pandas.concat.html) of all the dataframes inside of `dfs` 
- merge `wheat_exports_frames` with `econ_stats`, using `Country` and `Year` as the keys
- sort `econ_stats` by `Country` and `Year`
- reset the index (this will become clearer why in the method below) without appending the old index as a column (ie make the `drop` parameter `True`)


```python
#Your code here
```


```python
#run this cell to check your work

run_test(econ_stats['Wheat Exports'], 'wheat_exports_column')
```

## Way 3: .where()

[`.where()`](https://pandas.pydata.org/pandas-docs/version/0.25.2/reference/api/pandas.Series.where.html) is a *fast* way to replace values of one series or dataframe based on boolean conditionals.

In our case, we want to make a new column `Wheat Imports` that:
- has the value of `Data` when `Stat` == `"Wheat Imports"`
- replaces the values of `Data` where `Stat` != `"Wheat Imports"` with the values of `Data` when `Stat` == `"Wheat Imports"` (for the right `Country`/`Year` combination)

`.where()` as a method takes two parameters:
- a series or dataframe of `True` or `False` statements
- a replacement object to take substitute values from when the statments are `False`

It works like this: 
- at indices where the first argument is `True`, the value in the original column at that index is kept. 
- at indices where the first argument is `False`, the value from the second argument at that index is substituted.   

Both parameters can be a series or a dataframe.  

*Ex: `a` = pd.Series([1,2,3,4,5,6]), `b` = pd.Series([0,-1,-2,-3,-4,-5])*

*`a.where(a%2==0, b)` = 0,2,-2,4,-4,6*

<i>"At indices where the value of `a` at that index is divisible by 2, keep the value of `a` at that index. At indices where the `a` value is not divisible by 2, substitute the value of `b` at that index."</i>

To make the `Wheat Imports` column:
- append the `.where()` method to `econ_stats['Data']`
- for the first argument - a series or dataframe which has `True`/`False` statements at every value - create a series where each value states whether the `Stat` column at that index is equal to `"Wheat Imports"`.  

*Hint: you would use this series to filter `econ_stats` to show rows where `Stat`==`"Wheat Imports"`*

- where this series is `True`, the value at that index of `Data` will be kept.
- to create the series (the values of `Data` where `Stat`==`"Wheat Imports"`) to use as the second argument:
    - filter `econ_stats` to only show rows where `Stat` has the value of `Wheat Imports`
    - select the 'Data' column from that frame
    - [repeat](https://pandas.pydata.org/pandas-docs/version/0.25.0/reference/api/pandas.Series.repeat.html) the values 3 times (why?)
    - reset the index (why?)
    - select the `Data` column from that frame
- remember to assign the `econ_stats['Data'].where()` expression you made to `econ_stats['Wheat Imports']`

Make sure the frame is still sorted by `Country` and `Year` when you're done


```python
#Your code here
```


```python
#run this cell to test your work

run_test(econ_stats['Wheat Imports'], 'wheat_imports_column')
```

## Drop 

The `Stat` and `Data` columns are now redundant; drop 'em

We now have a bunch of duplicated rows

- Find the number of duplicated rows
- Drop the duplicated rows
- Make sure the resulting dataframe has the number of rows you expect


```python
#Your code here
```


```python
#run this cell to test your work
run_test(econ_stats, 'dropped_rows')
```

## Strrrretch goal: your turn

You may notice that we can continue the "widening" process further, by making columns for each `Country`'s data and having `Year` the only column left from our original frame

Let's do that

Create 15 new columns for each row, 3 of the data columns each for each of the five countries

Call each new column `"Country+existing data column name"`

Use whichever method you think is fastest, but apply the method dynamically (eg write the same code 15 times with some values changed)

Drop the `Country` column and the three data columns after you're done, so that the frame is just `Year` and the fifteen new data columns

Drop the duplicated rows, and check your work!


```python
#Your code here
```


```python
#run this cell to test your work
run_test(econ_stats, 'stretch_goal')
```


```python

```


```python

```
