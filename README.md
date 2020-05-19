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

#imports

#data manipulation
import pandas as pd
import numpy as np

#jn command to render matplotlib graphs inline
%matplotlib inline

#jn command to turn off klaxon alarms
import warnings
warnings.filterwarnings('ignore')
```

Read in the csv 'econ_stats' from the data folder as a dataframe

Assign it to the variable econ_stats


```python

econ_stats = pd.read_csv('data/econ_stats.csv')
```

## Data Exploration

Take a look at `econ_stats.head()`, `econ_stats.info()` and `econ_stats.describe(include='all')`

How many unique values are in the categorical variables `Country` and `Stat`?  If some repeat, what are they?

How many unique values in the numerical variables `Year` and `Data`?  If some repeat, what are they?

How does the data appear to be organized?


```python

print(econ_stats.head())
print(econ_stats.info())
print(econ_stats.describe(include='all'))

for column in econ_stats.columns:
    print(f'There are {len(econ_stats[column].unique())} unique values in {column}')

print(f'''
There are {len(econ_stats.Country.unique())} unique values in 'Country' {econ_stats.Country.unique()}

{len(econ_stats.Stat.unique())} in 'Stat' {econ_stats.Stat.unique()}

{len(econ_stats.Year.unique())} in 'Year' {econ_stats.Year.unique()}

and {len(econ_stats.Data.unique())} in 'Data'

For every year b/t 1956 and 1965, there are 5 countries and 3 statistics for which there is a 
unique piece of data
''')
```

      Country  Year                  Stat       Data
    0      US  1956  Domestic Wheat Price  22.981728
    1      US  1956         Wheat Exports  80.327631
    2      US  1956         Wheat Imports  54.982899
    3      US  1957  Domestic Wheat Price  50.507799
    4      US  1957         Wheat Exports  54.024780
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 150 entries, 0 to 149
    Data columns (total 4 columns):
    Country    150 non-null object
    Year       150 non-null int64
    Stat       150 non-null object
    Data       150 non-null float64
    dtypes: float64(1), int64(1), object(2)
    memory usage: 4.8+ KB
    None
           Country         Year           Stat        Data
    count      150   150.000000            150  150.000000
    unique       5          NaN              3         NaN
    top         US          NaN  Wheat Imports         NaN
    freq        30          NaN             50         NaN
    mean       NaN  1960.500000            NaN   82.056109
    std        NaN     2.881904            NaN   33.415946
    min        NaN  1956.000000            NaN    8.157261
    25%        NaN  1958.000000            NaN   58.285550
    50%        NaN  1960.500000            NaN   81.394829
    75%        NaN  1963.000000            NaN  106.787937
    max        NaN  1965.000000            NaN  154.399830
    There are 5 unique values in Country
    There are 10 unique values in Year
    There are 3 unique values in Stat
    There are 150 unique values in Data
    
    There are 5 unique values in 'Country' ['US' 'UK' 'Soviet Union' 'China' 'France']
    
    3 in 'Stat' ['Domestic Wheat Price' 'Wheat Exports' 'Wheat Imports']
    
    10 in 'Year' [1956 1957 1958 1959 1960 1961 1962 1963 1964 1965]
    
    and 150 in 'Data'
    
    For every year b/t 1956 and 1965, there are 5 countries and 3 statistics for which there is a 
    unique piece of data
    


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

econ_stats['Domestic Wheat Price'] = np.nan

econ_stats.head()

def fill_price(row, df):
    
    '''
    row-wise grabbing of 'Domestic Wheat Price' value
    
    Paramters: 
        row: specific row to grab information from
        df: dataframe w/ "Country", "Year", "Stat" and "Data" columns
            'Stat' must include "Domestic Wheat Price", with value in the "Data" column
            
    output: value of 'Stat' column for 'Domestic Wheat Price' for 
    values matching the Country and Year of given row
    
    
    '''
    row_year = df['Year'][row]
    
    row_country = df['Country'][row]
    
    mask = (
        (df['Year']==row_year) &
        (df['Country']==row_country) &
        (df['Stat']=='Domestic Wheat Price')
    )
    
    price = df[mask]['Data'].values[0]
    
    return price

econ_stats['Domestic Wheat Price'] = [
    fill_price(row, econ_stats) 
    for row 
    in econ_stats.index
]

print('There are nulls in "Domestic Wheat Price": {}'
      .format(
          econ_stats['Domestic Wheat Price']
          .isnull()
          .values
          .any()
      )
)

econ_stats.head()

# #used for tests
# pkl_dump([(
#     econ_stats['Domestic Wheat Price'], 'domestic_wheat_price_column'
# )])
```

    There are nulls in "Domestic Wheat Price": False





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
      <th>Country</th>
      <th>Year</th>
      <th>Stat</th>
      <th>Data</th>
      <th>Domestic Wheat Price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>US</td>
      <td>1956</td>
      <td>Domestic Wheat Price</td>
      <td>22.981728</td>
      <td>22.981728</td>
    </tr>
    <tr>
      <td>1</td>
      <td>US</td>
      <td>1956</td>
      <td>Wheat Exports</td>
      <td>80.327631</td>
      <td>22.981728</td>
    </tr>
    <tr>
      <td>2</td>
      <td>US</td>
      <td>1956</td>
      <td>Wheat Imports</td>
      <td>54.982899</td>
      <td>22.981728</td>
    </tr>
    <tr>
      <td>3</td>
      <td>US</td>
      <td>1957</td>
      <td>Domestic Wheat Price</td>
      <td>50.507799</td>
      <td>50.507799</td>
    </tr>
    <tr>
      <td>4</td>
      <td>US</td>
      <td>1957</td>
      <td>Wheat Exports</td>
      <td>54.024780</td>
      <td>50.507799</td>
    </tr>
  </tbody>
</table>
</div>



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

dfs = []

for country in econ_stats.Country.unique():

    mask = (
        (econ_stats['Country']== country) &
        (econ_stats['Stat'] == 'Wheat Exports')
    )

    temp = econ_stats[mask]

    temp.rename(columns={'Data':'Wheat Exports'}, inplace=True)

    temp.drop(['Stat', 'Domestic Wheat Price'], axis=1, inplace=True)

    dfs.append(temp)
    
print(f'Expect 5 frames, have {len(dfs)}')
    
wheat_exports = pd.concat(dfs)

econ_stats = pd.merge(
    econ_stats, wheat_exports, 
    how='inner', 
    on=['Country', 'Year']
)

econ_stats.sort_values(['Country', 'Year'], inplace=True)

econ_stats.reset_index(inplace=True, drop=True)

# econ_stats = econ_stats[['Country', 'Year', 'Stat', 'Data', 'Domestic Wheat Price', 'Wheat Exports']]

econ_stats.head()

# #used for tests
# pkl_dump([(
#     econ_stats['Wheat Exports'], 'wheat_exports_column'
# )])
```

    Expect 5 frames, have 5





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
      <th>Country</th>
      <th>Year</th>
      <th>Stat</th>
      <th>Data</th>
      <th>Domestic Wheat Price</th>
      <th>Wheat Exports</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>China</td>
      <td>1956</td>
      <td>Domestic Wheat Price</td>
      <td>81.933098</td>
      <td>81.933098</td>
      <td>56.343518</td>
    </tr>
    <tr>
      <td>1</td>
      <td>China</td>
      <td>1956</td>
      <td>Wheat Exports</td>
      <td>56.343518</td>
      <td>81.933098</td>
      <td>56.343518</td>
    </tr>
    <tr>
      <td>2</td>
      <td>China</td>
      <td>1956</td>
      <td>Wheat Imports</td>
      <td>49.741995</td>
      <td>81.933098</td>
      <td>56.343518</td>
    </tr>
    <tr>
      <td>3</td>
      <td>China</td>
      <td>1957</td>
      <td>Domestic Wheat Price</td>
      <td>74.558164</td>
      <td>74.558164</td>
      <td>79.839552</td>
    </tr>
    <tr>
      <td>4</td>
      <td>China</td>
      <td>1957</td>
      <td>Wheat Exports</td>
      <td>79.839552</td>
      <td>74.558164</td>
      <td>79.839552</td>
    </tr>
  </tbody>
</table>
</div>



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

econ_stats['Wheat Imports'] = econ_stats['Data'].where(
    econ_stats['Stat'] == 'Wheat Imports',
    econ_stats
    [econ_stats['Stat']=='Wheat Imports']
    ['Data']
    .repeat(3)
    .reset_index()
    ['Data']
)

# #used for tests
# pkl_dump([(
#     econ_stats['Wheat Imports'], 'wheat_imports_column'
# )])
econ_stats.head()
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
      <th>Country</th>
      <th>Year</th>
      <th>Stat</th>
      <th>Data</th>
      <th>Domestic Wheat Price</th>
      <th>Wheat Exports</th>
      <th>Wheat Imports</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>China</td>
      <td>1956</td>
      <td>Domestic Wheat Price</td>
      <td>81.933098</td>
      <td>81.933098</td>
      <td>56.343518</td>
      <td>49.741995</td>
    </tr>
    <tr>
      <td>1</td>
      <td>China</td>
      <td>1956</td>
      <td>Wheat Exports</td>
      <td>56.343518</td>
      <td>81.933098</td>
      <td>56.343518</td>
      <td>49.741995</td>
    </tr>
    <tr>
      <td>2</td>
      <td>China</td>
      <td>1956</td>
      <td>Wheat Imports</td>
      <td>49.741995</td>
      <td>81.933098</td>
      <td>56.343518</td>
      <td>49.741995</td>
    </tr>
    <tr>
      <td>3</td>
      <td>China</td>
      <td>1957</td>
      <td>Domestic Wheat Price</td>
      <td>74.558164</td>
      <td>74.558164</td>
      <td>79.839552</td>
      <td>62.476126</td>
    </tr>
    <tr>
      <td>4</td>
      <td>China</td>
      <td>1957</td>
      <td>Wheat Exports</td>
      <td>79.839552</td>
      <td>74.558164</td>
      <td>79.839552</td>
      <td>62.476126</td>
    </tr>
  </tbody>
</table>
</div>



## Drop 

The `Stat` and `Data` columns are now redundant; drop 'em

We now have a bunch of duplicated rows

- Find the number of duplicated rows
- Drop the duplicated rows
- Make sure the resulting dataframe has the number of rows you expect


```python
econ_stats.drop(['Stat', 'Data'], axis=1, inplace=True)

print(f'len before dropping {len(econ_stats)}')
print(f'number of dupes {econ_stats.duplicated().sum()}')

econ_stats.drop_duplicates(inplace=True)

print(f'len after dropping {len(econ_stats)}')

#used for tests
# pkl_dump([
#     (econ_stats, 'dropped_rows')
# ])
```

    len before dropping 150
    number of dupes 100
    len after dropping 50


## Strrrretch goal: your turn

You may notice that we can continue the "widening" process further, by making columns for each `Country`'s data and having `Year` the only column left from our original frame

Let's do that

Create 15 new columns for each row, 3 of the data columns each for each of the five countries

Call each new column `"Country+existing data column name"`

Use whichever method you think is fastest, but apply the method dynamically (eg write the same code 15 times with some values changed)

Drop the `Country` column and the three data columns after you're done, so that the frame is just `Year` and the fifteen new data columns

Drop the duplicated rows, and check your work!


```python

dfs = []

for country in econ_stats.Country.unique():

    mask = (
        (econ_stats['Country']== country) 
    )

    temp = econ_stats[mask]

    temp.rename(
        columns=
        {column:country+' '+column 
         for column 
         in temp.columns[2:]}, 
        inplace=True
    )

    temp.drop('Country', axis=1, inplace=True)
    dfs.append(temp)
    
frame = dfs[0]
for new_frame in dfs[1:]:
    frame = pd.merge(frame, new_frame, how='inner', on='Year')

econ_stats = pd.merge(
    econ_stats, frame, 
    how='outer', 
    on=['Year']
)

econ_stats.sort_values(['Country', 'Year'], inplace=True)

econ_stats.reset_index(inplace=True, drop=True)

econ_stats.drop(
    ['Country', 'Domestic Wheat Price', 
     'Wheat Exports', 'Wheat Imports'
    ], 
    axis=1, 
    inplace=True
)

print(f'len before dropping: {len(econ_stats)}')
print(f'number of dupes: {econ_stats.duplicated().sum()}')
econ_stats.drop_duplicates(inplace=True)
print(f'len after dropping: {len(econ_stats)}')

econ_stats.head()

# #used for tests
# pkl_dump([(
#     econ_stats, 'stretch_goal'
# )])
```

    len before dropping: 50
    number of dupes: 40
    len after dropping: 10





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
      <th>Year</th>
      <th>China Domestic Wheat Price</th>
      <th>China Wheat Exports</th>
      <th>China Wheat Imports</th>
      <th>France Domestic Wheat Price</th>
      <th>France Wheat Exports</th>
      <th>France Wheat Imports</th>
      <th>Soviet Union Domestic Wheat Price</th>
      <th>Soviet Union Wheat Exports</th>
      <th>Soviet Union Wheat Imports</th>
      <th>UK Domestic Wheat Price</th>
      <th>UK Wheat Exports</th>
      <th>UK Wheat Imports</th>
      <th>US Domestic Wheat Price</th>
      <th>US Wheat Exports</th>
      <th>US Wheat Imports</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1956</td>
      <td>81.933098</td>
      <td>56.343518</td>
      <td>49.741995</td>
      <td>112.472808</td>
      <td>137.834532</td>
      <td>129.978449</td>
      <td>34.082140</td>
      <td>66.369536</td>
      <td>49.033085</td>
      <td>37.206487</td>
      <td>87.307491</td>
      <td>27.734788</td>
      <td>22.981728</td>
      <td>80.327631</td>
      <td>54.982899</td>
    </tr>
    <tr>
      <td>1</td>
      <td>1957</td>
      <td>74.558164</td>
      <td>79.839552</td>
      <td>62.476126</td>
      <td>88.643140</td>
      <td>112.045101</td>
      <td>65.461591</td>
      <td>125.591115</td>
      <td>91.768343</td>
      <td>101.981566</td>
      <td>65.963443</td>
      <td>26.634591</td>
      <td>60.205639</td>
      <td>50.507799</td>
      <td>54.024780</td>
      <td>51.734145</td>
    </tr>
    <tr>
      <td>2</td>
      <td>1958</td>
      <td>134.687733</td>
      <td>115.448149</td>
      <td>99.996746</td>
      <td>55.646619</td>
      <td>108.453598</td>
      <td>136.327167</td>
      <td>108.848113</td>
      <td>66.582251</td>
      <td>74.202894</td>
      <td>74.318569</td>
      <td>26.855445</td>
      <td>96.128557</td>
      <td>100.431163</td>
      <td>55.146884</td>
      <td>8.744424</td>
    </tr>
    <tr>
      <td>3</td>
      <td>1959</td>
      <td>103.992156</td>
      <td>46.135510</td>
      <td>98.383959</td>
      <td>154.399830</td>
      <td>64.940812</td>
      <td>82.299856</td>
      <td>119.030836</td>
      <td>79.480691</td>
      <td>66.575412</td>
      <td>49.330824</td>
      <td>78.337677</td>
      <td>71.321469</td>
      <td>81.066514</td>
      <td>99.459582</td>
      <td>39.571007</td>
    </tr>
    <tr>
      <td>4</td>
      <td>1960</td>
      <td>83.759193</td>
      <td>133.809948</td>
      <td>136.716246</td>
      <td>142.823510</td>
      <td>83.228588</td>
      <td>94.345567</td>
      <td>67.750034</td>
      <td>82.201482</td>
      <td>90.913316</td>
      <td>67.046024</td>
      <td>37.165599</td>
      <td>64.487817</td>
      <td>8.157261</td>
      <td>20.588848</td>
      <td>63.653674</td>
    </tr>
  </tbody>
</table>
</div>


