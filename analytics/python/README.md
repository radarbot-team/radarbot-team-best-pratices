# Python for Data Science

![alt text](static/python-logo.png "Python")

## Index
  * [Load data](#load-data)
  * [Work with data](#work-with-data)
    * [Numpy](#numpy)
    * [SciPy](#scipy)
    * [pandas](#pandas)
  * [Visualization](#visualization)
  * Machine Learning
    * [scikit-learn](#scikit-learn)
  * [Titanic Example](Ejercicio_practico_Titanic.ipynb)


### <a name="load_data"></a>Load data

#### Pandas

*Pandas* is the most extended library for analytics using *Python*. It is really fast, thanks to the usage of *numpy*.

##### Input types

In order to read files, the best way is to use pandas's predefined functions. They allow the load of a panda object from the following file types:
- csv
- Excel
- hdf
- sql
- json
- msgpack
- html
- gbq
- stata
- sas
- pickle

It is also possible to read text from your clipboard.

An usage example is presented below:

````python
import pandas as pd
df = pd.read_csv('datos.csv')
````

##### Loading json

JSON is used widely, so it is not surprising that pandas allows to load information from it.

The **json_normalize** method can convert a json file into a pandas dataframe.

````python
data = [{'state': 'Florida',
          'shortname': 'FL',
         'info': {
               'governor': 'Rick Scott'
          },
          'counties': [{'name': 'Dade', 'population': 12345},
                      {'name': 'Broward', 'population': 40000},
                      {'name': 'Palm Beach', 'population': 60000}]},
         {'state': 'Ohio',
          'shortname': 'OH',
          'info': {
               'governor': 'John Kasich'
          },
          'counties': [{'name': 'Summit', 'population': 1234},
                       {'name': 'Cuyahoga', 'population': 1337}]}]
from pandas.io.json import json_normalize
result = json_normalize(data, 'counties', ['state', 'shortname',
                                          ['info', 'governor']])
````                                        

The resulting output would be:

|index|name| population | info.governor | state | shortname
|---|---|---|---|---|---|
|0|Dade|12345|Rick Scott|  Florida| FL|
|1| Broward| 40000| Rick Scott|Florida| FL
|2| Palm Beach| 60000|Rick Scott|Florida| FL
|3| Summit| 1234|John Kasich| Ohio| OH
|4| Cuyahoga|1337| John Kasich|Ohio| OH

Example taken from [pandas doc](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.io.json.json_normalize.html)

Loading nested objects is a bit more complicated, but it can be achieved with some additional operations. 

##### Separators and performance

Reading a dataset from files in which values are separated by commas or tabs requires *sep* or *delimiter* parameter (both are valid).

Using custom delimiters (other than commas or tabs) might force to change from *C* engine to *Python* engine. Although *C* engine is always faster, only allows some delimiters. On the other hand, *Python* engine is slower but supports a wider set of delimiters, even regular expressions.

````python
    import pandas as pd
    df = pd.read_csv('datos.csv', sep='::', engine='python') # Setting the engine removes warning message.
````

#### Loading process information

In order to visualize a loading bar when you are iterating over any dataset information,
**tqdm** library is a good choice.

If we wanted to perform a heavy modification operation in an input dataset, would be very appropriate to check if the process is actually running or blocked: 

````python
for elem in tqdm(np.nditer(elements), total=elements.shape[0]):
  do_something()
````

The resulting output would be:

76%|████████████████████████████        | 7568/10000 [00:33<00:10, 229.00it/s]

### Work with data

#### Numpy

*Numpy* library is an extension for *Python* which provides mathematical functions for problems where arrays and matrix computations are required. For **Matlab software** users, *Numpy* library could be a great substitute. Given that *Numpy* has been part of *Python* from the beginning, is a very mature library with a lot of developments. Loading the library can be done as below:

````python
    import numpy as np
````

The main characteristic of *Numpy* is array object class. It is quite similar to lists in Python, except one condition: **In a Numpy array all the elements must be of the same type** (ex. float, int, str ...). It is used to perform mathematical operations **faster and more efficiently than using lists**.

For example, using the next code a *Numpy* array (2 rows and 3 columns) is created. The function `np.shape()` is used to check the dimension, and it is useful in case of array multiplication errors.

````python
    X = np.array( [ [1,2,3], [4,5,6]])
    np.shape(X)
````
**How to index and slice a Numpy array?**

This could be one of the first questions when a person starts with this kind of numerical libraries. Using previous X array, the way to access to first element in the first row and its last element is shown in the code excerpt below. Unlike Matlab (or R), *Numpy* uses zero-based indexing, i.e. the first element is indexed with 0 and not with 1.

````python
    first = X[0][0]
    last = X[0][-1]
````
As in *Matlab* the `eye()`function is helpful for creating a 2D array with ones on the diagonal and zeros elsewhere. It can be used to reduce computational cost in many optimization algorithms...

*Numpy* library has a lot of useful functions that allows you working with random numbers. These functions can be imported using `numpy.random`. Notice that you must set a certain `seed()` before using these functions in order to get **reproducible results**.
````python
    np.random.seed(32) # example seed is set to 32
````    
Some functions from `numpy.random`are: 
- `randn()` which generates a 'standard normal' distribution 
- `randint` which returns random integers from a low to a high input values 
- `shuffle()` is useful to modify an input sequence by shuffling its contents 
- `permutation()` randomly permutes a sequence

#### Scipy
*SciPy* (Scientific Python) is a *Python* library which is often mentioned in the same way as *NumPy*. SciPy extends the capabilities of NumPy with further useful functions for minimization, regression, Fourier-transformation and many others.


#### Pandas
This part gives a brief introduction to pandas data structures and some advices. *Pandas* is a *Python* library for data analysis which has many functions for using DataFrame structures. A DataFrame structure called `df` is used to clarify all the examples contained in this part. The code excerpt below allows to import the library and to create an empty dataframe.

````python
    import pandas as pd
    df = pd.DataFrame()
````

An easy way to start with *Pandas* library is loading a dataset from a CSV file, returning a DataFrame structure. See the example below.

````python
    df= pd.read_csv('../datos.csv').fillna(" ")
````

In order to introduce this library some frequently asked questions are presented next.

**How to get information from a DataFrame structure?**

It is useful to extract and get some information from your DataFrame, for example with the functions `df.info` and `df.describe`. The second one also provides a brief statistical description about your dataset, for example the mean, standard deviation, maximum values and percentiles…

A really good function that allows types your DataFrame structure are composed of is `df.dtypes`.

A quickly way to see the first and the last records is to use `df.head(N)` and `df.tail(N)` respectively, where N is the number of records that you want to check.

**How to select a certain field or slicing a DataFrame structure?**

The easy way to select a column or field in a DataFrame is using the notation `df[‘name’]`. A great thing is to use the previous functions in order to get information just for this column. For example: `df[‘name’].describe()` or `df[‘name’].dtypes`. Several columns can be selected with an additional bracket as `df[[‘name1’, ‘name2’]]`.

**How to join, combine and group several DataFrame structures?**

In almost every analysis, we need to merge and join datasets, usually with a specific order and relational way. To resolve this issue pandas library contains at least 3 great functions; `groupby()`, `merge()` and `concat()`.

Groupby function is used basically to compute an aggregation (ex. Sum, mean…), split into slices or groups and perform a transformation. It returns an object called **GroupBy** which in turn allows other great features. Also, it provides the ability to group by multiple columns. An example could be, grouping by columns named A and B, compute its mean value (by group):

````python
Group = df.groupby('A','B']).mean()
````
Also useful if you want to apply multiple functions to a group and collect results. And again, `describe()`function is so useful after group and apply functions because it gives a lot of information about the output. pandas-groupby feature is great, it performs some operation on each of the pieces and it is similar as `plyr` and `dplyr` packages in R language.
 
For SQL programmers, `merge()` function provides two DataFrames to be joined on one or more keys, using common syntax (on, left, right, inner, outer...). For example:   

````python
    pd.merge(df1,df2, on ='key', how= 'outer')
````

This library also provides `concat()` as a way to combine DataFrame structures. It is similar to `UNION` function in SQL language. So useful when a different approach and model provides a part of the final result and you just want to combine.

````python
    pd.concat([df1, df2])
````

### Visualization
*Matplotlib*, *seaborn* and *Bokeh* libraries are used for plotting and visualization.
````python
    import matplotlib as mp
    import seaborn as sn
    import bokeh as bk
````

### scikit-learn
The main python library for Machine Learning is [scikit-learn](http://scikit-learn.org/). It is built on top of *Numpy*, *Scipy* and *Matplotlib*. And it's well documented.
````python
    # k nearest neighbours
    from sklearn.neighbors import KNeighborsClassifier
    # Random Forest
    from sklearn.ensemble import RandomForestClassifier
````
___

[BEEVA](https://www.beeva.com) | Technology and innovative solutions for companies
