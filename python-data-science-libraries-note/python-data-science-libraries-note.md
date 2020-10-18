# Python Data Science Libraries Note

- [Python Data Science Libraries Note](#python-data-science-libraries-note)
  - [NumPy](#numpy)
  - [Pandas](#pandas)
    - [Basic Concepts](#basic-concepts)
    - [Read Data Files](#read-data-files)
    - [Indexing and Selecting](#indexing-and-selecting)
      - [Python Native Accessor](#python-native-accessor)
      - [Pandas Accessor](#pandas-accessor)
        - [Index-based Selection: `iloc`](#index-based-selection-iloc)
        - [Label-based Selection: `loc`](#label-based-selection-loc)
        - [Diff between `iloc` and `loc`](#diff-between-iloc-and-loc)
      - [Manipulate Index](#manipulate-index)
      - [Conditional Selection](#conditional-selection)
    - [Assign Data](#assign-data)
    - [Summary Functions](#summary-functions)
    - [Mapping Operations](#mapping-operations)
      - [Built-ins](#built-ins)
    - [Grouping](#grouping)
    - [Sorting](#sorting)
    - [Snippets](#snippets)
  - [Scikit-Learn (sklearn)](#scikit-learn-sklearn)

---

## NumPy

- ndarray
- Given two lists, use NumPy to do element-wise calculations. 
- Do not need to iterate the list. You can operate all elements based on array.

```python
# import module
import numpy as np

# transfer a list to a numpy array 
array_name = np.array(list_name)

# subsetting 
# get elements that are greater than 10 
subset_array_name = array_name[array_name > 10]
```

---

## Pandas 

### Basic Concepts

A `DataFrame` is a table. 

An `entry` is a cell. 

```python
import pandas as pd

# create a DataFrame
pd.DataFrame({
  'Bob': ['I liked it.', 'It was awful.'], 
  'Sue': ['Pretty good.', 'Bland.']
  },
  index=['Product A', 'Product B'])
```

|   | Bob  | Sue  |
|---|---|---|
| Product A  | I liked it.  | Pretty good.  |
| Product B  | It was awful.  | Bland.  |

Column labels: "Bob", "Sue"

Row labels: "Product A", "Product B"

Index: The list of row labels used in a DataFrame.

If you do not specify index explicitly, it would be 0, 1, 2, 3 ...

A `Series` is a list, or basically a single column in a DataFrame. 

```python
# create a Series
pd.Series([1, 2, 3, 4, 5])

# result: 
# 0    1
# 1    2
# 2    3
# 3    4
# 4    5
# dtype: int64
```

A Series does not have a column name, it only has one overall name.

```python
pd.Series([30, 35, 40], index=['2015 Sales', '2016 Sales', '2017 Sales'], name='Product A')

# result: 
# 2015 Sales    30
# 2016 Sales    35
# 2017 Sales    40
# Name: Product A, dtype: int64
```

### Read Data Files

```python
# read a csv file
wine_reviews = pd.read_csv("../input/wine-reviews/winemag-data-130k-v2.csv")

# use parameter "index_col" to specify index column in the csv file
wine_reviews = pd.read_csv("../input/wine-reviews/winemag-data-130k-v2.csv", index_col=0)
```

By default, `pd.read_excel()` loads the first sheet of an Excel spreadsheet.

### Indexing and Selecting

#### Python Native Accessor

Indexing operator: `[]`

```python
# select a specific Series out of a DataFrame
wine_reviews.country
# or
wine_reviews['country']

# result:
# 0            Italy
# 1         Portugal
#             ...   
# 129969      France
# 129970      France
# Name: country, Length: 129971, dtype: object

# select a specific value of a cell
wine_reviews['country'][0]

# result:
# 'Italy'
```

#### Pandas Accessor

Both `loc` and `iloc` are row-first, column-second. This is the opposite of what we do in native Python, which is column-first, row-second.

##### Index-based Selection: `iloc`

```python
# select the first row of data in a DataFrame
wine_reviews.iloc[0]

# result:
# country                                                    Italy
# description    Aromas include tropical fruit, broom, brimston...
#                                      ...                        
# variety                                              White Blend
# winery                                                   Nicosia
# Name: 0, Length: 13, dtype: object

# select the first column
# ":" operator means everything
wine_reviews.iloc[:, 0]

# result:
# 0            Italy
# 1         Portugal
#             ...   
# 129969      France
# 129970      France
# Name: country, Length: 129971, dtype: object

# select the first 3 rows of the first column 
wine_reviews[:3, 0]

# result:
# 0       Italy
# 1    Portugal
# 2          US
# Name: country, dtype: object

# select the second and third row of the first column
wine_reviews[1:3, 0]

# result:
# 1    Portugal
# 2          US
# Name: country, dtype: object

# select the first, second and third row of the first column
wine_reviews[[0, 1, 2], 0]

# select the last 5 rows
wine_reviews[-5:]
```

##### Label-based Selection: `loc`

```python
# select the row with index 0 and column "country"
wine_reviews.loc[0, 'country']

# result:
# 'Italy'

# select column 'taster_name', 'taster_twitter_handle', 'points'
wine_reviews.loc[:, ['taster_name', 'taster_twitter_handle', 'points']]
```

##### Diff between `iloc` and `loc`

`iloc`: The first element of the range is included and the last one excluded. `0:10` will select entries 0,...,9.

`loc`: Both the first and the last element are included. `0:10` will select entries 0,...,10.  

#### Manipulate Index

Index is mutable. 

Set index to column "title": `wine_reviews.set_index('title')`

#### Conditional Selection

```python
# filter country is "Italy"
wine_reviews.loc[wine_reviews.country == 'Italy']

# filter country is "Italy" and points >= 90
wine_reviews.loc[(wine_reviews.country == 'Italy') & (wine_reviews.points >= 90)]

# filter country is "Italy" or "France"
wine_reviews.loc[wine_reviews.country.isin(['Italy', 'France'])]

# filter price is not null
wine_reviews.loc[wine_reviews.price.notnull()]

# filter price is null
wine_reviews.loc[wine_reviews.price.isnull()]
```

### Assign Data

```python
# create a new column with a constant value
wine_reviews['critic'] = 'everyone'

wine_reviews['critic']

# result:
# 0         everyone
# 1         everyone
#             ...   
# 129969    everyone
# 129970    everyone
# Name: critic, Length: 129971, dtype: object

# create a new column with an iterable of values
wine_reviews['index_backwards'] = range(len(wine_reviews), 0, -1)

wine_reviews['index_backwards']

# result:
# 0         129971
# 1         129970
#            ...  
# 129969         2
# 129970         1
# Name: index_backwards, Length: 129971, dtype: int64
```

### Summary Functions

```python
# check how large the DataFrame is
df.shape

# result: 
# (number_of_rows, number_of_columns)

# show the first 5 rows of a DataFrame
df.head()

# show the first n rows of a DataFrame
df.head(n)

# get a high-level summary of the attributes of a column
# "points" is a column name
wine_reviews.points.describe()

# result:
# count    129971.000000
# mean         88.447138
#              ...      
# 75%          91.000000
# max         100.000000
# Name: points, Length: 8, dtype: float64

# get the mean of a column
wine_reviews.points.mean()

# result:
# 88.44713820775404

# get a list of unique values of a column
wine_reviews.taster_name.unique()

# result:
# array(['Kerin O’Keefe', 'Roger Voss', 'Paul Gregutt',
#        'Alexander Peartree', 'Michael Schachner', 'Anna Lee C. Iijima',
#        'Virginie Boone', 'Matt Kettmann', nan, 'Sean P. Sullivan',
#        'Jim Gordon', 'Joe Czerwinski', 'Anne Krebiehl\xa0MW',
#        'Lauren Buzzeo', 'Mike DeSimone', 'Jeff Jenssen',
#        'Susan Kostrzewa', 'Carrie Dykes', 'Fiona Adams',
#        'Christina Pickard'], dtype=object)

# get count of unique values of a column
wine_reviews.taster_name.value_counts()

# result:
# Roger Voss           25514
# Michael Schachner    15134
#                      ...  
# Fiona Adams             27
# Christina Pickard        6
# Name: taster_name, Length: 19, dtype: int64
```

### Mapping Operations 

`map()` method is similar to the one in Spark. It takes one set of values and "maps" them to another set of values.

```python
# remean column "points": use each value of column "points" to minus the mean of all values
wine_reviews_points_mean = wine_reviews.points.mean()
wine_reviews.points.map(lambda p: p - wine_reviews_points_mean)

# result:
# 0        -1.447138
# 1        -1.447138
#             ...   
# 129969    1.552862
# 129970    1.552862
# Name: points, Length: 129971, dtype: float64
```

If you want to transform a whole DataFrame instead a Series by calling a custom method on each row, use `apply()`.

```python
def remean_points(row): 
  row.points = row.points - wine_reviews_points_mean
  return row

wine_reviews.apply(remean_points, axis='columns')
```

**NOTE** that `map()` and `apply()` return new, transformed Series and DataFrames, respectively. They do not modify the original data they are called on. 

#### Built-ins

Pandas provides many common mapping operations as built-ins. These operators are **faster** than `map()` or `apply()` because they uses speed ups built into Pandas.

```python
# remean column "points"
wine_reviews.points - wine_reviews_points_mean

# result:
# 0        -1.447138
# 1        -1.447138
#             ...   
# 129969    1.552862
# 129970    1.552862
# Name: points, Length: 129971, dtype: float64

# combine country and region info
wine_reviews.country + ' - ' + wine_reviews.region_1

# result:
# 0            Italy - Etna
# 1                     NaN
#                ...       
# 129969    France - Alsace
# 129970    France - Alsace
# Length: 129971, dtype: object
```

However, they are not as flexible as `map()` or `apply()`, which can do more advanced things, like applying conditional logic.

### Grouping 

You can think of each group as being a slice of the DataFrame containing only data with values that match.

```python
# get the number of count for each value of column "points"
wine_reviews.groupby('points').points.count()

# result:
# points
# 80     397
# 81     692
#       ... 
# 99      33
# 100     19
# Name: points, Length: 21, dtype: int64

# get the cheapest wine in each point value category
wine_reviews.groupby('points').price.min()

# result:
# points
# 80      5.0
# 81      5.0
#        ... 
# 99     44.0
# 100    80.0
# Name: price, Length: 21, dtype: float64

# get the name of the first wine reviewed from each winery in the dataset
wine_reviews.groupby('winery').apply(lambd df: df.title.iloc[0])

# result:
# winery
# 1+1=3                          1+1=3 NV Rosé Sparkling (Cava)
# 10 Knots                 10 Knots 2010 Viognier (Paso Robles)
#                    ...                        
# àMaurice    àMaurice 2013 Fred Estate Syrah (Walla Walla V...
# Štoka                         Štoka 2009 Izbrani Teran (Kras)
# Length: 16757, dtype: object


```

### Sorting 

### Snippets

```python
import pandas as pd

# set max number of rows to display as 5 when showing DataFrame
pd.set_option('display.max_rows', 5)
# set max number of columns to display as 5 when showing DataFrame
pd.set_option('display.max_columns', 5)




```



Extract the response variable: `target = <df_name>.pop("<column_name>")`

---

## Scikit-Learn (sklearn)

With sklearn, there are four main steps to train a machine learning model: 

1. Instantiate a model with specified hyperparameters: this will configure the machine learning model you want to train.
2. Train the model with training data: during this step, the model will learn the best parameters to get predictions as close as possible to the actual values of the target.
3. Predict the outcome from input data: using the learned parameter, the model will predict the outcome for new data.
4. Assess the performance of the model predictions: for checking whether the model learned the right patterns to get accurate predictions.

Each algorithm will have its own specific hyperparameters that can be tuned. If you leave the hyperparameters blank, the model will use the default values specified by sklearn.

**Recommend**: at least set the `random_state` hyperparameter in order to get reproducible results every time that you have to run the same code.